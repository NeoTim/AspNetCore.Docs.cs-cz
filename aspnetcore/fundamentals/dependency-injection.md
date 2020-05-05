---
title: Injektáž závislostí v ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak ASP.NET Core implementuje vkládání závislostí a jak ho používat.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/dependency-injection
ms.openlocfilehash: 8a4ee8bee09b3d6e9de932dab17bbc5c6494a492
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767522"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="0da37-103">Injektáž závislostí v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0da37-103">Dependency injection in ASP.NET Core</span></span>

<span data-ttu-id="0da37-104">[Steve Smith](https://ardalis.com/) a [Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="0da37-104">By [Steve Smith](https://ardalis.com/) and [Scott Addie](https://scottaddie.com)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0da37-105">ASP.NET Core podporuje vzor návrhu softwaru pro vkládání závislostí (DI), což je technika pro dosažení [inverze ovládacího prvku (IOC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) mezi třídami a jejich závislostmi.</span><span class="sxs-lookup"><span data-stu-id="0da37-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="0da37-106">Další informace, které jsou specifické pro vkládání závislostí v rámci <xref:mvc/controllers/dependency-injection>řadičů MVC, najdete v tématu.</span><span class="sxs-lookup"><span data-stu-id="0da37-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="0da37-107">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0da37-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="0da37-108">Přehled injektáže závislosti</span><span class="sxs-lookup"><span data-stu-id="0da37-108">Overview of dependency injection</span></span>

<span data-ttu-id="0da37-109">*Závislost* je libovolný objekt, který vyžaduje jiný objekt.</span><span class="sxs-lookup"><span data-stu-id="0da37-109">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="0da37-110">Projděte si `MyDependency` následující třídu s `WriteMessage` metodou, na které jsou závislé jiné třídy v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="0da37-110">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="0da37-111">Instance `MyDependency` třídy může být vytvořena, aby byla `WriteMessage` metoda k dispozici pro třídu.</span><span class="sxs-lookup"><span data-stu-id="0da37-111">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="0da37-112">`MyDependency` Třída je závislostí `IndexModel` třídy:</span><span class="sxs-lookup"><span data-stu-id="0da37-112">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="0da37-113">Třída vytvoří a přímo závisí na `MyDependency` instanci.</span><span class="sxs-lookup"><span data-stu-id="0da37-113">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="0da37-114">Závislosti kódu (například předchozí příklad) jsou problematické a je třeba se jim vyhnout z následujících důvodů:</span><span class="sxs-lookup"><span data-stu-id="0da37-114">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="0da37-115">Chcete- `MyDependency` li nahradit jinou implementací, třída musí být upravena.</span><span class="sxs-lookup"><span data-stu-id="0da37-115">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="0da37-116">Pokud `MyDependency` má závislosti, musí být nakonfigurovány třídou.</span><span class="sxs-lookup"><span data-stu-id="0da37-116">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="0da37-117">Ve velkém projektu s více třídami, v závislosti `MyDependency`na tom, je kód konfigurace rozptýlen napříč aplikací.</span><span class="sxs-lookup"><span data-stu-id="0da37-117">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="0da37-118">Tato implementace je obtížná pro testování částí.</span><span class="sxs-lookup"><span data-stu-id="0da37-118">This implementation is difficult to unit test.</span></span> <span data-ttu-id="0da37-119">Aplikace by měla používat třídu typu "nebo `MyDependency` zástupné procedury", což není u tohoto přístupu možné.</span><span class="sxs-lookup"><span data-stu-id="0da37-119">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="0da37-120">Vkládání závislostí řeší tyto problémy prostřednictvím:</span><span class="sxs-lookup"><span data-stu-id="0da37-120">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="0da37-121">Použití rozhraní nebo základní třídy k abstrakci implementace závislosti.</span><span class="sxs-lookup"><span data-stu-id="0da37-121">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="0da37-122">Registrace závislosti v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="0da37-122">Registration of the dependency in a service container.</span></span> <span data-ttu-id="0da37-123">ASP.NET Core poskytuje integrovaný kontejner služeb <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="0da37-123">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="0da37-124">Služby jsou registrovány v `Startup.ConfigureServices` metodě aplikace.</span><span class="sxs-lookup"><span data-stu-id="0da37-124">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="0da37-125">*Vložení* služby do konstruktoru třídy, kde se používá.</span><span class="sxs-lookup"><span data-stu-id="0da37-125">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="0da37-126">Rozhraní přebírá zodpovědnost za vytvoření instance závislosti a její odstranění, když už ji nepotřebujete.</span><span class="sxs-lookup"><span data-stu-id="0da37-126">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="0da37-127">V [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)definuje `IMyDependency` rozhraní metodu, kterou služba poskytuje aplikaci:</span><span class="sxs-lookup"><span data-stu-id="0da37-127">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="0da37-128">Toto rozhraní je implementováno konkrétním typem `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="0da37-128">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="0da37-129">`MyDependency`vyžádá <xref:Microsoft.Extensions.Logging.ILogger`1> v jeho konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="0da37-129">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="0da37-130">Není neobvyklé používat vkládání závislostí v zřetězeném způsobem.</span><span class="sxs-lookup"><span data-stu-id="0da37-130">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="0da37-131">Každá požadovaná závislost zase vyžádá své vlastní závislosti.</span><span class="sxs-lookup"><span data-stu-id="0da37-131">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="0da37-132">Kontejner vyřeší závislosti v grafu a vrátí plně přeloženou službu.</span><span class="sxs-lookup"><span data-stu-id="0da37-132">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="0da37-133">Kolektivní sada závislostí, které je třeba vyřešit, se obvykle označuje jako *strom závislosti*, *graf závislosti*nebo *graf objektů*.</span><span class="sxs-lookup"><span data-stu-id="0da37-133">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="0da37-134">`IMyDependency`a `ILogger<TCategoryName>` musí být registrována v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="0da37-134">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="0da37-135">`IMyDependency`je zaregistrován v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="0da37-135">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0da37-136">`ILogger<TCategoryName>`je zaregistrované v infrastruktuře abstrakce protokolování, takže se jedná o [službu](#framework-provided-services) , která je zaregistrovaná ve výchozím rozhraní.</span><span class="sxs-lookup"><span data-stu-id="0da37-136">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="0da37-137">Kontejner se překládá `ILogger<TCategoryName>` tím, že využije [(Obecné) otevřené typy](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)a eliminuje nutnost registrace každého [(obecného) konstruovaného typu](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="0da37-137">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="0da37-138">V ukázkové aplikaci je `IMyDependency` služba zaregistrovaná u konkrétního typu. `MyDependency`</span><span class="sxs-lookup"><span data-stu-id="0da37-138">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="0da37-139">Registruje rozsah platnosti služby po dobu životnosti jediného požadavku.</span><span class="sxs-lookup"><span data-stu-id="0da37-139">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="0da37-140">[Doby platnosti služeb](#service-lifetimes) jsou popsány dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="0da37-140">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="0da37-141">Jednotlivé `services.Add{SERVICE_NAME}` metody rozšíření přidávají (a případně nakonfigurují) služby.</span><span class="sxs-lookup"><span data-stu-id="0da37-141">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="0da37-142">Například `services.AddMvc()` přidá služby Razor Pages a MVC vyžaduje.</span><span class="sxs-lookup"><span data-stu-id="0da37-142">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="0da37-143">Doporučujeme, aby aplikace dodržovaly tuto konvenci.</span><span class="sxs-lookup"><span data-stu-id="0da37-143">We recommended that apps follow this convention.</span></span> <span data-ttu-id="0da37-144">Do oboru názvů [Microsoft. Extensions. DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) umístěte metody rozšíření pro zapouzdření skupin registrací služby.</span><span class="sxs-lookup"><span data-stu-id="0da37-144">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="0da37-145">Pokud konstruktor služby vyžaduje [vestavěný typ](/dotnet/csharp/language-reference/keywords/built-in-types-table), například `string`,, může být typ vložen pomocí [Konfigurace](xref:fundamentals/configuration/index) nebo [vzoru možností](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="0da37-145">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="0da37-146">Instance služby je požadována prostřednictvím konstruktoru třídy, kde je služba používána a přiřazena k soukromému poli.</span><span class="sxs-lookup"><span data-stu-id="0da37-146">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="0da37-147">Pole se používá pro přístup ke službě podle potřeby v celé třídě.</span><span class="sxs-lookup"><span data-stu-id="0da37-147">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="0da37-148">V ukázkové aplikaci je `IMyDependency` instance vyžádána a používána pro volání `WriteMessage` metody služby:</span><span class="sxs-lookup"><span data-stu-id="0da37-148">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="0da37-149">Služby vložené do spuštění</span><span class="sxs-lookup"><span data-stu-id="0da37-149">Services injected into Startup</span></span>

<span data-ttu-id="0da37-150">Při použití obecného hostitele ( `Startup` <xref:Microsoft.Extensions.Hosting.IHostBuilder>) mohou být do konstruktoru vloženy pouze následující typy služeb:</span><span class="sxs-lookup"><span data-stu-id="0da37-150">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="0da37-151">Služby je možné vložit do `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="0da37-151">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="0da37-152">Další informace naleznete v tématu <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="0da37-152">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="0da37-153">Služby poskytované rozhraním</span><span class="sxs-lookup"><span data-stu-id="0da37-153">Framework-provided services</span></span>

<span data-ttu-id="0da37-154">`Startup.ConfigureServices` Metoda zodpovídá za definování služeb, které aplikace používá, včetně funkcí platformy, jako je například Entity Framework Core a ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="0da37-154">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="0da37-155">`IServiceCollection` Zpočátku služba `ConfigureServices` má služby definované rozhraním v závislosti na [tom, jak byl hostitel nakonfigurovaný](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="0da37-155">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="0da37-156">Pro aplikaci, která je založená na šabloně ASP.NET Core, není běžné, že mají stovky služeb zaregistrovaných v rámci rozhraní.</span><span class="sxs-lookup"><span data-stu-id="0da37-156">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="0da37-157">V následující tabulce je uvedena malá ukázka služeb registrovaných v rozhraní.</span><span class="sxs-lookup"><span data-stu-id="0da37-157">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="0da37-158">Typ služby</span><span class="sxs-lookup"><span data-stu-id="0da37-158">Service Type</span></span> | <span data-ttu-id="0da37-159">Doba platnosti</span><span class="sxs-lookup"><span data-stu-id="0da37-159">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="0da37-160">Dočasný</span><span class="sxs-lookup"><span data-stu-id="0da37-160">Transient</span></span> |
| `IHostApplicationLifetime` | <span data-ttu-id="0da37-161">Singleton</span><span class="sxs-lookup"><span data-stu-id="0da37-161">Singleton</span></span> |
| `IWebHostEnvironment` | <span data-ttu-id="0da37-162">Singleton</span><span class="sxs-lookup"><span data-stu-id="0da37-162">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="0da37-163">Singleton</span><span class="sxs-lookup"><span data-stu-id="0da37-163">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="0da37-164">Dočasný</span><span class="sxs-lookup"><span data-stu-id="0da37-164">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="0da37-165">Singleton</span><span class="sxs-lookup"><span data-stu-id="0da37-165">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="0da37-166">Dočasný</span><span class="sxs-lookup"><span data-stu-id="0da37-166">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="0da37-167">Singleton</span><span class="sxs-lookup"><span data-stu-id="0da37-167">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="0da37-168">Singleton</span><span class="sxs-lookup"><span data-stu-id="0da37-168">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="0da37-169">Singleton</span><span class="sxs-lookup"><span data-stu-id="0da37-169">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="0da37-170">Dočasný</span><span class="sxs-lookup"><span data-stu-id="0da37-170">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="0da37-171">Singleton</span><span class="sxs-lookup"><span data-stu-id="0da37-171">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="0da37-172">Singleton</span><span class="sxs-lookup"><span data-stu-id="0da37-172">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="0da37-173">Singleton</span><span class="sxs-lookup"><span data-stu-id="0da37-173">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="0da37-174">Registrace dalších služeb pomocí rozšiřujících metod</span><span class="sxs-lookup"><span data-stu-id="0da37-174">Register additional services with extension methods</span></span>

<span data-ttu-id="0da37-175">Pokud je k dispozici metoda rozšíření kolekce služeb pro registraci služby (a jejích závislých služeb, v případě potřeby), konvence používá jedinou `Add{SERVICE_NAME}` metodu rozšíření k registraci všech služeb vyžadovaných touto službou.</span><span class="sxs-lookup"><span data-stu-id="0da37-175">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="0da37-176">Následující kód je příkladem přidání dalších služeb do kontejneru pomocí metod rozšíření [\<AddDbContext TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) a: <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*></span><span class="sxs-lookup"><span data-stu-id="0da37-176">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="0da37-177">Další informace najdete ve <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> třídě v dokumentaci k rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="0da37-177">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="0da37-178">Životnost služeb</span><span class="sxs-lookup"><span data-stu-id="0da37-178">Service lifetimes</span></span>

<span data-ttu-id="0da37-179">Vyberte odpovídající dobu života pro každou registrovanou službu.</span><span class="sxs-lookup"><span data-stu-id="0da37-179">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="0da37-180">Služby ASP.NET Core Services je možné nakonfigurovat s následujícími životnostmi:</span><span class="sxs-lookup"><span data-stu-id="0da37-180">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="0da37-181">Dočasný</span><span class="sxs-lookup"><span data-stu-id="0da37-181">Transient</span></span>

<span data-ttu-id="0da37-182">Služba přechodných životností (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) se vytvoří pokaždé, když se požadují z kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="0da37-182">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="0da37-183">Tato životnost funguje nejlépe pro odlehčené bezstavové služby.</span><span class="sxs-lookup"><span data-stu-id="0da37-183">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="0da37-184">Obor</span><span class="sxs-lookup"><span data-stu-id="0da37-184">Scoped</span></span>

<span data-ttu-id="0da37-185">Oborové služby ()<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>s rozsahem platnosti () se vytvoří jednou za požadavek klienta (připojení).</span><span class="sxs-lookup"><span data-stu-id="0da37-185">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="0da37-186">Při použití oboru služby v middlewaru založit službu do metody `Invoke` nebo. `InvokeAsync`</span><span class="sxs-lookup"><span data-stu-id="0da37-186">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="0da37-187">Nepoužívejte [vkládání pomocí injektáže konstruktoru](xref:mvc/controllers/dependency-injection#constructor-injection) , protože vynutí, aby se služba chovala jako typ singleton.</span><span class="sxs-lookup"><span data-stu-id="0da37-187">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="0da37-188">Další informace naleznete v tématu <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="0da37-188">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="0da37-189">Singleton</span><span class="sxs-lookup"><span data-stu-id="0da37-189">Singleton</span></span>

<span data-ttu-id="0da37-190">Služba singleton životnosti<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>() je vytvořena při prvním vyžádání (nebo při `Startup.ConfigureServices` spuštění a instance je určena pro registraci služby).</span><span class="sxs-lookup"><span data-stu-id="0da37-190">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="0da37-191">Každý další požadavek používá stejnou instanci.</span><span class="sxs-lookup"><span data-stu-id="0da37-191">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="0da37-192">Pokud aplikace vyžaduje chování singleton, doporučuje se povolit kontejneru služby spravovat dobu života služby.</span><span class="sxs-lookup"><span data-stu-id="0da37-192">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="0da37-193">Neimplementujte vzor návrhu singleton a poskytněte uživatelský kód pro správu životního cyklu objektu ve třídě.</span><span class="sxs-lookup"><span data-stu-id="0da37-193">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="0da37-194">Není bezpečné přeložit oborovou službu z typu singleton.</span><span class="sxs-lookup"><span data-stu-id="0da37-194">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="0da37-195">Může dojít k tomu, že služba má při zpracování dalších požadavků špatný stav.</span><span class="sxs-lookup"><span data-stu-id="0da37-195">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="0da37-196">Metody registrace služby</span><span class="sxs-lookup"><span data-stu-id="0da37-196">Service registration methods</span></span>

<span data-ttu-id="0da37-197">Metody rozšíření registrace služby nabízejí přetížení, která jsou užitečná pro konkrétní scénáře.</span><span class="sxs-lookup"><span data-stu-id="0da37-197">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="0da37-198">Metoda</span><span class="sxs-lookup"><span data-stu-id="0da37-198">Method</span></span> | <span data-ttu-id="0da37-199">Automaticky</span><span class="sxs-lookup"><span data-stu-id="0da37-199">Automatic</span></span><br><span data-ttu-id="0da37-200">objekt</span><span class="sxs-lookup"><span data-stu-id="0da37-200">object</span></span><br><span data-ttu-id="0da37-201">odvod</span><span class="sxs-lookup"><span data-stu-id="0da37-201">disposal</span></span> | <span data-ttu-id="0da37-202">Několik</span><span class="sxs-lookup"><span data-stu-id="0da37-202">Multiple</span></span><br><span data-ttu-id="0da37-203">implementace</span><span class="sxs-lookup"><span data-stu-id="0da37-203">implementations</span></span> | <span data-ttu-id="0da37-204">Pass – argumenty</span><span class="sxs-lookup"><span data-stu-id="0da37-204">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="0da37-205">Příklad:</span><span class="sxs-lookup"><span data-stu-id="0da37-205">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="0da37-206">Ano</span><span class="sxs-lookup"><span data-stu-id="0da37-206">Yes</span></span> | <span data-ttu-id="0da37-207">Ano</span><span class="sxs-lookup"><span data-stu-id="0da37-207">Yes</span></span> | <span data-ttu-id="0da37-208">Ne</span><span class="sxs-lookup"><span data-stu-id="0da37-208">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="0da37-209">Příklady:</span><span class="sxs-lookup"><span data-stu-id="0da37-209">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="0da37-210">Ano</span><span class="sxs-lookup"><span data-stu-id="0da37-210">Yes</span></span> | <span data-ttu-id="0da37-211">Ano</span><span class="sxs-lookup"><span data-stu-id="0da37-211">Yes</span></span> | <span data-ttu-id="0da37-212">Ano</span><span class="sxs-lookup"><span data-stu-id="0da37-212">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="0da37-213">Příklad:</span><span class="sxs-lookup"><span data-stu-id="0da37-213">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="0da37-214">Ano</span><span class="sxs-lookup"><span data-stu-id="0da37-214">Yes</span></span> | <span data-ttu-id="0da37-215">Ne</span><span class="sxs-lookup"><span data-stu-id="0da37-215">No</span></span> | <span data-ttu-id="0da37-216">Ne</span><span class="sxs-lookup"><span data-stu-id="0da37-216">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="0da37-217">Příklady:</span><span class="sxs-lookup"><span data-stu-id="0da37-217">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="0da37-218">Ne</span><span class="sxs-lookup"><span data-stu-id="0da37-218">No</span></span> | <span data-ttu-id="0da37-219">Ano</span><span class="sxs-lookup"><span data-stu-id="0da37-219">Yes</span></span> | <span data-ttu-id="0da37-220">Ano</span><span class="sxs-lookup"><span data-stu-id="0da37-220">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="0da37-221">Příklady:</span><span class="sxs-lookup"><span data-stu-id="0da37-221">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="0da37-222">Ne</span><span class="sxs-lookup"><span data-stu-id="0da37-222">No</span></span> | <span data-ttu-id="0da37-223">Ne</span><span class="sxs-lookup"><span data-stu-id="0da37-223">No</span></span> | <span data-ttu-id="0da37-224">Ano</span><span class="sxs-lookup"><span data-stu-id="0da37-224">Yes</span></span> |

<span data-ttu-id="0da37-225">Další informace o vyřazení typů najdete v části věnované [vyřazení služeb](#disposal-of-services) .</span><span class="sxs-lookup"><span data-stu-id="0da37-225">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="0da37-226">Běžným scénářem pro více implementací je vytvoření [typů pro testování](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="0da37-226">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="0da37-227">`TryAdd{LIFETIME}`metody registrují službu jenom v případě, že už není zaregistrovaná implementace.</span><span class="sxs-lookup"><span data-stu-id="0da37-227">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="0da37-228">V následujícím příkladu se první řádek registruje `MyDependency` pro. `IMyDependency`</span><span class="sxs-lookup"><span data-stu-id="0da37-228">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="0da37-229">Druhý řádek nemá žádný účinek, protože `IMyDependency` už má registrovanou implementaci:</span><span class="sxs-lookup"><span data-stu-id="0da37-229">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="0da37-230">Další informace naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="0da37-230">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="0da37-231">Metody [TryAddEnumerable (ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) registrují službu pouze v případě, že již není implementace *stejného typu*.</span><span class="sxs-lookup"><span data-stu-id="0da37-231">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="0da37-232">Několik služeb je vyřešeno `IEnumerable<{SERVICE}>`prostřednictvím.</span><span class="sxs-lookup"><span data-stu-id="0da37-232">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="0da37-233">Při registraci služeb chce vývojář přidat instanci jenom v případě, že už jeden ze stejného typu není přidaný.</span><span class="sxs-lookup"><span data-stu-id="0da37-233">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="0da37-234">Obecně je tato metoda používána autory knihovny k tomu, aby nedocházelo k registraci dvou kopií instance v kontejneru.</span><span class="sxs-lookup"><span data-stu-id="0da37-234">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="0da37-235">V následujícím příkladu se první řádek registruje `MyDep` pro. `IMyDep1`</span><span class="sxs-lookup"><span data-stu-id="0da37-235">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="0da37-236">Druhý řádek se `MyDep` registruje `IMyDep2`pro.</span><span class="sxs-lookup"><span data-stu-id="0da37-236">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="0da37-237">Třetí řádek nemá žádný vliv, protože `IMyDep1` už má registrovanou implementaci `MyDep`:</span><span class="sxs-lookup"><span data-stu-id="0da37-237">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="0da37-238">Chování injektáže konstruktoru</span><span class="sxs-lookup"><span data-stu-id="0da37-238">Constructor injection behavior</span></span>

<span data-ttu-id="0da37-239">Služby je možné vyřešit pomocí dvou mechanismů:</span><span class="sxs-lookup"><span data-stu-id="0da37-239">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="0da37-240"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>&ndash; Povoluje vytvoření objektu bez registrace služby v kontejneru pro vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="0da37-240"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities> &ndash; Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="0da37-241">`ActivatorUtilities`se používá s uživateli s přístupem k uživatelům, jako jsou například rutiny značek, řadiče MVC a pořadače modelů.</span><span class="sxs-lookup"><span data-stu-id="0da37-241">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="0da37-242">Konstruktory mohou přijímat argumenty, které nejsou poskytovány vkládáním závislostí, ale argumenty musí přiřadit výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="0da37-242">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="0da37-243">Když jsou služby vyřešeny `IServiceProvider` nebo `ActivatorUtilities`, [Injektáže konstruktoru](xref:mvc/controllers/dependency-injection#constructor-injection) vyžaduje *veřejný* konstruktor.</span><span class="sxs-lookup"><span data-stu-id="0da37-243">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="0da37-244">Když jsou služby vyřešeny `ActivatorUtilities`nástrojem, [Injektáže konstruktoru](xref:mvc/controllers/dependency-injection#constructor-injection) vyžaduje, aby existoval pouze jeden příslušný konstruktor.</span><span class="sxs-lookup"><span data-stu-id="0da37-244">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="0da37-245">Přetížení konstruktoru jsou podporovaná, ale může existovat jenom jedno přetížení, jehož argumenty můžou být splněné vkládáním závislostí.</span><span class="sxs-lookup"><span data-stu-id="0da37-245">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="0da37-246">Entity Framework kontexty</span><span class="sxs-lookup"><span data-stu-id="0da37-246">Entity Framework contexts</span></span>

<span data-ttu-id="0da37-247">Entity Framework kontexty se obvykle přidávají do kontejneru služby pomocí [rozsahu životnosti](#service-lifetimes) , protože operace databáze webové aplikace jsou obvykle vymezeny na požadavky klienta.</span><span class="sxs-lookup"><span data-stu-id="0da37-247">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="0da37-248">Výchozí doba života je vymezena, pokud při registraci kontextu databáze není určena doba platnosti [\<AddDbContext TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) přetížení.</span><span class="sxs-lookup"><span data-stu-id="0da37-248">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="0da37-249">Služby pro danou dobu života by neměly používat kontext databáze s kratší dobou životnosti než služba.</span><span class="sxs-lookup"><span data-stu-id="0da37-249">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="0da37-250">Možnosti života a registrace</span><span class="sxs-lookup"><span data-stu-id="0da37-250">Lifetime and registration options</span></span>

<span data-ttu-id="0da37-251">Chcete-li předvést rozdíl mezi možnostmi životního cyklu a registrací, zvažte následující rozhraní, která představují úlohy jako operaci s `OperationId`jedinečným identifikátorem.</span><span class="sxs-lookup"><span data-stu-id="0da37-251">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="0da37-252">V závislosti na tom, jak je životnost provozní služby nakonfigurována pro následující rozhraní, kontejner poskytuje buď stejnou nebo jinou instanci služby, když je vyžádána třídou:</span><span class="sxs-lookup"><span data-stu-id="0da37-252">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="0da37-253">Rozhraní jsou implementována ve `Operation` třídě.</span><span class="sxs-lookup"><span data-stu-id="0da37-253">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="0da37-254">`Operation` Konstruktor generuje identifikátor GUID, pokud není dodán:</span><span class="sxs-lookup"><span data-stu-id="0da37-254">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="0da37-255">`OperationService` Je zaregistrován, který závisí na každém z ostatních `Operation` typů.</span><span class="sxs-lookup"><span data-stu-id="0da37-255">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="0da37-256">Když `OperationService` je požadavek proveden prostřednictvím injektáže závislosti, obdrží buď novou instanci každé služby, nebo existující instanci na základě životnosti závislé služby.</span><span class="sxs-lookup"><span data-stu-id="0da37-256">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="0da37-257">Když se v případě vyžádání z kontejneru vytvoří přechodné služby `OperationId` , liší `IOperationTransient` `OperationId` se od služby. `OperationService`</span><span class="sxs-lookup"><span data-stu-id="0da37-257">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="0da37-258">`OperationService`přijme novou instanci `IOperationTransient` třídy.</span><span class="sxs-lookup"><span data-stu-id="0da37-258">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="0da37-259">Nová instance vrací jinou `OperationId`instanci.</span><span class="sxs-lookup"><span data-stu-id="0da37-259">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="0da37-260">Když se vytvoří vymezené služby pro jednotlivé požadavky klienta, `OperationId` `IOperationScoped` služba je stejná jako `OperationService` v rámci žádosti klienta.</span><span class="sxs-lookup"><span data-stu-id="0da37-260">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="0da37-261">V rámci požadavků klientů obě služby sdílejí jinou `OperationId` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="0da37-261">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="0da37-262">Pokud se služby instance singleton a singleton vytvoří jednou a použije se pro všechny požadavky klientů a všechny služby, je `OperationId` v rámci všech žádostí o službu konstantní.</span><span class="sxs-lookup"><span data-stu-id="0da37-262">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="0da37-263">V `Startup.ConfigureServices`je každý typ přidán do kontejneru podle jeho pojmenované životnosti:</span><span class="sxs-lookup"><span data-stu-id="0da37-263">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="0da37-264">`IOperationSingletonInstance` Služba používá konkrétní instanci se známým ID `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="0da37-264">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="0da37-265">Je jasné, že pokud se tento typ používá (jeho identifikátor GUID je všechny nuly).</span><span class="sxs-lookup"><span data-stu-id="0da37-265">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="0da37-266">Ukázková aplikace ukazuje dobu života objektu v rámci a mezi jednotlivými požadavky.</span><span class="sxs-lookup"><span data-stu-id="0da37-266">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="0da37-267">Ukázková aplikace `IndexModel` požaduje každý druh `IOperation` typu a. `OperationService`</span><span class="sxs-lookup"><span data-stu-id="0da37-267">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="0da37-268">Stránka potom zobrazí všechny `OperationId` hodnoty třídy modelu stránky a hodnoty služby prostřednictvím přiřazení vlastností:</span><span class="sxs-lookup"><span data-stu-id="0da37-268">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="0da37-269">Výsledkem dvou následujících výstupů jsou výsledky dvou požadavků:</span><span class="sxs-lookup"><span data-stu-id="0da37-269">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="0da37-270">**První požadavek:**</span><span class="sxs-lookup"><span data-stu-id="0da37-270">**First request:**</span></span>

<span data-ttu-id="0da37-271">Operace kontroleru:</span><span class="sxs-lookup"><span data-stu-id="0da37-271">Controller operations:</span></span>

<span data-ttu-id="0da37-272">Přechodný: d233e165-f417-469b-A866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="0da37-272">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="0da37-273">Vymezené: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="0da37-273">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="0da37-274">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="0da37-274">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="0da37-275">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="0da37-275">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="0da37-276">`OperationService`Operations</span><span class="sxs-lookup"><span data-stu-id="0da37-276">`OperationService` operations:</span></span>

<span data-ttu-id="0da37-277">Přechodný: c6b049eb-1318-4E31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="0da37-277">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="0da37-278">Vymezené: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="0da37-278">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="0da37-279">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="0da37-279">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="0da37-280">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="0da37-280">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="0da37-281">**Druhá žádost:**</span><span class="sxs-lookup"><span data-stu-id="0da37-281">**Second request:**</span></span>

<span data-ttu-id="0da37-282">Operace kontroleru:</span><span class="sxs-lookup"><span data-stu-id="0da37-282">Controller operations:</span></span>

<span data-ttu-id="0da37-283">Přechodný: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="0da37-283">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="0da37-284">Vymezené: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="0da37-284">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="0da37-285">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="0da37-285">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="0da37-286">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="0da37-286">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="0da37-287">`OperationService`Operations</span><span class="sxs-lookup"><span data-stu-id="0da37-287">`OperationService` operations:</span></span>

<span data-ttu-id="0da37-288">Přechodný: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="0da37-288">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="0da37-289">Vymezené: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="0da37-289">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="0da37-290">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="0da37-290">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="0da37-291">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="0da37-291">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="0da37-292">Pozor, které hodnoty `OperationId` se liší v rámci požadavku a mezi požadavky:</span><span class="sxs-lookup"><span data-stu-id="0da37-292">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="0da37-293">*Přechodné* objekty jsou vždy odlišné.</span><span class="sxs-lookup"><span data-stu-id="0da37-293">*Transient* objects are always different.</span></span> <span data-ttu-id="0da37-294">Přechodná `OperationId` hodnota pro první i druhý požadavek klienta se liší pro obě `OperationService` operace i mezi požadavky klientů.</span><span class="sxs-lookup"><span data-stu-id="0da37-294">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="0da37-295">Každé žádosti o službu a žádosti klienta se poskytne nová instance.</span><span class="sxs-lookup"><span data-stu-id="0da37-295">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="0da37-296">*Vymezené* objekty jsou v požadavku klienta stejné, ale v rámci požadavků klientů se liší.</span><span class="sxs-lookup"><span data-stu-id="0da37-296">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="0da37-297">Objekty *singleton* jsou stejné pro každý objekt a každý požadavek bez ohledu na to, zda `Operation` je instance uvedena v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="0da37-297">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="0da37-298">Volat služby z Main</span><span class="sxs-lookup"><span data-stu-id="0da37-298">Call services from main</span></span>

<span data-ttu-id="0da37-299">Vytvořte <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> pomocí [IServiceScopeFactory. CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) pro řešení oboru služby v rámci rozsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="0da37-299">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="0da37-300">Tento přístup je užitečný pro přístup k oboruované službě při spuštění za účelem spouštění úloh inicializace.</span><span class="sxs-lookup"><span data-stu-id="0da37-300">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="0da37-301">Následující příklad ukazuje, jak získat kontext pro `MyScopedService` `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="0da37-301">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="0da37-302">Ověřování oboru</span><span class="sxs-lookup"><span data-stu-id="0da37-302">Scope validation</span></span>

<span data-ttu-id="0da37-303">Když je aplikace spuštěná ve vývojovém prostředí a volá [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) , aby se vytvořila hostitel, použije výchozí poskytovatel služeb kontroly, aby ověřil, že:</span><span class="sxs-lookup"><span data-stu-id="0da37-303">When the app is running in the Development environment and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="0da37-304">Oborové služby nejsou přímo nebo nepřímo vyřešeny od poskytovatele kořenové služby.</span><span class="sxs-lookup"><span data-stu-id="0da37-304">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="0da37-305">Oborové služby nejsou přímo nebo nepřímo vložené do singleton.</span><span class="sxs-lookup"><span data-stu-id="0da37-305">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="0da37-306">Poskytovatel kořenové služby se vytvoří, když <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> se zavolá.</span><span class="sxs-lookup"><span data-stu-id="0da37-306">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="0da37-307">Doba života poskytovatele kořenové služby odpovídá době životnosti aplikace nebo serveru, když se poskytovatel spustí s aplikací a je uvolněný při ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="0da37-307">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="0da37-308">Oborové služby jsou uvolněny kontejnerem, který je vytvořil.</span><span class="sxs-lookup"><span data-stu-id="0da37-308">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="0da37-309">Pokud je v kořenovém kontejneru vytvořena vymezená služba, doba života služby je efektivně povýšena na typ singleton, protože je uvolněna pouze kořenovým kontejnerem při vypnutí aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="0da37-309">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="0da37-310">Při `BuildServiceProvider` ověřování oborů služeb se tyto situace zachytí.</span><span class="sxs-lookup"><span data-stu-id="0da37-310">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="0da37-311">Další informace naleznete v tématu <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="0da37-311">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="0da37-312">Žádosti o služby</span><span class="sxs-lookup"><span data-stu-id="0da37-312">Request Services</span></span>

<span data-ttu-id="0da37-313">Služby, které jsou k dispozici v `HttpContext` rámci žádosti o ASP.NET Core, jsou zpřístupněny prostřednictvím kolekce [HttpContext. RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) .</span><span class="sxs-lookup"><span data-stu-id="0da37-313">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="0da37-314">Žádosti o služby reprezentují služby nakonfigurované a požadované v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="0da37-314">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="0da37-315">Když objekty určují závislosti, jsou splněny typy nalezenými v `RequestServices`, ne. `ApplicationServices`</span><span class="sxs-lookup"><span data-stu-id="0da37-315">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="0da37-316">Obecně platí, že aplikace by tyto vlastnosti neměla používat přímo.</span><span class="sxs-lookup"><span data-stu-id="0da37-316">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="0da37-317">Místo toho požádejte o typy, které třídy vyžadují prostřednictvím konstruktorů třídy, a umožněte rozhraní vkládat závislosti.</span><span class="sxs-lookup"><span data-stu-id="0da37-317">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span> <span data-ttu-id="0da37-318">To poskytuje třídy, které jsou snáze testovány.</span><span class="sxs-lookup"><span data-stu-id="0da37-318">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="0da37-319">Preferovat požadavky na závislosti jako parametry konstruktoru pro přístup `RequestServices` ke kolekci.</span><span class="sxs-lookup"><span data-stu-id="0da37-319">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="0da37-320">Navrhnout služby pro vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="0da37-320">Design services for dependency injection</span></span>

<span data-ttu-id="0da37-321">Osvědčené postupy:</span><span class="sxs-lookup"><span data-stu-id="0da37-321">Best practices are to:</span></span>

* <span data-ttu-id="0da37-322">Navrhněte služby pro použití injektáže závislosti k získání jejich závislostí.</span><span class="sxs-lookup"><span data-stu-id="0da37-322">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="0da37-323">Vyhněte se stavovým, statickým třídám a členům.</span><span class="sxs-lookup"><span data-stu-id="0da37-323">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="0da37-324">Navrhněte aplikace, aby místo toho používaly služby s jedním prvkem, což se vyhne vytváření globálního stavu.</span><span class="sxs-lookup"><span data-stu-id="0da37-324">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="0da37-325">Vyhněte se přímému vytváření instancí závislých tříd v rámci služeb.</span><span class="sxs-lookup"><span data-stu-id="0da37-325">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="0da37-326">Přímá instance Couples kód na konkrétní implementaci.</span><span class="sxs-lookup"><span data-stu-id="0da37-326">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="0da37-327">Vytvářejte třídy aplikací malými, dobře faktoringně a snadno testované.</span><span class="sxs-lookup"><span data-stu-id="0da37-327">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="0da37-328">Pokud se zdá, že třída má příliš mnoho vložených závislostí, obvykle se jedná o znaménko, že třída má příliš mnoho zodpovědnosti a je v rozporu s [principem jediné zodpovědnosti (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="0da37-328">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="0da37-329">Pokuste se Refaktorovat třídu přesunutím některých odpovědností do nové třídy.</span><span class="sxs-lookup"><span data-stu-id="0da37-329">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="0da37-330">Mějte na paměti, že Razor Pages třídy modelu stránky a třídy kontroleru MVC by se měly soustředit na uživatelské rozhraní.</span><span class="sxs-lookup"><span data-stu-id="0da37-330">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="0da37-331">Obchodní pravidla a podrobnosti implementace přístupu k datům by měly být uchovávány ve třídách, které jsou vhodné pro tyto [samostatné obavy](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="0da37-331">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="0da37-332">Vyřazení služeb</span><span class="sxs-lookup"><span data-stu-id="0da37-332">Disposal of services</span></span>

<span data-ttu-id="0da37-333">Kontejner volá <xref:System.IDisposable.Dispose*> typy, které <xref:System.IDisposable> vytvoří.</span><span class="sxs-lookup"><span data-stu-id="0da37-333">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="0da37-334">Pokud je instance přidána do kontejneru pomocí uživatelského kódu, nebude uvolněna automaticky.</span><span class="sxs-lookup"><span data-stu-id="0da37-334">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="0da37-335">V následujícím příkladu jsou služby vytvořeny pomocí kontejneru služby a automaticky odstraněny:</span><span class="sxs-lookup"><span data-stu-id="0da37-335">In the following example, the services are created by the service container and disposed automatically:</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public interface IService3 {}
public class Service3 : IService3, IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddScoped<Service1>();
    services.AddSingleton<Service2>();
    services.AddSingleton<IService3>(sp => new Service3());
}
```

<span data-ttu-id="0da37-336">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="0da37-336">In the following example:</span></span>

* <span data-ttu-id="0da37-337">Instance služby nejsou vytvořeny kontejnerem služby.</span><span class="sxs-lookup"><span data-stu-id="0da37-337">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="0da37-338">Zamýšlené životnosti služeb nejsou rozhraním známy.</span><span class="sxs-lookup"><span data-stu-id="0da37-338">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="0da37-339">Rozhraní neodstraní služby automaticky.</span><span class="sxs-lookup"><span data-stu-id="0da37-339">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="0da37-340">Pokud se služby explicitně neodstraní v kódu pro vývojáře, zůstanou zachovány, dokud se aplikace neukončí.</span><span class="sxs-lookup"><span data-stu-id="0da37-340">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

<span data-ttu-id="0da37-341">Diskuzi o možnostech vyřazení služeb najdete [v článku čtyři způsoby odstranění IDisposable v ASP.NET Core](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/).</span><span class="sxs-lookup"><span data-stu-id="0da37-341">For a discussion of service disposal options, see [Four ways to dispose IDisposables in ASP.NET Core](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/).</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="0da37-342">Výchozí nahrazení kontejneru služby</span><span class="sxs-lookup"><span data-stu-id="0da37-342">Default service container replacement</span></span>

<span data-ttu-id="0da37-343">Integrovaný kontejner služeb je navržený tak, aby sloužil potřebám architektury a většině spotřebitelských aplikací.</span><span class="sxs-lookup"><span data-stu-id="0da37-343">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="0da37-344">Pokud nepotřebujete určitou funkci, kterou integrovaný kontejner nepodporuje, doporučujeme použít vestavěný kontejner, například:</span><span class="sxs-lookup"><span data-stu-id="0da37-344">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="0da37-345">Vkládání vlastností</span><span class="sxs-lookup"><span data-stu-id="0da37-345">Property injection</span></span>
* <span data-ttu-id="0da37-346">Vkládání na základě názvu</span><span class="sxs-lookup"><span data-stu-id="0da37-346">Injection based on name</span></span>
* <span data-ttu-id="0da37-347">Podřízené kontejnery</span><span class="sxs-lookup"><span data-stu-id="0da37-347">Child containers</span></span>
* <span data-ttu-id="0da37-348">Vlastní Správa životního cyklu</span><span class="sxs-lookup"><span data-stu-id="0da37-348">Custom lifetime management</span></span>
* <span data-ttu-id="0da37-349">`Func<T>`Podpora opožděné inicializace</span><span class="sxs-lookup"><span data-stu-id="0da37-349">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="0da37-350">Registrace založená na konvencích</span><span class="sxs-lookup"><span data-stu-id="0da37-350">Convention-based registration</span></span>

<span data-ttu-id="0da37-351">S ASP.NET Coremi aplikacemi je možné používat následující kontejnery třetích stran:</span><span class="sxs-lookup"><span data-stu-id="0da37-351">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="0da37-352">Autofac</span><span class="sxs-lookup"><span data-stu-id="0da37-352">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="0da37-353">DryIoc</span><span class="sxs-lookup"><span data-stu-id="0da37-353">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="0da37-354">Integrit</span><span class="sxs-lookup"><span data-stu-id="0da37-354">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="0da37-355">LightInject</span><span class="sxs-lookup"><span data-stu-id="0da37-355">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="0da37-356">Lamar</span><span class="sxs-lookup"><span data-stu-id="0da37-356">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="0da37-357">Stashbox</span><span class="sxs-lookup"><span data-stu-id="0da37-357">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="0da37-358">Unity</span><span class="sxs-lookup"><span data-stu-id="0da37-358">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="0da37-359">Bezpečnost vlákna</span><span class="sxs-lookup"><span data-stu-id="0da37-359">Thread safety</span></span>

<span data-ttu-id="0da37-360">Vytvořte služby, které jsou v bezpečí pro přístup z více vláken.</span><span class="sxs-lookup"><span data-stu-id="0da37-360">Create thread-safe singleton services.</span></span> <span data-ttu-id="0da37-361">Pokud má služba typu Singleton závislost na přechodné službě, přechodná služba může také vyžadovat zabezpečení vlákna v závislosti na tom, jak je používá typ singleton.</span><span class="sxs-lookup"><span data-stu-id="0da37-361">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="0da37-362">Výrobní metoda samostatné služby, jako je například druhý argument pro [AddSingleton\<TService> (IServiceCollection, Func\<IServiceProvider, TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), nemusí být bezpečná pro přístup z více vláken.</span><span class="sxs-lookup"><span data-stu-id="0da37-362">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="0da37-363">Podobně jako konstruktor typu`static`(), je zaručeno, že bude volán jednou vláknem.</span><span class="sxs-lookup"><span data-stu-id="0da37-363">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="0da37-364">Doporučení</span><span class="sxs-lookup"><span data-stu-id="0da37-364">Recommendations</span></span>

* <span data-ttu-id="0da37-365">`async/await`řešení `Task` služeb a se nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="0da37-365">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="0da37-366">Jazyk C# nepodporuje asynchronní konstruktory; Proto je doporučeným vzorem použití asynchronních metod po synchronním překladu služby.</span><span class="sxs-lookup"><span data-stu-id="0da37-366">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="0da37-367">Neukládejte data a konfiguraci přímo do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="0da37-367">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="0da37-368">Například nákupní košík uživatele by neměl být obvykle přidán do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="0da37-368">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="0da37-369">Konfigurace by měla používat [vzor možností](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="0da37-369">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="0da37-370">Podobně nepoužívejte objekty "držitel dat", které existují pouze k povolení přístupu k jinému objektu.</span><span class="sxs-lookup"><span data-stu-id="0da37-370">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="0da37-371">Je lepší žádat o skutečnou položku přes DI.</span><span class="sxs-lookup"><span data-stu-id="0da37-371">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="0da37-372">Vyhněte se statickému přístupu ke službám (například statickému psaní [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) pro použití jinde).</span><span class="sxs-lookup"><span data-stu-id="0da37-372">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="0da37-373">Vyhněte se použití *vzoru lokátoru služby*.</span><span class="sxs-lookup"><span data-stu-id="0da37-373">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="0da37-374">Například Nevolejte <xref:System.IServiceProvider.GetService*> pro získání instance služby, když můžete místo toho použít di:</span><span class="sxs-lookup"><span data-stu-id="0da37-374">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="0da37-375">**Špatný**</span><span class="sxs-lookup"><span data-stu-id="0da37-375">**Incorrect:**</span></span>

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

  <span data-ttu-id="0da37-376">**Správné**:</span><span class="sxs-lookup"><span data-stu-id="0da37-376">**Correct**:</span></span>

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

* <span data-ttu-id="0da37-377">Další změnou lokátoru služby, aby se zabránilo, je vložení továrny, která vyřeší závislosti za běhu.</span><span class="sxs-lookup"><span data-stu-id="0da37-377">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="0da37-378">Oba tyto postupy kombinují [inverze strategií řízení](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .</span><span class="sxs-lookup"><span data-stu-id="0da37-378">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

* <span data-ttu-id="0da37-379">Vyhněte se statickému přístupu k `HttpContext` (například [IHttpContextAccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="0da37-379">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="0da37-380">Podobně jako u všech sad doporučení se může stát, že se vyžaduje ignorování doporučení.</span><span class="sxs-lookup"><span data-stu-id="0da37-380">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="0da37-381">Výjimky jsou zřídka&mdash;většinou zvláštní případy v rámci samotného rozhraní.</span><span class="sxs-lookup"><span data-stu-id="0da37-381">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="0da37-382">DI je *alternativou* ke vzorům statických nebo globálních přístupů k objektům.</span><span class="sxs-lookup"><span data-stu-id="0da37-382">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="0da37-383">Je možné, že nebudete moci využít výhody DI, pokud je kombinujete se statickým přístupem k objektům.</span><span class="sxs-lookup"><span data-stu-id="0da37-383">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0da37-384">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="0da37-384">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="0da37-385">Zápis čistého kódu v ASP.NET Core pomocí injektáže závislosti (MSDN)</span><span class="sxs-lookup"><span data-stu-id="0da37-385">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="0da37-386">Explicitní závislosti – princip</span><span class="sxs-lookup"><span data-stu-id="0da37-386">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="0da37-387">Inverze řídicích kontejnerů a vzor injektáže závislosti (Martin Fowlera)</span><span class="sxs-lookup"><span data-stu-id="0da37-387">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="0da37-388">Jak zaregistrovat službu s více rozhraními v ASP.NET Core DI</span><span class="sxs-lookup"><span data-stu-id="0da37-388">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0da37-389">ASP.NET Core podporuje vzor návrhu softwaru pro vkládání závislostí (DI), což je technika pro dosažení [inverze ovládacího prvku (IOC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) mezi třídami a jejich závislostmi.</span><span class="sxs-lookup"><span data-stu-id="0da37-389">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="0da37-390">Další informace, které jsou specifické pro vkládání závislostí v rámci <xref:mvc/controllers/dependency-injection>řadičů MVC, najdete v tématu.</span><span class="sxs-lookup"><span data-stu-id="0da37-390">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="0da37-391">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0da37-391">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="0da37-392">Přehled injektáže závislosti</span><span class="sxs-lookup"><span data-stu-id="0da37-392">Overview of dependency injection</span></span>

<span data-ttu-id="0da37-393">*Závislost* je libovolný objekt, který vyžaduje jiný objekt.</span><span class="sxs-lookup"><span data-stu-id="0da37-393">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="0da37-394">Projděte si `MyDependency` následující třídu s `WriteMessage` metodou, na které jsou závislé jiné třídy v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="0da37-394">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="0da37-395">Instance `MyDependency` třídy může být vytvořena, aby byla `WriteMessage` metoda k dispozici pro třídu.</span><span class="sxs-lookup"><span data-stu-id="0da37-395">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="0da37-396">`MyDependency` Třída je závislostí `IndexModel` třídy:</span><span class="sxs-lookup"><span data-stu-id="0da37-396">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="0da37-397">Třída vytvoří a přímo závisí na `MyDependency` instanci.</span><span class="sxs-lookup"><span data-stu-id="0da37-397">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="0da37-398">Závislosti kódu (například předchozí příklad) jsou problematické a je třeba se jim vyhnout z následujících důvodů:</span><span class="sxs-lookup"><span data-stu-id="0da37-398">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="0da37-399">Chcete- `MyDependency` li nahradit jinou implementací, třída musí být upravena.</span><span class="sxs-lookup"><span data-stu-id="0da37-399">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="0da37-400">Pokud `MyDependency` má závislosti, musí být nakonfigurovány třídou.</span><span class="sxs-lookup"><span data-stu-id="0da37-400">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="0da37-401">Ve velkém projektu s více třídami, v závislosti `MyDependency`na tom, je kód konfigurace rozptýlen napříč aplikací.</span><span class="sxs-lookup"><span data-stu-id="0da37-401">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="0da37-402">Tato implementace je obtížná pro testování částí.</span><span class="sxs-lookup"><span data-stu-id="0da37-402">This implementation is difficult to unit test.</span></span> <span data-ttu-id="0da37-403">Aplikace by měla používat třídu typu "nebo `MyDependency` zástupné procedury", což není u tohoto přístupu možné.</span><span class="sxs-lookup"><span data-stu-id="0da37-403">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="0da37-404">Vkládání závislostí řeší tyto problémy prostřednictvím:</span><span class="sxs-lookup"><span data-stu-id="0da37-404">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="0da37-405">Použití rozhraní nebo základní třídy k abstrakci implementace závislosti.</span><span class="sxs-lookup"><span data-stu-id="0da37-405">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="0da37-406">Registrace závislosti v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="0da37-406">Registration of the dependency in a service container.</span></span> <span data-ttu-id="0da37-407">ASP.NET Core poskytuje integrovaný kontejner služeb <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="0da37-407">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="0da37-408">Služby jsou registrovány v `Startup.ConfigureServices` metodě aplikace.</span><span class="sxs-lookup"><span data-stu-id="0da37-408">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="0da37-409">*Vložení* služby do konstruktoru třídy, kde se používá.</span><span class="sxs-lookup"><span data-stu-id="0da37-409">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="0da37-410">Rozhraní přebírá zodpovědnost za vytvoření instance závislosti a její odstranění, když už ji nepotřebujete.</span><span class="sxs-lookup"><span data-stu-id="0da37-410">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="0da37-411">V [ukázkové aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)definuje `IMyDependency` rozhraní metodu, kterou služba poskytuje aplikaci:</span><span class="sxs-lookup"><span data-stu-id="0da37-411">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="0da37-412">Toto rozhraní je implementováno konkrétním typem `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="0da37-412">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="0da37-413">`MyDependency`vyžádá <xref:Microsoft.Extensions.Logging.ILogger`1> v jeho konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="0da37-413">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="0da37-414">Není neobvyklé používat vkládání závislostí v zřetězeném způsobem.</span><span class="sxs-lookup"><span data-stu-id="0da37-414">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="0da37-415">Každá požadovaná závislost zase vyžádá své vlastní závislosti.</span><span class="sxs-lookup"><span data-stu-id="0da37-415">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="0da37-416">Kontejner vyřeší závislosti v grafu a vrátí plně přeloženou službu.</span><span class="sxs-lookup"><span data-stu-id="0da37-416">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="0da37-417">Kolektivní sada závislostí, které je třeba vyřešit, se obvykle označuje jako *strom závislosti*, *graf závislosti*nebo *graf objektů*.</span><span class="sxs-lookup"><span data-stu-id="0da37-417">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="0da37-418">`IMyDependency`a `ILogger<TCategoryName>` musí být registrována v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="0da37-418">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="0da37-419">`IMyDependency`je zaregistrován v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="0da37-419">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0da37-420">`ILogger<TCategoryName>`je zaregistrované v infrastruktuře abstrakce protokolování, takže se jedná o [službu](#framework-provided-services) , která je zaregistrovaná ve výchozím rozhraní.</span><span class="sxs-lookup"><span data-stu-id="0da37-420">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="0da37-421">Kontejner se překládá `ILogger<TCategoryName>` tím, že využije [(Obecné) otevřené typy](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)a eliminuje nutnost registrace každého [(obecného) konstruovaného typu](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="0da37-421">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="0da37-422">V ukázkové aplikaci je `IMyDependency` služba zaregistrovaná u konkrétního typu. `MyDependency`</span><span class="sxs-lookup"><span data-stu-id="0da37-422">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="0da37-423">Registruje rozsah platnosti služby po dobu životnosti jediného požadavku.</span><span class="sxs-lookup"><span data-stu-id="0da37-423">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="0da37-424">[Doby platnosti služeb](#service-lifetimes) jsou popsány dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="0da37-424">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="0da37-425">Jednotlivé `services.Add{SERVICE_NAME}` metody rozšíření přidávají (a případně nakonfigurují) služby.</span><span class="sxs-lookup"><span data-stu-id="0da37-425">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="0da37-426">Například `services.AddMvc()` přidá služby Razor Pages a MVC vyžaduje.</span><span class="sxs-lookup"><span data-stu-id="0da37-426">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="0da37-427">Doporučujeme, aby aplikace dodržovaly tuto konvenci.</span><span class="sxs-lookup"><span data-stu-id="0da37-427">We recommended that apps follow this convention.</span></span> <span data-ttu-id="0da37-428">Do oboru názvů [Microsoft. Extensions. DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) umístěte metody rozšíření pro zapouzdření skupin registrací služby.</span><span class="sxs-lookup"><span data-stu-id="0da37-428">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="0da37-429">Pokud konstruktor služby vyžaduje [vestavěný typ](/dotnet/csharp/language-reference/keywords/built-in-types-table), například `string`,, může být typ vložen pomocí [Konfigurace](xref:fundamentals/configuration/index) nebo [vzoru možností](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="0da37-429">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="0da37-430">Instance služby je požadována prostřednictvím konstruktoru třídy, kde je služba používána a přiřazena k soukromému poli.</span><span class="sxs-lookup"><span data-stu-id="0da37-430">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="0da37-431">Pole se používá pro přístup ke službě podle potřeby v celé třídě.</span><span class="sxs-lookup"><span data-stu-id="0da37-431">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="0da37-432">V ukázkové aplikaci je `IMyDependency` instance vyžádána a používána pro volání `WriteMessage` metody služby:</span><span class="sxs-lookup"><span data-stu-id="0da37-432">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="0da37-433">Služby vložené do spuštění</span><span class="sxs-lookup"><span data-stu-id="0da37-433">Services injected into Startup</span></span>

<span data-ttu-id="0da37-434">Při použití obecného hostitele ( `Startup` <xref:Microsoft.Extensions.Hosting.IHostBuilder>) mohou být do konstruktoru vloženy pouze následující typy služeb:</span><span class="sxs-lookup"><span data-stu-id="0da37-434">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="0da37-435">Služby je možné vložit do `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="0da37-435">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="0da37-436">Další informace naleznete v tématu <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="0da37-436">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="0da37-437">Služby poskytované rozhraním</span><span class="sxs-lookup"><span data-stu-id="0da37-437">Framework-provided services</span></span>

<span data-ttu-id="0da37-438">`Startup.ConfigureServices` Metoda zodpovídá za definování služeb, které aplikace používá, včetně funkcí platformy, jako je například Entity Framework Core a ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="0da37-438">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="0da37-439">`IServiceCollection` Zpočátku služba `ConfigureServices` má služby definované rozhraním v závislosti na [tom, jak byl hostitel nakonfigurovaný](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="0da37-439">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="0da37-440">Pro aplikaci, která je založená na šabloně ASP.NET Core, není běžné, že mají stovky služeb zaregistrovaných v rámci rozhraní.</span><span class="sxs-lookup"><span data-stu-id="0da37-440">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="0da37-441">V následující tabulce je uvedena malá ukázka služeb registrovaných v rozhraní.</span><span class="sxs-lookup"><span data-stu-id="0da37-441">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="0da37-442">Typ služby</span><span class="sxs-lookup"><span data-stu-id="0da37-442">Service Type</span></span> | <span data-ttu-id="0da37-443">Doba platnosti</span><span class="sxs-lookup"><span data-stu-id="0da37-443">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="0da37-444">Dočasný</span><span class="sxs-lookup"><span data-stu-id="0da37-444">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="0da37-445">Singleton</span><span class="sxs-lookup"><span data-stu-id="0da37-445">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="0da37-446">Singleton</span><span class="sxs-lookup"><span data-stu-id="0da37-446">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="0da37-447">Singleton</span><span class="sxs-lookup"><span data-stu-id="0da37-447">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="0da37-448">Dočasný</span><span class="sxs-lookup"><span data-stu-id="0da37-448">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="0da37-449">Singleton</span><span class="sxs-lookup"><span data-stu-id="0da37-449">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="0da37-450">Dočasný</span><span class="sxs-lookup"><span data-stu-id="0da37-450">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="0da37-451">Singleton</span><span class="sxs-lookup"><span data-stu-id="0da37-451">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="0da37-452">Singleton</span><span class="sxs-lookup"><span data-stu-id="0da37-452">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="0da37-453">Singleton</span><span class="sxs-lookup"><span data-stu-id="0da37-453">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="0da37-454">Dočasný</span><span class="sxs-lookup"><span data-stu-id="0da37-454">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="0da37-455">Singleton</span><span class="sxs-lookup"><span data-stu-id="0da37-455">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="0da37-456">Singleton</span><span class="sxs-lookup"><span data-stu-id="0da37-456">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="0da37-457">Singleton</span><span class="sxs-lookup"><span data-stu-id="0da37-457">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="0da37-458">Registrace dalších služeb pomocí rozšiřujících metod</span><span class="sxs-lookup"><span data-stu-id="0da37-458">Register additional services with extension methods</span></span>

<span data-ttu-id="0da37-459">Pokud je k dispozici metoda rozšíření kolekce služeb pro registraci služby (a jejích závislých služeb, v případě potřeby), konvence používá jedinou `Add{SERVICE_NAME}` metodu rozšíření k registraci všech služeb vyžadovaných touto službou.</span><span class="sxs-lookup"><span data-stu-id="0da37-459">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="0da37-460">Následující kód je příkladem přidání dalších služeb do kontejneru pomocí metod rozšíření [\<AddDbContext TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) a: <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*></span><span class="sxs-lookup"><span data-stu-id="0da37-460">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="0da37-461">Další informace najdete ve <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> třídě v dokumentaci k rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="0da37-461">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="0da37-462">Životnost služeb</span><span class="sxs-lookup"><span data-stu-id="0da37-462">Service lifetimes</span></span>

<span data-ttu-id="0da37-463">Vyberte odpovídající dobu života pro každou registrovanou službu.</span><span class="sxs-lookup"><span data-stu-id="0da37-463">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="0da37-464">Služby ASP.NET Core Services je možné nakonfigurovat s následujícími životnostmi:</span><span class="sxs-lookup"><span data-stu-id="0da37-464">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="0da37-465">Dočasný</span><span class="sxs-lookup"><span data-stu-id="0da37-465">Transient</span></span>

<span data-ttu-id="0da37-466">Služba přechodných životností (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) se vytvoří pokaždé, když se požadují z kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="0da37-466">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="0da37-467">Tato životnost funguje nejlépe pro odlehčené bezstavové služby.</span><span class="sxs-lookup"><span data-stu-id="0da37-467">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="0da37-468">Obor</span><span class="sxs-lookup"><span data-stu-id="0da37-468">Scoped</span></span>

<span data-ttu-id="0da37-469">Oborové služby ()<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>s rozsahem platnosti () se vytvoří jednou za požadavek klienta (připojení).</span><span class="sxs-lookup"><span data-stu-id="0da37-469">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="0da37-470">Při použití oboru služby v middlewaru založit službu do metody `Invoke` nebo. `InvokeAsync`</span><span class="sxs-lookup"><span data-stu-id="0da37-470">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="0da37-471">Nepoužívejte [vkládání pomocí injektáže konstruktoru](xref:mvc/controllers/dependency-injection#constructor-injection) , protože vynutí, aby se služba chovala jako typ singleton.</span><span class="sxs-lookup"><span data-stu-id="0da37-471">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="0da37-472">Další informace naleznete v tématu <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="0da37-472">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="0da37-473">Singleton</span><span class="sxs-lookup"><span data-stu-id="0da37-473">Singleton</span></span>

<span data-ttu-id="0da37-474">Služba singleton životnosti<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>() je vytvořena při prvním vyžádání (nebo při `Startup.ConfigureServices` spuštění a instance je určena pro registraci služby).</span><span class="sxs-lookup"><span data-stu-id="0da37-474">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="0da37-475">Každý další požadavek používá stejnou instanci.</span><span class="sxs-lookup"><span data-stu-id="0da37-475">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="0da37-476">Pokud aplikace vyžaduje chování singleton, doporučuje se povolit kontejneru služby spravovat dobu života služby.</span><span class="sxs-lookup"><span data-stu-id="0da37-476">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="0da37-477">Neimplementujte vzor návrhu singleton a poskytněte uživatelský kód pro správu životního cyklu objektu ve třídě.</span><span class="sxs-lookup"><span data-stu-id="0da37-477">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="0da37-478">Není bezpečné přeložit oborovou službu z typu singleton.</span><span class="sxs-lookup"><span data-stu-id="0da37-478">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="0da37-479">Může dojít k tomu, že služba má při zpracování dalších požadavků špatný stav.</span><span class="sxs-lookup"><span data-stu-id="0da37-479">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="0da37-480">Metody registrace služby</span><span class="sxs-lookup"><span data-stu-id="0da37-480">Service registration methods</span></span>

<span data-ttu-id="0da37-481">Metody rozšíření registrace služby nabízejí přetížení, která jsou užitečná pro konkrétní scénáře.</span><span class="sxs-lookup"><span data-stu-id="0da37-481">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="0da37-482">Metoda</span><span class="sxs-lookup"><span data-stu-id="0da37-482">Method</span></span> | <span data-ttu-id="0da37-483">Automaticky</span><span class="sxs-lookup"><span data-stu-id="0da37-483">Automatic</span></span><br><span data-ttu-id="0da37-484">objekt</span><span class="sxs-lookup"><span data-stu-id="0da37-484">object</span></span><br><span data-ttu-id="0da37-485">odvod</span><span class="sxs-lookup"><span data-stu-id="0da37-485">disposal</span></span> | <span data-ttu-id="0da37-486">Několik</span><span class="sxs-lookup"><span data-stu-id="0da37-486">Multiple</span></span><br><span data-ttu-id="0da37-487">implementace</span><span class="sxs-lookup"><span data-stu-id="0da37-487">implementations</span></span> | <span data-ttu-id="0da37-488">Pass – argumenty</span><span class="sxs-lookup"><span data-stu-id="0da37-488">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="0da37-489">Příklad:</span><span class="sxs-lookup"><span data-stu-id="0da37-489">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="0da37-490">Ano</span><span class="sxs-lookup"><span data-stu-id="0da37-490">Yes</span></span> | <span data-ttu-id="0da37-491">Ano</span><span class="sxs-lookup"><span data-stu-id="0da37-491">Yes</span></span> | <span data-ttu-id="0da37-492">Ne</span><span class="sxs-lookup"><span data-stu-id="0da37-492">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="0da37-493">Příklady:</span><span class="sxs-lookup"><span data-stu-id="0da37-493">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="0da37-494">Ano</span><span class="sxs-lookup"><span data-stu-id="0da37-494">Yes</span></span> | <span data-ttu-id="0da37-495">Ano</span><span class="sxs-lookup"><span data-stu-id="0da37-495">Yes</span></span> | <span data-ttu-id="0da37-496">Ano</span><span class="sxs-lookup"><span data-stu-id="0da37-496">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="0da37-497">Příklad:</span><span class="sxs-lookup"><span data-stu-id="0da37-497">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="0da37-498">Ano</span><span class="sxs-lookup"><span data-stu-id="0da37-498">Yes</span></span> | <span data-ttu-id="0da37-499">Ne</span><span class="sxs-lookup"><span data-stu-id="0da37-499">No</span></span> | <span data-ttu-id="0da37-500">Ne</span><span class="sxs-lookup"><span data-stu-id="0da37-500">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="0da37-501">Příklady:</span><span class="sxs-lookup"><span data-stu-id="0da37-501">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="0da37-502">Ne</span><span class="sxs-lookup"><span data-stu-id="0da37-502">No</span></span> | <span data-ttu-id="0da37-503">Ano</span><span class="sxs-lookup"><span data-stu-id="0da37-503">Yes</span></span> | <span data-ttu-id="0da37-504">Ano</span><span class="sxs-lookup"><span data-stu-id="0da37-504">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="0da37-505">Příklady:</span><span class="sxs-lookup"><span data-stu-id="0da37-505">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="0da37-506">Ne</span><span class="sxs-lookup"><span data-stu-id="0da37-506">No</span></span> | <span data-ttu-id="0da37-507">Ne</span><span class="sxs-lookup"><span data-stu-id="0da37-507">No</span></span> | <span data-ttu-id="0da37-508">Ano</span><span class="sxs-lookup"><span data-stu-id="0da37-508">Yes</span></span> |

<span data-ttu-id="0da37-509">Další informace o vyřazení typů najdete v části věnované [vyřazení služeb](#disposal-of-services) .</span><span class="sxs-lookup"><span data-stu-id="0da37-509">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="0da37-510">Běžným scénářem pro více implementací je vytvoření [typů pro testování](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="0da37-510">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="0da37-511">`TryAdd{LIFETIME}`metody registrují službu jenom v případě, že už není zaregistrovaná implementace.</span><span class="sxs-lookup"><span data-stu-id="0da37-511">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="0da37-512">V následujícím příkladu se první řádek registruje `MyDependency` pro. `IMyDependency`</span><span class="sxs-lookup"><span data-stu-id="0da37-512">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="0da37-513">Druhý řádek nemá žádný účinek, protože `IMyDependency` už má registrovanou implementaci:</span><span class="sxs-lookup"><span data-stu-id="0da37-513">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="0da37-514">Další informace naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="0da37-514">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="0da37-515">Metody [TryAddEnumerable (ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) registrují službu pouze v případě, že již není implementace *stejného typu*.</span><span class="sxs-lookup"><span data-stu-id="0da37-515">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="0da37-516">Několik služeb je vyřešeno `IEnumerable<{SERVICE}>`prostřednictvím.</span><span class="sxs-lookup"><span data-stu-id="0da37-516">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="0da37-517">Při registraci služeb chce vývojář přidat instanci jenom v případě, že už jeden ze stejného typu není přidaný.</span><span class="sxs-lookup"><span data-stu-id="0da37-517">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="0da37-518">Obecně je tato metoda používána autory knihovny k tomu, aby nedocházelo k registraci dvou kopií instance v kontejneru.</span><span class="sxs-lookup"><span data-stu-id="0da37-518">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="0da37-519">V následujícím příkladu se první řádek registruje `MyDep` pro. `IMyDep1`</span><span class="sxs-lookup"><span data-stu-id="0da37-519">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="0da37-520">Druhý řádek se `MyDep` registruje `IMyDep2`pro.</span><span class="sxs-lookup"><span data-stu-id="0da37-520">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="0da37-521">Třetí řádek nemá žádný vliv, protože `IMyDep1` už má registrovanou implementaci `MyDep`:</span><span class="sxs-lookup"><span data-stu-id="0da37-521">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="0da37-522">Chování injektáže konstruktoru</span><span class="sxs-lookup"><span data-stu-id="0da37-522">Constructor injection behavior</span></span>

<span data-ttu-id="0da37-523">Služby je možné vyřešit pomocí dvou mechanismů:</span><span class="sxs-lookup"><span data-stu-id="0da37-523">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="0da37-524"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>&ndash; Povoluje vytvoření objektu bez registrace služby v kontejneru pro vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="0da37-524"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities> &ndash; Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="0da37-525">`ActivatorUtilities`se používá s uživateli s přístupem k uživatelům, jako jsou například rutiny značek, řadiče MVC a pořadače modelů.</span><span class="sxs-lookup"><span data-stu-id="0da37-525">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="0da37-526">Konstruktory mohou přijímat argumenty, které nejsou poskytovány vkládáním závislostí, ale argumenty musí přiřadit výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="0da37-526">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="0da37-527">Když jsou služby vyřešeny `IServiceProvider` nebo `ActivatorUtilities`, [Injektáže konstruktoru](xref:mvc/controllers/dependency-injection#constructor-injection) vyžaduje *veřejný* konstruktor.</span><span class="sxs-lookup"><span data-stu-id="0da37-527">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="0da37-528">Když jsou služby vyřešeny `ActivatorUtilities`nástrojem, [Injektáže konstruktoru](xref:mvc/controllers/dependency-injection#constructor-injection) vyžaduje, aby existoval pouze jeden příslušný konstruktor.</span><span class="sxs-lookup"><span data-stu-id="0da37-528">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="0da37-529">Přetížení konstruktoru jsou podporovaná, ale může existovat jenom jedno přetížení, jehož argumenty můžou být splněné vkládáním závislostí.</span><span class="sxs-lookup"><span data-stu-id="0da37-529">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="0da37-530">Entity Framework kontexty</span><span class="sxs-lookup"><span data-stu-id="0da37-530">Entity Framework contexts</span></span>

<span data-ttu-id="0da37-531">Entity Framework kontexty se obvykle přidávají do kontejneru služby pomocí [rozsahu životnosti](#service-lifetimes) , protože operace databáze webové aplikace jsou obvykle vymezeny na požadavky klienta.</span><span class="sxs-lookup"><span data-stu-id="0da37-531">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="0da37-532">Výchozí doba života je vymezena, pokud při registraci kontextu databáze není určena doba platnosti [\<AddDbContext TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) přetížení.</span><span class="sxs-lookup"><span data-stu-id="0da37-532">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="0da37-533">Služby pro danou dobu života by neměly používat kontext databáze s kratší dobou životnosti než služba.</span><span class="sxs-lookup"><span data-stu-id="0da37-533">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="0da37-534">Možnosti života a registrace</span><span class="sxs-lookup"><span data-stu-id="0da37-534">Lifetime and registration options</span></span>

<span data-ttu-id="0da37-535">Chcete-li předvést rozdíl mezi možnostmi životního cyklu a registrací, zvažte následující rozhraní, která představují úlohy jako operaci s `OperationId`jedinečným identifikátorem.</span><span class="sxs-lookup"><span data-stu-id="0da37-535">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="0da37-536">V závislosti na tom, jak je životnost provozní služby nakonfigurována pro následující rozhraní, kontejner poskytuje buď stejnou nebo jinou instanci služby, když je vyžádána třídou:</span><span class="sxs-lookup"><span data-stu-id="0da37-536">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="0da37-537">Rozhraní jsou implementována ve `Operation` třídě.</span><span class="sxs-lookup"><span data-stu-id="0da37-537">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="0da37-538">`Operation` Konstruktor generuje identifikátor GUID, pokud není dodán:</span><span class="sxs-lookup"><span data-stu-id="0da37-538">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="0da37-539">`OperationService` Je zaregistrován, který závisí na každém z ostatních `Operation` typů.</span><span class="sxs-lookup"><span data-stu-id="0da37-539">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="0da37-540">Když `OperationService` je požadavek proveden prostřednictvím injektáže závislosti, obdrží buď novou instanci každé služby, nebo existující instanci na základě životnosti závislé služby.</span><span class="sxs-lookup"><span data-stu-id="0da37-540">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="0da37-541">Když se v případě vyžádání z kontejneru vytvoří přechodné služby `OperationId` , liší `IOperationTransient` `OperationId` se od služby. `OperationService`</span><span class="sxs-lookup"><span data-stu-id="0da37-541">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="0da37-542">`OperationService`přijme novou instanci `IOperationTransient` třídy.</span><span class="sxs-lookup"><span data-stu-id="0da37-542">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="0da37-543">Nová instance vrací jinou `OperationId`instanci.</span><span class="sxs-lookup"><span data-stu-id="0da37-543">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="0da37-544">Když se vytvoří vymezené služby pro jednotlivé požadavky klienta, `OperationId` `IOperationScoped` služba je stejná jako `OperationService` v rámci žádosti klienta.</span><span class="sxs-lookup"><span data-stu-id="0da37-544">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="0da37-545">V rámci požadavků klientů obě služby sdílejí jinou `OperationId` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="0da37-545">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="0da37-546">Pokud se služby instance singleton a singleton vytvoří jednou a použije se pro všechny požadavky klientů a všechny služby, je `OperationId` v rámci všech žádostí o službu konstantní.</span><span class="sxs-lookup"><span data-stu-id="0da37-546">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="0da37-547">V `Startup.ConfigureServices`je každý typ přidán do kontejneru podle jeho pojmenované životnosti:</span><span class="sxs-lookup"><span data-stu-id="0da37-547">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="0da37-548">`IOperationSingletonInstance` Služba používá konkrétní instanci se známým ID `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="0da37-548">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="0da37-549">Je jasné, že pokud se tento typ používá (jeho identifikátor GUID je všechny nuly).</span><span class="sxs-lookup"><span data-stu-id="0da37-549">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="0da37-550">Ukázková aplikace ukazuje dobu života objektu v rámci a mezi jednotlivými požadavky.</span><span class="sxs-lookup"><span data-stu-id="0da37-550">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="0da37-551">Ukázková aplikace `IndexModel` požaduje každý druh `IOperation` typu a. `OperationService`</span><span class="sxs-lookup"><span data-stu-id="0da37-551">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="0da37-552">Stránka potom zobrazí všechny `OperationId` hodnoty třídy modelu stránky a hodnoty služby prostřednictvím přiřazení vlastností:</span><span class="sxs-lookup"><span data-stu-id="0da37-552">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="0da37-553">Výsledkem dvou následujících výstupů jsou výsledky dvou požadavků:</span><span class="sxs-lookup"><span data-stu-id="0da37-553">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="0da37-554">**První požadavek:**</span><span class="sxs-lookup"><span data-stu-id="0da37-554">**First request:**</span></span>

<span data-ttu-id="0da37-555">Operace kontroleru:</span><span class="sxs-lookup"><span data-stu-id="0da37-555">Controller operations:</span></span>

<span data-ttu-id="0da37-556">Přechodný: d233e165-f417-469b-A866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="0da37-556">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="0da37-557">Vymezené: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="0da37-557">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="0da37-558">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="0da37-558">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="0da37-559">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="0da37-559">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="0da37-560">`OperationService`Operations</span><span class="sxs-lookup"><span data-stu-id="0da37-560">`OperationService` operations:</span></span>

<span data-ttu-id="0da37-561">Přechodný: c6b049eb-1318-4E31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="0da37-561">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="0da37-562">Vymezené: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="0da37-562">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="0da37-563">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="0da37-563">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="0da37-564">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="0da37-564">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="0da37-565">**Druhá žádost:**</span><span class="sxs-lookup"><span data-stu-id="0da37-565">**Second request:**</span></span>

<span data-ttu-id="0da37-566">Operace kontroleru:</span><span class="sxs-lookup"><span data-stu-id="0da37-566">Controller operations:</span></span>

<span data-ttu-id="0da37-567">Přechodný: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="0da37-567">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="0da37-568">Vymezené: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="0da37-568">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="0da37-569">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="0da37-569">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="0da37-570">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="0da37-570">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="0da37-571">`OperationService`Operations</span><span class="sxs-lookup"><span data-stu-id="0da37-571">`OperationService` operations:</span></span>

<span data-ttu-id="0da37-572">Přechodný: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="0da37-572">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="0da37-573">Vymezené: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="0da37-573">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="0da37-574">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="0da37-574">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="0da37-575">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="0da37-575">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="0da37-576">Pozor, které hodnoty `OperationId` se liší v rámci požadavku a mezi požadavky:</span><span class="sxs-lookup"><span data-stu-id="0da37-576">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="0da37-577">*Přechodné* objekty jsou vždy odlišné.</span><span class="sxs-lookup"><span data-stu-id="0da37-577">*Transient* objects are always different.</span></span> <span data-ttu-id="0da37-578">Přechodná `OperationId` hodnota pro první i druhý požadavek klienta se liší pro obě `OperationService` operace i mezi požadavky klientů.</span><span class="sxs-lookup"><span data-stu-id="0da37-578">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="0da37-579">Každé žádosti o službu a žádosti klienta se poskytne nová instance.</span><span class="sxs-lookup"><span data-stu-id="0da37-579">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="0da37-580">*Vymezené* objekty jsou v požadavku klienta stejné, ale v rámci požadavků klientů se liší.</span><span class="sxs-lookup"><span data-stu-id="0da37-580">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="0da37-581">Objekty *singleton* jsou stejné pro každý objekt a každý požadavek bez ohledu na to, zda `Operation` je instance uvedena v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="0da37-581">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="0da37-582">Volat služby z Main</span><span class="sxs-lookup"><span data-stu-id="0da37-582">Call services from main</span></span>

<span data-ttu-id="0da37-583">Vytvořte <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> pomocí [IServiceScopeFactory. CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) pro řešení oboru služby v rámci rozsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="0da37-583">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="0da37-584">Tento přístup je užitečný pro přístup k oboruované službě při spuštění za účelem spouštění úloh inicializace.</span><span class="sxs-lookup"><span data-stu-id="0da37-584">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="0da37-585">Následující příklad ukazuje, jak získat kontext pro `MyScopedService` `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="0da37-585">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="0da37-586">Ověřování oboru</span><span class="sxs-lookup"><span data-stu-id="0da37-586">Scope validation</span></span>

<span data-ttu-id="0da37-587">Když je aplikace spuštěná ve vývojovém prostředí, provede výchozí poskytovatel služeb kontroly, aby ověřil, že:</span><span class="sxs-lookup"><span data-stu-id="0da37-587">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="0da37-588">Oborové služby nejsou přímo nebo nepřímo vyřešeny od poskytovatele kořenové služby.</span><span class="sxs-lookup"><span data-stu-id="0da37-588">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="0da37-589">Oborové služby nejsou přímo nebo nepřímo vložené do singleton.</span><span class="sxs-lookup"><span data-stu-id="0da37-589">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="0da37-590">Poskytovatel kořenové služby se vytvoří, když <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> se zavolá.</span><span class="sxs-lookup"><span data-stu-id="0da37-590">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="0da37-591">Doba života poskytovatele kořenové služby odpovídá době životnosti aplikace nebo serveru, když se poskytovatel spustí s aplikací a je uvolněný při ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="0da37-591">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="0da37-592">Oborové služby jsou uvolněny kontejnerem, který je vytvořil.</span><span class="sxs-lookup"><span data-stu-id="0da37-592">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="0da37-593">Pokud je v kořenovém kontejneru vytvořena vymezená služba, doba života služby je efektivně povýšena na typ singleton, protože je uvolněna pouze kořenovým kontejnerem při vypnutí aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="0da37-593">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="0da37-594">Při `BuildServiceProvider` ověřování oborů služeb se tyto situace zachytí.</span><span class="sxs-lookup"><span data-stu-id="0da37-594">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="0da37-595">Další informace naleznete v tématu <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="0da37-595">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="0da37-596">Žádosti o služby</span><span class="sxs-lookup"><span data-stu-id="0da37-596">Request Services</span></span>

<span data-ttu-id="0da37-597">Služby, které jsou k dispozici v `HttpContext` rámci žádosti o ASP.NET Core, jsou zpřístupněny prostřednictvím kolekce [HttpContext. RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) .</span><span class="sxs-lookup"><span data-stu-id="0da37-597">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="0da37-598">Žádosti o služby reprezentují služby nakonfigurované a požadované v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="0da37-598">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="0da37-599">Když objekty určují závislosti, jsou splněny typy nalezenými v `RequestServices`, ne. `ApplicationServices`</span><span class="sxs-lookup"><span data-stu-id="0da37-599">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="0da37-600">Obecně platí, že aplikace by tyto vlastnosti neměla používat přímo.</span><span class="sxs-lookup"><span data-stu-id="0da37-600">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="0da37-601">Místo toho požádejte o typy, které třídy vyžadují prostřednictvím konstruktorů třídy, a umožněte rozhraní vložit závislosti.</span><span class="sxs-lookup"><span data-stu-id="0da37-601">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="0da37-602">To poskytuje třídy, které jsou snáze testovány.</span><span class="sxs-lookup"><span data-stu-id="0da37-602">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="0da37-603">Preferovat požadavky na závislosti jako parametry konstruktoru pro přístup `RequestServices` ke kolekci.</span><span class="sxs-lookup"><span data-stu-id="0da37-603">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="0da37-604">Navrhnout služby pro vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="0da37-604">Design services for dependency injection</span></span>

<span data-ttu-id="0da37-605">Osvědčené postupy:</span><span class="sxs-lookup"><span data-stu-id="0da37-605">Best practices are to:</span></span>

* <span data-ttu-id="0da37-606">Navrhněte služby pro použití injektáže závislosti k získání jejich závislostí.</span><span class="sxs-lookup"><span data-stu-id="0da37-606">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="0da37-607">Vyhněte se stavovým, statickým třídám a členům.</span><span class="sxs-lookup"><span data-stu-id="0da37-607">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="0da37-608">Navrhněte aplikace, aby místo toho používaly služby s jedním prvkem, což se vyhne vytváření globálního stavu.</span><span class="sxs-lookup"><span data-stu-id="0da37-608">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="0da37-609">Vyhněte se přímému vytváření instancí závislých tříd v rámci služeb.</span><span class="sxs-lookup"><span data-stu-id="0da37-609">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="0da37-610">Přímá instance Couples kód na konkrétní implementaci.</span><span class="sxs-lookup"><span data-stu-id="0da37-610">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="0da37-611">Vytvářejte třídy aplikací malými, dobře faktoringně a snadno testované.</span><span class="sxs-lookup"><span data-stu-id="0da37-611">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="0da37-612">Pokud se zdá, že třída má příliš mnoho vložených závislostí, obvykle se jedná o znaménko, že třída má příliš mnoho zodpovědnosti a je v rozporu s [principem jediné zodpovědnosti (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="0da37-612">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="0da37-613">Pokuste se Refaktorovat třídu přesunutím některých odpovědností do nové třídy.</span><span class="sxs-lookup"><span data-stu-id="0da37-613">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="0da37-614">Mějte na paměti, Razor že stránky tříd modelu stránky a třídy kontroleru MVC by se měly zaměřit na uživatelské rozhraní.</span><span class="sxs-lookup"><span data-stu-id="0da37-614">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="0da37-615">Obchodní pravidla a podrobnosti implementace přístupu k datům by měly být uchovávány ve třídách, které jsou vhodné pro tyto [samostatné obavy](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="0da37-615">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="0da37-616">Vyřazení služeb</span><span class="sxs-lookup"><span data-stu-id="0da37-616">Disposal of services</span></span>

<span data-ttu-id="0da37-617">Kontejner volá <xref:System.IDisposable.Dispose*> typy, které <xref:System.IDisposable> vytvoří.</span><span class="sxs-lookup"><span data-stu-id="0da37-617">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="0da37-618">Pokud je instance přidána do kontejneru pomocí uživatelského kódu, nebude uvolněna automaticky.</span><span class="sxs-lookup"><span data-stu-id="0da37-618">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="0da37-619">V následujícím příkladu jsou služby vytvořeny pomocí kontejneru služby a automaticky odstraněny:</span><span class="sxs-lookup"><span data-stu-id="0da37-619">In the following example, the services are created by the service container and disposed automatically:</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public interface IService3 {}
public class Service3 : IService3, IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddScoped<Service1>();
    services.AddSingleton<Service2>();
    services.AddSingleton<IService3>(sp => new Service3());
}
```

<span data-ttu-id="0da37-620">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="0da37-620">In the following example:</span></span>

* <span data-ttu-id="0da37-621">Instance služby nejsou vytvořeny kontejnerem služby.</span><span class="sxs-lookup"><span data-stu-id="0da37-621">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="0da37-622">Zamýšlené životnosti služeb nejsou rozhraním známy.</span><span class="sxs-lookup"><span data-stu-id="0da37-622">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="0da37-623">Rozhraní neodstraní služby automaticky.</span><span class="sxs-lookup"><span data-stu-id="0da37-623">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="0da37-624">Pokud se služby explicitně neodstraní v kódu pro vývojáře, zůstanou zachovány, dokud se aplikace neukončí.</span><span class="sxs-lookup"><span data-stu-id="0da37-624">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

## <a name="default-service-container-replacement"></a><span data-ttu-id="0da37-625">Výchozí nahrazení kontejneru služby</span><span class="sxs-lookup"><span data-stu-id="0da37-625">Default service container replacement</span></span>

<span data-ttu-id="0da37-626">Integrovaný kontejner služeb je navržený tak, aby sloužil potřebám architektury a většině spotřebitelských aplikací.</span><span class="sxs-lookup"><span data-stu-id="0da37-626">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="0da37-627">Pokud nepotřebujete určitou funkci, kterou integrovaný kontejner nepodporuje, doporučujeme použít vestavěný kontejner, například:</span><span class="sxs-lookup"><span data-stu-id="0da37-627">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="0da37-628">Vkládání vlastností</span><span class="sxs-lookup"><span data-stu-id="0da37-628">Property injection</span></span>
* <span data-ttu-id="0da37-629">Vkládání na základě názvu</span><span class="sxs-lookup"><span data-stu-id="0da37-629">Injection based on name</span></span>
* <span data-ttu-id="0da37-630">Podřízené kontejnery</span><span class="sxs-lookup"><span data-stu-id="0da37-630">Child containers</span></span>
* <span data-ttu-id="0da37-631">Vlastní Správa životního cyklu</span><span class="sxs-lookup"><span data-stu-id="0da37-631">Custom lifetime management</span></span>
* <span data-ttu-id="0da37-632">`Func<T>`Podpora opožděné inicializace</span><span class="sxs-lookup"><span data-stu-id="0da37-632">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="0da37-633">Registrace založená na konvencích</span><span class="sxs-lookup"><span data-stu-id="0da37-633">Convention-based registration</span></span>

<span data-ttu-id="0da37-634">S ASP.NET Coremi aplikacemi je možné používat následující kontejnery třetích stran:</span><span class="sxs-lookup"><span data-stu-id="0da37-634">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="0da37-635">Autofac</span><span class="sxs-lookup"><span data-stu-id="0da37-635">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="0da37-636">DryIoc</span><span class="sxs-lookup"><span data-stu-id="0da37-636">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="0da37-637">Integrit</span><span class="sxs-lookup"><span data-stu-id="0da37-637">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="0da37-638">LightInject</span><span class="sxs-lookup"><span data-stu-id="0da37-638">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="0da37-639">Lamar</span><span class="sxs-lookup"><span data-stu-id="0da37-639">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="0da37-640">Stashbox</span><span class="sxs-lookup"><span data-stu-id="0da37-640">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="0da37-641">Unity</span><span class="sxs-lookup"><span data-stu-id="0da37-641">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="0da37-642">Bezpečnost vlákna</span><span class="sxs-lookup"><span data-stu-id="0da37-642">Thread safety</span></span>

<span data-ttu-id="0da37-643">Vytvořte služby, které jsou v bezpečí pro přístup z více vláken.</span><span class="sxs-lookup"><span data-stu-id="0da37-643">Create thread-safe singleton services.</span></span> <span data-ttu-id="0da37-644">Pokud má služba typu Singleton závislost na přechodné službě, přechodná služba může také vyžadovat zabezpečení vlákna v závislosti na tom, jak je používá typ singleton.</span><span class="sxs-lookup"><span data-stu-id="0da37-644">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="0da37-645">Výrobní metoda samostatné služby, jako je například druhý argument pro [AddSingleton\<TService> (IServiceCollection, Func\<IServiceProvider, TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), nemusí být bezpečná pro přístup z více vláken.</span><span class="sxs-lookup"><span data-stu-id="0da37-645">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="0da37-646">Podobně jako konstruktor typu`static`(), je zaručeno, že bude volán jednou vláknem.</span><span class="sxs-lookup"><span data-stu-id="0da37-646">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="0da37-647">Doporučení</span><span class="sxs-lookup"><span data-stu-id="0da37-647">Recommendations</span></span>

* <span data-ttu-id="0da37-648">`async/await`řešení `Task` služeb a se nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="0da37-648">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="0da37-649">Jazyk C# nepodporuje asynchronní konstruktory; Proto je doporučeným vzorem použití asynchronních metod po synchronním překladu služby.</span><span class="sxs-lookup"><span data-stu-id="0da37-649">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="0da37-650">Neukládejte data a konfiguraci přímo do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="0da37-650">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="0da37-651">Například nákupní košík uživatele by neměl být obvykle přidán do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="0da37-651">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="0da37-652">Konfigurace by měla používat [vzor možností](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="0da37-652">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="0da37-653">Podobně nepoužívejte objekty "držitel dat", které existují pouze k povolení přístupu k jinému objektu.</span><span class="sxs-lookup"><span data-stu-id="0da37-653">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="0da37-654">Je lepší žádat o skutečnou položku přes DI.</span><span class="sxs-lookup"><span data-stu-id="0da37-654">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="0da37-655">Vyhněte se statickému přístupu ke službám (například statickému psaní [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) pro použití jinde).</span><span class="sxs-lookup"><span data-stu-id="0da37-655">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="0da37-656">Vyhněte se použití *vzoru lokátoru služby*, který kombinuje [inverzi strategií řízení](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .</span><span class="sxs-lookup"><span data-stu-id="0da37-656">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

  * <span data-ttu-id="0da37-657">Nevolejte <xref:System.IServiceProvider.GetService*> pro získání instance služby, když můžete místo toho použít di:</span><span class="sxs-lookup"><span data-stu-id="0da37-657">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="0da37-658">**Špatný**</span><span class="sxs-lookup"><span data-stu-id="0da37-658">**Incorrect:**</span></span>

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

    <span data-ttu-id="0da37-659">**Správné**:</span><span class="sxs-lookup"><span data-stu-id="0da37-659">**Correct**:</span></span>

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

  * <span data-ttu-id="0da37-660">Vyhněte se vkládání továrny, která řeší závislosti za <xref:System.IServiceProvider.GetService*>běhu pomocí.</span><span class="sxs-lookup"><span data-stu-id="0da37-660">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>

* <span data-ttu-id="0da37-661">Vyhněte se statickému přístupu k `HttpContext` (například [IHttpContextAccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="0da37-661">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="0da37-662">Podobně jako u všech sad doporučení se může stát, že se vyžaduje ignorování doporučení.</span><span class="sxs-lookup"><span data-stu-id="0da37-662">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="0da37-663">Výjimky jsou zřídka&mdash;většinou zvláštní případy v rámci samotného rozhraní.</span><span class="sxs-lookup"><span data-stu-id="0da37-663">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="0da37-664">DI je *alternativou* ke vzorům statických nebo globálních přístupů k objektům.</span><span class="sxs-lookup"><span data-stu-id="0da37-664">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="0da37-665">Je možné, že nebudete moci využít výhody DI, pokud je kombinujete se statickým přístupem k objektům.</span><span class="sxs-lookup"><span data-stu-id="0da37-665">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0da37-666">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="0da37-666">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="0da37-667">Zápis čistého kódu v ASP.NET Core pomocí injektáže závislosti (MSDN)</span><span class="sxs-lookup"><span data-stu-id="0da37-667">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="0da37-668">Explicitní závislosti – princip</span><span class="sxs-lookup"><span data-stu-id="0da37-668">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="0da37-669">Inverze řídicích kontejnerů a vzor injektáže závislosti (Martin Fowlera)</span><span class="sxs-lookup"><span data-stu-id="0da37-669">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="0da37-670">Jak zaregistrovat službu s více rozhraními v ASP.NET Core DI</span><span class="sxs-lookup"><span data-stu-id="0da37-670">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

---
title: Injektáž závislostí v ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak ASP.NET Core implementuje vkládání závislostí a jak ho používat.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/21/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/dependency-injection
ms.openlocfilehash: 665f9f761f729fad59af298cacd0d48ab3a3f71f
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85400722"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="b1586-103">Injektáž závislostí v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b1586-103">Dependency injection in ASP.NET Core</span></span>

<span data-ttu-id="b1586-104">[Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com)a [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="b1586-104">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b1586-105">ASP.NET Core podporuje vzor návrhu softwaru pro vkládání závislostí (DI), což je technika pro dosažení [inverze ovládacího prvku (IOC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) mezi třídami a jejich závislostmi.</span><span class="sxs-lookup"><span data-stu-id="b1586-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="b1586-106">Další informace, které jsou specifické pro vkládání závislostí v rámci řadičů MVC, najdete v tématu <xref:mvc/controllers/dependency-injection> .</span><span class="sxs-lookup"><span data-stu-id="b1586-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="b1586-107">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b1586-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="b1586-108">Přehled injektáže závislosti</span><span class="sxs-lookup"><span data-stu-id="b1586-108">Overview of dependency injection</span></span>

<span data-ttu-id="b1586-109">*Závislost* je libovolný objekt, který vyžaduje jiný objekt.</span><span class="sxs-lookup"><span data-stu-id="b1586-109">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="b1586-110">Projděte si následující `MyDependency` třídu s `WriteMessage` metodou, na které jsou závislé jiné třídy v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="b1586-110">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="b1586-111">Instance `MyDependency` třídy může být vytvořena, aby byla `WriteMessage` Metoda k dispozici pro třídu.</span><span class="sxs-lookup"><span data-stu-id="b1586-111">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="b1586-112">`MyDependency`Třída je závislostí `IndexModel` třídy:</span><span class="sxs-lookup"><span data-stu-id="b1586-112">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="b1586-113">Třída vytvoří a přímo závisí na `MyDependency` instanci.</span><span class="sxs-lookup"><span data-stu-id="b1586-113">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="b1586-114">Závislosti kódu (například předchozí příklad) jsou problematické a je třeba se jim vyhnout z následujících důvodů:</span><span class="sxs-lookup"><span data-stu-id="b1586-114">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="b1586-115">Chcete-li nahradit `MyDependency` jinou implementací, třída musí být upravena.</span><span class="sxs-lookup"><span data-stu-id="b1586-115">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="b1586-116">Pokud `MyDependency` má závislosti, musí být nakonfigurovány třídou.</span><span class="sxs-lookup"><span data-stu-id="b1586-116">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="b1586-117">Ve velkém projektu s více třídami, v závislosti na tom `MyDependency` , je kód konfigurace rozptýlen napříč aplikací.</span><span class="sxs-lookup"><span data-stu-id="b1586-117">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="b1586-118">Tato implementace je obtížná pro testování částí.</span><span class="sxs-lookup"><span data-stu-id="b1586-118">This implementation is difficult to unit test.</span></span> <span data-ttu-id="b1586-119">Aplikace by měla používat třídu typu "nebo zástupné procedury" `MyDependency` , což není u tohoto přístupu možné.</span><span class="sxs-lookup"><span data-stu-id="b1586-119">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="b1586-120">Vkládání závislostí řeší tyto problémy prostřednictvím:</span><span class="sxs-lookup"><span data-stu-id="b1586-120">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="b1586-121">Použití rozhraní nebo základní třídy k abstrakci implementace závislosti.</span><span class="sxs-lookup"><span data-stu-id="b1586-121">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="b1586-122">Registrace závislosti v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="b1586-122">Registration of the dependency in a service container.</span></span> <span data-ttu-id="b1586-123">ASP.NET Core poskytuje integrovaný kontejner služeb <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="b1586-123">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="b1586-124">Služby jsou registrovány v metodě aplikace `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b1586-124">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="b1586-125">*Vložení* služby do konstruktoru třídy, kde se používá.</span><span class="sxs-lookup"><span data-stu-id="b1586-125">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="b1586-126">Rozhraní přebírá zodpovědnost za vytvoření instance závislosti a její odstranění, když už ji nepotřebujete.</span><span class="sxs-lookup"><span data-stu-id="b1586-126">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="b1586-127">V [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) `IMyDependency` definuje rozhraní metodu, kterou služba poskytuje aplikaci:</span><span class="sxs-lookup"><span data-stu-id="b1586-127">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="b1586-128">Toto rozhraní je implementováno konkrétním typem `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="b1586-128">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="b1586-129">`MyDependency`vyžádá <xref:Microsoft.Extensions.Logging.ILogger`1> v jeho konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="b1586-129">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="b1586-130">Není neobvyklé používat vkládání závislostí v zřetězeném způsobem.</span><span class="sxs-lookup"><span data-stu-id="b1586-130">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="b1586-131">Každá požadovaná závislost zase vyžádá své vlastní závislosti.</span><span class="sxs-lookup"><span data-stu-id="b1586-131">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="b1586-132">Kontejner vyřeší závislosti v grafu a vrátí plně přeloženou službu.</span><span class="sxs-lookup"><span data-stu-id="b1586-132">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="b1586-133">Kolektivní sada závislostí, které je třeba vyřešit, se obvykle označuje jako *strom závislosti*, *graf závislosti*nebo *graf objektů*.</span><span class="sxs-lookup"><span data-stu-id="b1586-133">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="b1586-134">`IMyDependency`a `ILogger<TCategoryName>` musí být registrována v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="b1586-134">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="b1586-135">`IMyDependency`je zaregistrován v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b1586-135">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b1586-136">`ILogger<TCategoryName>`je zaregistrované v infrastruktuře abstrakce protokolování, takže se jedná o [službu](#framework-provided-services) , která je zaregistrovaná ve výchozím rozhraní.</span><span class="sxs-lookup"><span data-stu-id="b1586-136">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="b1586-137">Kontejner se překládá `ILogger<TCategoryName>` tím, že využije [(Obecné) otevřené typy](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)a eliminuje nutnost registrace každého [(obecného) konstruovaného typu](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="b1586-137">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="b1586-138">V ukázkové aplikaci `IMyDependency` je služba zaregistrovaná u konkrétního typu `MyDependency` .</span><span class="sxs-lookup"><span data-stu-id="b1586-138">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="b1586-139">Registruje rozsah platnosti služby po dobu životnosti jediného požadavku.</span><span class="sxs-lookup"><span data-stu-id="b1586-139">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="b1586-140">[Doby platnosti služeb](#service-lifetimes) jsou popsány dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="b1586-140">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="b1586-141">Jednotlivé `services.Add{SERVICE_NAME}` metody rozšíření přidávají (a případně nakonfigurují) služby.</span><span class="sxs-lookup"><span data-stu-id="b1586-141">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="b1586-142">Například `services.AddMvc()` přidá Razor stránky služeb a MVC vyžaduje.</span><span class="sxs-lookup"><span data-stu-id="b1586-142">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="b1586-143">Doporučujeme, aby aplikace dodržovaly tuto konvenci.</span><span class="sxs-lookup"><span data-stu-id="b1586-143">We recommended that apps follow this convention.</span></span> <span data-ttu-id="b1586-144">Do oboru názvů [Microsoft. Extensions. DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) umístěte metody rozšíření pro zapouzdření skupin registrací služby.</span><span class="sxs-lookup"><span data-stu-id="b1586-144">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="b1586-145">Pokud konstruktor služby vyžaduje [vestavěný typ](/dotnet/csharp/language-reference/keywords/built-in-types-table), například `string` ,, může být typ vložen pomocí [Konfigurace](xref:fundamentals/configuration/index) nebo [vzoru možností](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="b1586-145">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="b1586-146">Instance služby je požadována prostřednictvím konstruktoru třídy, kde je služba používána a přiřazena k soukromému poli.</span><span class="sxs-lookup"><span data-stu-id="b1586-146">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="b1586-147">Pole se používá pro přístup ke službě podle potřeby v celé třídě.</span><span class="sxs-lookup"><span data-stu-id="b1586-147">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="b1586-148">V ukázkové aplikaci `IMyDependency` je instance vyžádána a používána pro volání `WriteMessage` metody služby:</span><span class="sxs-lookup"><span data-stu-id="b1586-148">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="b1586-149">Služby vložené do spuštění</span><span class="sxs-lookup"><span data-stu-id="b1586-149">Services injected into Startup</span></span>

<span data-ttu-id="b1586-150">`Startup`Při použití obecného hostitele () mohou být do konstruktoru vloženy pouze následující typy služeb <xref:Microsoft.Extensions.Hosting.IHostBuilder> :</span><span class="sxs-lookup"><span data-stu-id="b1586-150">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="b1586-151">Služby je možné vložit do `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="b1586-151">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="b1586-152">Další informace naleznete v tématu <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="b1586-152">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="b1586-153">Služby poskytované rozhraním</span><span class="sxs-lookup"><span data-stu-id="b1586-153">Framework-provided services</span></span>

<span data-ttu-id="b1586-154">`Startup.ConfigureServices`Metoda zodpovídá za definování služeb, které aplikace používá, včetně funkcí platformy, jako je například Entity Framework Core a ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="b1586-154">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="b1586-155">Zpočátku `IServiceCollection` `ConfigureServices` služba má služby definované rozhraním v závislosti na [tom, jak byl hostitel nakonfigurovaný](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="b1586-155">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="b1586-156">Pro aplikaci, která je založená na šabloně ASP.NET Core, není běžné, že mají stovky služeb zaregistrovaných v rámci rozhraní.</span><span class="sxs-lookup"><span data-stu-id="b1586-156">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="b1586-157">V následující tabulce je uvedena malá ukázka služeb registrovaných v rozhraní.</span><span class="sxs-lookup"><span data-stu-id="b1586-157">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="b1586-158">Typ služby</span><span class="sxs-lookup"><span data-stu-id="b1586-158">Service Type</span></span> | <span data-ttu-id="b1586-159">Doba platnosti</span><span class="sxs-lookup"><span data-stu-id="b1586-159">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="b1586-160">Dočasný</span><span class="sxs-lookup"><span data-stu-id="b1586-160">Transient</span></span> |
| `IHostApplicationLifetime` | <span data-ttu-id="b1586-161">Singleton</span><span class="sxs-lookup"><span data-stu-id="b1586-161">Singleton</span></span> |
| `IWebHostEnvironment` | <span data-ttu-id="b1586-162">Singleton</span><span class="sxs-lookup"><span data-stu-id="b1586-162">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="b1586-163">Singleton</span><span class="sxs-lookup"><span data-stu-id="b1586-163">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="b1586-164">Dočasný</span><span class="sxs-lookup"><span data-stu-id="b1586-164">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="b1586-165">Singleton</span><span class="sxs-lookup"><span data-stu-id="b1586-165">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="b1586-166">Dočasný</span><span class="sxs-lookup"><span data-stu-id="b1586-166">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="b1586-167">Singleton</span><span class="sxs-lookup"><span data-stu-id="b1586-167">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="b1586-168">Singleton</span><span class="sxs-lookup"><span data-stu-id="b1586-168">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="b1586-169">Singleton</span><span class="sxs-lookup"><span data-stu-id="b1586-169">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="b1586-170">Dočasný</span><span class="sxs-lookup"><span data-stu-id="b1586-170">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="b1586-171">Singleton</span><span class="sxs-lookup"><span data-stu-id="b1586-171">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="b1586-172">Singleton</span><span class="sxs-lookup"><span data-stu-id="b1586-172">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="b1586-173">Singleton</span><span class="sxs-lookup"><span data-stu-id="b1586-173">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="b1586-174">Registrace dalších služeb pomocí rozšiřujících metod</span><span class="sxs-lookup"><span data-stu-id="b1586-174">Register additional services with extension methods</span></span>

<span data-ttu-id="b1586-175">Pokud je k dispozici metoda rozšíření kolekce služeb pro registraci služby (a jejích závislých služeb, v případě potřeby), konvence používá jedinou `Add{SERVICE_NAME}` metodu rozšíření k registraci všech služeb vyžadovaných touto službou.</span><span class="sxs-lookup"><span data-stu-id="b1586-175">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="b1586-176">Následující kód je příkladem přidání dalších služeb do kontejneru pomocí metod rozšíření [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) a <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> :</span><span class="sxs-lookup"><span data-stu-id="b1586-176">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="b1586-177">Další informace najdete <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> ve třídě v dokumentaci k rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="b1586-177">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="b1586-178">Životnost služeb</span><span class="sxs-lookup"><span data-stu-id="b1586-178">Service lifetimes</span></span>

<span data-ttu-id="b1586-179">Vyberte odpovídající dobu života pro každou registrovanou službu.</span><span class="sxs-lookup"><span data-stu-id="b1586-179">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="b1586-180">Služby ASP.NET Core Services je možné nakonfigurovat s následujícími životnostmi:</span><span class="sxs-lookup"><span data-stu-id="b1586-180">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="b1586-181">Dočasný</span><span class="sxs-lookup"><span data-stu-id="b1586-181">Transient</span></span>

<span data-ttu-id="b1586-182">Služba přechodných životností ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*> ) se vytvoří pokaždé, když se požadují z kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="b1586-182">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="b1586-183">Tato životnost funguje nejlépe pro odlehčené bezstavové služby.</span><span class="sxs-lookup"><span data-stu-id="b1586-183">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="b1586-184">V aplikacích, které zpracovávají požadavky, jsou přechodné služby na konci žádosti zrušené.</span><span class="sxs-lookup"><span data-stu-id="b1586-184">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="b1586-185">Obor</span><span class="sxs-lookup"><span data-stu-id="b1586-185">Scoped</span></span>

<span data-ttu-id="b1586-186">Oborové služby () s rozsahem platnosti ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*> ) se vytvoří jednou za požadavek klienta (připojení).</span><span class="sxs-lookup"><span data-stu-id="b1586-186">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="b1586-187">V aplikacích, které zpracovávají požadavky, se vymezené služby na konci žádosti odstraní.</span><span class="sxs-lookup"><span data-stu-id="b1586-187">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="b1586-188">Při použití oboru služby v middlewaru založit službu do `Invoke` `InvokeAsync` metody nebo.</span><span class="sxs-lookup"><span data-stu-id="b1586-188">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="b1586-189">Nepoužívejte [vkládání pomocí injektáže konstruktoru](xref:mvc/controllers/dependency-injection#constructor-injection) , protože vynutí, aby se služba chovala jako typ singleton.</span><span class="sxs-lookup"><span data-stu-id="b1586-189">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="b1586-190">Další informace naleznete v tématu <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="b1586-190">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="b1586-191">Singleton</span><span class="sxs-lookup"><span data-stu-id="b1586-191">Singleton</span></span>

<span data-ttu-id="b1586-192">Služba singleton životnosti ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*> ) je vytvořena při prvním vyžádání (nebo při `Startup.ConfigureServices` spuštění a instance je určena pro registraci služby).</span><span class="sxs-lookup"><span data-stu-id="b1586-192">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="b1586-193">Každý další požadavek používá stejnou instanci.</span><span class="sxs-lookup"><span data-stu-id="b1586-193">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="b1586-194">Pokud aplikace vyžaduje chování singleton, doporučuje se povolit kontejneru služby spravovat dobu života služby.</span><span class="sxs-lookup"><span data-stu-id="b1586-194">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="b1586-195">Neimplementujte vzor návrhu singleton a poskytněte uživatelský kód pro správu životního cyklu objektu ve třídě.</span><span class="sxs-lookup"><span data-stu-id="b1586-195">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="b1586-196">V aplikacích, které zpracovávají požadavky, jsou nejednoznačné služby uvolněny, když <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> je uvolněna při vypnutí aplikace.</span><span class="sxs-lookup"><span data-stu-id="b1586-196">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="b1586-197">Není bezpečné přeložit oborovou službu z typu singleton.</span><span class="sxs-lookup"><span data-stu-id="b1586-197">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="b1586-198">Může dojít k tomu, že služba má při zpracování dalších požadavků špatný stav.</span><span class="sxs-lookup"><span data-stu-id="b1586-198">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="b1586-199">Metody registrace služby</span><span class="sxs-lookup"><span data-stu-id="b1586-199">Service registration methods</span></span>

<span data-ttu-id="b1586-200">Metody rozšíření registrace služby nabízejí přetížení, která jsou užitečná pro konkrétní scénáře.</span><span class="sxs-lookup"><span data-stu-id="b1586-200">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="b1586-201">Metoda</span><span class="sxs-lookup"><span data-stu-id="b1586-201">Method</span></span> | <span data-ttu-id="b1586-202">Automaticky</span><span class="sxs-lookup"><span data-stu-id="b1586-202">Automatic</span></span><br><span data-ttu-id="b1586-203">odkazy objektů</span><span class="sxs-lookup"><span data-stu-id="b1586-203">object</span></span><br><span data-ttu-id="b1586-204">odvod</span><span class="sxs-lookup"><span data-stu-id="b1586-204">disposal</span></span> | <span data-ttu-id="b1586-205">Několik</span><span class="sxs-lookup"><span data-stu-id="b1586-205">Multiple</span></span><br><span data-ttu-id="b1586-206">implementace</span><span class="sxs-lookup"><span data-stu-id="b1586-206">implementations</span></span> | <span data-ttu-id="b1586-207">Pass – argumenty</span><span class="sxs-lookup"><span data-stu-id="b1586-207">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="b1586-208">Příklad:</span><span class="sxs-lookup"><span data-stu-id="b1586-208">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="b1586-209">Ano</span><span class="sxs-lookup"><span data-stu-id="b1586-209">Yes</span></span> | <span data-ttu-id="b1586-210">Ano</span><span class="sxs-lookup"><span data-stu-id="b1586-210">Yes</span></span> | <span data-ttu-id="b1586-211">No</span><span class="sxs-lookup"><span data-stu-id="b1586-211">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="b1586-212">Příklady:</span><span class="sxs-lookup"><span data-stu-id="b1586-212">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="b1586-213">Ano</span><span class="sxs-lookup"><span data-stu-id="b1586-213">Yes</span></span> | <span data-ttu-id="b1586-214">Ano</span><span class="sxs-lookup"><span data-stu-id="b1586-214">Yes</span></span> | <span data-ttu-id="b1586-215">Ano</span><span class="sxs-lookup"><span data-stu-id="b1586-215">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="b1586-216">Příklad:</span><span class="sxs-lookup"><span data-stu-id="b1586-216">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="b1586-217">Yes</span><span class="sxs-lookup"><span data-stu-id="b1586-217">Yes</span></span> | <span data-ttu-id="b1586-218">Ne</span><span class="sxs-lookup"><span data-stu-id="b1586-218">No</span></span> | <span data-ttu-id="b1586-219">Ne</span><span class="sxs-lookup"><span data-stu-id="b1586-219">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="b1586-220">Příklady:</span><span class="sxs-lookup"><span data-stu-id="b1586-220">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="b1586-221">No</span><span class="sxs-lookup"><span data-stu-id="b1586-221">No</span></span> | <span data-ttu-id="b1586-222">Ano</span><span class="sxs-lookup"><span data-stu-id="b1586-222">Yes</span></span> | <span data-ttu-id="b1586-223">Ano</span><span class="sxs-lookup"><span data-stu-id="b1586-223">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="b1586-224">Příklady:</span><span class="sxs-lookup"><span data-stu-id="b1586-224">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="b1586-225">Ne</span><span class="sxs-lookup"><span data-stu-id="b1586-225">No</span></span> | <span data-ttu-id="b1586-226">No</span><span class="sxs-lookup"><span data-stu-id="b1586-226">No</span></span> | <span data-ttu-id="b1586-227">Yes</span><span class="sxs-lookup"><span data-stu-id="b1586-227">Yes</span></span> |

<span data-ttu-id="b1586-228">Další informace o vyřazení typů najdete v části věnované [vyřazení služeb](#disposal-of-services) .</span><span class="sxs-lookup"><span data-stu-id="b1586-228">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="b1586-229">Běžným scénářem pro více implementací je vytvoření [typů pro testování](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="b1586-229">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="b1586-230">`TryAdd{LIFETIME}`metody registrují službu jenom v případě, že už není zaregistrovaná implementace.</span><span class="sxs-lookup"><span data-stu-id="b1586-230">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="b1586-231">V následujícím příkladu se první řádek registruje `MyDependency` pro `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="b1586-231">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="b1586-232">Druhý řádek nemá žádný účinek, protože `IMyDependency` už má registrovanou implementaci:</span><span class="sxs-lookup"><span data-stu-id="b1586-232">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="b1586-233">Další informace naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="b1586-233">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="b1586-234">Metody [TryAddEnumerable (ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) registrují službu pouze v případě, že již není implementace *stejného typu*.</span><span class="sxs-lookup"><span data-stu-id="b1586-234">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="b1586-235">Několik služeb je vyřešeno prostřednictvím `IEnumerable<{SERVICE}>` .</span><span class="sxs-lookup"><span data-stu-id="b1586-235">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="b1586-236">Při registraci služeb chce vývojář přidat instanci jenom v případě, že už jeden ze stejného typu není přidaný.</span><span class="sxs-lookup"><span data-stu-id="b1586-236">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="b1586-237">Obecně je tato metoda používána autory knihovny k tomu, aby nedocházelo k registraci dvou kopií instance v kontejneru.</span><span class="sxs-lookup"><span data-stu-id="b1586-237">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="b1586-238">V následujícím příkladu se první řádek registruje `MyDep` pro `IMyDep1` .</span><span class="sxs-lookup"><span data-stu-id="b1586-238">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="b1586-239">Druhý řádek se registruje `MyDep` pro `IMyDep2` .</span><span class="sxs-lookup"><span data-stu-id="b1586-239">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="b1586-240">Třetí řádek nemá žádný vliv, protože `IMyDep1` už má registrovanou implementaci `MyDep` :</span><span class="sxs-lookup"><span data-stu-id="b1586-240">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="b1586-241">Chování injektáže konstruktoru</span><span class="sxs-lookup"><span data-stu-id="b1586-241">Constructor injection behavior</span></span>

<span data-ttu-id="b1586-242">Služby je možné vyřešit pomocí dvou mechanismů:</span><span class="sxs-lookup"><span data-stu-id="b1586-242">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="b1586-243"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Povoluje vytvoření objektu bez registrace služby v kontejneru vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="b1586-243"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="b1586-244">`ActivatorUtilities`se používá s uživateli s přístupem k uživatelům, jako jsou například rutiny značek, řadiče MVC a pořadače modelů.</span><span class="sxs-lookup"><span data-stu-id="b1586-244">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="b1586-245">Konstruktory mohou přijímat argumenty, které nejsou poskytovány vkládáním závislostí, ale argumenty musí přiřadit výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="b1586-245">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="b1586-246">Když jsou služby vyřešeny `IServiceProvider` nebo `ActivatorUtilities` , [Injektáže konstruktoru](xref:mvc/controllers/dependency-injection#constructor-injection) vyžaduje *veřejný* konstruktor.</span><span class="sxs-lookup"><span data-stu-id="b1586-246">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="b1586-247">Když jsou služby vyřešeny nástrojem `ActivatorUtilities` , [Injektáže konstruktoru](xref:mvc/controllers/dependency-injection#constructor-injection) vyžaduje, aby existoval pouze jeden příslušný konstruktor.</span><span class="sxs-lookup"><span data-stu-id="b1586-247">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="b1586-248">Přetížení konstruktoru jsou podporovaná, ale může existovat jenom jedno přetížení, jehož argumenty můžou být splněné vkládáním závislostí.</span><span class="sxs-lookup"><span data-stu-id="b1586-248">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="b1586-249">Entity Framework kontexty</span><span class="sxs-lookup"><span data-stu-id="b1586-249">Entity Framework contexts</span></span>

<span data-ttu-id="b1586-250">Entity Framework kontexty se obvykle přidávají do kontejneru služby pomocí [rozsahu životnosti](#service-lifetimes) , protože operace databáze webové aplikace jsou obvykle vymezeny na požadavky klienta.</span><span class="sxs-lookup"><span data-stu-id="b1586-250">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="b1586-251">Výchozí doba života je vymezena v případě, že při registraci kontextu databáze není zadána doba života při [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) přetížení.</span><span class="sxs-lookup"><span data-stu-id="b1586-251">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="b1586-252">Služby pro danou dobu života by neměly používat kontext databáze s kratší dobou životnosti než služba.</span><span class="sxs-lookup"><span data-stu-id="b1586-252">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="b1586-253">Možnosti života a registrace</span><span class="sxs-lookup"><span data-stu-id="b1586-253">Lifetime and registration options</span></span>

<span data-ttu-id="b1586-254">Chcete-li předvést rozdíl mezi možnostmi životního cyklu a registrací, zvažte následující rozhraní, která představují úlohy jako operaci s jedinečným identifikátorem `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="b1586-254">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="b1586-255">V závislosti na tom, jak je životnost provozní služby nakonfigurována pro následující rozhraní, kontejner poskytuje buď stejnou nebo jinou instanci služby, když je vyžádána třídou:</span><span class="sxs-lookup"><span data-stu-id="b1586-255">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="b1586-256">Rozhraní jsou implementována ve `Operation` třídě.</span><span class="sxs-lookup"><span data-stu-id="b1586-256">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="b1586-257">`Operation`Konstruktor generuje identifikátor GUID, pokud není dodán:</span><span class="sxs-lookup"><span data-stu-id="b1586-257">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="b1586-258">`OperationService`Je zaregistrován, který závisí na každém z ostatních `Operation` typů.</span><span class="sxs-lookup"><span data-stu-id="b1586-258">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="b1586-259">Když `OperationService` je požadavek proveden prostřednictvím injektáže závislosti, obdrží buď novou instanci každé služby, nebo existující instanci na základě životnosti závislé služby.</span><span class="sxs-lookup"><span data-stu-id="b1586-259">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="b1586-260">Když se v případě vyžádání z kontejneru vytvoří přechodné služby, `OperationId` liší se od `IOperationTransient` služby `OperationId` `OperationService` .</span><span class="sxs-lookup"><span data-stu-id="b1586-260">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="b1586-261">`OperationService`přijme novou instanci `IOperationTransient` třídy.</span><span class="sxs-lookup"><span data-stu-id="b1586-261">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="b1586-262">Nová instance vrací jinou instanci `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="b1586-262">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="b1586-263">Když se vytvoří vymezené služby pro jednotlivé požadavky klienta, `OperationId` `IOperationScoped` služba je stejná jako `OperationService` v rámci žádosti klienta.</span><span class="sxs-lookup"><span data-stu-id="b1586-263">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="b1586-264">V rámci požadavků klientů obě služby sdílejí jinou `OperationId` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="b1586-264">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="b1586-265">Pokud se služby instance singleton a singleton vytvoří jednou a použije se pro všechny požadavky klientů a všechny služby, `OperationId` je v rámci všech žádostí o službu konstantní.</span><span class="sxs-lookup"><span data-stu-id="b1586-265">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="b1586-266">V `Startup.ConfigureServices` je každý typ přidán do kontejneru podle jeho pojmenované životnosti:</span><span class="sxs-lookup"><span data-stu-id="b1586-266">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="b1586-267">`IOperationSingletonInstance`Služba používá konkrétní instanci se známým ID `Guid.Empty` .</span><span class="sxs-lookup"><span data-stu-id="b1586-267">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="b1586-268">Je jasné, že pokud se tento typ používá (jeho identifikátor GUID je všechny nuly).</span><span class="sxs-lookup"><span data-stu-id="b1586-268">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="b1586-269">Ukázková aplikace ukazuje dobu života objektu v rámci a mezi jednotlivými požadavky.</span><span class="sxs-lookup"><span data-stu-id="b1586-269">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="b1586-270">Ukázková aplikace `IndexModel` požaduje každý druh `IOperation` typu a `OperationService` .</span><span class="sxs-lookup"><span data-stu-id="b1586-270">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="b1586-271">Stránka potom zobrazí všechny hodnoty třídy modelu stránky a `OperationId` hodnoty služby prostřednictvím přiřazení vlastností:</span><span class="sxs-lookup"><span data-stu-id="b1586-271">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="b1586-272">Výsledkem dvou následujících výstupů jsou výsledky dvou požadavků:</span><span class="sxs-lookup"><span data-stu-id="b1586-272">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="b1586-273">**První požadavek:**</span><span class="sxs-lookup"><span data-stu-id="b1586-273">**First request:**</span></span>

<span data-ttu-id="b1586-274">Operace kontroleru:</span><span class="sxs-lookup"><span data-stu-id="b1586-274">Controller operations:</span></span>

<span data-ttu-id="b1586-275">Přechodný: d233e165-f417-469b-A866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="b1586-275">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="b1586-276">Vymezené: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="b1586-276">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="b1586-277">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="b1586-277">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="b1586-278">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="b1586-278">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="b1586-279">`OperationService`Operations</span><span class="sxs-lookup"><span data-stu-id="b1586-279">`OperationService` operations:</span></span>

<span data-ttu-id="b1586-280">Přechodný: c6b049eb-1318-4E31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="b1586-280">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="b1586-281">Vymezené: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="b1586-281">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="b1586-282">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="b1586-282">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="b1586-283">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="b1586-283">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="b1586-284">**Druhá žádost:**</span><span class="sxs-lookup"><span data-stu-id="b1586-284">**Second request:**</span></span>

<span data-ttu-id="b1586-285">Operace kontroleru:</span><span class="sxs-lookup"><span data-stu-id="b1586-285">Controller operations:</span></span>

<span data-ttu-id="b1586-286">Přechodný: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="b1586-286">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="b1586-287">Vymezené: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="b1586-287">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="b1586-288">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="b1586-288">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="b1586-289">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="b1586-289">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="b1586-290">`OperationService`Operations</span><span class="sxs-lookup"><span data-stu-id="b1586-290">`OperationService` operations:</span></span>

<span data-ttu-id="b1586-291">Přechodný: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="b1586-291">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="b1586-292">Vymezené: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="b1586-292">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="b1586-293">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="b1586-293">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="b1586-294">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="b1586-294">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="b1586-295">Pozor, které `OperationId` hodnoty se liší v rámci požadavku a mezi požadavky:</span><span class="sxs-lookup"><span data-stu-id="b1586-295">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="b1586-296">*Přechodné* objekty jsou vždy odlišné.</span><span class="sxs-lookup"><span data-stu-id="b1586-296">*Transient* objects are always different.</span></span> <span data-ttu-id="b1586-297">Přechodná `OperationId` hodnota pro první i druhý požadavek klienta se liší pro obě `OperationService` operace i mezi požadavky klientů.</span><span class="sxs-lookup"><span data-stu-id="b1586-297">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="b1586-298">Každé žádosti o službu a žádosti klienta se poskytne nová instance.</span><span class="sxs-lookup"><span data-stu-id="b1586-298">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="b1586-299">*Vymezené* objekty jsou v požadavku klienta stejné, ale v rámci požadavků klientů se liší.</span><span class="sxs-lookup"><span data-stu-id="b1586-299">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="b1586-300">Objekty *singleton* jsou stejné pro každý objekt a každý požadavek bez ohledu na to, zda `Operation` je instance uvedena v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b1586-300">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="b1586-301">Volat služby z Main</span><span class="sxs-lookup"><span data-stu-id="b1586-301">Call services from main</span></span>

<span data-ttu-id="b1586-302">Vytvořte <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> pomocí [IServiceScopeFactory. CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) pro řešení oboru služby v rámci rozsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="b1586-302">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="b1586-303">Tento přístup je užitečný pro přístup k oboruované službě při spuštění za účelem spouštění úloh inicializace.</span><span class="sxs-lookup"><span data-stu-id="b1586-303">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="b1586-304">Následující příklad ukazuje, jak získat kontext pro `MyScopedService` `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="b1586-304">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="b1586-305">Ověřování oboru</span><span class="sxs-lookup"><span data-stu-id="b1586-305">Scope validation</span></span>

<span data-ttu-id="b1586-306">Když je aplikace spuštěná ve vývojovém prostředí a volá [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) , aby se vytvořila hostitel, použije výchozí poskytovatel služeb kontroly, aby ověřil, že:</span><span class="sxs-lookup"><span data-stu-id="b1586-306">When the app is running in the Development environment and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="b1586-307">Oborové služby nejsou přímo nebo nepřímo vyřešeny od poskytovatele kořenové služby.</span><span class="sxs-lookup"><span data-stu-id="b1586-307">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="b1586-308">Oborové služby nejsou přímo nebo nepřímo vložené do singleton.</span><span class="sxs-lookup"><span data-stu-id="b1586-308">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="b1586-309">Poskytovatel kořenové služby se vytvoří, když <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> se zavolá.</span><span class="sxs-lookup"><span data-stu-id="b1586-309">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="b1586-310">Doba života poskytovatele kořenové služby odpovídá době životnosti aplikace nebo serveru, když se poskytovatel spustí s aplikací a je uvolněný při ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="b1586-310">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="b1586-311">Oborové služby jsou uvolněny kontejnerem, který je vytvořil.</span><span class="sxs-lookup"><span data-stu-id="b1586-311">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="b1586-312">Pokud je v kořenovém kontejneru vytvořena vymezená služba, doba života služby je efektivně povýšena na typ singleton, protože je uvolněna pouze kořenovým kontejnerem při vypnutí aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="b1586-312">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="b1586-313">Při ověřování oborů služeb se tyto situace zachytí `BuildServiceProvider` .</span><span class="sxs-lookup"><span data-stu-id="b1586-313">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="b1586-314">Další informace naleznete v tématu <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="b1586-314">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="b1586-315">Žádosti o služby</span><span class="sxs-lookup"><span data-stu-id="b1586-315">Request Services</span></span>

<span data-ttu-id="b1586-316">Služby, které jsou k dispozici v rámci žádosti o ASP.NET Core, `HttpContext` jsou zpřístupněny prostřednictvím kolekce [HttpContext. RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) .</span><span class="sxs-lookup"><span data-stu-id="b1586-316">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="b1586-317">Žádosti o služby reprezentují služby nakonfigurované a požadované v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="b1586-317">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="b1586-318">Když objekty určují závislosti, jsou splněny typy nalezenými v `RequestServices` , ne `ApplicationServices` .</span><span class="sxs-lookup"><span data-stu-id="b1586-318">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="b1586-319">Obecně platí, že aplikace by tyto vlastnosti neměla používat přímo.</span><span class="sxs-lookup"><span data-stu-id="b1586-319">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="b1586-320">Místo toho požádejte o typy, které třídy vyžadují prostřednictvím konstruktorů třídy, a umožněte rozhraní vkládat závislosti.</span><span class="sxs-lookup"><span data-stu-id="b1586-320">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span> <span data-ttu-id="b1586-321">To poskytuje třídy, které jsou snáze testovány.</span><span class="sxs-lookup"><span data-stu-id="b1586-321">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="b1586-322">Preferovat požadavky na závislosti jako parametry konstruktoru pro přístup ke `RequestServices` kolekci.</span><span class="sxs-lookup"><span data-stu-id="b1586-322">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="b1586-323">Navrhnout služby pro vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="b1586-323">Design services for dependency injection</span></span>

<span data-ttu-id="b1586-324">Osvědčené postupy:</span><span class="sxs-lookup"><span data-stu-id="b1586-324">Best practices are to:</span></span>

* <span data-ttu-id="b1586-325">Navrhněte služby pro použití injektáže závislosti k získání jejich závislostí.</span><span class="sxs-lookup"><span data-stu-id="b1586-325">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="b1586-326">Vyhněte se stavovým, statickým třídám a členům.</span><span class="sxs-lookup"><span data-stu-id="b1586-326">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="b1586-327">Navrhněte aplikace, aby místo toho používaly služby s jedním prvkem, což se vyhne vytváření globálního stavu.</span><span class="sxs-lookup"><span data-stu-id="b1586-327">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="b1586-328">Vyhněte se přímému vytváření instancí závislých tříd v rámci služeb.</span><span class="sxs-lookup"><span data-stu-id="b1586-328">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="b1586-329">Přímá instance Couples kód na konkrétní implementaci.</span><span class="sxs-lookup"><span data-stu-id="b1586-329">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="b1586-330">Vytvářejte třídy aplikací malými, dobře faktoringně a snadno testované.</span><span class="sxs-lookup"><span data-stu-id="b1586-330">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="b1586-331">Pokud se zdá, že třída má příliš mnoho vložených závislostí, obvykle se jedná o znaménko, že třída má příliš mnoho zodpovědnosti a je v rozporu s [principem jediné zodpovědnosti (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="b1586-331">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="b1586-332">Pokuste se Refaktorovat třídu přesunutím některých odpovědností do nové třídy.</span><span class="sxs-lookup"><span data-stu-id="b1586-332">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="b1586-333">Mějte na paměti, že Razor stránky tříd modelu stránky a třídy KONTROLERU MVC by se měly zaměřit na uživatelské rozhraní.</span><span class="sxs-lookup"><span data-stu-id="b1586-333">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="b1586-334">Obchodní pravidla a podrobnosti implementace přístupu k datům by měly být uchovávány ve třídách, které jsou vhodné pro tyto [samostatné obavy](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="b1586-334">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="b1586-335">Vyřazení služeb</span><span class="sxs-lookup"><span data-stu-id="b1586-335">Disposal of services</span></span>

<span data-ttu-id="b1586-336">Kontejner volá <xref:System.IDisposable.Dispose*> typy, které <xref:System.IDisposable> vytvoří.</span><span class="sxs-lookup"><span data-stu-id="b1586-336">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="b1586-337">Pokud je instance přidána do kontejneru pomocí uživatelského kódu, nebude uvolněna automaticky.</span><span class="sxs-lookup"><span data-stu-id="b1586-337">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="b1586-338">V následujícím příkladu jsou služby vytvořeny pomocí kontejneru služby a automaticky odstraněny:</span><span class="sxs-lookup"><span data-stu-id="b1586-338">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="b1586-339">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="b1586-339">In the following example:</span></span>

* <span data-ttu-id="b1586-340">Instance služby nejsou vytvořeny kontejnerem služby.</span><span class="sxs-lookup"><span data-stu-id="b1586-340">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="b1586-341">Zamýšlené životnosti služeb nejsou rozhraním známy.</span><span class="sxs-lookup"><span data-stu-id="b1586-341">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="b1586-342">Rozhraní neodstraní služby automaticky.</span><span class="sxs-lookup"><span data-stu-id="b1586-342">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="b1586-343">Pokud se služby explicitně neodstraní v kódu pro vývojáře, zůstanou zachovány, dokud se aplikace neukončí.</span><span class="sxs-lookup"><span data-stu-id="b1586-343">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="b1586-344">Doprovodné materiály k rozhraní IDisposable pro přechodné a sdílené instance</span><span class="sxs-lookup"><span data-stu-id="b1586-344">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="b1586-345">Přechodná, omezená doba života</span><span class="sxs-lookup"><span data-stu-id="b1586-345">Transient, limited lifetime</span></span>

<span data-ttu-id="b1586-346">**Scénář**</span><span class="sxs-lookup"><span data-stu-id="b1586-346">**Scenario**</span></span>

<span data-ttu-id="b1586-347">Aplikace vyžaduje <xref:System.IDisposable> instanci s přechodným životností pro některý z následujících scénářů:</span><span class="sxs-lookup"><span data-stu-id="b1586-347">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="b1586-348">Instance je vyřešena v kořenovém oboru.</span><span class="sxs-lookup"><span data-stu-id="b1586-348">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="b1586-349">Instance by měla být uvolněna před ukončením oboru.</span><span class="sxs-lookup"><span data-stu-id="b1586-349">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="b1586-350">**Řešení**</span><span class="sxs-lookup"><span data-stu-id="b1586-350">**Solution**</span></span>

<span data-ttu-id="b1586-351">Pomocí modelu továrny vytvořte instanci mimo nadřazený obor.</span><span class="sxs-lookup"><span data-stu-id="b1586-351">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="b1586-352">V takové situaci by aplikace měla obvykle `Create` metodu, která volá přímo konstruktor finálního typu.</span><span class="sxs-lookup"><span data-stu-id="b1586-352">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="b1586-353">Pokud má konečný typ jiné závislosti, továrna může:</span><span class="sxs-lookup"><span data-stu-id="b1586-353">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="b1586-354">Přijmout <xref:System.IServiceProvider> ve svém konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="b1586-354">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="b1586-355">Slouží <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> k vytvoření instance instance mimo kontejner, při použití kontejneru pro jeho závislosti.</span><span class="sxs-lookup"><span data-stu-id="b1586-355">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="b1586-356">Sdílená instance, omezená doba života</span><span class="sxs-lookup"><span data-stu-id="b1586-356">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="b1586-357">**Scénář**</span><span class="sxs-lookup"><span data-stu-id="b1586-357">**Scenario**</span></span>

<span data-ttu-id="b1586-358">Aplikace vyžaduje sdílenou <xref:System.IDisposable> instanci napříč více službami, ale <xref:System.IDisposable> měla by mít omezené trvání.</span><span class="sxs-lookup"><span data-stu-id="b1586-358">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="b1586-359">**Řešení**</span><span class="sxs-lookup"><span data-stu-id="b1586-359">**Solution**</span></span>

<span data-ttu-id="b1586-360">Zaregistrujte instanci s vymezenou životností.</span><span class="sxs-lookup"><span data-stu-id="b1586-360">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="b1586-361">Použijte <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> k zahájení a vytvoření nového <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> .</span><span class="sxs-lookup"><span data-stu-id="b1586-361">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="b1586-362"><xref:System.IServiceProvider>Požadované služby získáte pomocí tohoto oboru.</span><span class="sxs-lookup"><span data-stu-id="b1586-362">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="b1586-363">Vyřadit rozsah, pokud by životnost měla končit.</span><span class="sxs-lookup"><span data-stu-id="b1586-363">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="b1586-364">Obecné pokyny</span><span class="sxs-lookup"><span data-stu-id="b1586-364">General Guidelines</span></span>

* <span data-ttu-id="b1586-365">Neregistrujte <xref:System.IDisposable> instance s přechodným oborem.</span><span class="sxs-lookup"><span data-stu-id="b1586-365">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="b1586-366">Místo toho použijte výrobní model.</span><span class="sxs-lookup"><span data-stu-id="b1586-366">Use the factory pattern instead.</span></span>
* <span data-ttu-id="b1586-367">V kořenovém oboru neřešte přechodné nebo vymezené <xref:System.IDisposable> instance.</span><span class="sxs-lookup"><span data-stu-id="b1586-367">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="b1586-368">Jedinou obecnou výjimkou je, když aplikace vytvoří nebo znovu vytvoří a odstraní <xref:System.IServiceProvider> , což není ideální vzor.</span><span class="sxs-lookup"><span data-stu-id="b1586-368">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="b1586-369">Příjem <xref:System.IDisposable> závislosti přes di nevyžaduje, aby se příjemce sám implementoval <xref:System.IDisposable> .</span><span class="sxs-lookup"><span data-stu-id="b1586-369">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="b1586-370">Příjemce <xref:System.IDisposable> závislosti by neměl volat <xref:System.IDisposable.Dispose%2A> tuto závislost.</span><span class="sxs-lookup"><span data-stu-id="b1586-370">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="b1586-371">Obory by měly sloužit k řízení životnosti služeb.</span><span class="sxs-lookup"><span data-stu-id="b1586-371">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="b1586-372">Obory nejsou hierarchické a mezi obory není žádné zvláštní připojení.</span><span class="sxs-lookup"><span data-stu-id="b1586-372">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="b1586-373">Výchozí nahrazení kontejneru služby</span><span class="sxs-lookup"><span data-stu-id="b1586-373">Default service container replacement</span></span>

<span data-ttu-id="b1586-374">Integrovaný kontejner služeb je navržený tak, aby sloužil potřebám architektury a většině spotřebitelských aplikací.</span><span class="sxs-lookup"><span data-stu-id="b1586-374">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="b1586-375">Pokud nepotřebujete určitou funkci, kterou integrovaný kontejner nepodporuje, doporučujeme použít vestavěný kontejner, například:</span><span class="sxs-lookup"><span data-stu-id="b1586-375">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="b1586-376">Vkládání vlastností</span><span class="sxs-lookup"><span data-stu-id="b1586-376">Property injection</span></span>
* <span data-ttu-id="b1586-377">Vkládání na základě názvu</span><span class="sxs-lookup"><span data-stu-id="b1586-377">Injection based on name</span></span>
* <span data-ttu-id="b1586-378">Podřízené kontejnery</span><span class="sxs-lookup"><span data-stu-id="b1586-378">Child containers</span></span>
* <span data-ttu-id="b1586-379">Vlastní Správa životního cyklu</span><span class="sxs-lookup"><span data-stu-id="b1586-379">Custom lifetime management</span></span>
* <span data-ttu-id="b1586-380">`Func<T>`Podpora opožděné inicializace</span><span class="sxs-lookup"><span data-stu-id="b1586-380">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="b1586-381">Registrace založená na konvencích</span><span class="sxs-lookup"><span data-stu-id="b1586-381">Convention-based registration</span></span>

<span data-ttu-id="b1586-382">S ASP.NET Coremi aplikacemi je možné používat následující kontejnery třetích stran:</span><span class="sxs-lookup"><span data-stu-id="b1586-382">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="b1586-383">Autofac</span><span class="sxs-lookup"><span data-stu-id="b1586-383">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="b1586-384">DryIoc</span><span class="sxs-lookup"><span data-stu-id="b1586-384">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="b1586-385">Integrit</span><span class="sxs-lookup"><span data-stu-id="b1586-385">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="b1586-386">LightInject</span><span class="sxs-lookup"><span data-stu-id="b1586-386">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="b1586-387">Lamar</span><span class="sxs-lookup"><span data-stu-id="b1586-387">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="b1586-388">Stashbox</span><span class="sxs-lookup"><span data-stu-id="b1586-388">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="b1586-389">Unity</span><span class="sxs-lookup"><span data-stu-id="b1586-389">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="b1586-390">Bezpečnost vlákna</span><span class="sxs-lookup"><span data-stu-id="b1586-390">Thread safety</span></span>

<span data-ttu-id="b1586-391">Vytvořte služby, které jsou v bezpečí pro přístup z více vláken.</span><span class="sxs-lookup"><span data-stu-id="b1586-391">Create thread-safe singleton services.</span></span> <span data-ttu-id="b1586-392">Pokud má služba typu Singleton závislost na přechodné službě, přechodná služba může také vyžadovat zabezpečení vlákna v závislosti na tom, jak je používá typ singleton.</span><span class="sxs-lookup"><span data-stu-id="b1586-392">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="b1586-393">Výrobní metoda samostatné služby, jako je například druhý argument pro [AddSingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), nemusí být bezpečná pro přístup z více vláken.</span><span class="sxs-lookup"><span data-stu-id="b1586-393">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="b1586-394">Podobně jako konstruktor typu ( `static` ), je zaručeno, že bude volán jednou vláknem.</span><span class="sxs-lookup"><span data-stu-id="b1586-394">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="b1586-395">Doporučení</span><span class="sxs-lookup"><span data-stu-id="b1586-395">Recommendations</span></span>

* <span data-ttu-id="b1586-396">`async/await``Task`řešení služeb a se nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="b1586-396">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="b1586-397">Jazyk C# nepodporuje asynchronní konstruktory; Proto je doporučeným vzorem použití asynchronních metod po synchronním překladu služby.</span><span class="sxs-lookup"><span data-stu-id="b1586-397">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="b1586-398">Neukládejte data a konfiguraci přímo do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="b1586-398">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="b1586-399">Například nákupní košík uživatele by neměl být obvykle přidán do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="b1586-399">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="b1586-400">Konfigurace by měla používat [vzor možností](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="b1586-400">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="b1586-401">Podobně nepoužívejte objekty "držitel dat", které existují pouze k povolení přístupu k jinému objektu.</span><span class="sxs-lookup"><span data-stu-id="b1586-401">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="b1586-402">Je lepší žádat o skutečnou položku přes DI.</span><span class="sxs-lookup"><span data-stu-id="b1586-402">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="b1586-403">Vyhněte se statickému přístupu ke službám (například statickému psaní [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) pro použití jinde).</span><span class="sxs-lookup"><span data-stu-id="b1586-403">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="b1586-404">Vyhněte se použití *vzoru lokátoru služby*.</span><span class="sxs-lookup"><span data-stu-id="b1586-404">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="b1586-405">Například Nevolejte <xref:System.IServiceProvider.GetService*> pro získání instance služby, když můžete místo toho použít di:</span><span class="sxs-lookup"><span data-stu-id="b1586-405">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="b1586-406">**Nesprávně:**</span><span class="sxs-lookup"><span data-stu-id="b1586-406">**Incorrect:**</span></span>

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

  <span data-ttu-id="b1586-407">**Správné**:</span><span class="sxs-lookup"><span data-stu-id="b1586-407">**Correct**:</span></span>

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

* <span data-ttu-id="b1586-408">Další změnou lokátoru služby, aby se zabránilo, je vložení továrny, která vyřeší závislosti za běhu.</span><span class="sxs-lookup"><span data-stu-id="b1586-408">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="b1586-409">Oba tyto postupy kombinují [inverze strategií řízení](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .</span><span class="sxs-lookup"><span data-stu-id="b1586-409">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

* <span data-ttu-id="b1586-410">Vyhněte se statickému přístupu k `HttpContext` (například [IHttpContextAccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="b1586-410">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="b1586-411">Podobně jako u všech sad doporučení se může stát, že se vyžaduje ignorování doporučení.</span><span class="sxs-lookup"><span data-stu-id="b1586-411">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="b1586-412">Výjimky jsou zřídka, většinou zvláštní případy v rámci samotného rozhraní.</span><span class="sxs-lookup"><span data-stu-id="b1586-412">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="b1586-413">DI je *alternativou* ke vzorům statických nebo globálních přístupů k objektům.</span><span class="sxs-lookup"><span data-stu-id="b1586-413">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="b1586-414">Je možné, že nebudete moci využít výhody DI, pokud je kombinujete se statickým přístupem k objektům.</span><span class="sxs-lookup"><span data-stu-id="b1586-414">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="b1586-415">Doporučené vzory pro víceklientské architektury v DI</span><span class="sxs-lookup"><span data-stu-id="b1586-415">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="b1586-416">[Sadu jader](https://github.com/OrchardCMS/OrchardCore) nabízí víceklientské architektury.</span><span class="sxs-lookup"><span data-stu-id="b1586-416">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) provides multi-tenancy.</span></span> <span data-ttu-id="b1586-417">Další informace najdete v dokumentaci k sadě [sad](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="b1586-417">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="b1586-418">V ukázkových aplikacích v tématu najdete https://github.com/OrchardCMS/OrchardCore.Samples příklady vytváření modulárních a víceklientské aplikací s využitím jenom sady Core Core Framework bez jakýchkoli funkcí specifických pro CMS.</span><span class="sxs-lookup"><span data-stu-id="b1586-418">See the samples apps at https://github.com/OrchardCMS/OrchardCore.Samples for examples of how to build modular and multi-tenant apps using just Orchard Core Framework without any of the CMS specific features.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b1586-419">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="b1586-419">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="b1586-420">Čtyři způsoby, jak vyřadit IDisposable v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b1586-420">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="b1586-421">Zápis čistého kódu v ASP.NET Core pomocí injektáže závislosti (MSDN)</span><span class="sxs-lookup"><span data-stu-id="b1586-421">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="b1586-422">Explicitní závislosti – princip</span><span class="sxs-lookup"><span data-stu-id="b1586-422">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="b1586-423">Inverze řídicích kontejnerů a vzor injektáže závislosti (Martin Fowlera)</span><span class="sxs-lookup"><span data-stu-id="b1586-423">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="b1586-424">Jak zaregistrovat službu s více rozhraními v ASP.NET Core DI</span><span class="sxs-lookup"><span data-stu-id="b1586-424">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b1586-425">ASP.NET Core podporuje vzor návrhu softwaru pro vkládání závislostí (DI), což je technika pro dosažení [inverze ovládacího prvku (IOC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) mezi třídami a jejich závislostmi.</span><span class="sxs-lookup"><span data-stu-id="b1586-425">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="b1586-426">Další informace, které jsou specifické pro vkládání závislostí v rámci řadičů MVC, najdete v tématu <xref:mvc/controllers/dependency-injection> .</span><span class="sxs-lookup"><span data-stu-id="b1586-426">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="b1586-427">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b1586-427">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="b1586-428">Přehled injektáže závislosti</span><span class="sxs-lookup"><span data-stu-id="b1586-428">Overview of dependency injection</span></span>

<span data-ttu-id="b1586-429">*Závislost* je libovolný objekt, který vyžaduje jiný objekt.</span><span class="sxs-lookup"><span data-stu-id="b1586-429">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="b1586-430">Projděte si následující `MyDependency` třídu s `WriteMessage` metodou, na které jsou závislé jiné třídy v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="b1586-430">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="b1586-431">Instance `MyDependency` třídy může být vytvořena, aby byla `WriteMessage` Metoda k dispozici pro třídu.</span><span class="sxs-lookup"><span data-stu-id="b1586-431">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="b1586-432">`MyDependency`Třída je závislostí `IndexModel` třídy:</span><span class="sxs-lookup"><span data-stu-id="b1586-432">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="b1586-433">Třída vytvoří a přímo závisí na `MyDependency` instanci.</span><span class="sxs-lookup"><span data-stu-id="b1586-433">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="b1586-434">Závislosti kódu (například předchozí příklad) jsou problematické a je třeba se jim vyhnout z následujících důvodů:</span><span class="sxs-lookup"><span data-stu-id="b1586-434">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="b1586-435">Chcete-li nahradit `MyDependency` jinou implementací, třída musí být upravena.</span><span class="sxs-lookup"><span data-stu-id="b1586-435">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="b1586-436">Pokud `MyDependency` má závislosti, musí být nakonfigurovány třídou.</span><span class="sxs-lookup"><span data-stu-id="b1586-436">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="b1586-437">Ve velkém projektu s více třídami, v závislosti na tom `MyDependency` , je kód konfigurace rozptýlen napříč aplikací.</span><span class="sxs-lookup"><span data-stu-id="b1586-437">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="b1586-438">Tato implementace je obtížná pro testování částí.</span><span class="sxs-lookup"><span data-stu-id="b1586-438">This implementation is difficult to unit test.</span></span> <span data-ttu-id="b1586-439">Aplikace by měla používat třídu typu "nebo zástupné procedury" `MyDependency` , což není u tohoto přístupu možné.</span><span class="sxs-lookup"><span data-stu-id="b1586-439">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="b1586-440">Vkládání závislostí řeší tyto problémy prostřednictvím:</span><span class="sxs-lookup"><span data-stu-id="b1586-440">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="b1586-441">Použití rozhraní nebo základní třídy k abstrakci implementace závislosti.</span><span class="sxs-lookup"><span data-stu-id="b1586-441">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="b1586-442">Registrace závislosti v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="b1586-442">Registration of the dependency in a service container.</span></span> <span data-ttu-id="b1586-443">ASP.NET Core poskytuje integrovaný kontejner služeb <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="b1586-443">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="b1586-444">Služby jsou registrovány v metodě aplikace `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b1586-444">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="b1586-445">*Vložení* služby do konstruktoru třídy, kde se používá.</span><span class="sxs-lookup"><span data-stu-id="b1586-445">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="b1586-446">Rozhraní přebírá zodpovědnost za vytvoření instance závislosti a její odstranění, když už ji nepotřebujete.</span><span class="sxs-lookup"><span data-stu-id="b1586-446">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="b1586-447">V [ukázkové aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) `IMyDependency` definuje rozhraní metodu, kterou služba poskytuje aplikaci:</span><span class="sxs-lookup"><span data-stu-id="b1586-447">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="b1586-448">Toto rozhraní je implementováno konkrétním typem `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="b1586-448">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="b1586-449">`MyDependency`vyžádá <xref:Microsoft.Extensions.Logging.ILogger`1> v jeho konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="b1586-449">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="b1586-450">Není neobvyklé používat vkládání závislostí v zřetězeném způsobem.</span><span class="sxs-lookup"><span data-stu-id="b1586-450">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="b1586-451">Každá požadovaná závislost zase vyžádá své vlastní závislosti.</span><span class="sxs-lookup"><span data-stu-id="b1586-451">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="b1586-452">Kontejner vyřeší závislosti v grafu a vrátí plně přeloženou službu.</span><span class="sxs-lookup"><span data-stu-id="b1586-452">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="b1586-453">Kolektivní sada závislostí, které je třeba vyřešit, se obvykle označuje jako *strom závislosti*, *graf závislosti*nebo *graf objektů*.</span><span class="sxs-lookup"><span data-stu-id="b1586-453">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="b1586-454">`IMyDependency`a `ILogger<TCategoryName>` musí být registrována v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="b1586-454">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="b1586-455">`IMyDependency`je zaregistrován v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b1586-455">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b1586-456">`ILogger<TCategoryName>`je zaregistrované v infrastruktuře abstrakce protokolování, takže se jedná o [službu](#framework-provided-services) , která je zaregistrovaná ve výchozím rozhraní.</span><span class="sxs-lookup"><span data-stu-id="b1586-456">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="b1586-457">Kontejner se překládá `ILogger<TCategoryName>` tím, že využije [(Obecné) otevřené typy](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)a eliminuje nutnost registrace každého [(obecného) konstruovaného typu](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="b1586-457">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="b1586-458">V ukázkové aplikaci `IMyDependency` je služba zaregistrovaná u konkrétního typu `MyDependency` .</span><span class="sxs-lookup"><span data-stu-id="b1586-458">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="b1586-459">Registruje rozsah platnosti služby po dobu životnosti jediného požadavku.</span><span class="sxs-lookup"><span data-stu-id="b1586-459">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="b1586-460">[Doby platnosti služeb](#service-lifetimes) jsou popsány dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="b1586-460">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="b1586-461">Jednotlivé `services.Add{SERVICE_NAME}` metody rozšíření přidávají (a případně nakonfigurují) služby.</span><span class="sxs-lookup"><span data-stu-id="b1586-461">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="b1586-462">Například `services.AddMvc()` přidá Razor stránky služeb a MVC vyžaduje.</span><span class="sxs-lookup"><span data-stu-id="b1586-462">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="b1586-463">Doporučujeme, aby aplikace dodržovaly tuto konvenci.</span><span class="sxs-lookup"><span data-stu-id="b1586-463">We recommended that apps follow this convention.</span></span> <span data-ttu-id="b1586-464">Do oboru názvů [Microsoft. Extensions. DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) umístěte metody rozšíření pro zapouzdření skupin registrací služby.</span><span class="sxs-lookup"><span data-stu-id="b1586-464">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="b1586-465">Pokud konstruktor služby vyžaduje [vestavěný typ](/dotnet/csharp/language-reference/keywords/built-in-types-table), například `string` ,, může být typ vložen pomocí [Konfigurace](xref:fundamentals/configuration/index) nebo [vzoru možností](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="b1586-465">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="b1586-466">Instance služby je požadována prostřednictvím konstruktoru třídy, kde je služba používána a přiřazena k soukromému poli.</span><span class="sxs-lookup"><span data-stu-id="b1586-466">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="b1586-467">Pole se používá pro přístup ke službě podle potřeby v celé třídě.</span><span class="sxs-lookup"><span data-stu-id="b1586-467">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="b1586-468">V ukázkové aplikaci `IMyDependency` je instance vyžádána a používána pro volání `WriteMessage` metody služby:</span><span class="sxs-lookup"><span data-stu-id="b1586-468">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="b1586-469">Služby vložené do spuštění</span><span class="sxs-lookup"><span data-stu-id="b1586-469">Services injected into Startup</span></span>

<span data-ttu-id="b1586-470">`Startup`Při použití obecného hostitele () mohou být do konstruktoru vloženy pouze následující typy služeb <xref:Microsoft.Extensions.Hosting.IHostBuilder> :</span><span class="sxs-lookup"><span data-stu-id="b1586-470">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="b1586-471">Služby je možné vložit do `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="b1586-471">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="b1586-472">Další informace naleznete v tématu <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="b1586-472">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="b1586-473">Služby poskytované rozhraním</span><span class="sxs-lookup"><span data-stu-id="b1586-473">Framework-provided services</span></span>

<span data-ttu-id="b1586-474">`Startup.ConfigureServices`Metoda zodpovídá za definování služeb, které aplikace používá, včetně funkcí platformy, jako je například Entity Framework Core a ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="b1586-474">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="b1586-475">Zpočátku `IServiceCollection` `ConfigureServices` služba má služby definované rozhraním v závislosti na [tom, jak byl hostitel nakonfigurovaný](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="b1586-475">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="b1586-476">Pro aplikaci, která je založená na šabloně ASP.NET Core, není běžné, že mají stovky služeb zaregistrovaných v rámci rozhraní.</span><span class="sxs-lookup"><span data-stu-id="b1586-476">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="b1586-477">V následující tabulce je uvedena malá ukázka služeb registrovaných v rozhraní.</span><span class="sxs-lookup"><span data-stu-id="b1586-477">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="b1586-478">Typ služby</span><span class="sxs-lookup"><span data-stu-id="b1586-478">Service Type</span></span> | <span data-ttu-id="b1586-479">Doba platnosti</span><span class="sxs-lookup"><span data-stu-id="b1586-479">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="b1586-480">Dočasný</span><span class="sxs-lookup"><span data-stu-id="b1586-480">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="b1586-481">Singleton</span><span class="sxs-lookup"><span data-stu-id="b1586-481">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="b1586-482">Singleton</span><span class="sxs-lookup"><span data-stu-id="b1586-482">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="b1586-483">Singleton</span><span class="sxs-lookup"><span data-stu-id="b1586-483">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="b1586-484">Dočasný</span><span class="sxs-lookup"><span data-stu-id="b1586-484">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="b1586-485">Singleton</span><span class="sxs-lookup"><span data-stu-id="b1586-485">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="b1586-486">Dočasný</span><span class="sxs-lookup"><span data-stu-id="b1586-486">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="b1586-487">Singleton</span><span class="sxs-lookup"><span data-stu-id="b1586-487">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="b1586-488">Singleton</span><span class="sxs-lookup"><span data-stu-id="b1586-488">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="b1586-489">Singleton</span><span class="sxs-lookup"><span data-stu-id="b1586-489">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="b1586-490">Dočasný</span><span class="sxs-lookup"><span data-stu-id="b1586-490">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="b1586-491">Singleton</span><span class="sxs-lookup"><span data-stu-id="b1586-491">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="b1586-492">Singleton</span><span class="sxs-lookup"><span data-stu-id="b1586-492">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="b1586-493">Singleton</span><span class="sxs-lookup"><span data-stu-id="b1586-493">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="b1586-494">Registrace dalších služeb pomocí rozšiřujících metod</span><span class="sxs-lookup"><span data-stu-id="b1586-494">Register additional services with extension methods</span></span>

<span data-ttu-id="b1586-495">Pokud je k dispozici metoda rozšíření kolekce služeb pro registraci služby (a jejích závislých služeb, v případě potřeby), konvence používá jedinou `Add{SERVICE_NAME}` metodu rozšíření k registraci všech služeb vyžadovaných touto službou.</span><span class="sxs-lookup"><span data-stu-id="b1586-495">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="b1586-496">Následující kód je příkladem přidání dalších služeb do kontejneru pomocí metod rozšíření [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) a <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> :</span><span class="sxs-lookup"><span data-stu-id="b1586-496">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="b1586-497">Další informace najdete <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> ve třídě v dokumentaci k rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="b1586-497">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="b1586-498">Životnost služeb</span><span class="sxs-lookup"><span data-stu-id="b1586-498">Service lifetimes</span></span>

<span data-ttu-id="b1586-499">Vyberte odpovídající dobu života pro každou registrovanou službu.</span><span class="sxs-lookup"><span data-stu-id="b1586-499">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="b1586-500">Služby ASP.NET Core Services je možné nakonfigurovat s následujícími životnostmi:</span><span class="sxs-lookup"><span data-stu-id="b1586-500">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="b1586-501">Dočasný</span><span class="sxs-lookup"><span data-stu-id="b1586-501">Transient</span></span>

<span data-ttu-id="b1586-502">Služba přechodných životností ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*> ) se vytvoří pokaždé, když se požadují z kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="b1586-502">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="b1586-503">Tato životnost funguje nejlépe pro odlehčené bezstavové služby.</span><span class="sxs-lookup"><span data-stu-id="b1586-503">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="b1586-504">V aplikacích, které zpracovávají požadavky, jsou přechodné služby na konci žádosti zrušené.</span><span class="sxs-lookup"><span data-stu-id="b1586-504">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="b1586-505">Obor</span><span class="sxs-lookup"><span data-stu-id="b1586-505">Scoped</span></span>

<span data-ttu-id="b1586-506">Oborové služby () s rozsahem platnosti ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*> ) se vytvoří jednou za požadavek klienta (připojení).</span><span class="sxs-lookup"><span data-stu-id="b1586-506">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="b1586-507">V aplikacích, které zpracovávají požadavky, se vymezené služby na konci žádosti odstraní.</span><span class="sxs-lookup"><span data-stu-id="b1586-507">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="b1586-508">Při použití oboru služby v middlewaru založit službu do `Invoke` `InvokeAsync` metody nebo.</span><span class="sxs-lookup"><span data-stu-id="b1586-508">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="b1586-509">Nepoužívejte [vkládání pomocí injektáže konstruktoru](xref:mvc/controllers/dependency-injection#constructor-injection) , protože vynutí, aby se služba chovala jako typ singleton.</span><span class="sxs-lookup"><span data-stu-id="b1586-509">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="b1586-510">Další informace naleznete v tématu <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="b1586-510">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="b1586-511">Singleton</span><span class="sxs-lookup"><span data-stu-id="b1586-511">Singleton</span></span>

<span data-ttu-id="b1586-512">Služba singleton životnosti ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*> ) je vytvořena při prvním vyžádání (nebo při `Startup.ConfigureServices` spuštění a instance je určena pro registraci služby).</span><span class="sxs-lookup"><span data-stu-id="b1586-512">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="b1586-513">Každý další požadavek používá stejnou instanci.</span><span class="sxs-lookup"><span data-stu-id="b1586-513">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="b1586-514">Pokud aplikace vyžaduje chování singleton, doporučuje se povolit kontejneru služby spravovat dobu života služby.</span><span class="sxs-lookup"><span data-stu-id="b1586-514">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="b1586-515">Neimplementujte vzor návrhu singleton a poskytněte uživatelský kód pro správu životního cyklu objektu ve třídě.</span><span class="sxs-lookup"><span data-stu-id="b1586-515">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="b1586-516">V aplikacích, které zpracovávají požadavky, jsou nejednoznačné služby uvolněny, když <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> je uvolněna při vypnutí aplikace.</span><span class="sxs-lookup"><span data-stu-id="b1586-516">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="b1586-517">Není bezpečné přeložit oborovou službu z typu singleton.</span><span class="sxs-lookup"><span data-stu-id="b1586-517">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="b1586-518">Může dojít k tomu, že služba má při zpracování dalších požadavků špatný stav.</span><span class="sxs-lookup"><span data-stu-id="b1586-518">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="b1586-519">Metody registrace služby</span><span class="sxs-lookup"><span data-stu-id="b1586-519">Service registration methods</span></span>

<span data-ttu-id="b1586-520">Metody rozšíření registrace služby nabízejí přetížení, která jsou užitečná pro konkrétní scénáře.</span><span class="sxs-lookup"><span data-stu-id="b1586-520">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="b1586-521">Metoda</span><span class="sxs-lookup"><span data-stu-id="b1586-521">Method</span></span> | <span data-ttu-id="b1586-522">Automaticky</span><span class="sxs-lookup"><span data-stu-id="b1586-522">Automatic</span></span><br><span data-ttu-id="b1586-523">odkazy objektů</span><span class="sxs-lookup"><span data-stu-id="b1586-523">object</span></span><br><span data-ttu-id="b1586-524">odvod</span><span class="sxs-lookup"><span data-stu-id="b1586-524">disposal</span></span> | <span data-ttu-id="b1586-525">Několik</span><span class="sxs-lookup"><span data-stu-id="b1586-525">Multiple</span></span><br><span data-ttu-id="b1586-526">implementace</span><span class="sxs-lookup"><span data-stu-id="b1586-526">implementations</span></span> | <span data-ttu-id="b1586-527">Pass – argumenty</span><span class="sxs-lookup"><span data-stu-id="b1586-527">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="b1586-528">Příklad:</span><span class="sxs-lookup"><span data-stu-id="b1586-528">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="b1586-529">Ano</span><span class="sxs-lookup"><span data-stu-id="b1586-529">Yes</span></span> | <span data-ttu-id="b1586-530">Ano</span><span class="sxs-lookup"><span data-stu-id="b1586-530">Yes</span></span> | <span data-ttu-id="b1586-531">No</span><span class="sxs-lookup"><span data-stu-id="b1586-531">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="b1586-532">Příklady:</span><span class="sxs-lookup"><span data-stu-id="b1586-532">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="b1586-533">Ano</span><span class="sxs-lookup"><span data-stu-id="b1586-533">Yes</span></span> | <span data-ttu-id="b1586-534">Ano</span><span class="sxs-lookup"><span data-stu-id="b1586-534">Yes</span></span> | <span data-ttu-id="b1586-535">Ano</span><span class="sxs-lookup"><span data-stu-id="b1586-535">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="b1586-536">Příklad:</span><span class="sxs-lookup"><span data-stu-id="b1586-536">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="b1586-537">Yes</span><span class="sxs-lookup"><span data-stu-id="b1586-537">Yes</span></span> | <span data-ttu-id="b1586-538">Ne</span><span class="sxs-lookup"><span data-stu-id="b1586-538">No</span></span> | <span data-ttu-id="b1586-539">Ne</span><span class="sxs-lookup"><span data-stu-id="b1586-539">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="b1586-540">Příklady:</span><span class="sxs-lookup"><span data-stu-id="b1586-540">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="b1586-541">No</span><span class="sxs-lookup"><span data-stu-id="b1586-541">No</span></span> | <span data-ttu-id="b1586-542">Ano</span><span class="sxs-lookup"><span data-stu-id="b1586-542">Yes</span></span> | <span data-ttu-id="b1586-543">Ano</span><span class="sxs-lookup"><span data-stu-id="b1586-543">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="b1586-544">Příklady:</span><span class="sxs-lookup"><span data-stu-id="b1586-544">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="b1586-545">Ne</span><span class="sxs-lookup"><span data-stu-id="b1586-545">No</span></span> | <span data-ttu-id="b1586-546">No</span><span class="sxs-lookup"><span data-stu-id="b1586-546">No</span></span> | <span data-ttu-id="b1586-547">Yes</span><span class="sxs-lookup"><span data-stu-id="b1586-547">Yes</span></span> |

<span data-ttu-id="b1586-548">Další informace o vyřazení typů najdete v části věnované [vyřazení služeb](#disposal-of-services) .</span><span class="sxs-lookup"><span data-stu-id="b1586-548">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="b1586-549">Běžným scénářem pro více implementací je vytvoření [typů pro testování](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="b1586-549">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="b1586-550">`TryAdd{LIFETIME}`metody registrují službu jenom v případě, že už není zaregistrovaná implementace.</span><span class="sxs-lookup"><span data-stu-id="b1586-550">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="b1586-551">V následujícím příkladu se první řádek registruje `MyDependency` pro `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="b1586-551">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="b1586-552">Druhý řádek nemá žádný účinek, protože `IMyDependency` už má registrovanou implementaci:</span><span class="sxs-lookup"><span data-stu-id="b1586-552">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="b1586-553">Další informace naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="b1586-553">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="b1586-554">Metody [TryAddEnumerable (ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) registrují službu pouze v případě, že již není implementace *stejného typu*.</span><span class="sxs-lookup"><span data-stu-id="b1586-554">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="b1586-555">Několik služeb je vyřešeno prostřednictvím `IEnumerable<{SERVICE}>` .</span><span class="sxs-lookup"><span data-stu-id="b1586-555">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="b1586-556">Při registraci služeb chce vývojář přidat instanci jenom v případě, že už jeden ze stejného typu není přidaný.</span><span class="sxs-lookup"><span data-stu-id="b1586-556">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="b1586-557">Obecně je tato metoda používána autory knihovny k tomu, aby nedocházelo k registraci dvou kopií instance v kontejneru.</span><span class="sxs-lookup"><span data-stu-id="b1586-557">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="b1586-558">V následujícím příkladu se první řádek registruje `MyDep` pro `IMyDep1` .</span><span class="sxs-lookup"><span data-stu-id="b1586-558">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="b1586-559">Druhý řádek se registruje `MyDep` pro `IMyDep2` .</span><span class="sxs-lookup"><span data-stu-id="b1586-559">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="b1586-560">Třetí řádek nemá žádný vliv, protože `IMyDep1` už má registrovanou implementaci `MyDep` :</span><span class="sxs-lookup"><span data-stu-id="b1586-560">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="b1586-561">Chování injektáže konstruktoru</span><span class="sxs-lookup"><span data-stu-id="b1586-561">Constructor injection behavior</span></span>

<span data-ttu-id="b1586-562">Služby je možné vyřešit pomocí dvou mechanismů:</span><span class="sxs-lookup"><span data-stu-id="b1586-562">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="b1586-563"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Povoluje vytvoření objektu bez registrace služby v kontejneru vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="b1586-563"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="b1586-564">`ActivatorUtilities`se používá s uživateli s přístupem k uživatelům, jako jsou například rutiny značek, řadiče MVC a pořadače modelů.</span><span class="sxs-lookup"><span data-stu-id="b1586-564">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="b1586-565">Konstruktory mohou přijímat argumenty, které nejsou poskytovány vkládáním závislostí, ale argumenty musí přiřadit výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="b1586-565">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="b1586-566">Když jsou služby vyřešeny `IServiceProvider` nebo `ActivatorUtilities` , [Injektáže konstruktoru](xref:mvc/controllers/dependency-injection#constructor-injection) vyžaduje *veřejný* konstruktor.</span><span class="sxs-lookup"><span data-stu-id="b1586-566">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="b1586-567">Když jsou služby vyřešeny nástrojem `ActivatorUtilities` , [Injektáže konstruktoru](xref:mvc/controllers/dependency-injection#constructor-injection) vyžaduje, aby existoval pouze jeden příslušný konstruktor.</span><span class="sxs-lookup"><span data-stu-id="b1586-567">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="b1586-568">Přetížení konstruktoru jsou podporovaná, ale může existovat jenom jedno přetížení, jehož argumenty můžou být splněné vkládáním závislostí.</span><span class="sxs-lookup"><span data-stu-id="b1586-568">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="b1586-569">Entity Framework kontexty</span><span class="sxs-lookup"><span data-stu-id="b1586-569">Entity Framework contexts</span></span>

<span data-ttu-id="b1586-570">Entity Framework kontexty se obvykle přidávají do kontejneru služby pomocí [rozsahu životnosti](#service-lifetimes) , protože operace databáze webové aplikace jsou obvykle vymezeny na požadavky klienta.</span><span class="sxs-lookup"><span data-stu-id="b1586-570">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="b1586-571">Výchozí doba života je vymezena v případě, že při registraci kontextu databáze není zadána doba života při [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) přetížení.</span><span class="sxs-lookup"><span data-stu-id="b1586-571">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="b1586-572">Služby pro danou dobu života by neměly používat kontext databáze s kratší dobou životnosti než služba.</span><span class="sxs-lookup"><span data-stu-id="b1586-572">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="b1586-573">Možnosti života a registrace</span><span class="sxs-lookup"><span data-stu-id="b1586-573">Lifetime and registration options</span></span>

<span data-ttu-id="b1586-574">Chcete-li předvést rozdíl mezi možnostmi životního cyklu a registrací, zvažte následující rozhraní, která představují úlohy jako operaci s jedinečným identifikátorem `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="b1586-574">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="b1586-575">V závislosti na tom, jak je životnost provozní služby nakonfigurována pro následující rozhraní, kontejner poskytuje buď stejnou nebo jinou instanci služby, když je vyžádána třídou:</span><span class="sxs-lookup"><span data-stu-id="b1586-575">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="b1586-576">Rozhraní jsou implementována ve `Operation` třídě.</span><span class="sxs-lookup"><span data-stu-id="b1586-576">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="b1586-577">`Operation`Konstruktor generuje identifikátor GUID, pokud není dodán:</span><span class="sxs-lookup"><span data-stu-id="b1586-577">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="b1586-578">`OperationService`Je zaregistrován, který závisí na každém z ostatních `Operation` typů.</span><span class="sxs-lookup"><span data-stu-id="b1586-578">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="b1586-579">Když `OperationService` je požadavek proveden prostřednictvím injektáže závislosti, obdrží buď novou instanci každé služby, nebo existující instanci na základě životnosti závislé služby.</span><span class="sxs-lookup"><span data-stu-id="b1586-579">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="b1586-580">Když se v případě vyžádání z kontejneru vytvoří přechodné služby, `OperationId` liší se od `IOperationTransient` služby `OperationId` `OperationService` .</span><span class="sxs-lookup"><span data-stu-id="b1586-580">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="b1586-581">`OperationService`přijme novou instanci `IOperationTransient` třídy.</span><span class="sxs-lookup"><span data-stu-id="b1586-581">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="b1586-582">Nová instance vrací jinou instanci `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="b1586-582">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="b1586-583">Když se vytvoří vymezené služby pro jednotlivé požadavky klienta, `OperationId` `IOperationScoped` služba je stejná jako `OperationService` v rámci žádosti klienta.</span><span class="sxs-lookup"><span data-stu-id="b1586-583">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="b1586-584">V rámci požadavků klientů obě služby sdílejí jinou `OperationId` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="b1586-584">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="b1586-585">Pokud se služby instance singleton a singleton vytvoří jednou a použije se pro všechny požadavky klientů a všechny služby, `OperationId` je v rámci všech žádostí o službu konstantní.</span><span class="sxs-lookup"><span data-stu-id="b1586-585">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="b1586-586">V `Startup.ConfigureServices` je každý typ přidán do kontejneru podle jeho pojmenované životnosti:</span><span class="sxs-lookup"><span data-stu-id="b1586-586">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="b1586-587">`IOperationSingletonInstance`Služba používá konkrétní instanci se známým ID `Guid.Empty` .</span><span class="sxs-lookup"><span data-stu-id="b1586-587">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="b1586-588">Je jasné, že pokud se tento typ používá (jeho identifikátor GUID je všechny nuly).</span><span class="sxs-lookup"><span data-stu-id="b1586-588">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="b1586-589">Ukázková aplikace ukazuje dobu života objektu v rámci a mezi jednotlivými požadavky.</span><span class="sxs-lookup"><span data-stu-id="b1586-589">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="b1586-590">Ukázková aplikace `IndexModel` požaduje každý druh `IOperation` typu a `OperationService` .</span><span class="sxs-lookup"><span data-stu-id="b1586-590">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="b1586-591">Stránka potom zobrazí všechny hodnoty třídy modelu stránky a `OperationId` hodnoty služby prostřednictvím přiřazení vlastností:</span><span class="sxs-lookup"><span data-stu-id="b1586-591">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="b1586-592">Výsledkem dvou následujících výstupů jsou výsledky dvou požadavků:</span><span class="sxs-lookup"><span data-stu-id="b1586-592">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="b1586-593">**První požadavek:**</span><span class="sxs-lookup"><span data-stu-id="b1586-593">**First request:**</span></span>

<span data-ttu-id="b1586-594">Operace kontroleru:</span><span class="sxs-lookup"><span data-stu-id="b1586-594">Controller operations:</span></span>

<span data-ttu-id="b1586-595">Přechodný: d233e165-f417-469b-A866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="b1586-595">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="b1586-596">Vymezené: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="b1586-596">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="b1586-597">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="b1586-597">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="b1586-598">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="b1586-598">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="b1586-599">`OperationService`Operations</span><span class="sxs-lookup"><span data-stu-id="b1586-599">`OperationService` operations:</span></span>

<span data-ttu-id="b1586-600">Přechodný: c6b049eb-1318-4E31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="b1586-600">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="b1586-601">Vymezené: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="b1586-601">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="b1586-602">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="b1586-602">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="b1586-603">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="b1586-603">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="b1586-604">**Druhá žádost:**</span><span class="sxs-lookup"><span data-stu-id="b1586-604">**Second request:**</span></span>

<span data-ttu-id="b1586-605">Operace kontroleru:</span><span class="sxs-lookup"><span data-stu-id="b1586-605">Controller operations:</span></span>

<span data-ttu-id="b1586-606">Přechodný: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="b1586-606">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="b1586-607">Vymezené: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="b1586-607">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="b1586-608">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="b1586-608">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="b1586-609">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="b1586-609">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="b1586-610">`OperationService`Operations</span><span class="sxs-lookup"><span data-stu-id="b1586-610">`OperationService` operations:</span></span>

<span data-ttu-id="b1586-611">Přechodný: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="b1586-611">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="b1586-612">Vymezené: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="b1586-612">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="b1586-613">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="b1586-613">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="b1586-614">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="b1586-614">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="b1586-615">Pozor, které `OperationId` hodnoty se liší v rámci požadavku a mezi požadavky:</span><span class="sxs-lookup"><span data-stu-id="b1586-615">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="b1586-616">*Přechodné* objekty jsou vždy odlišné.</span><span class="sxs-lookup"><span data-stu-id="b1586-616">*Transient* objects are always different.</span></span> <span data-ttu-id="b1586-617">Přechodná `OperationId` hodnota pro první i druhý požadavek klienta se liší pro obě `OperationService` operace i mezi požadavky klientů.</span><span class="sxs-lookup"><span data-stu-id="b1586-617">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="b1586-618">Každé žádosti o službu a žádosti klienta se poskytne nová instance.</span><span class="sxs-lookup"><span data-stu-id="b1586-618">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="b1586-619">*Vymezené* objekty jsou v požadavku klienta stejné, ale v rámci požadavků klientů se liší.</span><span class="sxs-lookup"><span data-stu-id="b1586-619">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="b1586-620">Objekty *singleton* jsou stejné pro každý objekt a každý požadavek bez ohledu na to, zda `Operation` je instance uvedena v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b1586-620">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="b1586-621">Volat služby z Main</span><span class="sxs-lookup"><span data-stu-id="b1586-621">Call services from main</span></span>

<span data-ttu-id="b1586-622">Vytvořte <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> pomocí [IServiceScopeFactory. CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) pro řešení oboru služby v rámci rozsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="b1586-622">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="b1586-623">Tento přístup je užitečný pro přístup k oboruované službě při spuštění za účelem spouštění úloh inicializace.</span><span class="sxs-lookup"><span data-stu-id="b1586-623">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="b1586-624">Následující příklad ukazuje, jak získat kontext pro `MyScopedService` `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="b1586-624">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="b1586-625">Ověřování oboru</span><span class="sxs-lookup"><span data-stu-id="b1586-625">Scope validation</span></span>

<span data-ttu-id="b1586-626">Když je aplikace spuštěná ve vývojovém prostředí, provede výchozí poskytovatel služeb kontroly, aby ověřil, že:</span><span class="sxs-lookup"><span data-stu-id="b1586-626">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="b1586-627">Oborové služby nejsou přímo nebo nepřímo vyřešeny od poskytovatele kořenové služby.</span><span class="sxs-lookup"><span data-stu-id="b1586-627">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="b1586-628">Oborové služby nejsou přímo nebo nepřímo vložené do singleton.</span><span class="sxs-lookup"><span data-stu-id="b1586-628">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="b1586-629">Poskytovatel kořenové služby se vytvoří, když <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> se zavolá.</span><span class="sxs-lookup"><span data-stu-id="b1586-629">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="b1586-630">Doba života poskytovatele kořenové služby odpovídá době životnosti aplikace nebo serveru, když se poskytovatel spustí s aplikací a je uvolněný při ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="b1586-630">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="b1586-631">Oborové služby jsou uvolněny kontejnerem, který je vytvořil.</span><span class="sxs-lookup"><span data-stu-id="b1586-631">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="b1586-632">Pokud je v kořenovém kontejneru vytvořena vymezená služba, doba života služby je efektivně povýšena na typ singleton, protože je uvolněna pouze kořenovým kontejnerem při vypnutí aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="b1586-632">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="b1586-633">Při ověřování oborů služeb se tyto situace zachytí `BuildServiceProvider` .</span><span class="sxs-lookup"><span data-stu-id="b1586-633">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="b1586-634">Další informace naleznete v tématu <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="b1586-634">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="b1586-635">Žádosti o služby</span><span class="sxs-lookup"><span data-stu-id="b1586-635">Request Services</span></span>

<span data-ttu-id="b1586-636">Služby, které jsou k dispozici v rámci žádosti o ASP.NET Core, `HttpContext` jsou zpřístupněny prostřednictvím kolekce [HttpContext. RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) .</span><span class="sxs-lookup"><span data-stu-id="b1586-636">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="b1586-637">Žádosti o služby reprezentují služby nakonfigurované a požadované v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="b1586-637">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="b1586-638">Když objekty určují závislosti, jsou splněny typy nalezenými v `RequestServices` , ne `ApplicationServices` .</span><span class="sxs-lookup"><span data-stu-id="b1586-638">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="b1586-639">Obecně platí, že aplikace by tyto vlastnosti neměla používat přímo.</span><span class="sxs-lookup"><span data-stu-id="b1586-639">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="b1586-640">Místo toho požádejte o typy, které třídy vyžadují prostřednictvím konstruktorů třídy, a umožněte rozhraní vložit závislosti.</span><span class="sxs-lookup"><span data-stu-id="b1586-640">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="b1586-641">To poskytuje třídy, které jsou snáze testovány.</span><span class="sxs-lookup"><span data-stu-id="b1586-641">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="b1586-642">Preferovat požadavky na závislosti jako parametry konstruktoru pro přístup ke `RequestServices` kolekci.</span><span class="sxs-lookup"><span data-stu-id="b1586-642">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="b1586-643">Navrhnout služby pro vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="b1586-643">Design services for dependency injection</span></span>

<span data-ttu-id="b1586-644">Osvědčené postupy:</span><span class="sxs-lookup"><span data-stu-id="b1586-644">Best practices are to:</span></span>

* <span data-ttu-id="b1586-645">Navrhněte služby pro použití injektáže závislosti k získání jejich závislostí.</span><span class="sxs-lookup"><span data-stu-id="b1586-645">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="b1586-646">Vyhněte se stavovým, statickým třídám a členům.</span><span class="sxs-lookup"><span data-stu-id="b1586-646">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="b1586-647">Navrhněte aplikace, aby místo toho používaly služby s jedním prvkem, což se vyhne vytváření globálního stavu.</span><span class="sxs-lookup"><span data-stu-id="b1586-647">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="b1586-648">Vyhněte se přímému vytváření instancí závislých tříd v rámci služeb.</span><span class="sxs-lookup"><span data-stu-id="b1586-648">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="b1586-649">Přímá instance Couples kód na konkrétní implementaci.</span><span class="sxs-lookup"><span data-stu-id="b1586-649">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="b1586-650">Vytvářejte třídy aplikací malými, dobře faktoringně a snadno testované.</span><span class="sxs-lookup"><span data-stu-id="b1586-650">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="b1586-651">Pokud se zdá, že třída má příliš mnoho vložených závislostí, obvykle se jedná o znaménko, že třída má příliš mnoho zodpovědnosti a je v rozporu s [principem jediné zodpovědnosti (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="b1586-651">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="b1586-652">Pokuste se Refaktorovat třídu přesunutím některých odpovědností do nové třídy.</span><span class="sxs-lookup"><span data-stu-id="b1586-652">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="b1586-653">Mějte na paměti, že Razor stránky tříd modelu stránky a třídy KONTROLERU MVC by se měly zaměřit na uživatelské rozhraní.</span><span class="sxs-lookup"><span data-stu-id="b1586-653">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="b1586-654">Obchodní pravidla a podrobnosti implementace přístupu k datům by měly být uchovávány ve třídách, které jsou vhodné pro tyto [samostatné obavy](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="b1586-654">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="b1586-655">Vyřazení služeb</span><span class="sxs-lookup"><span data-stu-id="b1586-655">Disposal of services</span></span>

<span data-ttu-id="b1586-656">Kontejner volá <xref:System.IDisposable.Dispose*> typy, které <xref:System.IDisposable> vytvoří.</span><span class="sxs-lookup"><span data-stu-id="b1586-656">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="b1586-657">Pokud je instance přidána do kontejneru pomocí uživatelského kódu, nebude uvolněna automaticky.</span><span class="sxs-lookup"><span data-stu-id="b1586-657">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="b1586-658">V následujícím příkladu jsou služby vytvořeny pomocí kontejneru služby a automaticky odstraněny:</span><span class="sxs-lookup"><span data-stu-id="b1586-658">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="b1586-659">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="b1586-659">In the following example:</span></span>

* <span data-ttu-id="b1586-660">Instance služby nejsou vytvořeny kontejnerem služby.</span><span class="sxs-lookup"><span data-stu-id="b1586-660">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="b1586-661">Zamýšlené životnosti služeb nejsou rozhraním známy.</span><span class="sxs-lookup"><span data-stu-id="b1586-661">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="b1586-662">Rozhraní neodstraní služby automaticky.</span><span class="sxs-lookup"><span data-stu-id="b1586-662">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="b1586-663">Pokud se služby explicitně neodstraní v kódu pro vývojáře, zůstanou zachovány, dokud se aplikace neukončí.</span><span class="sxs-lookup"><span data-stu-id="b1586-663">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="b1586-664">Doprovodné materiály k rozhraní IDisposable pro přechodné a sdílené instance</span><span class="sxs-lookup"><span data-stu-id="b1586-664">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="b1586-665">Přechodná, omezená doba života</span><span class="sxs-lookup"><span data-stu-id="b1586-665">Transient, limited lifetime</span></span>

<span data-ttu-id="b1586-666">**Scénář**</span><span class="sxs-lookup"><span data-stu-id="b1586-666">**Scenario**</span></span>

<span data-ttu-id="b1586-667">Aplikace vyžaduje <xref:System.IDisposable> instanci s přechodným životností pro některý z následujících scénářů:</span><span class="sxs-lookup"><span data-stu-id="b1586-667">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="b1586-668">Instance je vyřešena v kořenovém oboru.</span><span class="sxs-lookup"><span data-stu-id="b1586-668">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="b1586-669">Instance by měla být uvolněna před ukončením oboru.</span><span class="sxs-lookup"><span data-stu-id="b1586-669">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="b1586-670">**Řešení**</span><span class="sxs-lookup"><span data-stu-id="b1586-670">**Solution**</span></span>

<span data-ttu-id="b1586-671">Pomocí modelu továrny vytvořte instanci mimo nadřazený obor.</span><span class="sxs-lookup"><span data-stu-id="b1586-671">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="b1586-672">V takové situaci by aplikace měla obvykle `Create` metodu, která volá přímo konstruktor finálního typu.</span><span class="sxs-lookup"><span data-stu-id="b1586-672">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="b1586-673">Pokud má konečný typ jiné závislosti, továrna může:</span><span class="sxs-lookup"><span data-stu-id="b1586-673">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="b1586-674">Přijmout <xref:System.IServiceProvider> ve svém konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="b1586-674">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="b1586-675">Slouží <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> k vytvoření instance instance mimo kontejner, při použití kontejneru pro jeho závislosti.</span><span class="sxs-lookup"><span data-stu-id="b1586-675">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="b1586-676">Sdílená instance, omezená doba života</span><span class="sxs-lookup"><span data-stu-id="b1586-676">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="b1586-677">**Scénář**</span><span class="sxs-lookup"><span data-stu-id="b1586-677">**Scenario**</span></span>

<span data-ttu-id="b1586-678">Aplikace vyžaduje sdílenou <xref:System.IDisposable> instanci napříč více službami, ale <xref:System.IDisposable> měla by mít omezené trvání.</span><span class="sxs-lookup"><span data-stu-id="b1586-678">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="b1586-679">**Řešení**</span><span class="sxs-lookup"><span data-stu-id="b1586-679">**Solution**</span></span>

<span data-ttu-id="b1586-680">Zaregistrujte instanci s vymezenou životností.</span><span class="sxs-lookup"><span data-stu-id="b1586-680">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="b1586-681">Použijte <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> k zahájení a vytvoření nového <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> .</span><span class="sxs-lookup"><span data-stu-id="b1586-681">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="b1586-682"><xref:System.IServiceProvider>Požadované služby získáte pomocí tohoto oboru.</span><span class="sxs-lookup"><span data-stu-id="b1586-682">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="b1586-683">Vyřadit rozsah, pokud by životnost měla končit.</span><span class="sxs-lookup"><span data-stu-id="b1586-683">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="b1586-684">Obecné pokyny</span><span class="sxs-lookup"><span data-stu-id="b1586-684">General Guidelines</span></span>

* <span data-ttu-id="b1586-685">Neregistrujte <xref:System.IDisposable> instance s přechodným oborem.</span><span class="sxs-lookup"><span data-stu-id="b1586-685">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="b1586-686">Místo toho použijte výrobní model.</span><span class="sxs-lookup"><span data-stu-id="b1586-686">Use the factory pattern instead.</span></span>
* <span data-ttu-id="b1586-687">V kořenovém oboru neřešte přechodné nebo vymezené <xref:System.IDisposable> instance.</span><span class="sxs-lookup"><span data-stu-id="b1586-687">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="b1586-688">Jedinou obecnou výjimkou je, když aplikace vytvoří nebo znovu vytvoří a odstraní <xref:System.IServiceProvider> , což není ideální vzor.</span><span class="sxs-lookup"><span data-stu-id="b1586-688">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="b1586-689">Příjem <xref:System.IDisposable> závislosti přes di nevyžaduje, aby se příjemce sám implementoval <xref:System.IDisposable> .</span><span class="sxs-lookup"><span data-stu-id="b1586-689">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="b1586-690">Příjemce <xref:System.IDisposable> závislosti by neměl volat <xref:System.IDisposable.Dispose%2A> tuto závislost.</span><span class="sxs-lookup"><span data-stu-id="b1586-690">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="b1586-691">Obory by měly sloužit k řízení životnosti služeb.</span><span class="sxs-lookup"><span data-stu-id="b1586-691">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="b1586-692">Obory nejsou hierarchické a mezi obory není žádné zvláštní připojení.</span><span class="sxs-lookup"><span data-stu-id="b1586-692">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="b1586-693">Výchozí nahrazení kontejneru služby</span><span class="sxs-lookup"><span data-stu-id="b1586-693">Default service container replacement</span></span>

<span data-ttu-id="b1586-694">Integrovaný kontejner služeb je navržený tak, aby sloužil potřebám architektury a většině spotřebitelských aplikací.</span><span class="sxs-lookup"><span data-stu-id="b1586-694">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="b1586-695">Pokud nepotřebujete určitou funkci, kterou integrovaný kontejner nepodporuje, doporučujeme použít vestavěný kontejner, například:</span><span class="sxs-lookup"><span data-stu-id="b1586-695">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="b1586-696">Vkládání vlastností</span><span class="sxs-lookup"><span data-stu-id="b1586-696">Property injection</span></span>
* <span data-ttu-id="b1586-697">Vkládání na základě názvu</span><span class="sxs-lookup"><span data-stu-id="b1586-697">Injection based on name</span></span>
* <span data-ttu-id="b1586-698">Podřízené kontejnery</span><span class="sxs-lookup"><span data-stu-id="b1586-698">Child containers</span></span>
* <span data-ttu-id="b1586-699">Vlastní Správa životního cyklu</span><span class="sxs-lookup"><span data-stu-id="b1586-699">Custom lifetime management</span></span>
* <span data-ttu-id="b1586-700">`Func<T>`Podpora opožděné inicializace</span><span class="sxs-lookup"><span data-stu-id="b1586-700">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="b1586-701">Registrace založená na konvencích</span><span class="sxs-lookup"><span data-stu-id="b1586-701">Convention-based registration</span></span>

<span data-ttu-id="b1586-702">S ASP.NET Coremi aplikacemi je možné používat následující kontejnery třetích stran:</span><span class="sxs-lookup"><span data-stu-id="b1586-702">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="b1586-703">Autofac</span><span class="sxs-lookup"><span data-stu-id="b1586-703">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="b1586-704">DryIoc</span><span class="sxs-lookup"><span data-stu-id="b1586-704">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="b1586-705">Integrit</span><span class="sxs-lookup"><span data-stu-id="b1586-705">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="b1586-706">LightInject</span><span class="sxs-lookup"><span data-stu-id="b1586-706">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="b1586-707">Lamar</span><span class="sxs-lookup"><span data-stu-id="b1586-707">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="b1586-708">Stashbox</span><span class="sxs-lookup"><span data-stu-id="b1586-708">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="b1586-709">Unity</span><span class="sxs-lookup"><span data-stu-id="b1586-709">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="b1586-710">Bezpečnost vlákna</span><span class="sxs-lookup"><span data-stu-id="b1586-710">Thread safety</span></span>

<span data-ttu-id="b1586-711">Vytvořte služby, které jsou v bezpečí pro přístup z více vláken.</span><span class="sxs-lookup"><span data-stu-id="b1586-711">Create thread-safe singleton services.</span></span> <span data-ttu-id="b1586-712">Pokud má služba typu Singleton závislost na přechodné službě, přechodná služba může také vyžadovat zabezpečení vlákna v závislosti na tom, jak je používá typ singleton.</span><span class="sxs-lookup"><span data-stu-id="b1586-712">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="b1586-713">Výrobní metoda samostatné služby, jako je například druhý argument pro [AddSingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), nemusí být bezpečná pro přístup z více vláken.</span><span class="sxs-lookup"><span data-stu-id="b1586-713">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="b1586-714">Podobně jako konstruktor typu ( `static` ), je zaručeno, že bude volán jednou vláknem.</span><span class="sxs-lookup"><span data-stu-id="b1586-714">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="b1586-715">Doporučení</span><span class="sxs-lookup"><span data-stu-id="b1586-715">Recommendations</span></span>

* <span data-ttu-id="b1586-716">`async/await``Task`řešení služeb a se nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="b1586-716">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="b1586-717">Jazyk C# nepodporuje asynchronní konstruktory; Proto je doporučeným vzorem použití asynchronních metod po synchronním překladu služby.</span><span class="sxs-lookup"><span data-stu-id="b1586-717">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="b1586-718">Neukládejte data a konfiguraci přímo do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="b1586-718">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="b1586-719">Například nákupní košík uživatele by neměl být obvykle přidán do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="b1586-719">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="b1586-720">Konfigurace by měla používat [vzor možností](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="b1586-720">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="b1586-721">Podobně nepoužívejte objekty "držitel dat", které existují pouze k povolení přístupu k jinému objektu.</span><span class="sxs-lookup"><span data-stu-id="b1586-721">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="b1586-722">Je lepší žádat o skutečnou položku přes DI.</span><span class="sxs-lookup"><span data-stu-id="b1586-722">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="b1586-723">Vyhněte se statickému přístupu ke službám (například statickému psaní [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) pro použití jinde).</span><span class="sxs-lookup"><span data-stu-id="b1586-723">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="b1586-724">Vyhněte se použití *vzoru lokátoru služby*, který kombinuje [inverzi strategií řízení](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .</span><span class="sxs-lookup"><span data-stu-id="b1586-724">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

  * <span data-ttu-id="b1586-725">Nevolejte <xref:System.IServiceProvider.GetService*> pro získání instance služby, když můžete místo toho použít di:</span><span class="sxs-lookup"><span data-stu-id="b1586-725">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="b1586-726">**Nesprávně:**</span><span class="sxs-lookup"><span data-stu-id="b1586-726">**Incorrect:**</span></span>

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

    <span data-ttu-id="b1586-727">**Správné**:</span><span class="sxs-lookup"><span data-stu-id="b1586-727">**Correct**:</span></span>

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

  * <span data-ttu-id="b1586-728">Vyhněte se vkládání továrny, která řeší závislosti za běhu pomocí <xref:System.IServiceProvider.GetService*> .</span><span class="sxs-lookup"><span data-stu-id="b1586-728">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>

* <span data-ttu-id="b1586-729">Vyhněte se statickému přístupu k `HttpContext` (například [IHttpContextAccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="b1586-729">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="b1586-730">Podobně jako u všech sad doporučení se může stát, že se vyžaduje ignorování doporučení.</span><span class="sxs-lookup"><span data-stu-id="b1586-730">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="b1586-731">Výjimky jsou zřídka, většinou zvláštní případy v rámci samotného rozhraní.</span><span class="sxs-lookup"><span data-stu-id="b1586-731">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="b1586-732">DI je *alternativou* ke vzorům statických nebo globálních přístupů k objektům.</span><span class="sxs-lookup"><span data-stu-id="b1586-732">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="b1586-733">Je možné, že nebudete moci využít výhody DI, pokud je kombinujete se statickým přístupem k objektům.</span><span class="sxs-lookup"><span data-stu-id="b1586-733">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b1586-734">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="b1586-734">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="b1586-735">Čtyři způsoby, jak vyřadit IDisposable v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b1586-735">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="b1586-736">Zápis čistého kódu v ASP.NET Core pomocí injektáže závislosti (MSDN)</span><span class="sxs-lookup"><span data-stu-id="b1586-736">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="b1586-737">Explicitní závislosti – princip</span><span class="sxs-lookup"><span data-stu-id="b1586-737">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="b1586-738">Inverze řídicích kontejnerů a vzor injektáže závislosti (Martin Fowlera)</span><span class="sxs-lookup"><span data-stu-id="b1586-738">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="b1586-739">Jak zaregistrovat službu s více rozhraními v ASP.NET Core DI</span><span class="sxs-lookup"><span data-stu-id="b1586-739">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

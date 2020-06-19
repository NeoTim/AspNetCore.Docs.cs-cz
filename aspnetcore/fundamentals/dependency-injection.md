---
title: Injektáž závislostí v ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak ASP.NET Core implementuje vkládání závislostí a jak ho používat.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/14/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/dependency-injection
ms.openlocfilehash: ddb583f69758055500ff63960f469c1cea44c77e
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2020
ms.locfileid: "85102600"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="320a6-103">Injektáž závislostí v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="320a6-103">Dependency injection in ASP.NET Core</span></span>

<span data-ttu-id="320a6-104">[Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com)a [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="320a6-104">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="320a6-105">ASP.NET Core podporuje vzor návrhu softwaru pro vkládání závislostí (DI), což je technika pro dosažení [inverze ovládacího prvku (IOC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) mezi třídami a jejich závislostmi.</span><span class="sxs-lookup"><span data-stu-id="320a6-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="320a6-106">Další informace, které jsou specifické pro vkládání závislostí v rámci řadičů MVC, najdete v tématu <xref:mvc/controllers/dependency-injection> .</span><span class="sxs-lookup"><span data-stu-id="320a6-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="320a6-107">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="320a6-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="320a6-108">Přehled injektáže závislosti</span><span class="sxs-lookup"><span data-stu-id="320a6-108">Overview of dependency injection</span></span>

<span data-ttu-id="320a6-109">*Závislost* je libovolný objekt, který vyžaduje jiný objekt.</span><span class="sxs-lookup"><span data-stu-id="320a6-109">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="320a6-110">Projděte si následující `MyDependency` třídu s `WriteMessage` metodou, na které jsou závislé jiné třídy v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="320a6-110">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="320a6-111">Instance `MyDependency` třídy může být vytvořena, aby byla `WriteMessage` Metoda k dispozici pro třídu.</span><span class="sxs-lookup"><span data-stu-id="320a6-111">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="320a6-112">`MyDependency`Třída je závislostí `IndexModel` třídy:</span><span class="sxs-lookup"><span data-stu-id="320a6-112">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="320a6-113">Třída vytvoří a přímo závisí na `MyDependency` instanci.</span><span class="sxs-lookup"><span data-stu-id="320a6-113">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="320a6-114">Závislosti kódu (například předchozí příklad) jsou problematické a je třeba se jim vyhnout z následujících důvodů:</span><span class="sxs-lookup"><span data-stu-id="320a6-114">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="320a6-115">Chcete-li nahradit `MyDependency` jinou implementací, třída musí být upravena.</span><span class="sxs-lookup"><span data-stu-id="320a6-115">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="320a6-116">Pokud `MyDependency` má závislosti, musí být nakonfigurovány třídou.</span><span class="sxs-lookup"><span data-stu-id="320a6-116">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="320a6-117">Ve velkém projektu s více třídami, v závislosti na tom `MyDependency` , je kód konfigurace rozptýlen napříč aplikací.</span><span class="sxs-lookup"><span data-stu-id="320a6-117">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="320a6-118">Tato implementace je obtížná pro testování částí.</span><span class="sxs-lookup"><span data-stu-id="320a6-118">This implementation is difficult to unit test.</span></span> <span data-ttu-id="320a6-119">Aplikace by měla používat třídu typu "nebo zástupné procedury" `MyDependency` , což není u tohoto přístupu možné.</span><span class="sxs-lookup"><span data-stu-id="320a6-119">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="320a6-120">Vkládání závislostí řeší tyto problémy prostřednictvím:</span><span class="sxs-lookup"><span data-stu-id="320a6-120">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="320a6-121">Použití rozhraní nebo základní třídy k abstrakci implementace závislosti.</span><span class="sxs-lookup"><span data-stu-id="320a6-121">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="320a6-122">Registrace závislosti v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="320a6-122">Registration of the dependency in a service container.</span></span> <span data-ttu-id="320a6-123">ASP.NET Core poskytuje integrovaný kontejner služeb <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="320a6-123">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="320a6-124">Služby jsou registrovány v metodě aplikace `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="320a6-124">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="320a6-125">*Vložení* služby do konstruktoru třídy, kde se používá.</span><span class="sxs-lookup"><span data-stu-id="320a6-125">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="320a6-126">Rozhraní přebírá zodpovědnost za vytvoření instance závislosti a její odstranění, když už ji nepotřebujete.</span><span class="sxs-lookup"><span data-stu-id="320a6-126">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="320a6-127">V [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) `IMyDependency` definuje rozhraní metodu, kterou služba poskytuje aplikaci:</span><span class="sxs-lookup"><span data-stu-id="320a6-127">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="320a6-128">Toto rozhraní je implementováno konkrétním typem `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="320a6-128">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="320a6-129">`MyDependency`vyžádá <xref:Microsoft.Extensions.Logging.ILogger`1> v jeho konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="320a6-129">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="320a6-130">Není neobvyklé používat vkládání závislostí v zřetězeném způsobem.</span><span class="sxs-lookup"><span data-stu-id="320a6-130">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="320a6-131">Každá požadovaná závislost zase vyžádá své vlastní závislosti.</span><span class="sxs-lookup"><span data-stu-id="320a6-131">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="320a6-132">Kontejner vyřeší závislosti v grafu a vrátí plně přeloženou službu.</span><span class="sxs-lookup"><span data-stu-id="320a6-132">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="320a6-133">Kolektivní sada závislostí, které je třeba vyřešit, se obvykle označuje jako *strom závislosti*, *graf závislosti*nebo *graf objektů*.</span><span class="sxs-lookup"><span data-stu-id="320a6-133">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="320a6-134">`IMyDependency`a `ILogger<TCategoryName>` musí být registrována v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="320a6-134">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="320a6-135">`IMyDependency`je zaregistrován v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="320a6-135">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="320a6-136">`ILogger<TCategoryName>`je zaregistrované v infrastruktuře abstrakce protokolování, takže se jedná o [službu](#framework-provided-services) , která je zaregistrovaná ve výchozím rozhraní.</span><span class="sxs-lookup"><span data-stu-id="320a6-136">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="320a6-137">Kontejner se překládá `ILogger<TCategoryName>` tím, že využije [(Obecné) otevřené typy](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)a eliminuje nutnost registrace každého [(obecného) konstruovaného typu](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="320a6-137">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="320a6-138">V ukázkové aplikaci `IMyDependency` je služba zaregistrovaná u konkrétního typu `MyDependency` .</span><span class="sxs-lookup"><span data-stu-id="320a6-138">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="320a6-139">Registruje rozsah platnosti služby po dobu životnosti jediného požadavku.</span><span class="sxs-lookup"><span data-stu-id="320a6-139">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="320a6-140">[Doby platnosti služeb](#service-lifetimes) jsou popsány dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="320a6-140">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="320a6-141">Jednotlivé `services.Add{SERVICE_NAME}` metody rozšíření přidávají (a případně nakonfigurují) služby.</span><span class="sxs-lookup"><span data-stu-id="320a6-141">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="320a6-142">Například `services.AddMvc()` přidá Razor stránky služeb a MVC vyžaduje.</span><span class="sxs-lookup"><span data-stu-id="320a6-142">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="320a6-143">Doporučujeme, aby aplikace dodržovaly tuto konvenci.</span><span class="sxs-lookup"><span data-stu-id="320a6-143">We recommended that apps follow this convention.</span></span> <span data-ttu-id="320a6-144">Do oboru názvů [Microsoft. Extensions. DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) umístěte metody rozšíření pro zapouzdření skupin registrací služby.</span><span class="sxs-lookup"><span data-stu-id="320a6-144">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="320a6-145">Pokud konstruktor služby vyžaduje [vestavěný typ](/dotnet/csharp/language-reference/keywords/built-in-types-table), například `string` ,, může být typ vložen pomocí [Konfigurace](xref:fundamentals/configuration/index) nebo [vzoru možností](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="320a6-145">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="320a6-146">Instance služby je požadována prostřednictvím konstruktoru třídy, kde je služba používána a přiřazena k soukromému poli.</span><span class="sxs-lookup"><span data-stu-id="320a6-146">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="320a6-147">Pole se používá pro přístup ke službě podle potřeby v celé třídě.</span><span class="sxs-lookup"><span data-stu-id="320a6-147">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="320a6-148">V ukázkové aplikaci `IMyDependency` je instance vyžádána a používána pro volání `WriteMessage` metody služby:</span><span class="sxs-lookup"><span data-stu-id="320a6-148">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="320a6-149">Služby vložené do spuštění</span><span class="sxs-lookup"><span data-stu-id="320a6-149">Services injected into Startup</span></span>

<span data-ttu-id="320a6-150">`Startup`Při použití obecného hostitele () mohou být do konstruktoru vloženy pouze následující typy služeb <xref:Microsoft.Extensions.Hosting.IHostBuilder> :</span><span class="sxs-lookup"><span data-stu-id="320a6-150">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="320a6-151">Služby je možné vložit do `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="320a6-151">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="320a6-152">Další informace naleznete v tématu <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="320a6-152">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="320a6-153">Služby poskytované rozhraním</span><span class="sxs-lookup"><span data-stu-id="320a6-153">Framework-provided services</span></span>

<span data-ttu-id="320a6-154">`Startup.ConfigureServices`Metoda zodpovídá za definování služeb, které aplikace používá, včetně funkcí platformy, jako je například Entity Framework Core a ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="320a6-154">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="320a6-155">Zpočátku `IServiceCollection` `ConfigureServices` služba má služby definované rozhraním v závislosti na [tom, jak byl hostitel nakonfigurovaný](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="320a6-155">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="320a6-156">Pro aplikaci, která je založená na šabloně ASP.NET Core, není běžné, že mají stovky služeb zaregistrovaných v rámci rozhraní.</span><span class="sxs-lookup"><span data-stu-id="320a6-156">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="320a6-157">V následující tabulce je uvedena malá ukázka služeb registrovaných v rozhraní.</span><span class="sxs-lookup"><span data-stu-id="320a6-157">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="320a6-158">Typ služby</span><span class="sxs-lookup"><span data-stu-id="320a6-158">Service Type</span></span> | <span data-ttu-id="320a6-159">Doba platnosti</span><span class="sxs-lookup"><span data-stu-id="320a6-159">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="320a6-160">Dočasný</span><span class="sxs-lookup"><span data-stu-id="320a6-160">Transient</span></span> |
| `IHostApplicationLifetime` | <span data-ttu-id="320a6-161">Singleton</span><span class="sxs-lookup"><span data-stu-id="320a6-161">Singleton</span></span> |
| `IWebHostEnvironment` | <span data-ttu-id="320a6-162">Singleton</span><span class="sxs-lookup"><span data-stu-id="320a6-162">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="320a6-163">Singleton</span><span class="sxs-lookup"><span data-stu-id="320a6-163">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="320a6-164">Dočasný</span><span class="sxs-lookup"><span data-stu-id="320a6-164">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="320a6-165">Singleton</span><span class="sxs-lookup"><span data-stu-id="320a6-165">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="320a6-166">Dočasný</span><span class="sxs-lookup"><span data-stu-id="320a6-166">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="320a6-167">Singleton</span><span class="sxs-lookup"><span data-stu-id="320a6-167">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="320a6-168">Singleton</span><span class="sxs-lookup"><span data-stu-id="320a6-168">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="320a6-169">Singleton</span><span class="sxs-lookup"><span data-stu-id="320a6-169">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="320a6-170">Dočasný</span><span class="sxs-lookup"><span data-stu-id="320a6-170">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="320a6-171">Singleton</span><span class="sxs-lookup"><span data-stu-id="320a6-171">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="320a6-172">Singleton</span><span class="sxs-lookup"><span data-stu-id="320a6-172">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="320a6-173">Singleton</span><span class="sxs-lookup"><span data-stu-id="320a6-173">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="320a6-174">Registrace dalších služeb pomocí rozšiřujících metod</span><span class="sxs-lookup"><span data-stu-id="320a6-174">Register additional services with extension methods</span></span>

<span data-ttu-id="320a6-175">Pokud je k dispozici metoda rozšíření kolekce služeb pro registraci služby (a jejích závislých služeb, v případě potřeby), konvence používá jedinou `Add{SERVICE_NAME}` metodu rozšíření k registraci všech služeb vyžadovaných touto službou.</span><span class="sxs-lookup"><span data-stu-id="320a6-175">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="320a6-176">Následující kód je příkladem přidání dalších služeb do kontejneru pomocí metod rozšíření [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) a <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> :</span><span class="sxs-lookup"><span data-stu-id="320a6-176">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="320a6-177">Další informace najdete <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> ve třídě v dokumentaci k rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="320a6-177">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="320a6-178">Životnost služeb</span><span class="sxs-lookup"><span data-stu-id="320a6-178">Service lifetimes</span></span>

<span data-ttu-id="320a6-179">Vyberte odpovídající dobu života pro každou registrovanou službu.</span><span class="sxs-lookup"><span data-stu-id="320a6-179">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="320a6-180">Služby ASP.NET Core Services je možné nakonfigurovat s následujícími životnostmi:</span><span class="sxs-lookup"><span data-stu-id="320a6-180">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="320a6-181">Dočasný</span><span class="sxs-lookup"><span data-stu-id="320a6-181">Transient</span></span>

<span data-ttu-id="320a6-182">Služba přechodných životností ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*> ) se vytvoří pokaždé, když se požadují z kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="320a6-182">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="320a6-183">Tato životnost funguje nejlépe pro odlehčené bezstavové služby.</span><span class="sxs-lookup"><span data-stu-id="320a6-183">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="320a6-184">Obor</span><span class="sxs-lookup"><span data-stu-id="320a6-184">Scoped</span></span>

<span data-ttu-id="320a6-185">Oborové služby () s rozsahem platnosti ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*> ) se vytvoří jednou za požadavek klienta (připojení).</span><span class="sxs-lookup"><span data-stu-id="320a6-185">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="320a6-186">Při použití oboru služby v middlewaru založit službu do `Invoke` `InvokeAsync` metody nebo.</span><span class="sxs-lookup"><span data-stu-id="320a6-186">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="320a6-187">Nepoužívejte [vkládání pomocí injektáže konstruktoru](xref:mvc/controllers/dependency-injection#constructor-injection) , protože vynutí, aby se služba chovala jako typ singleton.</span><span class="sxs-lookup"><span data-stu-id="320a6-187">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="320a6-188">Další informace naleznete v tématu <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="320a6-188">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="320a6-189">Singleton</span><span class="sxs-lookup"><span data-stu-id="320a6-189">Singleton</span></span>

<span data-ttu-id="320a6-190">Služba singleton životnosti ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*> ) je vytvořena při prvním vyžádání (nebo při `Startup.ConfigureServices` spuštění a instance je určena pro registraci služby).</span><span class="sxs-lookup"><span data-stu-id="320a6-190">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="320a6-191">Každý další požadavek používá stejnou instanci.</span><span class="sxs-lookup"><span data-stu-id="320a6-191">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="320a6-192">Pokud aplikace vyžaduje chování singleton, doporučuje se povolit kontejneru služby spravovat dobu života služby.</span><span class="sxs-lookup"><span data-stu-id="320a6-192">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="320a6-193">Neimplementujte vzor návrhu singleton a poskytněte uživatelský kód pro správu životního cyklu objektu ve třídě.</span><span class="sxs-lookup"><span data-stu-id="320a6-193">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="320a6-194">Není bezpečné přeložit oborovou službu z typu singleton.</span><span class="sxs-lookup"><span data-stu-id="320a6-194">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="320a6-195">Může dojít k tomu, že služba má při zpracování dalších požadavků špatný stav.</span><span class="sxs-lookup"><span data-stu-id="320a6-195">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="320a6-196">Metody registrace služby</span><span class="sxs-lookup"><span data-stu-id="320a6-196">Service registration methods</span></span>

<span data-ttu-id="320a6-197">Metody rozšíření registrace služby nabízejí přetížení, která jsou užitečná pro konkrétní scénáře.</span><span class="sxs-lookup"><span data-stu-id="320a6-197">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="320a6-198">Metoda</span><span class="sxs-lookup"><span data-stu-id="320a6-198">Method</span></span> | <span data-ttu-id="320a6-199">Automaticky</span><span class="sxs-lookup"><span data-stu-id="320a6-199">Automatic</span></span><br><span data-ttu-id="320a6-200">odkazy objektů</span><span class="sxs-lookup"><span data-stu-id="320a6-200">object</span></span><br><span data-ttu-id="320a6-201">odvod</span><span class="sxs-lookup"><span data-stu-id="320a6-201">disposal</span></span> | <span data-ttu-id="320a6-202">Několik</span><span class="sxs-lookup"><span data-stu-id="320a6-202">Multiple</span></span><br><span data-ttu-id="320a6-203">implementace</span><span class="sxs-lookup"><span data-stu-id="320a6-203">implementations</span></span> | <span data-ttu-id="320a6-204">Pass – argumenty</span><span class="sxs-lookup"><span data-stu-id="320a6-204">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="320a6-205">Příklad:</span><span class="sxs-lookup"><span data-stu-id="320a6-205">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="320a6-206">Ano</span><span class="sxs-lookup"><span data-stu-id="320a6-206">Yes</span></span> | <span data-ttu-id="320a6-207">Ano</span><span class="sxs-lookup"><span data-stu-id="320a6-207">Yes</span></span> | <span data-ttu-id="320a6-208">Ne</span><span class="sxs-lookup"><span data-stu-id="320a6-208">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="320a6-209">Příklady:</span><span class="sxs-lookup"><span data-stu-id="320a6-209">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="320a6-210">Ano</span><span class="sxs-lookup"><span data-stu-id="320a6-210">Yes</span></span> | <span data-ttu-id="320a6-211">Ano</span><span class="sxs-lookup"><span data-stu-id="320a6-211">Yes</span></span> | <span data-ttu-id="320a6-212">Ano</span><span class="sxs-lookup"><span data-stu-id="320a6-212">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="320a6-213">Příklad:</span><span class="sxs-lookup"><span data-stu-id="320a6-213">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="320a6-214">Ano</span><span class="sxs-lookup"><span data-stu-id="320a6-214">Yes</span></span> | <span data-ttu-id="320a6-215">Ne</span><span class="sxs-lookup"><span data-stu-id="320a6-215">No</span></span> | <span data-ttu-id="320a6-216">Ne</span><span class="sxs-lookup"><span data-stu-id="320a6-216">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="320a6-217">Příklady:</span><span class="sxs-lookup"><span data-stu-id="320a6-217">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="320a6-218">No</span><span class="sxs-lookup"><span data-stu-id="320a6-218">No</span></span> | <span data-ttu-id="320a6-219">Ano</span><span class="sxs-lookup"><span data-stu-id="320a6-219">Yes</span></span> | <span data-ttu-id="320a6-220">Ano</span><span class="sxs-lookup"><span data-stu-id="320a6-220">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="320a6-221">Příklady:</span><span class="sxs-lookup"><span data-stu-id="320a6-221">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="320a6-222">Ne</span><span class="sxs-lookup"><span data-stu-id="320a6-222">No</span></span> | <span data-ttu-id="320a6-223">Ne</span><span class="sxs-lookup"><span data-stu-id="320a6-223">No</span></span> | <span data-ttu-id="320a6-224">Ano</span><span class="sxs-lookup"><span data-stu-id="320a6-224">Yes</span></span> |

<span data-ttu-id="320a6-225">Další informace o vyřazení typů najdete v části věnované [vyřazení služeb](#disposal-of-services) .</span><span class="sxs-lookup"><span data-stu-id="320a6-225">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="320a6-226">Běžným scénářem pro více implementací je vytvoření [typů pro testování](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="320a6-226">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="320a6-227">`TryAdd{LIFETIME}`metody registrují službu jenom v případě, že už není zaregistrovaná implementace.</span><span class="sxs-lookup"><span data-stu-id="320a6-227">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="320a6-228">V následujícím příkladu se první řádek registruje `MyDependency` pro `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="320a6-228">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="320a6-229">Druhý řádek nemá žádný účinek, protože `IMyDependency` už má registrovanou implementaci:</span><span class="sxs-lookup"><span data-stu-id="320a6-229">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="320a6-230">Další informace naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="320a6-230">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="320a6-231">Metody [TryAddEnumerable (ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) registrují službu pouze v případě, že již není implementace *stejného typu*.</span><span class="sxs-lookup"><span data-stu-id="320a6-231">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="320a6-232">Několik služeb je vyřešeno prostřednictvím `IEnumerable<{SERVICE}>` .</span><span class="sxs-lookup"><span data-stu-id="320a6-232">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="320a6-233">Při registraci služeb chce vývojář přidat instanci jenom v případě, že už jeden ze stejného typu není přidaný.</span><span class="sxs-lookup"><span data-stu-id="320a6-233">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="320a6-234">Obecně je tato metoda používána autory knihovny k tomu, aby nedocházelo k registraci dvou kopií instance v kontejneru.</span><span class="sxs-lookup"><span data-stu-id="320a6-234">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="320a6-235">V následujícím příkladu se první řádek registruje `MyDep` pro `IMyDep1` .</span><span class="sxs-lookup"><span data-stu-id="320a6-235">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="320a6-236">Druhý řádek se registruje `MyDep` pro `IMyDep2` .</span><span class="sxs-lookup"><span data-stu-id="320a6-236">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="320a6-237">Třetí řádek nemá žádný vliv, protože `IMyDep1` už má registrovanou implementaci `MyDep` :</span><span class="sxs-lookup"><span data-stu-id="320a6-237">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="320a6-238">Chování injektáže konstruktoru</span><span class="sxs-lookup"><span data-stu-id="320a6-238">Constructor injection behavior</span></span>

<span data-ttu-id="320a6-239">Služby je možné vyřešit pomocí dvou mechanismů:</span><span class="sxs-lookup"><span data-stu-id="320a6-239">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="320a6-240"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Povoluje vytvoření objektu bez registrace služby v kontejneru vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="320a6-240"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="320a6-241">`ActivatorUtilities`se používá s uživateli s přístupem k uživatelům, jako jsou například rutiny značek, řadiče MVC a pořadače modelů.</span><span class="sxs-lookup"><span data-stu-id="320a6-241">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="320a6-242">Konstruktory mohou přijímat argumenty, které nejsou poskytovány vkládáním závislostí, ale argumenty musí přiřadit výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="320a6-242">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="320a6-243">Když jsou služby vyřešeny `IServiceProvider` nebo `ActivatorUtilities` , [Injektáže konstruktoru](xref:mvc/controllers/dependency-injection#constructor-injection) vyžaduje *veřejný* konstruktor.</span><span class="sxs-lookup"><span data-stu-id="320a6-243">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="320a6-244">Když jsou služby vyřešeny nástrojem `ActivatorUtilities` , [Injektáže konstruktoru](xref:mvc/controllers/dependency-injection#constructor-injection) vyžaduje, aby existoval pouze jeden příslušný konstruktor.</span><span class="sxs-lookup"><span data-stu-id="320a6-244">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="320a6-245">Přetížení konstruktoru jsou podporovaná, ale může existovat jenom jedno přetížení, jehož argumenty můžou být splněné vkládáním závislostí.</span><span class="sxs-lookup"><span data-stu-id="320a6-245">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="320a6-246">Entity Framework kontexty</span><span class="sxs-lookup"><span data-stu-id="320a6-246">Entity Framework contexts</span></span>

<span data-ttu-id="320a6-247">Entity Framework kontexty se obvykle přidávají do kontejneru služby pomocí [rozsahu životnosti](#service-lifetimes) , protože operace databáze webové aplikace jsou obvykle vymezeny na požadavky klienta.</span><span class="sxs-lookup"><span data-stu-id="320a6-247">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="320a6-248">Výchozí doba života je vymezena v případě, že při registraci kontextu databáze není zadána doba života při [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) přetížení.</span><span class="sxs-lookup"><span data-stu-id="320a6-248">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="320a6-249">Služby pro danou dobu života by neměly používat kontext databáze s kratší dobou životnosti než služba.</span><span class="sxs-lookup"><span data-stu-id="320a6-249">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="320a6-250">Možnosti života a registrace</span><span class="sxs-lookup"><span data-stu-id="320a6-250">Lifetime and registration options</span></span>

<span data-ttu-id="320a6-251">Chcete-li předvést rozdíl mezi možnostmi životního cyklu a registrací, zvažte následující rozhraní, která představují úlohy jako operaci s jedinečným identifikátorem `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="320a6-251">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="320a6-252">V závislosti na tom, jak je životnost provozní služby nakonfigurována pro následující rozhraní, kontejner poskytuje buď stejnou nebo jinou instanci služby, když je vyžádána třídou:</span><span class="sxs-lookup"><span data-stu-id="320a6-252">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="320a6-253">Rozhraní jsou implementována ve `Operation` třídě.</span><span class="sxs-lookup"><span data-stu-id="320a6-253">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="320a6-254">`Operation`Konstruktor generuje identifikátor GUID, pokud není dodán:</span><span class="sxs-lookup"><span data-stu-id="320a6-254">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="320a6-255">`OperationService`Je zaregistrován, který závisí na každém z ostatních `Operation` typů.</span><span class="sxs-lookup"><span data-stu-id="320a6-255">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="320a6-256">Když `OperationService` je požadavek proveden prostřednictvím injektáže závislosti, obdrží buď novou instanci každé služby, nebo existující instanci na základě životnosti závislé služby.</span><span class="sxs-lookup"><span data-stu-id="320a6-256">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="320a6-257">Když se v případě vyžádání z kontejneru vytvoří přechodné služby, `OperationId` liší se od `IOperationTransient` služby `OperationId` `OperationService` .</span><span class="sxs-lookup"><span data-stu-id="320a6-257">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="320a6-258">`OperationService`přijme novou instanci `IOperationTransient` třídy.</span><span class="sxs-lookup"><span data-stu-id="320a6-258">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="320a6-259">Nová instance vrací jinou instanci `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="320a6-259">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="320a6-260">Když se vytvoří vymezené služby pro jednotlivé požadavky klienta, `OperationId` `IOperationScoped` služba je stejná jako `OperationService` v rámci žádosti klienta.</span><span class="sxs-lookup"><span data-stu-id="320a6-260">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="320a6-261">V rámci požadavků klientů obě služby sdílejí jinou `OperationId` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="320a6-261">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="320a6-262">Pokud se služby instance singleton a singleton vytvoří jednou a použije se pro všechny požadavky klientů a všechny služby, `OperationId` je v rámci všech žádostí o službu konstantní.</span><span class="sxs-lookup"><span data-stu-id="320a6-262">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="320a6-263">V `Startup.ConfigureServices` je každý typ přidán do kontejneru podle jeho pojmenované životnosti:</span><span class="sxs-lookup"><span data-stu-id="320a6-263">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="320a6-264">`IOperationSingletonInstance`Služba používá konkrétní instanci se známým ID `Guid.Empty` .</span><span class="sxs-lookup"><span data-stu-id="320a6-264">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="320a6-265">Je jasné, že pokud se tento typ používá (jeho identifikátor GUID je všechny nuly).</span><span class="sxs-lookup"><span data-stu-id="320a6-265">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="320a6-266">Ukázková aplikace ukazuje dobu života objektu v rámci a mezi jednotlivými požadavky.</span><span class="sxs-lookup"><span data-stu-id="320a6-266">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="320a6-267">Ukázková aplikace `IndexModel` požaduje každý druh `IOperation` typu a `OperationService` .</span><span class="sxs-lookup"><span data-stu-id="320a6-267">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="320a6-268">Stránka potom zobrazí všechny hodnoty třídy modelu stránky a `OperationId` hodnoty služby prostřednictvím přiřazení vlastností:</span><span class="sxs-lookup"><span data-stu-id="320a6-268">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="320a6-269">Výsledkem dvou následujících výstupů jsou výsledky dvou požadavků:</span><span class="sxs-lookup"><span data-stu-id="320a6-269">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="320a6-270">**První požadavek:**</span><span class="sxs-lookup"><span data-stu-id="320a6-270">**First request:**</span></span>

<span data-ttu-id="320a6-271">Operace kontroleru:</span><span class="sxs-lookup"><span data-stu-id="320a6-271">Controller operations:</span></span>

<span data-ttu-id="320a6-272">Přechodný: d233e165-f417-469b-A866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="320a6-272">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="320a6-273">Vymezené: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="320a6-273">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="320a6-274">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="320a6-274">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="320a6-275">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="320a6-275">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="320a6-276">`OperationService`Operations</span><span class="sxs-lookup"><span data-stu-id="320a6-276">`OperationService` operations:</span></span>

<span data-ttu-id="320a6-277">Přechodný: c6b049eb-1318-4E31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="320a6-277">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="320a6-278">Vymezené: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="320a6-278">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="320a6-279">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="320a6-279">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="320a6-280">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="320a6-280">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="320a6-281">**Druhá žádost:**</span><span class="sxs-lookup"><span data-stu-id="320a6-281">**Second request:**</span></span>

<span data-ttu-id="320a6-282">Operace kontroleru:</span><span class="sxs-lookup"><span data-stu-id="320a6-282">Controller operations:</span></span>

<span data-ttu-id="320a6-283">Přechodný: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="320a6-283">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="320a6-284">Vymezené: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="320a6-284">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="320a6-285">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="320a6-285">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="320a6-286">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="320a6-286">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="320a6-287">`OperationService`Operations</span><span class="sxs-lookup"><span data-stu-id="320a6-287">`OperationService` operations:</span></span>

<span data-ttu-id="320a6-288">Přechodný: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="320a6-288">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="320a6-289">Vymezené: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="320a6-289">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="320a6-290">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="320a6-290">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="320a6-291">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="320a6-291">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="320a6-292">Pozor, které `OperationId` hodnoty se liší v rámci požadavku a mezi požadavky:</span><span class="sxs-lookup"><span data-stu-id="320a6-292">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="320a6-293">*Přechodné* objekty jsou vždy odlišné.</span><span class="sxs-lookup"><span data-stu-id="320a6-293">*Transient* objects are always different.</span></span> <span data-ttu-id="320a6-294">Přechodná `OperationId` hodnota pro první i druhý požadavek klienta se liší pro obě `OperationService` operace i mezi požadavky klientů.</span><span class="sxs-lookup"><span data-stu-id="320a6-294">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="320a6-295">Každé žádosti o službu a žádosti klienta se poskytne nová instance.</span><span class="sxs-lookup"><span data-stu-id="320a6-295">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="320a6-296">*Vymezené* objekty jsou v požadavku klienta stejné, ale v rámci požadavků klientů se liší.</span><span class="sxs-lookup"><span data-stu-id="320a6-296">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="320a6-297">Objekty *singleton* jsou stejné pro každý objekt a každý požadavek bez ohledu na to, zda `Operation` je instance uvedena v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="320a6-297">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="320a6-298">Volat služby z Main</span><span class="sxs-lookup"><span data-stu-id="320a6-298">Call services from main</span></span>

<span data-ttu-id="320a6-299">Vytvořte <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> pomocí [IServiceScopeFactory. CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) pro řešení oboru služby v rámci rozsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="320a6-299">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="320a6-300">Tento přístup je užitečný pro přístup k oboruované službě při spuštění za účelem spouštění úloh inicializace.</span><span class="sxs-lookup"><span data-stu-id="320a6-300">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="320a6-301">Následující příklad ukazuje, jak získat kontext pro `MyScopedService` `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="320a6-301">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="320a6-302">Ověřování oboru</span><span class="sxs-lookup"><span data-stu-id="320a6-302">Scope validation</span></span>

<span data-ttu-id="320a6-303">Když je aplikace spuštěná ve vývojovém prostředí a volá [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) , aby se vytvořila hostitel, použije výchozí poskytovatel služeb kontroly, aby ověřil, že:</span><span class="sxs-lookup"><span data-stu-id="320a6-303">When the app is running in the Development environment and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="320a6-304">Oborové služby nejsou přímo nebo nepřímo vyřešeny od poskytovatele kořenové služby.</span><span class="sxs-lookup"><span data-stu-id="320a6-304">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="320a6-305">Oborové služby nejsou přímo nebo nepřímo vložené do singleton.</span><span class="sxs-lookup"><span data-stu-id="320a6-305">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="320a6-306">Poskytovatel kořenové služby se vytvoří, když <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> se zavolá.</span><span class="sxs-lookup"><span data-stu-id="320a6-306">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="320a6-307">Doba života poskytovatele kořenové služby odpovídá době životnosti aplikace nebo serveru, když se poskytovatel spustí s aplikací a je uvolněný při ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="320a6-307">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="320a6-308">Oborové služby jsou uvolněny kontejnerem, který je vytvořil.</span><span class="sxs-lookup"><span data-stu-id="320a6-308">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="320a6-309">Pokud je v kořenovém kontejneru vytvořena vymezená služba, doba života služby je efektivně povýšena na typ singleton, protože je uvolněna pouze kořenovým kontejnerem při vypnutí aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="320a6-309">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="320a6-310">Při ověřování oborů služeb se tyto situace zachytí `BuildServiceProvider` .</span><span class="sxs-lookup"><span data-stu-id="320a6-310">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="320a6-311">Další informace naleznete v tématu <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="320a6-311">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="320a6-312">Žádosti o služby</span><span class="sxs-lookup"><span data-stu-id="320a6-312">Request Services</span></span>

<span data-ttu-id="320a6-313">Služby, které jsou k dispozici v rámci žádosti o ASP.NET Core, `HttpContext` jsou zpřístupněny prostřednictvím kolekce [HttpContext. RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) .</span><span class="sxs-lookup"><span data-stu-id="320a6-313">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="320a6-314">Žádosti o služby reprezentují služby nakonfigurované a požadované v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="320a6-314">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="320a6-315">Když objekty určují závislosti, jsou splněny typy nalezenými v `RequestServices` , ne `ApplicationServices` .</span><span class="sxs-lookup"><span data-stu-id="320a6-315">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="320a6-316">Obecně platí, že aplikace by tyto vlastnosti neměla používat přímo.</span><span class="sxs-lookup"><span data-stu-id="320a6-316">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="320a6-317">Místo toho požádejte o typy, které třídy vyžadují prostřednictvím konstruktorů třídy, a umožněte rozhraní vkládat závislosti.</span><span class="sxs-lookup"><span data-stu-id="320a6-317">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span> <span data-ttu-id="320a6-318">To poskytuje třídy, které jsou snáze testovány.</span><span class="sxs-lookup"><span data-stu-id="320a6-318">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="320a6-319">Preferovat požadavky na závislosti jako parametry konstruktoru pro přístup ke `RequestServices` kolekci.</span><span class="sxs-lookup"><span data-stu-id="320a6-319">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="320a6-320">Navrhnout služby pro vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="320a6-320">Design services for dependency injection</span></span>

<span data-ttu-id="320a6-321">Osvědčené postupy:</span><span class="sxs-lookup"><span data-stu-id="320a6-321">Best practices are to:</span></span>

* <span data-ttu-id="320a6-322">Navrhněte služby pro použití injektáže závislosti k získání jejich závislostí.</span><span class="sxs-lookup"><span data-stu-id="320a6-322">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="320a6-323">Vyhněte se stavovým, statickým třídám a členům.</span><span class="sxs-lookup"><span data-stu-id="320a6-323">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="320a6-324">Navrhněte aplikace, aby místo toho používaly služby s jedním prvkem, což se vyhne vytváření globálního stavu.</span><span class="sxs-lookup"><span data-stu-id="320a6-324">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="320a6-325">Vyhněte se přímému vytváření instancí závislých tříd v rámci služeb.</span><span class="sxs-lookup"><span data-stu-id="320a6-325">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="320a6-326">Přímá instance Couples kód na konkrétní implementaci.</span><span class="sxs-lookup"><span data-stu-id="320a6-326">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="320a6-327">Vytvářejte třídy aplikací malými, dobře faktoringně a snadno testované.</span><span class="sxs-lookup"><span data-stu-id="320a6-327">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="320a6-328">Pokud se zdá, že třída má příliš mnoho vložených závislostí, obvykle se jedná o znaménko, že třída má příliš mnoho zodpovědnosti a je v rozporu s [principem jediné zodpovědnosti (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="320a6-328">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="320a6-329">Pokuste se Refaktorovat třídu přesunutím některých odpovědností do nové třídy.</span><span class="sxs-lookup"><span data-stu-id="320a6-329">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="320a6-330">Mějte na paměti, že Razor stránky tříd modelu stránky a třídy KONTROLERU MVC by se měly zaměřit na uživatelské rozhraní.</span><span class="sxs-lookup"><span data-stu-id="320a6-330">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="320a6-331">Obchodní pravidla a podrobnosti implementace přístupu k datům by měly být uchovávány ve třídách, které jsou vhodné pro tyto [samostatné obavy](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="320a6-331">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="320a6-332">Vyřazení služeb</span><span class="sxs-lookup"><span data-stu-id="320a6-332">Disposal of services</span></span>

<span data-ttu-id="320a6-333">Kontejner volá <xref:System.IDisposable.Dispose*> typy, které <xref:System.IDisposable> vytvoří.</span><span class="sxs-lookup"><span data-stu-id="320a6-333">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="320a6-334">Pokud je instance přidána do kontejneru pomocí uživatelského kódu, nebude uvolněna automaticky.</span><span class="sxs-lookup"><span data-stu-id="320a6-334">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="320a6-335">V následujícím příkladu jsou služby vytvořeny pomocí kontejneru služby a automaticky odstraněny:</span><span class="sxs-lookup"><span data-stu-id="320a6-335">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="320a6-336">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="320a6-336">In the following example:</span></span>

* <span data-ttu-id="320a6-337">Instance služby nejsou vytvořeny kontejnerem služby.</span><span class="sxs-lookup"><span data-stu-id="320a6-337">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="320a6-338">Zamýšlené životnosti služeb nejsou rozhraním známy.</span><span class="sxs-lookup"><span data-stu-id="320a6-338">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="320a6-339">Rozhraní neodstraní služby automaticky.</span><span class="sxs-lookup"><span data-stu-id="320a6-339">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="320a6-340">Pokud se služby explicitně neodstraní v kódu pro vývojáře, zůstanou zachovány, dokud se aplikace neukončí.</span><span class="sxs-lookup"><span data-stu-id="320a6-340">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="320a6-341">Doprovodné materiály k rozhraní IDisposable pro přechodné a sdílené instance</span><span class="sxs-lookup"><span data-stu-id="320a6-341">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="320a6-342">Přechodná, omezená doba života</span><span class="sxs-lookup"><span data-stu-id="320a6-342">Transient, limited lifetime</span></span>

<span data-ttu-id="320a6-343">**Scénář**</span><span class="sxs-lookup"><span data-stu-id="320a6-343">**Scenario**</span></span>

<span data-ttu-id="320a6-344">Aplikace vyžaduje <xref:System.IDisposable> instanci s přechodným životností pro některý z následujících scénářů:</span><span class="sxs-lookup"><span data-stu-id="320a6-344">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="320a6-345">Instance je vyřešena v kořenovém oboru.</span><span class="sxs-lookup"><span data-stu-id="320a6-345">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="320a6-346">Instance by měla být uvolněna před ukončením oboru.</span><span class="sxs-lookup"><span data-stu-id="320a6-346">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="320a6-347">**Řešení**</span><span class="sxs-lookup"><span data-stu-id="320a6-347">**Solution**</span></span>

<span data-ttu-id="320a6-348">Pomocí modelu továrny vytvořte instanci mimo nadřazený obor.</span><span class="sxs-lookup"><span data-stu-id="320a6-348">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="320a6-349">V takové situaci by aplikace měla obvykle `Create` metodu, která volá přímo konstruktor finálního typu.</span><span class="sxs-lookup"><span data-stu-id="320a6-349">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="320a6-350">Pokud má konečný typ jiné závislosti, továrna může:</span><span class="sxs-lookup"><span data-stu-id="320a6-350">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="320a6-351">Přijmout <xref:System.IServiceProvider> ve svém konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="320a6-351">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="320a6-352">Slouží <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> k vytvoření instance instance mimo kontejner, při použití kontejneru pro jeho závislosti.</span><span class="sxs-lookup"><span data-stu-id="320a6-352">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="320a6-353">Sdílená instance, omezená doba života</span><span class="sxs-lookup"><span data-stu-id="320a6-353">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="320a6-354">**Scénář**</span><span class="sxs-lookup"><span data-stu-id="320a6-354">**Scenario**</span></span>

<span data-ttu-id="320a6-355">Aplikace vyžaduje sdílenou <xref:System.IDisposable> instanci napříč více službami, ale <xref:System.IDisposable> měla by mít omezené trvání.</span><span class="sxs-lookup"><span data-stu-id="320a6-355">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="320a6-356">**Řešení**</span><span class="sxs-lookup"><span data-stu-id="320a6-356">**Solution**</span></span>

<span data-ttu-id="320a6-357">Zaregistrujte instanci s vymezenou životností.</span><span class="sxs-lookup"><span data-stu-id="320a6-357">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="320a6-358">Použijte <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> k zahájení a vytvoření nového <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> .</span><span class="sxs-lookup"><span data-stu-id="320a6-358">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="320a6-359"><xref:System.IServiceProvider>Požadované služby získáte pomocí tohoto oboru.</span><span class="sxs-lookup"><span data-stu-id="320a6-359">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="320a6-360">Vyřadit rozsah, pokud by životnost měla končit.</span><span class="sxs-lookup"><span data-stu-id="320a6-360">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="320a6-361">Obecné pokyny</span><span class="sxs-lookup"><span data-stu-id="320a6-361">General Guidelines</span></span>

* <span data-ttu-id="320a6-362">Neregistrujte <xref:System.IDisposable> instance s přechodným oborem.</span><span class="sxs-lookup"><span data-stu-id="320a6-362">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="320a6-363">Místo toho použijte výrobní model.</span><span class="sxs-lookup"><span data-stu-id="320a6-363">Use the factory pattern instead.</span></span>
* <span data-ttu-id="320a6-364">V kořenovém oboru neřešte přechodné nebo vymezené <xref:System.IDisposable> instance.</span><span class="sxs-lookup"><span data-stu-id="320a6-364">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="320a6-365">Jedinou obecnou výjimkou je, když aplikace vytvoří nebo znovu vytvoří a odstraní <xref:System.IServiceProvider> , což není ideální vzor.</span><span class="sxs-lookup"><span data-stu-id="320a6-365">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="320a6-366">Příjem <xref:System.IDisposable> závislosti přes di nevyžaduje, aby se příjemce sám implementoval <xref:System.IDisposable> .</span><span class="sxs-lookup"><span data-stu-id="320a6-366">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="320a6-367">Příjemce <xref:System.IDisposable> závislosti by neměl volat <xref:System.IDisposable.Dispose%2A> tuto závislost.</span><span class="sxs-lookup"><span data-stu-id="320a6-367">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="320a6-368">Obory by měly sloužit k řízení životnosti služeb.</span><span class="sxs-lookup"><span data-stu-id="320a6-368">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="320a6-369">Obory nejsou hierarchické a mezi obory není žádné zvláštní připojení.</span><span class="sxs-lookup"><span data-stu-id="320a6-369">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="320a6-370">Výchozí nahrazení kontejneru služby</span><span class="sxs-lookup"><span data-stu-id="320a6-370">Default service container replacement</span></span>

<span data-ttu-id="320a6-371">Integrovaný kontejner služeb je navržený tak, aby sloužil potřebám architektury a většině spotřebitelských aplikací.</span><span class="sxs-lookup"><span data-stu-id="320a6-371">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="320a6-372">Pokud nepotřebujete určitou funkci, kterou integrovaný kontejner nepodporuje, doporučujeme použít vestavěný kontejner, například:</span><span class="sxs-lookup"><span data-stu-id="320a6-372">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="320a6-373">Vkládání vlastností</span><span class="sxs-lookup"><span data-stu-id="320a6-373">Property injection</span></span>
* <span data-ttu-id="320a6-374">Vkládání na základě názvu</span><span class="sxs-lookup"><span data-stu-id="320a6-374">Injection based on name</span></span>
* <span data-ttu-id="320a6-375">Podřízené kontejnery</span><span class="sxs-lookup"><span data-stu-id="320a6-375">Child containers</span></span>
* <span data-ttu-id="320a6-376">Vlastní Správa životního cyklu</span><span class="sxs-lookup"><span data-stu-id="320a6-376">Custom lifetime management</span></span>
* <span data-ttu-id="320a6-377">`Func<T>`Podpora opožděné inicializace</span><span class="sxs-lookup"><span data-stu-id="320a6-377">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="320a6-378">Registrace založená na konvencích</span><span class="sxs-lookup"><span data-stu-id="320a6-378">Convention-based registration</span></span>

<span data-ttu-id="320a6-379">S ASP.NET Coremi aplikacemi je možné používat následující kontejnery třetích stran:</span><span class="sxs-lookup"><span data-stu-id="320a6-379">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="320a6-380">Autofac</span><span class="sxs-lookup"><span data-stu-id="320a6-380">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="320a6-381">DryIoc</span><span class="sxs-lookup"><span data-stu-id="320a6-381">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="320a6-382">Integrit</span><span class="sxs-lookup"><span data-stu-id="320a6-382">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="320a6-383">LightInject</span><span class="sxs-lookup"><span data-stu-id="320a6-383">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="320a6-384">Lamar</span><span class="sxs-lookup"><span data-stu-id="320a6-384">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="320a6-385">Stashbox</span><span class="sxs-lookup"><span data-stu-id="320a6-385">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="320a6-386">Unity</span><span class="sxs-lookup"><span data-stu-id="320a6-386">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="320a6-387">Bezpečnost vlákna</span><span class="sxs-lookup"><span data-stu-id="320a6-387">Thread safety</span></span>

<span data-ttu-id="320a6-388">Vytvořte služby, které jsou v bezpečí pro přístup z více vláken.</span><span class="sxs-lookup"><span data-stu-id="320a6-388">Create thread-safe singleton services.</span></span> <span data-ttu-id="320a6-389">Pokud má služba typu Singleton závislost na přechodné službě, přechodná služba může také vyžadovat zabezpečení vlákna v závislosti na tom, jak je používá typ singleton.</span><span class="sxs-lookup"><span data-stu-id="320a6-389">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="320a6-390">Výrobní metoda samostatné služby, jako je například druhý argument pro [AddSingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), nemusí být bezpečná pro přístup z více vláken.</span><span class="sxs-lookup"><span data-stu-id="320a6-390">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="320a6-391">Podobně jako konstruktor typu ( `static` ), je zaručeno, že bude volán jednou vláknem.</span><span class="sxs-lookup"><span data-stu-id="320a6-391">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="320a6-392">Doporučení</span><span class="sxs-lookup"><span data-stu-id="320a6-392">Recommendations</span></span>

* <span data-ttu-id="320a6-393">`async/await``Task`řešení služeb a se nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="320a6-393">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="320a6-394">Jazyk C# nepodporuje asynchronní konstruktory; Proto je doporučeným vzorem použití asynchronních metod po synchronním překladu služby.</span><span class="sxs-lookup"><span data-stu-id="320a6-394">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="320a6-395">Neukládejte data a konfiguraci přímo do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="320a6-395">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="320a6-396">Například nákupní košík uživatele by neměl být obvykle přidán do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="320a6-396">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="320a6-397">Konfigurace by měla používat [vzor možností](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="320a6-397">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="320a6-398">Podobně nepoužívejte objekty "držitel dat", které existují pouze k povolení přístupu k jinému objektu.</span><span class="sxs-lookup"><span data-stu-id="320a6-398">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="320a6-399">Je lepší žádat o skutečnou položku přes DI.</span><span class="sxs-lookup"><span data-stu-id="320a6-399">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="320a6-400">Vyhněte se statickému přístupu ke službám (například statickému psaní [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) pro použití jinde).</span><span class="sxs-lookup"><span data-stu-id="320a6-400">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="320a6-401">Vyhněte se použití *vzoru lokátoru služby*.</span><span class="sxs-lookup"><span data-stu-id="320a6-401">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="320a6-402">Například Nevolejte <xref:System.IServiceProvider.GetService*> pro získání instance služby, když můžete místo toho použít di:</span><span class="sxs-lookup"><span data-stu-id="320a6-402">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="320a6-403">**Špatný**</span><span class="sxs-lookup"><span data-stu-id="320a6-403">**Incorrect:**</span></span>

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

  <span data-ttu-id="320a6-404">**Správné**:</span><span class="sxs-lookup"><span data-stu-id="320a6-404">**Correct**:</span></span>

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

* <span data-ttu-id="320a6-405">Další změnou lokátoru služby, aby se zabránilo, je vložení továrny, která vyřeší závislosti za běhu.</span><span class="sxs-lookup"><span data-stu-id="320a6-405">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="320a6-406">Oba tyto postupy kombinují [inverze strategií řízení](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .</span><span class="sxs-lookup"><span data-stu-id="320a6-406">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

* <span data-ttu-id="320a6-407">Vyhněte se statickému přístupu k `HttpContext` (například [IHttpContextAccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="320a6-407">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="320a6-408">Podobně jako u všech sad doporučení se může stát, že se vyžaduje ignorování doporučení.</span><span class="sxs-lookup"><span data-stu-id="320a6-408">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="320a6-409">Výjimky jsou zřídka, většinou zvláštní případy v rámci samotného rozhraní.</span><span class="sxs-lookup"><span data-stu-id="320a6-409">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="320a6-410">DI je *alternativou* ke vzorům statických nebo globálních přístupů k objektům.</span><span class="sxs-lookup"><span data-stu-id="320a6-410">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="320a6-411">Je možné, že nebudete moci využít výhody DI, pokud je kombinujete se statickým přístupem k objektům.</span><span class="sxs-lookup"><span data-stu-id="320a6-411">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="320a6-412">Doporučené vzory pro víceklientské architektury v DI</span><span class="sxs-lookup"><span data-stu-id="320a6-412">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="320a6-413">[Sadu jader](https://github.com/OrchardCMS/OrchardCore) nabízí víceklientské architektury.</span><span class="sxs-lookup"><span data-stu-id="320a6-413">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) provides multi-tenancy.</span></span> <span data-ttu-id="320a6-414">Další informace najdete v dokumentaci k sadě [sad](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="320a6-414">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="320a6-415">V ukázkových aplikacích v tématu najdete https://github.com/OrchardCMS/OrchardCore.Samples příklady vytváření modulárních a víceklientské aplikací s využitím jenom sady Core Core Framework bez jakýchkoli funkcí specifických pro CMS.</span><span class="sxs-lookup"><span data-stu-id="320a6-415">See the samples apps at https://github.com/OrchardCMS/OrchardCore.Samples for examples of how to build modular and multi-tenant apps using just Orchard Core Framework without any of the CMS specific features.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="320a6-416">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="320a6-416">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="320a6-417">Čtyři způsoby, jak vyřadit IDisposable v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="320a6-417">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="320a6-418">Zápis čistého kódu v ASP.NET Core pomocí injektáže závislosti (MSDN)</span><span class="sxs-lookup"><span data-stu-id="320a6-418">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="320a6-419">Explicitní závislosti – princip</span><span class="sxs-lookup"><span data-stu-id="320a6-419">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="320a6-420">Inverze řídicích kontejnerů a vzor injektáže závislosti (Martin Fowlera)</span><span class="sxs-lookup"><span data-stu-id="320a6-420">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="320a6-421">Jak zaregistrovat službu s více rozhraními v ASP.NET Core DI</span><span class="sxs-lookup"><span data-stu-id="320a6-421">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="320a6-422">ASP.NET Core podporuje vzor návrhu softwaru pro vkládání závislostí (DI), což je technika pro dosažení [inverze ovládacího prvku (IOC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) mezi třídami a jejich závislostmi.</span><span class="sxs-lookup"><span data-stu-id="320a6-422">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="320a6-423">Další informace, které jsou specifické pro vkládání závislostí v rámci řadičů MVC, najdete v tématu <xref:mvc/controllers/dependency-injection> .</span><span class="sxs-lookup"><span data-stu-id="320a6-423">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="320a6-424">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="320a6-424">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="320a6-425">Přehled injektáže závislosti</span><span class="sxs-lookup"><span data-stu-id="320a6-425">Overview of dependency injection</span></span>

<span data-ttu-id="320a6-426">*Závislost* je libovolný objekt, který vyžaduje jiný objekt.</span><span class="sxs-lookup"><span data-stu-id="320a6-426">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="320a6-427">Projděte si následující `MyDependency` třídu s `WriteMessage` metodou, na které jsou závislé jiné třídy v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="320a6-427">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="320a6-428">Instance `MyDependency` třídy může být vytvořena, aby byla `WriteMessage` Metoda k dispozici pro třídu.</span><span class="sxs-lookup"><span data-stu-id="320a6-428">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="320a6-429">`MyDependency`Třída je závislostí `IndexModel` třídy:</span><span class="sxs-lookup"><span data-stu-id="320a6-429">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="320a6-430">Třída vytvoří a přímo závisí na `MyDependency` instanci.</span><span class="sxs-lookup"><span data-stu-id="320a6-430">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="320a6-431">Závislosti kódu (například předchozí příklad) jsou problematické a je třeba se jim vyhnout z následujících důvodů:</span><span class="sxs-lookup"><span data-stu-id="320a6-431">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="320a6-432">Chcete-li nahradit `MyDependency` jinou implementací, třída musí být upravena.</span><span class="sxs-lookup"><span data-stu-id="320a6-432">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="320a6-433">Pokud `MyDependency` má závislosti, musí být nakonfigurovány třídou.</span><span class="sxs-lookup"><span data-stu-id="320a6-433">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="320a6-434">Ve velkém projektu s více třídami, v závislosti na tom `MyDependency` , je kód konfigurace rozptýlen napříč aplikací.</span><span class="sxs-lookup"><span data-stu-id="320a6-434">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="320a6-435">Tato implementace je obtížná pro testování částí.</span><span class="sxs-lookup"><span data-stu-id="320a6-435">This implementation is difficult to unit test.</span></span> <span data-ttu-id="320a6-436">Aplikace by měla používat třídu typu "nebo zástupné procedury" `MyDependency` , což není u tohoto přístupu možné.</span><span class="sxs-lookup"><span data-stu-id="320a6-436">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="320a6-437">Vkládání závislostí řeší tyto problémy prostřednictvím:</span><span class="sxs-lookup"><span data-stu-id="320a6-437">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="320a6-438">Použití rozhraní nebo základní třídy k abstrakci implementace závislosti.</span><span class="sxs-lookup"><span data-stu-id="320a6-438">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="320a6-439">Registrace závislosti v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="320a6-439">Registration of the dependency in a service container.</span></span> <span data-ttu-id="320a6-440">ASP.NET Core poskytuje integrovaný kontejner služeb <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="320a6-440">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="320a6-441">Služby jsou registrovány v metodě aplikace `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="320a6-441">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="320a6-442">*Vložení* služby do konstruktoru třídy, kde se používá.</span><span class="sxs-lookup"><span data-stu-id="320a6-442">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="320a6-443">Rozhraní přebírá zodpovědnost za vytvoření instance závislosti a její odstranění, když už ji nepotřebujete.</span><span class="sxs-lookup"><span data-stu-id="320a6-443">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="320a6-444">V [ukázkové aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) `IMyDependency` definuje rozhraní metodu, kterou služba poskytuje aplikaci:</span><span class="sxs-lookup"><span data-stu-id="320a6-444">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="320a6-445">Toto rozhraní je implementováno konkrétním typem `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="320a6-445">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="320a6-446">`MyDependency`vyžádá <xref:Microsoft.Extensions.Logging.ILogger`1> v jeho konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="320a6-446">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="320a6-447">Není neobvyklé používat vkládání závislostí v zřetězeném způsobem.</span><span class="sxs-lookup"><span data-stu-id="320a6-447">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="320a6-448">Každá požadovaná závislost zase vyžádá své vlastní závislosti.</span><span class="sxs-lookup"><span data-stu-id="320a6-448">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="320a6-449">Kontejner vyřeší závislosti v grafu a vrátí plně přeloženou službu.</span><span class="sxs-lookup"><span data-stu-id="320a6-449">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="320a6-450">Kolektivní sada závislostí, které je třeba vyřešit, se obvykle označuje jako *strom závislosti*, *graf závislosti*nebo *graf objektů*.</span><span class="sxs-lookup"><span data-stu-id="320a6-450">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="320a6-451">`IMyDependency`a `ILogger<TCategoryName>` musí být registrována v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="320a6-451">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="320a6-452">`IMyDependency`je zaregistrován v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="320a6-452">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="320a6-453">`ILogger<TCategoryName>`je zaregistrované v infrastruktuře abstrakce protokolování, takže se jedná o [službu](#framework-provided-services) , která je zaregistrovaná ve výchozím rozhraní.</span><span class="sxs-lookup"><span data-stu-id="320a6-453">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="320a6-454">Kontejner se překládá `ILogger<TCategoryName>` tím, že využije [(Obecné) otevřené typy](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)a eliminuje nutnost registrace každého [(obecného) konstruovaného typu](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="320a6-454">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="320a6-455">V ukázkové aplikaci `IMyDependency` je služba zaregistrovaná u konkrétního typu `MyDependency` .</span><span class="sxs-lookup"><span data-stu-id="320a6-455">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="320a6-456">Registruje rozsah platnosti služby po dobu životnosti jediného požadavku.</span><span class="sxs-lookup"><span data-stu-id="320a6-456">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="320a6-457">[Doby platnosti služeb](#service-lifetimes) jsou popsány dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="320a6-457">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="320a6-458">Jednotlivé `services.Add{SERVICE_NAME}` metody rozšíření přidávají (a případně nakonfigurují) služby.</span><span class="sxs-lookup"><span data-stu-id="320a6-458">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="320a6-459">Například `services.AddMvc()` přidá Razor stránky služeb a MVC vyžaduje.</span><span class="sxs-lookup"><span data-stu-id="320a6-459">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="320a6-460">Doporučujeme, aby aplikace dodržovaly tuto konvenci.</span><span class="sxs-lookup"><span data-stu-id="320a6-460">We recommended that apps follow this convention.</span></span> <span data-ttu-id="320a6-461">Do oboru názvů [Microsoft. Extensions. DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) umístěte metody rozšíření pro zapouzdření skupin registrací služby.</span><span class="sxs-lookup"><span data-stu-id="320a6-461">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="320a6-462">Pokud konstruktor služby vyžaduje [vestavěný typ](/dotnet/csharp/language-reference/keywords/built-in-types-table), například `string` ,, může být typ vložen pomocí [Konfigurace](xref:fundamentals/configuration/index) nebo [vzoru možností](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="320a6-462">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="320a6-463">Instance služby je požadována prostřednictvím konstruktoru třídy, kde je služba používána a přiřazena k soukromému poli.</span><span class="sxs-lookup"><span data-stu-id="320a6-463">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="320a6-464">Pole se používá pro přístup ke službě podle potřeby v celé třídě.</span><span class="sxs-lookup"><span data-stu-id="320a6-464">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="320a6-465">V ukázkové aplikaci `IMyDependency` je instance vyžádána a používána pro volání `WriteMessage` metody služby:</span><span class="sxs-lookup"><span data-stu-id="320a6-465">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="320a6-466">Služby vložené do spuštění</span><span class="sxs-lookup"><span data-stu-id="320a6-466">Services injected into Startup</span></span>

<span data-ttu-id="320a6-467">`Startup`Při použití obecného hostitele () mohou být do konstruktoru vloženy pouze následující typy služeb <xref:Microsoft.Extensions.Hosting.IHostBuilder> :</span><span class="sxs-lookup"><span data-stu-id="320a6-467">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="320a6-468">Služby je možné vložit do `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="320a6-468">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="320a6-469">Další informace naleznete v tématu <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="320a6-469">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="320a6-470">Služby poskytované rozhraním</span><span class="sxs-lookup"><span data-stu-id="320a6-470">Framework-provided services</span></span>

<span data-ttu-id="320a6-471">`Startup.ConfigureServices`Metoda zodpovídá za definování služeb, které aplikace používá, včetně funkcí platformy, jako je například Entity Framework Core a ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="320a6-471">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="320a6-472">Zpočátku `IServiceCollection` `ConfigureServices` služba má služby definované rozhraním v závislosti na [tom, jak byl hostitel nakonfigurovaný](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="320a6-472">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="320a6-473">Pro aplikaci, která je založená na šabloně ASP.NET Core, není běžné, že mají stovky služeb zaregistrovaných v rámci rozhraní.</span><span class="sxs-lookup"><span data-stu-id="320a6-473">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="320a6-474">V následující tabulce je uvedena malá ukázka služeb registrovaných v rozhraní.</span><span class="sxs-lookup"><span data-stu-id="320a6-474">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="320a6-475">Typ služby</span><span class="sxs-lookup"><span data-stu-id="320a6-475">Service Type</span></span> | <span data-ttu-id="320a6-476">Doba platnosti</span><span class="sxs-lookup"><span data-stu-id="320a6-476">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="320a6-477">Dočasný</span><span class="sxs-lookup"><span data-stu-id="320a6-477">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="320a6-478">Singleton</span><span class="sxs-lookup"><span data-stu-id="320a6-478">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="320a6-479">Singleton</span><span class="sxs-lookup"><span data-stu-id="320a6-479">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="320a6-480">Singleton</span><span class="sxs-lookup"><span data-stu-id="320a6-480">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="320a6-481">Dočasný</span><span class="sxs-lookup"><span data-stu-id="320a6-481">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="320a6-482">Singleton</span><span class="sxs-lookup"><span data-stu-id="320a6-482">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="320a6-483">Dočasný</span><span class="sxs-lookup"><span data-stu-id="320a6-483">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="320a6-484">Singleton</span><span class="sxs-lookup"><span data-stu-id="320a6-484">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="320a6-485">Singleton</span><span class="sxs-lookup"><span data-stu-id="320a6-485">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="320a6-486">Singleton</span><span class="sxs-lookup"><span data-stu-id="320a6-486">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="320a6-487">Dočasný</span><span class="sxs-lookup"><span data-stu-id="320a6-487">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="320a6-488">Singleton</span><span class="sxs-lookup"><span data-stu-id="320a6-488">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="320a6-489">Singleton</span><span class="sxs-lookup"><span data-stu-id="320a6-489">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="320a6-490">Singleton</span><span class="sxs-lookup"><span data-stu-id="320a6-490">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="320a6-491">Registrace dalších služeb pomocí rozšiřujících metod</span><span class="sxs-lookup"><span data-stu-id="320a6-491">Register additional services with extension methods</span></span>

<span data-ttu-id="320a6-492">Pokud je k dispozici metoda rozšíření kolekce služeb pro registraci služby (a jejích závislých služeb, v případě potřeby), konvence používá jedinou `Add{SERVICE_NAME}` metodu rozšíření k registraci všech služeb vyžadovaných touto službou.</span><span class="sxs-lookup"><span data-stu-id="320a6-492">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="320a6-493">Následující kód je příkladem přidání dalších služeb do kontejneru pomocí metod rozšíření [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) a <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> :</span><span class="sxs-lookup"><span data-stu-id="320a6-493">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="320a6-494">Další informace najdete <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> ve třídě v dokumentaci k rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="320a6-494">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="320a6-495">Životnost služeb</span><span class="sxs-lookup"><span data-stu-id="320a6-495">Service lifetimes</span></span>

<span data-ttu-id="320a6-496">Vyberte odpovídající dobu života pro každou registrovanou službu.</span><span class="sxs-lookup"><span data-stu-id="320a6-496">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="320a6-497">Služby ASP.NET Core Services je možné nakonfigurovat s následujícími životnostmi:</span><span class="sxs-lookup"><span data-stu-id="320a6-497">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="320a6-498">Dočasný</span><span class="sxs-lookup"><span data-stu-id="320a6-498">Transient</span></span>

<span data-ttu-id="320a6-499">Služba přechodných životností ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*> ) se vytvoří pokaždé, když se požadují z kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="320a6-499">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="320a6-500">Tato životnost funguje nejlépe pro odlehčené bezstavové služby.</span><span class="sxs-lookup"><span data-stu-id="320a6-500">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="320a6-501">Obor</span><span class="sxs-lookup"><span data-stu-id="320a6-501">Scoped</span></span>

<span data-ttu-id="320a6-502">Oborové služby () s rozsahem platnosti ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*> ) se vytvoří jednou za požadavek klienta (připojení).</span><span class="sxs-lookup"><span data-stu-id="320a6-502">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="320a6-503">Při použití oboru služby v middlewaru založit službu do `Invoke` `InvokeAsync` metody nebo.</span><span class="sxs-lookup"><span data-stu-id="320a6-503">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="320a6-504">Nepoužívejte [vkládání pomocí injektáže konstruktoru](xref:mvc/controllers/dependency-injection#constructor-injection) , protože vynutí, aby se služba chovala jako typ singleton.</span><span class="sxs-lookup"><span data-stu-id="320a6-504">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="320a6-505">Další informace naleznete v tématu <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="320a6-505">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="320a6-506">Singleton</span><span class="sxs-lookup"><span data-stu-id="320a6-506">Singleton</span></span>

<span data-ttu-id="320a6-507">Služba singleton životnosti ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*> ) je vytvořena při prvním vyžádání (nebo při `Startup.ConfigureServices` spuštění a instance je určena pro registraci služby).</span><span class="sxs-lookup"><span data-stu-id="320a6-507">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="320a6-508">Každý další požadavek používá stejnou instanci.</span><span class="sxs-lookup"><span data-stu-id="320a6-508">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="320a6-509">Pokud aplikace vyžaduje chování singleton, doporučuje se povolit kontejneru služby spravovat dobu života služby.</span><span class="sxs-lookup"><span data-stu-id="320a6-509">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="320a6-510">Neimplementujte vzor návrhu singleton a poskytněte uživatelský kód pro správu životního cyklu objektu ve třídě.</span><span class="sxs-lookup"><span data-stu-id="320a6-510">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="320a6-511">Není bezpečné přeložit oborovou službu z typu singleton.</span><span class="sxs-lookup"><span data-stu-id="320a6-511">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="320a6-512">Může dojít k tomu, že služba má při zpracování dalších požadavků špatný stav.</span><span class="sxs-lookup"><span data-stu-id="320a6-512">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="320a6-513">Metody registrace služby</span><span class="sxs-lookup"><span data-stu-id="320a6-513">Service registration methods</span></span>

<span data-ttu-id="320a6-514">Metody rozšíření registrace služby nabízejí přetížení, která jsou užitečná pro konkrétní scénáře.</span><span class="sxs-lookup"><span data-stu-id="320a6-514">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="320a6-515">Metoda</span><span class="sxs-lookup"><span data-stu-id="320a6-515">Method</span></span> | <span data-ttu-id="320a6-516">Automaticky</span><span class="sxs-lookup"><span data-stu-id="320a6-516">Automatic</span></span><br><span data-ttu-id="320a6-517">odkazy objektů</span><span class="sxs-lookup"><span data-stu-id="320a6-517">object</span></span><br><span data-ttu-id="320a6-518">odvod</span><span class="sxs-lookup"><span data-stu-id="320a6-518">disposal</span></span> | <span data-ttu-id="320a6-519">Několik</span><span class="sxs-lookup"><span data-stu-id="320a6-519">Multiple</span></span><br><span data-ttu-id="320a6-520">implementace</span><span class="sxs-lookup"><span data-stu-id="320a6-520">implementations</span></span> | <span data-ttu-id="320a6-521">Pass – argumenty</span><span class="sxs-lookup"><span data-stu-id="320a6-521">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="320a6-522">Příklad:</span><span class="sxs-lookup"><span data-stu-id="320a6-522">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="320a6-523">Ano</span><span class="sxs-lookup"><span data-stu-id="320a6-523">Yes</span></span> | <span data-ttu-id="320a6-524">Ano</span><span class="sxs-lookup"><span data-stu-id="320a6-524">Yes</span></span> | <span data-ttu-id="320a6-525">Ne</span><span class="sxs-lookup"><span data-stu-id="320a6-525">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="320a6-526">Příklady:</span><span class="sxs-lookup"><span data-stu-id="320a6-526">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="320a6-527">Ano</span><span class="sxs-lookup"><span data-stu-id="320a6-527">Yes</span></span> | <span data-ttu-id="320a6-528">Ano</span><span class="sxs-lookup"><span data-stu-id="320a6-528">Yes</span></span> | <span data-ttu-id="320a6-529">Ano</span><span class="sxs-lookup"><span data-stu-id="320a6-529">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="320a6-530">Příklad:</span><span class="sxs-lookup"><span data-stu-id="320a6-530">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="320a6-531">Ano</span><span class="sxs-lookup"><span data-stu-id="320a6-531">Yes</span></span> | <span data-ttu-id="320a6-532">Ne</span><span class="sxs-lookup"><span data-stu-id="320a6-532">No</span></span> | <span data-ttu-id="320a6-533">Ne</span><span class="sxs-lookup"><span data-stu-id="320a6-533">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="320a6-534">Příklady:</span><span class="sxs-lookup"><span data-stu-id="320a6-534">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="320a6-535">No</span><span class="sxs-lookup"><span data-stu-id="320a6-535">No</span></span> | <span data-ttu-id="320a6-536">Ano</span><span class="sxs-lookup"><span data-stu-id="320a6-536">Yes</span></span> | <span data-ttu-id="320a6-537">Ano</span><span class="sxs-lookup"><span data-stu-id="320a6-537">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="320a6-538">Příklady:</span><span class="sxs-lookup"><span data-stu-id="320a6-538">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="320a6-539">Ne</span><span class="sxs-lookup"><span data-stu-id="320a6-539">No</span></span> | <span data-ttu-id="320a6-540">Ne</span><span class="sxs-lookup"><span data-stu-id="320a6-540">No</span></span> | <span data-ttu-id="320a6-541">Ano</span><span class="sxs-lookup"><span data-stu-id="320a6-541">Yes</span></span> |

<span data-ttu-id="320a6-542">Další informace o vyřazení typů najdete v části věnované [vyřazení služeb](#disposal-of-services) .</span><span class="sxs-lookup"><span data-stu-id="320a6-542">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="320a6-543">Běžným scénářem pro více implementací je vytvoření [typů pro testování](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="320a6-543">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="320a6-544">`TryAdd{LIFETIME}`metody registrují službu jenom v případě, že už není zaregistrovaná implementace.</span><span class="sxs-lookup"><span data-stu-id="320a6-544">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="320a6-545">V následujícím příkladu se první řádek registruje `MyDependency` pro `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="320a6-545">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="320a6-546">Druhý řádek nemá žádný účinek, protože `IMyDependency` už má registrovanou implementaci:</span><span class="sxs-lookup"><span data-stu-id="320a6-546">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="320a6-547">Další informace naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="320a6-547">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="320a6-548">Metody [TryAddEnumerable (ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) registrují službu pouze v případě, že již není implementace *stejného typu*.</span><span class="sxs-lookup"><span data-stu-id="320a6-548">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="320a6-549">Několik služeb je vyřešeno prostřednictvím `IEnumerable<{SERVICE}>` .</span><span class="sxs-lookup"><span data-stu-id="320a6-549">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="320a6-550">Při registraci služeb chce vývojář přidat instanci jenom v případě, že už jeden ze stejného typu není přidaný.</span><span class="sxs-lookup"><span data-stu-id="320a6-550">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="320a6-551">Obecně je tato metoda používána autory knihovny k tomu, aby nedocházelo k registraci dvou kopií instance v kontejneru.</span><span class="sxs-lookup"><span data-stu-id="320a6-551">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="320a6-552">V následujícím příkladu se první řádek registruje `MyDep` pro `IMyDep1` .</span><span class="sxs-lookup"><span data-stu-id="320a6-552">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="320a6-553">Druhý řádek se registruje `MyDep` pro `IMyDep2` .</span><span class="sxs-lookup"><span data-stu-id="320a6-553">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="320a6-554">Třetí řádek nemá žádný vliv, protože `IMyDep1` už má registrovanou implementaci `MyDep` :</span><span class="sxs-lookup"><span data-stu-id="320a6-554">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="320a6-555">Chování injektáže konstruktoru</span><span class="sxs-lookup"><span data-stu-id="320a6-555">Constructor injection behavior</span></span>

<span data-ttu-id="320a6-556">Služby je možné vyřešit pomocí dvou mechanismů:</span><span class="sxs-lookup"><span data-stu-id="320a6-556">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="320a6-557"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Povoluje vytvoření objektu bez registrace služby v kontejneru vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="320a6-557"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="320a6-558">`ActivatorUtilities`se používá s uživateli s přístupem k uživatelům, jako jsou například rutiny značek, řadiče MVC a pořadače modelů.</span><span class="sxs-lookup"><span data-stu-id="320a6-558">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="320a6-559">Konstruktory mohou přijímat argumenty, které nejsou poskytovány vkládáním závislostí, ale argumenty musí přiřadit výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="320a6-559">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="320a6-560">Když jsou služby vyřešeny `IServiceProvider` nebo `ActivatorUtilities` , [Injektáže konstruktoru](xref:mvc/controllers/dependency-injection#constructor-injection) vyžaduje *veřejný* konstruktor.</span><span class="sxs-lookup"><span data-stu-id="320a6-560">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="320a6-561">Když jsou služby vyřešeny nástrojem `ActivatorUtilities` , [Injektáže konstruktoru](xref:mvc/controllers/dependency-injection#constructor-injection) vyžaduje, aby existoval pouze jeden příslušný konstruktor.</span><span class="sxs-lookup"><span data-stu-id="320a6-561">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="320a6-562">Přetížení konstruktoru jsou podporovaná, ale může existovat jenom jedno přetížení, jehož argumenty můžou být splněné vkládáním závislostí.</span><span class="sxs-lookup"><span data-stu-id="320a6-562">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="320a6-563">Entity Framework kontexty</span><span class="sxs-lookup"><span data-stu-id="320a6-563">Entity Framework contexts</span></span>

<span data-ttu-id="320a6-564">Entity Framework kontexty se obvykle přidávají do kontejneru služby pomocí [rozsahu životnosti](#service-lifetimes) , protože operace databáze webové aplikace jsou obvykle vymezeny na požadavky klienta.</span><span class="sxs-lookup"><span data-stu-id="320a6-564">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="320a6-565">Výchozí doba života je vymezena v případě, že při registraci kontextu databáze není zadána doba života při [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) přetížení.</span><span class="sxs-lookup"><span data-stu-id="320a6-565">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="320a6-566">Služby pro danou dobu života by neměly používat kontext databáze s kratší dobou životnosti než služba.</span><span class="sxs-lookup"><span data-stu-id="320a6-566">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="320a6-567">Možnosti života a registrace</span><span class="sxs-lookup"><span data-stu-id="320a6-567">Lifetime and registration options</span></span>

<span data-ttu-id="320a6-568">Chcete-li předvést rozdíl mezi možnostmi životního cyklu a registrací, zvažte následující rozhraní, která představují úlohy jako operaci s jedinečným identifikátorem `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="320a6-568">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="320a6-569">V závislosti na tom, jak je životnost provozní služby nakonfigurována pro následující rozhraní, kontejner poskytuje buď stejnou nebo jinou instanci služby, když je vyžádána třídou:</span><span class="sxs-lookup"><span data-stu-id="320a6-569">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="320a6-570">Rozhraní jsou implementována ve `Operation` třídě.</span><span class="sxs-lookup"><span data-stu-id="320a6-570">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="320a6-571">`Operation`Konstruktor generuje identifikátor GUID, pokud není dodán:</span><span class="sxs-lookup"><span data-stu-id="320a6-571">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="320a6-572">`OperationService`Je zaregistrován, který závisí na každém z ostatních `Operation` typů.</span><span class="sxs-lookup"><span data-stu-id="320a6-572">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="320a6-573">Když `OperationService` je požadavek proveden prostřednictvím injektáže závislosti, obdrží buď novou instanci každé služby, nebo existující instanci na základě životnosti závislé služby.</span><span class="sxs-lookup"><span data-stu-id="320a6-573">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="320a6-574">Když se v případě vyžádání z kontejneru vytvoří přechodné služby, `OperationId` liší se od `IOperationTransient` služby `OperationId` `OperationService` .</span><span class="sxs-lookup"><span data-stu-id="320a6-574">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="320a6-575">`OperationService`přijme novou instanci `IOperationTransient` třídy.</span><span class="sxs-lookup"><span data-stu-id="320a6-575">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="320a6-576">Nová instance vrací jinou instanci `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="320a6-576">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="320a6-577">Když se vytvoří vymezené služby pro jednotlivé požadavky klienta, `OperationId` `IOperationScoped` služba je stejná jako `OperationService` v rámci žádosti klienta.</span><span class="sxs-lookup"><span data-stu-id="320a6-577">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="320a6-578">V rámci požadavků klientů obě služby sdílejí jinou `OperationId` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="320a6-578">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="320a6-579">Pokud se služby instance singleton a singleton vytvoří jednou a použije se pro všechny požadavky klientů a všechny služby, `OperationId` je v rámci všech žádostí o službu konstantní.</span><span class="sxs-lookup"><span data-stu-id="320a6-579">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="320a6-580">V `Startup.ConfigureServices` je každý typ přidán do kontejneru podle jeho pojmenované životnosti:</span><span class="sxs-lookup"><span data-stu-id="320a6-580">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="320a6-581">`IOperationSingletonInstance`Služba používá konkrétní instanci se známým ID `Guid.Empty` .</span><span class="sxs-lookup"><span data-stu-id="320a6-581">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="320a6-582">Je jasné, že pokud se tento typ používá (jeho identifikátor GUID je všechny nuly).</span><span class="sxs-lookup"><span data-stu-id="320a6-582">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="320a6-583">Ukázková aplikace ukazuje dobu života objektu v rámci a mezi jednotlivými požadavky.</span><span class="sxs-lookup"><span data-stu-id="320a6-583">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="320a6-584">Ukázková aplikace `IndexModel` požaduje každý druh `IOperation` typu a `OperationService` .</span><span class="sxs-lookup"><span data-stu-id="320a6-584">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="320a6-585">Stránka potom zobrazí všechny hodnoty třídy modelu stránky a `OperationId` hodnoty služby prostřednictvím přiřazení vlastností:</span><span class="sxs-lookup"><span data-stu-id="320a6-585">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="320a6-586">Výsledkem dvou následujících výstupů jsou výsledky dvou požadavků:</span><span class="sxs-lookup"><span data-stu-id="320a6-586">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="320a6-587">**První požadavek:**</span><span class="sxs-lookup"><span data-stu-id="320a6-587">**First request:**</span></span>

<span data-ttu-id="320a6-588">Operace kontroleru:</span><span class="sxs-lookup"><span data-stu-id="320a6-588">Controller operations:</span></span>

<span data-ttu-id="320a6-589">Přechodný: d233e165-f417-469b-A866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="320a6-589">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="320a6-590">Vymezené: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="320a6-590">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="320a6-591">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="320a6-591">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="320a6-592">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="320a6-592">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="320a6-593">`OperationService`Operations</span><span class="sxs-lookup"><span data-stu-id="320a6-593">`OperationService` operations:</span></span>

<span data-ttu-id="320a6-594">Přechodný: c6b049eb-1318-4E31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="320a6-594">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="320a6-595">Vymezené: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="320a6-595">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="320a6-596">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="320a6-596">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="320a6-597">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="320a6-597">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="320a6-598">**Druhá žádost:**</span><span class="sxs-lookup"><span data-stu-id="320a6-598">**Second request:**</span></span>

<span data-ttu-id="320a6-599">Operace kontroleru:</span><span class="sxs-lookup"><span data-stu-id="320a6-599">Controller operations:</span></span>

<span data-ttu-id="320a6-600">Přechodný: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="320a6-600">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="320a6-601">Vymezené: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="320a6-601">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="320a6-602">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="320a6-602">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="320a6-603">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="320a6-603">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="320a6-604">`OperationService`Operations</span><span class="sxs-lookup"><span data-stu-id="320a6-604">`OperationService` operations:</span></span>

<span data-ttu-id="320a6-605">Přechodný: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="320a6-605">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="320a6-606">Vymezené: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="320a6-606">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="320a6-607">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="320a6-607">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="320a6-608">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="320a6-608">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="320a6-609">Pozor, které `OperationId` hodnoty se liší v rámci požadavku a mezi požadavky:</span><span class="sxs-lookup"><span data-stu-id="320a6-609">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="320a6-610">*Přechodné* objekty jsou vždy odlišné.</span><span class="sxs-lookup"><span data-stu-id="320a6-610">*Transient* objects are always different.</span></span> <span data-ttu-id="320a6-611">Přechodná `OperationId` hodnota pro první i druhý požadavek klienta se liší pro obě `OperationService` operace i mezi požadavky klientů.</span><span class="sxs-lookup"><span data-stu-id="320a6-611">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="320a6-612">Každé žádosti o službu a žádosti klienta se poskytne nová instance.</span><span class="sxs-lookup"><span data-stu-id="320a6-612">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="320a6-613">*Vymezené* objekty jsou v požadavku klienta stejné, ale v rámci požadavků klientů se liší.</span><span class="sxs-lookup"><span data-stu-id="320a6-613">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="320a6-614">Objekty *singleton* jsou stejné pro každý objekt a každý požadavek bez ohledu na to, zda `Operation` je instance uvedena v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="320a6-614">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="320a6-615">Volat služby z Main</span><span class="sxs-lookup"><span data-stu-id="320a6-615">Call services from main</span></span>

<span data-ttu-id="320a6-616">Vytvořte <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> pomocí [IServiceScopeFactory. CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) pro řešení oboru služby v rámci rozsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="320a6-616">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="320a6-617">Tento přístup je užitečný pro přístup k oboruované službě při spuštění za účelem spouštění úloh inicializace.</span><span class="sxs-lookup"><span data-stu-id="320a6-617">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="320a6-618">Následující příklad ukazuje, jak získat kontext pro `MyScopedService` `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="320a6-618">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="320a6-619">Ověřování oboru</span><span class="sxs-lookup"><span data-stu-id="320a6-619">Scope validation</span></span>

<span data-ttu-id="320a6-620">Když je aplikace spuštěná ve vývojovém prostředí, provede výchozí poskytovatel služeb kontroly, aby ověřil, že:</span><span class="sxs-lookup"><span data-stu-id="320a6-620">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="320a6-621">Oborové služby nejsou přímo nebo nepřímo vyřešeny od poskytovatele kořenové služby.</span><span class="sxs-lookup"><span data-stu-id="320a6-621">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="320a6-622">Oborové služby nejsou přímo nebo nepřímo vložené do singleton.</span><span class="sxs-lookup"><span data-stu-id="320a6-622">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="320a6-623">Poskytovatel kořenové služby se vytvoří, když <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> se zavolá.</span><span class="sxs-lookup"><span data-stu-id="320a6-623">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="320a6-624">Doba života poskytovatele kořenové služby odpovídá době životnosti aplikace nebo serveru, když se poskytovatel spustí s aplikací a je uvolněný při ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="320a6-624">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="320a6-625">Oborové služby jsou uvolněny kontejnerem, který je vytvořil.</span><span class="sxs-lookup"><span data-stu-id="320a6-625">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="320a6-626">Pokud je v kořenovém kontejneru vytvořena vymezená služba, doba života služby je efektivně povýšena na typ singleton, protože je uvolněna pouze kořenovým kontejnerem při vypnutí aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="320a6-626">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="320a6-627">Při ověřování oborů služeb se tyto situace zachytí `BuildServiceProvider` .</span><span class="sxs-lookup"><span data-stu-id="320a6-627">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="320a6-628">Další informace naleznete v tématu <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="320a6-628">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="320a6-629">Žádosti o služby</span><span class="sxs-lookup"><span data-stu-id="320a6-629">Request Services</span></span>

<span data-ttu-id="320a6-630">Služby, které jsou k dispozici v rámci žádosti o ASP.NET Core, `HttpContext` jsou zpřístupněny prostřednictvím kolekce [HttpContext. RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) .</span><span class="sxs-lookup"><span data-stu-id="320a6-630">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="320a6-631">Žádosti o služby reprezentují služby nakonfigurované a požadované v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="320a6-631">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="320a6-632">Když objekty určují závislosti, jsou splněny typy nalezenými v `RequestServices` , ne `ApplicationServices` .</span><span class="sxs-lookup"><span data-stu-id="320a6-632">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="320a6-633">Obecně platí, že aplikace by tyto vlastnosti neměla používat přímo.</span><span class="sxs-lookup"><span data-stu-id="320a6-633">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="320a6-634">Místo toho požádejte o typy, které třídy vyžadují prostřednictvím konstruktorů třídy, a umožněte rozhraní vložit závislosti.</span><span class="sxs-lookup"><span data-stu-id="320a6-634">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="320a6-635">To poskytuje třídy, které jsou snáze testovány.</span><span class="sxs-lookup"><span data-stu-id="320a6-635">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="320a6-636">Preferovat požadavky na závislosti jako parametry konstruktoru pro přístup ke `RequestServices` kolekci.</span><span class="sxs-lookup"><span data-stu-id="320a6-636">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="320a6-637">Navrhnout služby pro vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="320a6-637">Design services for dependency injection</span></span>

<span data-ttu-id="320a6-638">Osvědčené postupy:</span><span class="sxs-lookup"><span data-stu-id="320a6-638">Best practices are to:</span></span>

* <span data-ttu-id="320a6-639">Navrhněte služby pro použití injektáže závislosti k získání jejich závislostí.</span><span class="sxs-lookup"><span data-stu-id="320a6-639">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="320a6-640">Vyhněte se stavovým, statickým třídám a členům.</span><span class="sxs-lookup"><span data-stu-id="320a6-640">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="320a6-641">Navrhněte aplikace, aby místo toho používaly služby s jedním prvkem, což se vyhne vytváření globálního stavu.</span><span class="sxs-lookup"><span data-stu-id="320a6-641">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="320a6-642">Vyhněte se přímému vytváření instancí závislých tříd v rámci služeb.</span><span class="sxs-lookup"><span data-stu-id="320a6-642">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="320a6-643">Přímá instance Couples kód na konkrétní implementaci.</span><span class="sxs-lookup"><span data-stu-id="320a6-643">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="320a6-644">Vytvářejte třídy aplikací malými, dobře faktoringně a snadno testované.</span><span class="sxs-lookup"><span data-stu-id="320a6-644">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="320a6-645">Pokud se zdá, že třída má příliš mnoho vložených závislostí, obvykle se jedná o znaménko, že třída má příliš mnoho zodpovědnosti a je v rozporu s [principem jediné zodpovědnosti (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="320a6-645">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="320a6-646">Pokuste se Refaktorovat třídu přesunutím některých odpovědností do nové třídy.</span><span class="sxs-lookup"><span data-stu-id="320a6-646">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="320a6-647">Mějte na paměti, že Razor stránky tříd modelu stránky a třídy KONTROLERU MVC by se měly zaměřit na uživatelské rozhraní.</span><span class="sxs-lookup"><span data-stu-id="320a6-647">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="320a6-648">Obchodní pravidla a podrobnosti implementace přístupu k datům by měly být uchovávány ve třídách, které jsou vhodné pro tyto [samostatné obavy](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="320a6-648">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="320a6-649">Vyřazení služeb</span><span class="sxs-lookup"><span data-stu-id="320a6-649">Disposal of services</span></span>

<span data-ttu-id="320a6-650">Kontejner volá <xref:System.IDisposable.Dispose*> typy, které <xref:System.IDisposable> vytvoří.</span><span class="sxs-lookup"><span data-stu-id="320a6-650">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="320a6-651">Pokud je instance přidána do kontejneru pomocí uživatelského kódu, nebude uvolněna automaticky.</span><span class="sxs-lookup"><span data-stu-id="320a6-651">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="320a6-652">V následujícím příkladu jsou služby vytvořeny pomocí kontejneru služby a automaticky odstraněny:</span><span class="sxs-lookup"><span data-stu-id="320a6-652">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="320a6-653">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="320a6-653">In the following example:</span></span>

* <span data-ttu-id="320a6-654">Instance služby nejsou vytvořeny kontejnerem služby.</span><span class="sxs-lookup"><span data-stu-id="320a6-654">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="320a6-655">Zamýšlené životnosti služeb nejsou rozhraním známy.</span><span class="sxs-lookup"><span data-stu-id="320a6-655">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="320a6-656">Rozhraní neodstraní služby automaticky.</span><span class="sxs-lookup"><span data-stu-id="320a6-656">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="320a6-657">Pokud se služby explicitně neodstraní v kódu pro vývojáře, zůstanou zachovány, dokud se aplikace neukončí.</span><span class="sxs-lookup"><span data-stu-id="320a6-657">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="320a6-658">Doprovodné materiály k rozhraní IDisposable pro přechodné a sdílené instance</span><span class="sxs-lookup"><span data-stu-id="320a6-658">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="320a6-659">Přechodná, omezená doba života</span><span class="sxs-lookup"><span data-stu-id="320a6-659">Transient, limited lifetime</span></span>

<span data-ttu-id="320a6-660">**Scénář**</span><span class="sxs-lookup"><span data-stu-id="320a6-660">**Scenario**</span></span>

<span data-ttu-id="320a6-661">Aplikace vyžaduje <xref:System.IDisposable> instanci s přechodným životností pro některý z následujících scénářů:</span><span class="sxs-lookup"><span data-stu-id="320a6-661">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="320a6-662">Instance je vyřešena v kořenovém oboru.</span><span class="sxs-lookup"><span data-stu-id="320a6-662">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="320a6-663">Instance by měla být uvolněna před ukončením oboru.</span><span class="sxs-lookup"><span data-stu-id="320a6-663">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="320a6-664">**Řešení**</span><span class="sxs-lookup"><span data-stu-id="320a6-664">**Solution**</span></span>

<span data-ttu-id="320a6-665">Pomocí modelu továrny vytvořte instanci mimo nadřazený obor.</span><span class="sxs-lookup"><span data-stu-id="320a6-665">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="320a6-666">V takové situaci by aplikace měla obvykle `Create` metodu, která volá přímo konstruktor finálního typu.</span><span class="sxs-lookup"><span data-stu-id="320a6-666">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="320a6-667">Pokud má konečný typ jiné závislosti, továrna může:</span><span class="sxs-lookup"><span data-stu-id="320a6-667">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="320a6-668">Přijmout <xref:System.IServiceProvider> ve svém konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="320a6-668">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="320a6-669">Slouží <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> k vytvoření instance instance mimo kontejner, při použití kontejneru pro jeho závislosti.</span><span class="sxs-lookup"><span data-stu-id="320a6-669">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="320a6-670">Sdílená instance, omezená doba života</span><span class="sxs-lookup"><span data-stu-id="320a6-670">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="320a6-671">**Scénář**</span><span class="sxs-lookup"><span data-stu-id="320a6-671">**Scenario**</span></span>

<span data-ttu-id="320a6-672">Aplikace vyžaduje sdílenou <xref:System.IDisposable> instanci napříč více službami, ale <xref:System.IDisposable> měla by mít omezené trvání.</span><span class="sxs-lookup"><span data-stu-id="320a6-672">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="320a6-673">**Řešení**</span><span class="sxs-lookup"><span data-stu-id="320a6-673">**Solution**</span></span>

<span data-ttu-id="320a6-674">Zaregistrujte instanci s vymezenou životností.</span><span class="sxs-lookup"><span data-stu-id="320a6-674">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="320a6-675">Použijte <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> k zahájení a vytvoření nového <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> .</span><span class="sxs-lookup"><span data-stu-id="320a6-675">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="320a6-676"><xref:System.IServiceProvider>Požadované služby získáte pomocí tohoto oboru.</span><span class="sxs-lookup"><span data-stu-id="320a6-676">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="320a6-677">Vyřadit rozsah, pokud by životnost měla končit.</span><span class="sxs-lookup"><span data-stu-id="320a6-677">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="320a6-678">Obecné pokyny</span><span class="sxs-lookup"><span data-stu-id="320a6-678">General Guidelines</span></span>

* <span data-ttu-id="320a6-679">Neregistrujte <xref:System.IDisposable> instance s přechodným oborem.</span><span class="sxs-lookup"><span data-stu-id="320a6-679">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="320a6-680">Místo toho použijte výrobní model.</span><span class="sxs-lookup"><span data-stu-id="320a6-680">Use the factory pattern instead.</span></span>
* <span data-ttu-id="320a6-681">V kořenovém oboru neřešte přechodné nebo vymezené <xref:System.IDisposable> instance.</span><span class="sxs-lookup"><span data-stu-id="320a6-681">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="320a6-682">Jedinou obecnou výjimkou je, když aplikace vytvoří nebo znovu vytvoří a odstraní <xref:System.IServiceProvider> , což není ideální vzor.</span><span class="sxs-lookup"><span data-stu-id="320a6-682">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="320a6-683">Příjem <xref:System.IDisposable> závislosti přes di nevyžaduje, aby se příjemce sám implementoval <xref:System.IDisposable> .</span><span class="sxs-lookup"><span data-stu-id="320a6-683">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="320a6-684">Příjemce <xref:System.IDisposable> závislosti by neměl volat <xref:System.IDisposable.Dispose%2A> tuto závislost.</span><span class="sxs-lookup"><span data-stu-id="320a6-684">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="320a6-685">Obory by měly sloužit k řízení životnosti služeb.</span><span class="sxs-lookup"><span data-stu-id="320a6-685">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="320a6-686">Obory nejsou hierarchické a mezi obory není žádné zvláštní připojení.</span><span class="sxs-lookup"><span data-stu-id="320a6-686">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="320a6-687">Výchozí nahrazení kontejneru služby</span><span class="sxs-lookup"><span data-stu-id="320a6-687">Default service container replacement</span></span>

<span data-ttu-id="320a6-688">Integrovaný kontejner služeb je navržený tak, aby sloužil potřebám architektury a většině spotřebitelských aplikací.</span><span class="sxs-lookup"><span data-stu-id="320a6-688">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="320a6-689">Pokud nepotřebujete určitou funkci, kterou integrovaný kontejner nepodporuje, doporučujeme použít vestavěný kontejner, například:</span><span class="sxs-lookup"><span data-stu-id="320a6-689">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="320a6-690">Vkládání vlastností</span><span class="sxs-lookup"><span data-stu-id="320a6-690">Property injection</span></span>
* <span data-ttu-id="320a6-691">Vkládání na základě názvu</span><span class="sxs-lookup"><span data-stu-id="320a6-691">Injection based on name</span></span>
* <span data-ttu-id="320a6-692">Podřízené kontejnery</span><span class="sxs-lookup"><span data-stu-id="320a6-692">Child containers</span></span>
* <span data-ttu-id="320a6-693">Vlastní Správa životního cyklu</span><span class="sxs-lookup"><span data-stu-id="320a6-693">Custom lifetime management</span></span>
* <span data-ttu-id="320a6-694">`Func<T>`Podpora opožděné inicializace</span><span class="sxs-lookup"><span data-stu-id="320a6-694">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="320a6-695">Registrace založená na konvencích</span><span class="sxs-lookup"><span data-stu-id="320a6-695">Convention-based registration</span></span>

<span data-ttu-id="320a6-696">S ASP.NET Coremi aplikacemi je možné používat následující kontejnery třetích stran:</span><span class="sxs-lookup"><span data-stu-id="320a6-696">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="320a6-697">Autofac</span><span class="sxs-lookup"><span data-stu-id="320a6-697">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="320a6-698">DryIoc</span><span class="sxs-lookup"><span data-stu-id="320a6-698">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="320a6-699">Integrit</span><span class="sxs-lookup"><span data-stu-id="320a6-699">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="320a6-700">LightInject</span><span class="sxs-lookup"><span data-stu-id="320a6-700">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="320a6-701">Lamar</span><span class="sxs-lookup"><span data-stu-id="320a6-701">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="320a6-702">Stashbox</span><span class="sxs-lookup"><span data-stu-id="320a6-702">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="320a6-703">Unity</span><span class="sxs-lookup"><span data-stu-id="320a6-703">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="320a6-704">Bezpečnost vlákna</span><span class="sxs-lookup"><span data-stu-id="320a6-704">Thread safety</span></span>

<span data-ttu-id="320a6-705">Vytvořte služby, které jsou v bezpečí pro přístup z více vláken.</span><span class="sxs-lookup"><span data-stu-id="320a6-705">Create thread-safe singleton services.</span></span> <span data-ttu-id="320a6-706">Pokud má služba typu Singleton závislost na přechodné službě, přechodná služba může také vyžadovat zabezpečení vlákna v závislosti na tom, jak je používá typ singleton.</span><span class="sxs-lookup"><span data-stu-id="320a6-706">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="320a6-707">Výrobní metoda samostatné služby, jako je například druhý argument pro [AddSingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), nemusí být bezpečná pro přístup z více vláken.</span><span class="sxs-lookup"><span data-stu-id="320a6-707">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="320a6-708">Podobně jako konstruktor typu ( `static` ), je zaručeno, že bude volán jednou vláknem.</span><span class="sxs-lookup"><span data-stu-id="320a6-708">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="320a6-709">Doporučení</span><span class="sxs-lookup"><span data-stu-id="320a6-709">Recommendations</span></span>

* <span data-ttu-id="320a6-710">`async/await``Task`řešení služeb a se nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="320a6-710">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="320a6-711">Jazyk C# nepodporuje asynchronní konstruktory; Proto je doporučeným vzorem použití asynchronních metod po synchronním překladu služby.</span><span class="sxs-lookup"><span data-stu-id="320a6-711">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="320a6-712">Neukládejte data a konfiguraci přímo do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="320a6-712">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="320a6-713">Například nákupní košík uživatele by neměl být obvykle přidán do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="320a6-713">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="320a6-714">Konfigurace by měla používat [vzor možností](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="320a6-714">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="320a6-715">Podobně nepoužívejte objekty "držitel dat", které existují pouze k povolení přístupu k jinému objektu.</span><span class="sxs-lookup"><span data-stu-id="320a6-715">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="320a6-716">Je lepší žádat o skutečnou položku přes DI.</span><span class="sxs-lookup"><span data-stu-id="320a6-716">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="320a6-717">Vyhněte se statickému přístupu ke službám (například statickému psaní [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) pro použití jinde).</span><span class="sxs-lookup"><span data-stu-id="320a6-717">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="320a6-718">Vyhněte se použití *vzoru lokátoru služby*, který kombinuje [inverzi strategií řízení](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .</span><span class="sxs-lookup"><span data-stu-id="320a6-718">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

  * <span data-ttu-id="320a6-719">Nevolejte <xref:System.IServiceProvider.GetService*> pro získání instance služby, když můžete místo toho použít di:</span><span class="sxs-lookup"><span data-stu-id="320a6-719">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="320a6-720">**Špatný**</span><span class="sxs-lookup"><span data-stu-id="320a6-720">**Incorrect:**</span></span>

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

    <span data-ttu-id="320a6-721">**Správné**:</span><span class="sxs-lookup"><span data-stu-id="320a6-721">**Correct**:</span></span>

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

  * <span data-ttu-id="320a6-722">Vyhněte se vkládání továrny, která řeší závislosti za běhu pomocí <xref:System.IServiceProvider.GetService*> .</span><span class="sxs-lookup"><span data-stu-id="320a6-722">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>

* <span data-ttu-id="320a6-723">Vyhněte se statickému přístupu k `HttpContext` (například [IHttpContextAccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="320a6-723">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="320a6-724">Podobně jako u všech sad doporučení se může stát, že se vyžaduje ignorování doporučení.</span><span class="sxs-lookup"><span data-stu-id="320a6-724">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="320a6-725">Výjimky jsou zřídka, většinou zvláštní případy v rámci samotného rozhraní.</span><span class="sxs-lookup"><span data-stu-id="320a6-725">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="320a6-726">DI je *alternativou* ke vzorům statických nebo globálních přístupů k objektům.</span><span class="sxs-lookup"><span data-stu-id="320a6-726">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="320a6-727">Je možné, že nebudete moci využít výhody DI, pokud je kombinujete se statickým přístupem k objektům.</span><span class="sxs-lookup"><span data-stu-id="320a6-727">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="320a6-728">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="320a6-728">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="320a6-729">Čtyři způsoby, jak vyřadit IDisposable v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="320a6-729">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="320a6-730">Zápis čistého kódu v ASP.NET Core pomocí injektáže závislosti (MSDN)</span><span class="sxs-lookup"><span data-stu-id="320a6-730">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="320a6-731">Explicitní závislosti – princip</span><span class="sxs-lookup"><span data-stu-id="320a6-731">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="320a6-732">Inverze řídicích kontejnerů a vzor injektáže závislosti (Martin Fowlera)</span><span class="sxs-lookup"><span data-stu-id="320a6-732">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="320a6-733">Jak zaregistrovat službu s více rozhraními v ASP.NET Core DI</span><span class="sxs-lookup"><span data-stu-id="320a6-733">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

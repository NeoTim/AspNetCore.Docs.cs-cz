---
title: Injektáž závislostí v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak ASP.NET Core implementuje vkládání závislostí a jak ji používat.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
uid: fundamentals/dependency-injection
ms.openlocfilehash: 4e990329b7ebcfc9cbbff8a3c9895604a22461d3
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661701"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="870e0-103">Injektáž závislostí v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="870e0-103">Dependency injection in ASP.NET Core</span></span>

<span data-ttu-id="870e0-104">[Steve Smith](https://ardalis.com/) a [Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="870e0-104">By [Steve Smith](https://ardalis.com/) and [Scott Addie](https://scottaddie.com)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="870e0-105">ASP.NET Core podporuje vzor návrhu softwaru vkládání závislostí (DI), což je technika pro dosažení [inverze řízení (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) mezi třídami a jejich závislostmi.</span><span class="sxs-lookup"><span data-stu-id="870e0-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="870e0-106">Další informace specifické pro vkládání závislostí v rámci <xref:mvc/controllers/dependency-injection>řadičů MVC naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="870e0-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="870e0-107">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="870e0-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="870e0-108">Přehled vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="870e0-108">Overview of dependency injection</span></span>

<span data-ttu-id="870e0-109">*Závislost* je libovolný objekt, který vyžaduje jiný objekt.</span><span class="sxs-lookup"><span data-stu-id="870e0-109">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="870e0-110">Prozkoumejte `MyDependency` následující třídu `WriteMessage` metodou, na které závisí jiné třídy v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="870e0-110">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="870e0-111">Instance třídy `MyDependency` lze vytvořit tak, `WriteMessage` aby metoda k dispozici třídy.</span><span class="sxs-lookup"><span data-stu-id="870e0-111">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="870e0-112">Třída `MyDependency` je závislost `IndexModel` třídy:</span><span class="sxs-lookup"><span data-stu-id="870e0-112">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="870e0-113">Třída vytvoří a přímo `MyDependency` závisí na instanci.</span><span class="sxs-lookup"><span data-stu-id="870e0-113">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="870e0-114">Závislosti kódu (například v předchozím příkladu) jsou problematické a je třeba se vyhnout z následujících důvodů:</span><span class="sxs-lookup"><span data-stu-id="870e0-114">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="870e0-115">Chcete-li nahradit `MyDependency` jinou implementaci, musí být třída změněna.</span><span class="sxs-lookup"><span data-stu-id="870e0-115">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="870e0-116">Pokud `MyDependency` má závislosti, musí být nakonfigurovány třídou.</span><span class="sxs-lookup"><span data-stu-id="870e0-116">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="870e0-117">Ve velkém projektu s více `MyDependency`tříd v závislosti na , konfigurační kód se rozptýlí v celé aplikaci.</span><span class="sxs-lookup"><span data-stu-id="870e0-117">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="870e0-118">Tato implementace je obtížné testování částí.</span><span class="sxs-lookup"><span data-stu-id="870e0-118">This implementation is difficult to unit test.</span></span> <span data-ttu-id="870e0-119">Aplikace by měla používat mock `MyDependency` nebo stub třídy, což není možné s tímto přístupem.</span><span class="sxs-lookup"><span data-stu-id="870e0-119">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="870e0-120">Vkládání závislostí řeší tyto problémy prostřednictvím:</span><span class="sxs-lookup"><span data-stu-id="870e0-120">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="870e0-121">Použití rozhraní nebo základní třídy k abstrahovat implementaci závislostí.</span><span class="sxs-lookup"><span data-stu-id="870e0-121">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="870e0-122">Registrace závislosti v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="870e0-122">Registration of the dependency in a service container.</span></span> <span data-ttu-id="870e0-123">ASP.NET Core poskytuje vestavěný kontejner <xref:System.IServiceProvider>služeb .</span><span class="sxs-lookup"><span data-stu-id="870e0-123">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="870e0-124">Služby jsou registrovány `Startup.ConfigureServices` metodou aplikace.</span><span class="sxs-lookup"><span data-stu-id="870e0-124">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="870e0-125">*Vstřikování* služby do konstruktoru třídy, kde se používá.</span><span class="sxs-lookup"><span data-stu-id="870e0-125">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="870e0-126">Rámec přebírá odpovědnost za vytvoření instance závislosti a její likvidaci, když už není potřeba.</span><span class="sxs-lookup"><span data-stu-id="870e0-126">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="870e0-127">V [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) `IMyDependency` rozhraní definuje metodu, kterou služba poskytuje aplikaci:</span><span class="sxs-lookup"><span data-stu-id="870e0-127">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="870e0-128">Toto rozhraní je implementováno konkrétním typem: `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="870e0-128">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="870e0-129">`MyDependency`požaduje v <xref:Microsoft.Extensions.Logging.ILogger`1> jeho konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="870e0-129">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="870e0-130">Není neobvyklé používat vkládání závislostí zřetězenou módou.</span><span class="sxs-lookup"><span data-stu-id="870e0-130">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="870e0-131">Každá požadovaná závislost zase požaduje vlastní závislosti.</span><span class="sxs-lookup"><span data-stu-id="870e0-131">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="870e0-132">Kontejner řeší závislosti v grafu a vrátí plně vyřešené služby.</span><span class="sxs-lookup"><span data-stu-id="870e0-132">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="870e0-133">Kolektivní sada závislostí, které musí být vyřešeny, se obvykle označuje jako *strom závislostí*, *graf závislostí*nebo *graf objektů*.</span><span class="sxs-lookup"><span data-stu-id="870e0-133">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="870e0-134">`IMyDependency`a `ILogger<TCategoryName>` musí být registrovány v servisním kontejneru.</span><span class="sxs-lookup"><span data-stu-id="870e0-134">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="870e0-135">`IMyDependency`je registrován `Startup.ConfigureServices`v .</span><span class="sxs-lookup"><span data-stu-id="870e0-135">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="870e0-136">`ILogger<TCategoryName>`je registrována infrastrukturou protokolování abstrakcí, takže se jedná o službu poskytovanou v [rámci,](#framework-provided-services) kterou standardně registruje.</span><span class="sxs-lookup"><span data-stu-id="870e0-136">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="870e0-137">Kontejner řeší `ILogger<TCategoryName>` využitím [(obecné) otevřené typy](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), což eliminuje potřebu registrovat každý [(obecný) konstruovaný typ](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="870e0-137">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="870e0-138">V ukázkové aplikaci je `IMyDependency` služba registrována s konkrétním typem `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="870e0-138">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="870e0-139">Registrace obory životnost i do provozní doby životnosti jednoho požadavku.</span><span class="sxs-lookup"><span data-stu-id="870e0-139">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="870e0-140">[Životnost služby](#service-lifetimes) jsou popsány dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="870e0-140">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="870e0-141">Každá `services.Add{SERVICE_NAME}` metoda rozšíření přidává (a potenciálně konfiguruje) služby.</span><span class="sxs-lookup"><span data-stu-id="870e0-141">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="870e0-142">Například `services.AddMvc()` přidá služby Razor Pages a MVC vyžadují.</span><span class="sxs-lookup"><span data-stu-id="870e0-142">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="870e0-143">Doporučujeme, aby aplikace dodržovaly tuto konvenci.</span><span class="sxs-lookup"><span data-stu-id="870e0-143">We recommended that apps follow this convention.</span></span> <span data-ttu-id="870e0-144">Umístěte metody rozšíření do oboru názvů [Microsoft.Extensions.DependencyInjection,](/dotnet/api/microsoft.extensions.dependencyinjection) abyste zapouzdřili skupiny registrací služby.</span><span class="sxs-lookup"><span data-stu-id="870e0-144">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="870e0-145">Pokud konstruktor služby vyžaduje [vestavěný typ](/dotnet/csharp/language-reference/keywords/built-in-types-table), `string`například , typ může být vložen pomocí [konfigurace](xref:fundamentals/configuration/index) nebo [vzoru voleb](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="870e0-145">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="870e0-146">Instance služby je požadována prostřednictvím konstruktoru třídy, kde je služba používána a přiřazena k soukromému poli.</span><span class="sxs-lookup"><span data-stu-id="870e0-146">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="870e0-147">Toto pole se používá pro přístup ke službě podle potřeby v celé třídě.</span><span class="sxs-lookup"><span data-stu-id="870e0-147">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="870e0-148">V ukázkové aplikaci je `IMyDependency` instance požadována a `WriteMessage` použita k volání metody služby:</span><span class="sxs-lookup"><span data-stu-id="870e0-148">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="870e0-149">Služby vložené do startupu</span><span class="sxs-lookup"><span data-stu-id="870e0-149">Services injected into Startup</span></span>

<span data-ttu-id="870e0-150">Při použití obecného `Startup` hostitele (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span><span class="sxs-lookup"><span data-stu-id="870e0-150">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="870e0-151">Služby mohou být `Startup.Configure`vloženy do :</span><span class="sxs-lookup"><span data-stu-id="870e0-151">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="870e0-152">Další informace naleznete v tématu <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="870e0-152">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="870e0-153">Rámcové služby</span><span class="sxs-lookup"><span data-stu-id="870e0-153">Framework-provided services</span></span>

<span data-ttu-id="870e0-154">Metoda `Startup.ConfigureServices` je zodpovědná za definování služeb, které aplikace používá, včetně funkcí platformy, jako je například Core framework entity a ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="870e0-154">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="870e0-155">Zpočátku `IServiceCollection` poskytované `ConfigureServices` má služby definované v rámci v závislosti na [tom, jak byl nakonfigurován hostitel](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="870e0-155">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="870e0-156">Není neobvyklé, že aplikace založená na šabloně ASP.NET Core má stovky služeb registrovaných v rámci.</span><span class="sxs-lookup"><span data-stu-id="870e0-156">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="870e0-157">Malý vzorek služeb registrovaných v rámci je uveden v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="870e0-157">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="870e0-158">Typ služby</span><span class="sxs-lookup"><span data-stu-id="870e0-158">Service Type</span></span> | <span data-ttu-id="870e0-159">Doba platnosti</span><span class="sxs-lookup"><span data-stu-id="870e0-159">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="870e0-160">Přechodné</span><span class="sxs-lookup"><span data-stu-id="870e0-160">Transient</span></span> |
| `IHostApplicationLifetime` | <span data-ttu-id="870e0-161">Singleton</span><span class="sxs-lookup"><span data-stu-id="870e0-161">Singleton</span></span> |
| `IWebHostEnvironment` | <span data-ttu-id="870e0-162">Singleton</span><span class="sxs-lookup"><span data-stu-id="870e0-162">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="870e0-163">Singleton</span><span class="sxs-lookup"><span data-stu-id="870e0-163">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="870e0-164">Přechodné</span><span class="sxs-lookup"><span data-stu-id="870e0-164">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="870e0-165">Singleton</span><span class="sxs-lookup"><span data-stu-id="870e0-165">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="870e0-166">Přechodné</span><span class="sxs-lookup"><span data-stu-id="870e0-166">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="870e0-167">Singleton</span><span class="sxs-lookup"><span data-stu-id="870e0-167">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="870e0-168">Singleton</span><span class="sxs-lookup"><span data-stu-id="870e0-168">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="870e0-169">Singleton</span><span class="sxs-lookup"><span data-stu-id="870e0-169">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="870e0-170">Přechodné</span><span class="sxs-lookup"><span data-stu-id="870e0-170">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="870e0-171">Singleton</span><span class="sxs-lookup"><span data-stu-id="870e0-171">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="870e0-172">Singleton</span><span class="sxs-lookup"><span data-stu-id="870e0-172">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="870e0-173">Singleton</span><span class="sxs-lookup"><span data-stu-id="870e0-173">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="870e0-174">Registrace doplňkových služeb pomocí rozšiřujících metod</span><span class="sxs-lookup"><span data-stu-id="870e0-174">Register additional services with extension methods</span></span>

<span data-ttu-id="870e0-175">Pokud je k dispozici metoda rozšíření kolekce služby pro registraci služby (a `Add{SERVICE_NAME}` její závislé služby, pokud je požadováno), konvence je použít metodu jednotné rozšíření zaregistrovat všechny služby požadované této služby.</span><span class="sxs-lookup"><span data-stu-id="870e0-175">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="870e0-176">Následující kód je příkladem toho, jak přidat další služby do kontejneru pomocí <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>rozšiřujících metod [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) a :</span><span class="sxs-lookup"><span data-stu-id="870e0-176">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="870e0-177">Další informace naleznete <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> v části třída v dokumentaci k rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="870e0-177">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="870e0-178">Životnost služby</span><span class="sxs-lookup"><span data-stu-id="870e0-178">Service lifetimes</span></span>

<span data-ttu-id="870e0-179">Zvolte vhodnou dobu života pro každou registrovanou službu.</span><span class="sxs-lookup"><span data-stu-id="870e0-179">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="870e0-180">ASP.NET základní služby lze konfigurovat s následujícími dobami života:</span><span class="sxs-lookup"><span data-stu-id="870e0-180">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="870e0-181">Přechodné</span><span class="sxs-lookup"><span data-stu-id="870e0-181">Transient</span></span>

<span data-ttu-id="870e0-182">Přechodné služby životnosti (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) jsou vytvořeny pokaždé, když jsou požadovány z kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="870e0-182">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="870e0-183">Tato životnost funguje nejlépe pro lehké služby bez státní příslušnosti.</span><span class="sxs-lookup"><span data-stu-id="870e0-183">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="870e0-184">Rozsahem</span><span class="sxs-lookup"><span data-stu-id="870e0-184">Scoped</span></span>

<span data-ttu-id="870e0-185">Služby životnosti<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>s rozsahem ( ) jsou vytvořeny jednou za požadavek klienta (připojení).</span><span class="sxs-lookup"><span data-stu-id="870e0-185">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="870e0-186">Při použití služby s vymezeným oborem v `Invoke` `InvokeAsync` middleware, vložte službu do metody nebo.</span><span class="sxs-lookup"><span data-stu-id="870e0-186">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="870e0-187">Nevstřikovat prostřednictvím [vstřikování konstruktoru,](xref:mvc/controllers/dependency-injection#constructor-injection) protože nutí službu chovat jako singleton.</span><span class="sxs-lookup"><span data-stu-id="870e0-187">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="870e0-188">Další informace naleznete v tématu <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="870e0-188">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="870e0-189">Singleton</span><span class="sxs-lookup"><span data-stu-id="870e0-189">Singleton</span></span>

<span data-ttu-id="870e0-190">Služby životnosti<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>Singleton ( ) jsou vytvořeny při `Startup.ConfigureServices` prvním požadavku (nebo při spuštění a instance je zadána s registrací služby).</span><span class="sxs-lookup"><span data-stu-id="870e0-190">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="870e0-191">Každý následující požadavek používá stejnou instanci.</span><span class="sxs-lookup"><span data-stu-id="870e0-191">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="870e0-192">Pokud aplikace vyžaduje singleton chování, povolení kontejneru služby ke správě životnosti služby se doporučuje.</span><span class="sxs-lookup"><span data-stu-id="870e0-192">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="870e0-193">Neimplementujte návrhový vzor singleton a zadejte uživatelský kód pro správu životnosti objektu ve třídě.</span><span class="sxs-lookup"><span data-stu-id="870e0-193">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="870e0-194">Je nebezpečné vyřešit službu s rozsahem z singletonu.</span><span class="sxs-lookup"><span data-stu-id="870e0-194">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="870e0-195">Může způsobit, že služba má nesprávný stav při zpracování následných požadavků.</span><span class="sxs-lookup"><span data-stu-id="870e0-195">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="870e0-196">Metody registrace služby</span><span class="sxs-lookup"><span data-stu-id="870e0-196">Service registration methods</span></span>

<span data-ttu-id="870e0-197">Metody rozšíření registrace služby nabízejí přetížení, které jsou užitečné v konkrétních scénářích.</span><span class="sxs-lookup"><span data-stu-id="870e0-197">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="870e0-198">Metoda</span><span class="sxs-lookup"><span data-stu-id="870e0-198">Method</span></span> | <span data-ttu-id="870e0-199">Automaticky</span><span class="sxs-lookup"><span data-stu-id="870e0-199">Automatic</span></span><br><span data-ttu-id="870e0-200">objekt</span><span class="sxs-lookup"><span data-stu-id="870e0-200">object</span></span><br><span data-ttu-id="870e0-201">K dispozici</span><span class="sxs-lookup"><span data-stu-id="870e0-201">disposal</span></span> | <span data-ttu-id="870e0-202">Několik</span><span class="sxs-lookup"><span data-stu-id="870e0-202">Multiple</span></span><br><span data-ttu-id="870e0-203">implementace</span><span class="sxs-lookup"><span data-stu-id="870e0-203">implementations</span></span> | <span data-ttu-id="870e0-204">Předat args</span><span class="sxs-lookup"><span data-stu-id="870e0-204">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="870e0-205">Příklad:</span><span class="sxs-lookup"><span data-stu-id="870e0-205">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="870e0-206">Ano</span><span class="sxs-lookup"><span data-stu-id="870e0-206">Yes</span></span> | <span data-ttu-id="870e0-207">Ano</span><span class="sxs-lookup"><span data-stu-id="870e0-207">Yes</span></span> | <span data-ttu-id="870e0-208">Ne</span><span class="sxs-lookup"><span data-stu-id="870e0-208">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="870e0-209">Příklady:</span><span class="sxs-lookup"><span data-stu-id="870e0-209">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="870e0-210">Ano</span><span class="sxs-lookup"><span data-stu-id="870e0-210">Yes</span></span> | <span data-ttu-id="870e0-211">Ano</span><span class="sxs-lookup"><span data-stu-id="870e0-211">Yes</span></span> | <span data-ttu-id="870e0-212">Ano</span><span class="sxs-lookup"><span data-stu-id="870e0-212">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="870e0-213">Příklad:</span><span class="sxs-lookup"><span data-stu-id="870e0-213">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="870e0-214">Ano</span><span class="sxs-lookup"><span data-stu-id="870e0-214">Yes</span></span> | <span data-ttu-id="870e0-215">Ne</span><span class="sxs-lookup"><span data-stu-id="870e0-215">No</span></span> | <span data-ttu-id="870e0-216">Ne</span><span class="sxs-lookup"><span data-stu-id="870e0-216">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="870e0-217">Příklady:</span><span class="sxs-lookup"><span data-stu-id="870e0-217">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="870e0-218">Ne</span><span class="sxs-lookup"><span data-stu-id="870e0-218">No</span></span> | <span data-ttu-id="870e0-219">Ano</span><span class="sxs-lookup"><span data-stu-id="870e0-219">Yes</span></span> | <span data-ttu-id="870e0-220">Ano</span><span class="sxs-lookup"><span data-stu-id="870e0-220">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="870e0-221">Příklady:</span><span class="sxs-lookup"><span data-stu-id="870e0-221">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="870e0-222">Ne</span><span class="sxs-lookup"><span data-stu-id="870e0-222">No</span></span> | <span data-ttu-id="870e0-223">Ne</span><span class="sxs-lookup"><span data-stu-id="870e0-223">No</span></span> | <span data-ttu-id="870e0-224">Ano</span><span class="sxs-lookup"><span data-stu-id="870e0-224">Yes</span></span> |

<span data-ttu-id="870e0-225">Další informace o likvidaci typů naleznete v části [Likvidace služeb.](#disposal-of-services)</span><span class="sxs-lookup"><span data-stu-id="870e0-225">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="870e0-226">Běžný scénář pro více implementací je [zesměšňování typů pro testování](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="870e0-226">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="870e0-227">`TryAdd{LIFETIME}`metody zaregistrujte službu pouze v případě, že již není registrována implementace.</span><span class="sxs-lookup"><span data-stu-id="870e0-227">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="870e0-228">V následujícím příkladu se první `MyDependency` `IMyDependency`řádek registruje pro .</span><span class="sxs-lookup"><span data-stu-id="870e0-228">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="870e0-229">Druhý řádek nemá žádný `IMyDependency` vliv, protože již má registrovanou implementaci:</span><span class="sxs-lookup"><span data-stu-id="870e0-229">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="870e0-230">Další informace naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="870e0-230">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="870e0-231">[Metody TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) zaregistrujte službu pouze v případě, že již neexistuje implementace *stejného typu*.</span><span class="sxs-lookup"><span data-stu-id="870e0-231">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="870e0-232">Více služeb jsou `IEnumerable<{SERVICE}>`vyřešeny prostřednictvím .</span><span class="sxs-lookup"><span data-stu-id="870e0-232">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="870e0-233">Při registraci služeb vývojář chce přidat instanci pouze v případě, že jeden ze stejného typu ještě nebyl přidán.</span><span class="sxs-lookup"><span data-stu-id="870e0-233">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="870e0-234">Obecně tato metoda se používá autoři knihovny, aby se zabránilo registraci dvou kopií instance v kontejneru.</span><span class="sxs-lookup"><span data-stu-id="870e0-234">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="870e0-235">V následujícím příkladu se první `MyDep` `IMyDep1`řádek registruje pro .</span><span class="sxs-lookup"><span data-stu-id="870e0-235">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="870e0-236">Druhý řádek se `MyDep` `IMyDep2`registruje pro .</span><span class="sxs-lookup"><span data-stu-id="870e0-236">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="870e0-237">Třetí řádek nemá žádný `IMyDep1` účinek, protože `MyDep`již má registrovanou implementaci :</span><span class="sxs-lookup"><span data-stu-id="870e0-237">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="870e0-238">Chování vstřikování konstruktoru</span><span class="sxs-lookup"><span data-stu-id="870e0-238">Constructor injection behavior</span></span>

<span data-ttu-id="870e0-239">Služby lze vyřešit dvěma mechanismy:</span><span class="sxs-lookup"><span data-stu-id="870e0-239">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="870e0-240"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>&ndash; Umožňuje vytváření objektů bez registrace služby v kontejneru vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="870e0-240"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities> &ndash; Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="870e0-241">`ActivatorUtilities`používá se s abstrakcemi směřujícími k uživatelům, jako jsou pomocné spoje značek, řadiče MVC a pořadače modelů.</span><span class="sxs-lookup"><span data-stu-id="870e0-241">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="870e0-242">Konstruktory mohou přijímat argumenty, které nejsou poskytovány vkládání závislostí, ale argumenty musí přiřadit výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="870e0-242">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="870e0-243">Pokud jsou služby `IServiceProvider` vyřešeny `ActivatorUtilities`nebo , [vstřikování konstruktoru](xref:mvc/controllers/dependency-injection#constructor-injection) vyžaduje *veřejný* konstruktor.</span><span class="sxs-lookup"><span data-stu-id="870e0-243">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="870e0-244">Pokud jsou služby vyřešeny `ActivatorUtilities`, [vstřikování konstruktoru](xref:mvc/controllers/dependency-injection#constructor-injection) vyžaduje, aby existoval pouze jeden příslušný konstruktor.</span><span class="sxs-lookup"><span data-stu-id="870e0-244">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="870e0-245">Přetížení konstruktoru jsou podporovány, ale může existovat pouze jedno přetížení, jehož argumenty mohou být splněny vkládáním závislostí.</span><span class="sxs-lookup"><span data-stu-id="870e0-245">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="870e0-246">Kontexty rozhraní entity</span><span class="sxs-lookup"><span data-stu-id="870e0-246">Entity Framework contexts</span></span>

<span data-ttu-id="870e0-247">Kontexty entity Framework jsou obvykle přidány do kontejneru služby pomocí [životnosti oboru,](#service-lifetimes) protože operace databáze webové aplikace jsou obvykle vymezeny na požadavek klienta.</span><span class="sxs-lookup"><span data-stu-id="870e0-247">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="870e0-248">Výchozí doba života je vymezena, pokud životnost není [zadána AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) přetížení při registraci kontextu databáze.</span><span class="sxs-lookup"><span data-stu-id="870e0-248">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="870e0-249">Služby dané životnosti by neměly používat kontext databáze s kratší životností než služba.</span><span class="sxs-lookup"><span data-stu-id="870e0-249">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="870e0-250">Možnosti životnosti a registrace</span><span class="sxs-lookup"><span data-stu-id="870e0-250">Lifetime and registration options</span></span>

<span data-ttu-id="870e0-251">Chcete-li demonstrovat rozdíl mezi možností životnosti a registrace, zvažte následující rozhraní, která představují úkoly jako operaci s jedinečným identifikátorem . `OperationId`</span><span class="sxs-lookup"><span data-stu-id="870e0-251">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="870e0-252">V závislosti na tom, jak je nakonfigurována životnost provozní služby pro následující rozhraní, kontejner poskytuje buď stejnou nebo jinou instanci služby na vyžádání třídou:</span><span class="sxs-lookup"><span data-stu-id="870e0-252">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="870e0-253">Rozhraní jsou implementovány `Operation` ve třídě.</span><span class="sxs-lookup"><span data-stu-id="870e0-253">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="870e0-254">Konstruktor `Operation` generuje identifikátor GUID, pokud není zadán:</span><span class="sxs-lookup"><span data-stu-id="870e0-254">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="870e0-255">Je `OperationService` registrován, který závisí na `Operation` jednotlivých typech.</span><span class="sxs-lookup"><span data-stu-id="870e0-255">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="870e0-256">Pokud `OperationService` je požadováno prostřednictvím vkládání závislostí, obdrží buď novou instanci každé služby nebo existující instance na základě životnosti závislé služby.</span><span class="sxs-lookup"><span data-stu-id="870e0-256">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="870e0-257">Při přechodných služeb jsou vytvořeny na vyžádání `OperationId` z `IOperationTransient` kontejneru, `OperationId` služby `OperationService`se liší od .</span><span class="sxs-lookup"><span data-stu-id="870e0-257">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="870e0-258">`OperationService`obdrží novou instanci `IOperationTransient` třídy.</span><span class="sxs-lookup"><span data-stu-id="870e0-258">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="870e0-259">Nová instance poskytuje jiný `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="870e0-259">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="870e0-260">Při scoped služby jsou vytvořeny `OperationId` na `IOperationScoped` požadavek klienta, `OperationService` služby je stejný jako v rámci požadavku klienta.</span><span class="sxs-lookup"><span data-stu-id="870e0-260">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="870e0-261">Napříč požadavky klientů obě služby sdílejí jinou `OperationId` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="870e0-261">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="870e0-262">Při singleton a singleton instance služby jsou vytvořeny jednou a používají `OperationId` se ve všech požadavcích klientů a všechny služby, je konstantní ve všech požadavcích na služby.</span><span class="sxs-lookup"><span data-stu-id="870e0-262">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="870e0-263">V `Startup.ConfigureServices`, každý typ je přidán do kontejneru podle jeho pojmenované životnosti:</span><span class="sxs-lookup"><span data-stu-id="870e0-263">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="870e0-264">Služba `IOperationSingletonInstance` používá konkrétní instanci se známým ID aplikace `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="870e0-264">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="870e0-265">Je jasné, kdy je tento typ používán (jeho IDENTIFIKÁTOR GUID je všechny nuly).</span><span class="sxs-lookup"><span data-stu-id="870e0-265">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="870e0-266">Ukázková aplikace ukazuje životnost objektů v rámci jednotlivých požadavků a mezi nimi.</span><span class="sxs-lookup"><span data-stu-id="870e0-266">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="870e0-267">Ukázkové `IndexModel` aplikace požaduje každý druh `IOperation` typu `OperationService`a .</span><span class="sxs-lookup"><span data-stu-id="870e0-267">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="870e0-268">Stránka pak zobrazí všechny `OperationId` hodnoty třídy modelu stránky a služby prostřednictvím přiřazení vlastností:</span><span class="sxs-lookup"><span data-stu-id="870e0-268">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="870e0-269">Dva následující výstupy ukazují výsledky dvou požadavků:</span><span class="sxs-lookup"><span data-stu-id="870e0-269">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="870e0-270">**První žádost:**</span><span class="sxs-lookup"><span data-stu-id="870e0-270">**First request:**</span></span>

<span data-ttu-id="870e0-271">Provoz řídicí jednotky:</span><span class="sxs-lookup"><span data-stu-id="870e0-271">Controller operations:</span></span>

<span data-ttu-id="870e0-272">Přechodné: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="870e0-272">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="870e0-273">Rozsah: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="870e0-273">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="870e0-274">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="870e0-274">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="870e0-275">Instance: 00000000-0000-0000-0000-000000000</span><span class="sxs-lookup"><span data-stu-id="870e0-275">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="870e0-276">`OperationService`Operace:</span><span class="sxs-lookup"><span data-stu-id="870e0-276">`OperationService` operations:</span></span>

<span data-ttu-id="870e0-277">Přechodné: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="870e0-277">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="870e0-278">Rozsah: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="870e0-278">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="870e0-279">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="870e0-279">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="870e0-280">Instance: 00000000-0000-0000-0000-000000000</span><span class="sxs-lookup"><span data-stu-id="870e0-280">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="870e0-281">**Druhý požadavek:**</span><span class="sxs-lookup"><span data-stu-id="870e0-281">**Second request:**</span></span>

<span data-ttu-id="870e0-282">Provoz řídicí jednotky:</span><span class="sxs-lookup"><span data-stu-id="870e0-282">Controller operations:</span></span>

<span data-ttu-id="870e0-283">Přechodné: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="870e0-283">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="870e0-284">Rozsah: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="870e0-284">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="870e0-285">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="870e0-285">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="870e0-286">Instance: 00000000-0000-0000-0000-000000000</span><span class="sxs-lookup"><span data-stu-id="870e0-286">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="870e0-287">`OperationService`Operace:</span><span class="sxs-lookup"><span data-stu-id="870e0-287">`OperationService` operations:</span></span>

<span data-ttu-id="870e0-288">Přechodné: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="870e0-288">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="870e0-289">Rozsah: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="870e0-289">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="870e0-290">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="870e0-290">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="870e0-291">Instance: 00000000-0000-0000-0000-000000000</span><span class="sxs-lookup"><span data-stu-id="870e0-291">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="870e0-292">Sledujte, `OperationId` které z hodnot se liší v rámci požadavku a mezi požadavky:</span><span class="sxs-lookup"><span data-stu-id="870e0-292">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="870e0-293">*Přechodné* objekty se vždy liší.</span><span class="sxs-lookup"><span data-stu-id="870e0-293">*Transient* objects are always different.</span></span> <span data-ttu-id="870e0-294">Přechodná `OperationId` hodnota pro první a druhé požadavky klienta `OperationService` se liší pro operace i napříč požadavky klientů.</span><span class="sxs-lookup"><span data-stu-id="870e0-294">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="870e0-295">Pro každý požadavek na službu a požadavek klienta je k dispozici nová instance.</span><span class="sxs-lookup"><span data-stu-id="870e0-295">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="870e0-296">*Scoped* objekty jsou stejné v rámci požadavku klienta, ale liší se mezi požadavky klienta.</span><span class="sxs-lookup"><span data-stu-id="870e0-296">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="870e0-297">*Singleton* objekty jsou stejné pro každý objekt a `Operation` každý požadavek `Startup.ConfigureServices`bez ohledu na to, zda je k dispozici instance v .</span><span class="sxs-lookup"><span data-stu-id="870e0-297">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="870e0-298">Volejte služby z hlavního</span><span class="sxs-lookup"><span data-stu-id="870e0-298">Call services from main</span></span>

<span data-ttu-id="870e0-299">Vytvořte <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> s [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) vyřešit službu s oborem v rámci oboru aplikace.</span><span class="sxs-lookup"><span data-stu-id="870e0-299">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="870e0-300">Tento přístup je užitečný pro přístup ke službě s vymezeným oborem při spuštění ke spuštění úloh inicializace.</span><span class="sxs-lookup"><span data-stu-id="870e0-300">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="870e0-301">Následující příklad ukazuje, jak získat `MyScopedService` kontext `Program.Main`pro in :</span><span class="sxs-lookup"><span data-stu-id="870e0-301">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="870e0-302">Ověření oboru</span><span class="sxs-lookup"><span data-stu-id="870e0-302">Scope validation</span></span>

<span data-ttu-id="870e0-303">Když je aplikace spuštěná ve vývojovém prostředí a volá [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) k sestavení hostitele, výchozí poskytovatel služeb provádí kontroly, aby ověřil, že:</span><span class="sxs-lookup"><span data-stu-id="870e0-303">When the app is running in the Development environment and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="870e0-304">Služby s oborem nejsou přímo ani nepřímo vyřešeny od kořenového poskytovatele služeb.</span><span class="sxs-lookup"><span data-stu-id="870e0-304">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="870e0-305">Oborové služby nejsou přímo nebo nepřímo vloženy do singletons.</span><span class="sxs-lookup"><span data-stu-id="870e0-305">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="870e0-306">Kořenový poskytovatel služeb <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> je vytvořen při volání.</span><span class="sxs-lookup"><span data-stu-id="870e0-306">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="870e0-307">Životnost kořenového poskytovatele služeb odpovídá životnosti aplikace nebo serveru, když poskytovatel začne s aplikací a je uvolněn, když se aplikace vypne.</span><span class="sxs-lookup"><span data-stu-id="870e0-307">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="870e0-308">Služby s vymezeným oborem jsou uvolněny kontejnerem, který je vytvořil.</span><span class="sxs-lookup"><span data-stu-id="870e0-308">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="870e0-309">Pokud je služba s vymezeným oborem vytvořena v kořenovém kontejneru, životnost služby je efektivně povýšena na singleton, protože je uvolněna pouze kořenovým kontejnerem při vypnutí aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="870e0-309">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="870e0-310">Ověřování oborů služby zachytí tyto situace, když `BuildServiceProvider` je volána.</span><span class="sxs-lookup"><span data-stu-id="870e0-310">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="870e0-311">Další informace naleznete v tématu <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="870e0-311">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="870e0-312">Vyžádat si služby</span><span class="sxs-lookup"><span data-stu-id="870e0-312">Request Services</span></span>

<span data-ttu-id="870e0-313">Služby, které jsou k `HttpContext` dispozici v rámci ASP.NET základní požadavek z jsou vystaveny prostřednictvím [Kolekce HttpContext.RequestServices.](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices)</span><span class="sxs-lookup"><span data-stu-id="870e0-313">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="870e0-314">Služby Žádosti představují služby nakonfigurované a požadované jako součást aplikace.</span><span class="sxs-lookup"><span data-stu-id="870e0-314">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="870e0-315">Když objekty určují závislosti, jsou splněny typy `RequestServices`nalezenými v , nikoli `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="870e0-315">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="870e0-316">Obecně platí, že aplikace by neměla používat tyto vlastnosti přímo.</span><span class="sxs-lookup"><span data-stu-id="870e0-316">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="870e0-317">Místo toho požádejte o typy, které vyžadují třídy prostřednictvím konstruktorů třídy a povolit rozhraní pro vložení závislostí.</span><span class="sxs-lookup"><span data-stu-id="870e0-317">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span> <span data-ttu-id="870e0-318">To dává třídy, které jsou snadněji testovat.</span><span class="sxs-lookup"><span data-stu-id="870e0-318">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="870e0-319">Upřednostňovat požadování závislostí jako parametry `RequestServices` konstruktoru pro přístup k kolekci.</span><span class="sxs-lookup"><span data-stu-id="870e0-319">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="870e0-320">Návrhové služby pro vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="870e0-320">Design services for dependency injection</span></span>

<span data-ttu-id="870e0-321">Osvědčenými postupy jsou:</span><span class="sxs-lookup"><span data-stu-id="870e0-321">Best practices are to:</span></span>

* <span data-ttu-id="870e0-322">Navrhnout služby pro použití vkládání závislostí k získání jejich závislosti.</span><span class="sxs-lookup"><span data-stu-id="870e0-322">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="870e0-323">Vyhněte se stavové, statické třídy a členy.</span><span class="sxs-lookup"><span data-stu-id="870e0-323">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="870e0-324">Navrhněte aplikace tak, aby místo toho používaly služby singleton, které se vyhýbají vytváření globálního stavu.</span><span class="sxs-lookup"><span data-stu-id="870e0-324">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="870e0-325">Vyhněte se přímé konkretizovat závislé třídy v rámci služeb.</span><span class="sxs-lookup"><span data-stu-id="870e0-325">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="870e0-326">Přímá instance propojuje kód s konkrétní implementací.</span><span class="sxs-lookup"><span data-stu-id="870e0-326">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="870e0-327">Třídy aplikací jsou malé, dobře zapracované a snadno testované.</span><span class="sxs-lookup"><span data-stu-id="870e0-327">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="870e0-328">Pokud se zdá, že třída má příliš mnoho injekčně závislých, je to obecně známkou toho, že třída má příliš mnoho odpovědností a porušuje [zásadu jedné odpovědnosti (SRP).](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)</span><span class="sxs-lookup"><span data-stu-id="870e0-328">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="870e0-329">Pokus o refaktorování třídy přesunutím některé z jeho odpovědností do nové třídy.</span><span class="sxs-lookup"><span data-stu-id="870e0-329">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="870e0-330">Mějte na paměti, že třídy modelu stránky Razor Pages a třídy kontroleru MVC by se měly zaměřit na obavy o ui.</span><span class="sxs-lookup"><span data-stu-id="870e0-330">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="870e0-331">Obchodní pravidla a podrobnosti implementace přístupu k údajům by měly být uchovávány ve třídách odpovídajících těmto [samostatným obavám](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="870e0-331">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="870e0-332">Likvidace služeb</span><span class="sxs-lookup"><span data-stu-id="870e0-332">Disposal of services</span></span>

<span data-ttu-id="870e0-333">Kontejner volá <xref:System.IDisposable.Dispose*> typy, které <xref:System.IDisposable> vytvoří.</span><span class="sxs-lookup"><span data-stu-id="870e0-333">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="870e0-334">Pokud je instance přidána do kontejneru podle uživatelského kódu, není automaticky uvolněna.</span><span class="sxs-lookup"><span data-stu-id="870e0-334">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="870e0-335">V následujícím příkladu jsou služby vytvořeny kontejnerem služby a uvolněny automaticky:</span><span class="sxs-lookup"><span data-stu-id="870e0-335">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="870e0-336">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="870e0-336">In the following example:</span></span>

* <span data-ttu-id="870e0-337">Instance služby nejsou vytvořeny kontejnerem služby.</span><span class="sxs-lookup"><span data-stu-id="870e0-337">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="870e0-338">Zamýšlené životnosti nejsou známy v rámci.</span><span class="sxs-lookup"><span data-stu-id="870e0-338">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="870e0-339">Rozhraní framework nevyřazené služby automaticky.</span><span class="sxs-lookup"><span data-stu-id="870e0-339">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="870e0-340">Pokud služby nejsou explicitně uvolněny v kódu vývojáře, přetrvávají, dokud se aplikace nevypne.</span><span class="sxs-lookup"><span data-stu-id="870e0-340">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

<span data-ttu-id="870e0-341">Diskuse o možnostech likvidace služeb naleznete v tématu [Čtyři způsoby, jak zlikvidovat IDisposables v ASP.NET Core](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/).</span><span class="sxs-lookup"><span data-stu-id="870e0-341">For a discussion of service disposal options, see [Four ways to dispose IDisposables in ASP.NET Core](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/).</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="870e0-342">Výchozí nahrazení kontejneru služby</span><span class="sxs-lookup"><span data-stu-id="870e0-342">Default service container replacement</span></span>

<span data-ttu-id="870e0-343">Integrovaný kontejner služeb je navržen tak, aby sloužil potřebám architektury a většiny spotřebitelských aplikací.</span><span class="sxs-lookup"><span data-stu-id="870e0-343">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="870e0-344">Doporučujeme používat předdefinovaný kontejner, pokud nepotřebujete určitou funkci, kterou integrovaný kontejner nepodporuje, například:</span><span class="sxs-lookup"><span data-stu-id="870e0-344">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="870e0-345">Vstřikování majetku</span><span class="sxs-lookup"><span data-stu-id="870e0-345">Property injection</span></span>
* <span data-ttu-id="870e0-346">Injekce na základě názvu</span><span class="sxs-lookup"><span data-stu-id="870e0-346">Injection based on name</span></span>
* <span data-ttu-id="870e0-347">Dětské kontejnery</span><span class="sxs-lookup"><span data-stu-id="870e0-347">Child containers</span></span>
* <span data-ttu-id="870e0-348">Vlastní správa životnosti</span><span class="sxs-lookup"><span data-stu-id="870e0-348">Custom lifetime management</span></span>
* <span data-ttu-id="870e0-349">`Func<T>`podpora opožděné inicializace</span><span class="sxs-lookup"><span data-stu-id="870e0-349">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="870e0-350">Registrace založená na úmluvách</span><span class="sxs-lookup"><span data-stu-id="870e0-350">Convention-based registration</span></span>

<span data-ttu-id="870e0-351">Následující kontejnery třetích stran lze použít s aplikacemi ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="870e0-351">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="870e0-352">Autofac</span><span class="sxs-lookup"><span data-stu-id="870e0-352">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="870e0-353">DryIoc</span><span class="sxs-lookup"><span data-stu-id="870e0-353">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="870e0-354">Milost</span><span class="sxs-lookup"><span data-stu-id="870e0-354">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="870e0-355">Lehkývstřik</span><span class="sxs-lookup"><span data-stu-id="870e0-355">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="870e0-356">Lamar</span><span class="sxs-lookup"><span data-stu-id="870e0-356">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="870e0-357">Skrýš</span><span class="sxs-lookup"><span data-stu-id="870e0-357">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="870e0-358">Unity</span><span class="sxs-lookup"><span data-stu-id="870e0-358">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="870e0-359">Bezpečnost vlákna</span><span class="sxs-lookup"><span data-stu-id="870e0-359">Thread safety</span></span>

<span data-ttu-id="870e0-360">Vytvořte služby singleton bezpečné pro přístup z více vláken.</span><span class="sxs-lookup"><span data-stu-id="870e0-360">Create thread-safe singleton services.</span></span> <span data-ttu-id="870e0-361">Pokud je služba singleton závislá na přechodné službě, může přechodná služba také vyžadovat bezpečnost podprocesu v závislosti na tom, jak je používána singletonem.</span><span class="sxs-lookup"><span data-stu-id="870e0-361">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="870e0-362">Metoda výroby jedné služby, jako je například druhý argument [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), nemusí být bezpečná pro přístup z více vláken.</span><span class="sxs-lookup"><span data-stu-id="870e0-362">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="870e0-363">Stejně jako`static`typ ( ) konstruktor, je zaručeno, že bude volán jednou jedním vláknem.</span><span class="sxs-lookup"><span data-stu-id="870e0-363">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="870e0-364">Doporučení</span><span class="sxs-lookup"><span data-stu-id="870e0-364">Recommendations</span></span>

* <span data-ttu-id="870e0-365">`async/await`a `Task` řešení služby založené na tom není podporováno.</span><span class="sxs-lookup"><span data-stu-id="870e0-365">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="870e0-366">C# nepodporuje asynchronní konstruktory; proto je doporučenývzor použít asynchronní metody po synchronní řešení služby.</span><span class="sxs-lookup"><span data-stu-id="870e0-366">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="870e0-367">Vyhněte se ukládání dat a konfigurace přímo v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="870e0-367">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="870e0-368">Například nákupní košík uživatele by obvykle neměl být přidán do kontejneru služeb.</span><span class="sxs-lookup"><span data-stu-id="870e0-368">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="870e0-369">Konfigurace by měla používat [vzor voleb](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="870e0-369">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="870e0-370">Podobně se vyhněte "držitel dat" objekty, které existují pouze umožnit přístup k některé jiné objekty.</span><span class="sxs-lookup"><span data-stu-id="870e0-370">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="870e0-371">Je lepší požádat o skutečnou položku prostřednictvím DI.</span><span class="sxs-lookup"><span data-stu-id="870e0-371">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="870e0-372">Vyhněte se statickému přístupu ke službám (například staticky zadáváním [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) pro použití jinde).</span><span class="sxs-lookup"><span data-stu-id="870e0-372">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="870e0-373">Nepoužívejte *vzor servisního lokátoru*.</span><span class="sxs-lookup"><span data-stu-id="870e0-373">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="870e0-374">Například nevyvolávejte <xref:System.IServiceProvider.GetService*> získat instanci služby, pokud můžete použít DI místo:</span><span class="sxs-lookup"><span data-stu-id="870e0-374">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="870e0-375">**Nesprávné:**</span><span class="sxs-lookup"><span data-stu-id="870e0-375">**Incorrect:**</span></span>

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

  <span data-ttu-id="870e0-376">**Správně**:</span><span class="sxs-lookup"><span data-stu-id="870e0-376">**Correct**:</span></span>

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

* <span data-ttu-id="870e0-377">Další varianta lokátoru služby, které je třeba se vyhnout, je vstřikování mincovny, která řeší závislosti za běhu.</span><span class="sxs-lookup"><span data-stu-id="870e0-377">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="870e0-378">Obě tyto postupy kombinují [inverzi strategií řízení.](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)</span><span class="sxs-lookup"><span data-stu-id="870e0-378">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

* <span data-ttu-id="870e0-379">Vyhněte `HttpContext` se statickému přístupu (například [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="870e0-379">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="870e0-380">Stejně jako všechny sady doporučení, může dojít k situacím, kdy je vyžadováno ignorování doporučení.</span><span class="sxs-lookup"><span data-stu-id="870e0-380">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="870e0-381">Výjimky jsou&mdash;vzácné většinou zvláštní případy v rámci samotném.</span><span class="sxs-lookup"><span data-stu-id="870e0-381">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="870e0-382">DI je *alternativou* ke vzorům statického/globálního přístupu k objektům.</span><span class="sxs-lookup"><span data-stu-id="870e0-382">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="870e0-383">Je možné, že nebudete moci realizovat výhody DI, pokud jej smícháte s přístupem ke statickému objektu.</span><span class="sxs-lookup"><span data-stu-id="870e0-383">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="870e0-384">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="870e0-384">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="870e0-385">Zápis čistého kódu v ASP.NET jádra s vkládáním závislostí (MSDN)</span><span class="sxs-lookup"><span data-stu-id="870e0-385">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="870e0-386">Princip explicitních závislostí</span><span class="sxs-lookup"><span data-stu-id="870e0-386">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="870e0-387">Inverze řídicích kontejnerů a vzor vstřikování závislostí (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="870e0-387">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="870e0-388">Jak zaregistrovat službu s více rozhraními v ASP.NET Core DI</span><span class="sxs-lookup"><span data-stu-id="870e0-388">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="870e0-389">ASP.NET Core podporuje vzor návrhu softwaru vkládání závislostí (DI), což je technika pro dosažení [inverze řízení (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) mezi třídami a jejich závislostmi.</span><span class="sxs-lookup"><span data-stu-id="870e0-389">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="870e0-390">Další informace specifické pro vkládání závislostí v rámci <xref:mvc/controllers/dependency-injection>řadičů MVC naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="870e0-390">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="870e0-391">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="870e0-391">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="870e0-392">Přehled vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="870e0-392">Overview of dependency injection</span></span>

<span data-ttu-id="870e0-393">*Závislost* je libovolný objekt, který vyžaduje jiný objekt.</span><span class="sxs-lookup"><span data-stu-id="870e0-393">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="870e0-394">Prozkoumejte `MyDependency` následující třídu `WriteMessage` metodou, na které závisí jiné třídy v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="870e0-394">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="870e0-395">Instance třídy `MyDependency` lze vytvořit tak, `WriteMessage` aby metoda k dispozici třídy.</span><span class="sxs-lookup"><span data-stu-id="870e0-395">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="870e0-396">Třída `MyDependency` je závislost `IndexModel` třídy:</span><span class="sxs-lookup"><span data-stu-id="870e0-396">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="870e0-397">Třída vytvoří a přímo `MyDependency` závisí na instanci.</span><span class="sxs-lookup"><span data-stu-id="870e0-397">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="870e0-398">Závislosti kódu (například v předchozím příkladu) jsou problematické a je třeba se vyhnout z následujících důvodů:</span><span class="sxs-lookup"><span data-stu-id="870e0-398">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="870e0-399">Chcete-li nahradit `MyDependency` jinou implementaci, musí být třída změněna.</span><span class="sxs-lookup"><span data-stu-id="870e0-399">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="870e0-400">Pokud `MyDependency` má závislosti, musí být nakonfigurovány třídou.</span><span class="sxs-lookup"><span data-stu-id="870e0-400">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="870e0-401">Ve velkém projektu s více `MyDependency`tříd v závislosti na , konfigurační kód se rozptýlí v celé aplikaci.</span><span class="sxs-lookup"><span data-stu-id="870e0-401">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="870e0-402">Tato implementace je obtížné testování částí.</span><span class="sxs-lookup"><span data-stu-id="870e0-402">This implementation is difficult to unit test.</span></span> <span data-ttu-id="870e0-403">Aplikace by měla používat mock `MyDependency` nebo stub třídy, což není možné s tímto přístupem.</span><span class="sxs-lookup"><span data-stu-id="870e0-403">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="870e0-404">Vkládání závislostí řeší tyto problémy prostřednictvím:</span><span class="sxs-lookup"><span data-stu-id="870e0-404">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="870e0-405">Použití rozhraní nebo základní třídy k abstrahovat implementaci závislostí.</span><span class="sxs-lookup"><span data-stu-id="870e0-405">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="870e0-406">Registrace závislosti v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="870e0-406">Registration of the dependency in a service container.</span></span> <span data-ttu-id="870e0-407">ASP.NET Core poskytuje vestavěný kontejner <xref:System.IServiceProvider>služeb .</span><span class="sxs-lookup"><span data-stu-id="870e0-407">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="870e0-408">Služby jsou registrovány `Startup.ConfigureServices` metodou aplikace.</span><span class="sxs-lookup"><span data-stu-id="870e0-408">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="870e0-409">*Vstřikování* služby do konstruktoru třídy, kde se používá.</span><span class="sxs-lookup"><span data-stu-id="870e0-409">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="870e0-410">Rámec přebírá odpovědnost za vytvoření instance závislosti a její likvidaci, když už není potřeba.</span><span class="sxs-lookup"><span data-stu-id="870e0-410">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="870e0-411">V [ukázkové aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) `IMyDependency` rozhraní definuje metodu, kterou služba poskytuje aplikaci:</span><span class="sxs-lookup"><span data-stu-id="870e0-411">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="870e0-412">Toto rozhraní je implementováno konkrétním typem: `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="870e0-412">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="870e0-413">`MyDependency`požaduje v <xref:Microsoft.Extensions.Logging.ILogger`1> jeho konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="870e0-413">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="870e0-414">Není neobvyklé používat vkládání závislostí zřetězenou módou.</span><span class="sxs-lookup"><span data-stu-id="870e0-414">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="870e0-415">Každá požadovaná závislost zase požaduje vlastní závislosti.</span><span class="sxs-lookup"><span data-stu-id="870e0-415">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="870e0-416">Kontejner řeší závislosti v grafu a vrátí plně vyřešené služby.</span><span class="sxs-lookup"><span data-stu-id="870e0-416">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="870e0-417">Kolektivní sada závislostí, které musí být vyřešeny, se obvykle označuje jako *strom závislostí*, *graf závislostí*nebo *graf objektů*.</span><span class="sxs-lookup"><span data-stu-id="870e0-417">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="870e0-418">`IMyDependency`a `ILogger<TCategoryName>` musí být registrovány v servisním kontejneru.</span><span class="sxs-lookup"><span data-stu-id="870e0-418">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="870e0-419">`IMyDependency`je registrován `Startup.ConfigureServices`v .</span><span class="sxs-lookup"><span data-stu-id="870e0-419">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="870e0-420">`ILogger<TCategoryName>`je registrována infrastrukturou protokolování abstrakcí, takže se jedná o službu poskytovanou v [rámci,](#framework-provided-services) kterou standardně registruje.</span><span class="sxs-lookup"><span data-stu-id="870e0-420">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="870e0-421">Kontejner řeší `ILogger<TCategoryName>` využitím [(obecné) otevřené typy](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), což eliminuje potřebu registrovat každý [(obecný) konstruovaný typ](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="870e0-421">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="870e0-422">V ukázkové aplikaci je `IMyDependency` služba registrována s konkrétním typem `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="870e0-422">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="870e0-423">Registrace obory životnost i do provozní doby životnosti jednoho požadavku.</span><span class="sxs-lookup"><span data-stu-id="870e0-423">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="870e0-424">[Životnost služby](#service-lifetimes) jsou popsány dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="870e0-424">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="870e0-425">Každá `services.Add{SERVICE_NAME}` metoda rozšíření přidává (a potenciálně konfiguruje) služby.</span><span class="sxs-lookup"><span data-stu-id="870e0-425">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="870e0-426">Například `services.AddMvc()` přidá služby Razor Pages a MVC vyžadují.</span><span class="sxs-lookup"><span data-stu-id="870e0-426">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="870e0-427">Doporučujeme, aby aplikace dodržovaly tuto konvenci.</span><span class="sxs-lookup"><span data-stu-id="870e0-427">We recommended that apps follow this convention.</span></span> <span data-ttu-id="870e0-428">Umístěte metody rozšíření do oboru názvů [Microsoft.Extensions.DependencyInjection,](/dotnet/api/microsoft.extensions.dependencyinjection) abyste zapouzdřili skupiny registrací služby.</span><span class="sxs-lookup"><span data-stu-id="870e0-428">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="870e0-429">Pokud konstruktor služby vyžaduje [vestavěný typ](/dotnet/csharp/language-reference/keywords/built-in-types-table), `string`například , typ může být vložen pomocí [konfigurace](xref:fundamentals/configuration/index) nebo [vzoru voleb](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="870e0-429">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="870e0-430">Instance služby je požadována prostřednictvím konstruktoru třídy, kde je služba používána a přiřazena k soukromému poli.</span><span class="sxs-lookup"><span data-stu-id="870e0-430">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="870e0-431">Toto pole se používá pro přístup ke službě podle potřeby v celé třídě.</span><span class="sxs-lookup"><span data-stu-id="870e0-431">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="870e0-432">V ukázkové aplikaci je `IMyDependency` instance požadována a `WriteMessage` použita k volání metody služby:</span><span class="sxs-lookup"><span data-stu-id="870e0-432">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="870e0-433">Služby vložené do startupu</span><span class="sxs-lookup"><span data-stu-id="870e0-433">Services injected into Startup</span></span>

<span data-ttu-id="870e0-434">Při použití obecného `Startup` hostitele (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span><span class="sxs-lookup"><span data-stu-id="870e0-434">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="870e0-435">Služby mohou být `Startup.Configure`vloženy do :</span><span class="sxs-lookup"><span data-stu-id="870e0-435">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="870e0-436">Další informace naleznete v tématu <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="870e0-436">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="870e0-437">Rámcové služby</span><span class="sxs-lookup"><span data-stu-id="870e0-437">Framework-provided services</span></span>

<span data-ttu-id="870e0-438">Metoda `Startup.ConfigureServices` je zodpovědná za definování služeb, které aplikace používá, včetně funkcí platformy, jako je například Core framework entity a ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="870e0-438">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="870e0-439">Zpočátku `IServiceCollection` poskytované `ConfigureServices` má služby definované v rámci v závislosti na [tom, jak byl nakonfigurován hostitel](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="870e0-439">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="870e0-440">Není neobvyklé, že aplikace založená na šabloně ASP.NET Core má stovky služeb registrovaných v rámci.</span><span class="sxs-lookup"><span data-stu-id="870e0-440">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="870e0-441">Malý vzorek služeb registrovaných v rámci je uveden v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="870e0-441">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="870e0-442">Typ služby</span><span class="sxs-lookup"><span data-stu-id="870e0-442">Service Type</span></span> | <span data-ttu-id="870e0-443">Doba platnosti</span><span class="sxs-lookup"><span data-stu-id="870e0-443">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="870e0-444">Přechodné</span><span class="sxs-lookup"><span data-stu-id="870e0-444">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="870e0-445">Singleton</span><span class="sxs-lookup"><span data-stu-id="870e0-445">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="870e0-446">Singleton</span><span class="sxs-lookup"><span data-stu-id="870e0-446">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="870e0-447">Singleton</span><span class="sxs-lookup"><span data-stu-id="870e0-447">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="870e0-448">Přechodné</span><span class="sxs-lookup"><span data-stu-id="870e0-448">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="870e0-449">Singleton</span><span class="sxs-lookup"><span data-stu-id="870e0-449">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="870e0-450">Přechodné</span><span class="sxs-lookup"><span data-stu-id="870e0-450">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="870e0-451">Singleton</span><span class="sxs-lookup"><span data-stu-id="870e0-451">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="870e0-452">Singleton</span><span class="sxs-lookup"><span data-stu-id="870e0-452">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="870e0-453">Singleton</span><span class="sxs-lookup"><span data-stu-id="870e0-453">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="870e0-454">Přechodné</span><span class="sxs-lookup"><span data-stu-id="870e0-454">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="870e0-455">Singleton</span><span class="sxs-lookup"><span data-stu-id="870e0-455">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="870e0-456">Singleton</span><span class="sxs-lookup"><span data-stu-id="870e0-456">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="870e0-457">Singleton</span><span class="sxs-lookup"><span data-stu-id="870e0-457">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="870e0-458">Registrace doplňkových služeb pomocí rozšiřujících metod</span><span class="sxs-lookup"><span data-stu-id="870e0-458">Register additional services with extension methods</span></span>

<span data-ttu-id="870e0-459">Pokud je k dispozici metoda rozšíření kolekce služby pro registraci služby (a `Add{SERVICE_NAME}` její závislé služby, pokud je požadováno), konvence je použít metodu jednotné rozšíření zaregistrovat všechny služby požadované této služby.</span><span class="sxs-lookup"><span data-stu-id="870e0-459">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="870e0-460">Následující kód je příkladem toho, jak přidat další služby do kontejneru pomocí <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>rozšiřujících metod [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) a :</span><span class="sxs-lookup"><span data-stu-id="870e0-460">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="870e0-461">Další informace naleznete <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> v části třída v dokumentaci k rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="870e0-461">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="870e0-462">Životnost služby</span><span class="sxs-lookup"><span data-stu-id="870e0-462">Service lifetimes</span></span>

<span data-ttu-id="870e0-463">Zvolte vhodnou dobu života pro každou registrovanou službu.</span><span class="sxs-lookup"><span data-stu-id="870e0-463">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="870e0-464">ASP.NET základní služby lze konfigurovat s následujícími dobami života:</span><span class="sxs-lookup"><span data-stu-id="870e0-464">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="870e0-465">Přechodné</span><span class="sxs-lookup"><span data-stu-id="870e0-465">Transient</span></span>

<span data-ttu-id="870e0-466">Přechodné služby životnosti (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) jsou vytvořeny pokaždé, když jsou požadovány z kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="870e0-466">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="870e0-467">Tato životnost funguje nejlépe pro lehké služby bez státní příslušnosti.</span><span class="sxs-lookup"><span data-stu-id="870e0-467">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="870e0-468">Rozsahem</span><span class="sxs-lookup"><span data-stu-id="870e0-468">Scoped</span></span>

<span data-ttu-id="870e0-469">Služby životnosti<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>s rozsahem ( ) jsou vytvořeny jednou za požadavek klienta (připojení).</span><span class="sxs-lookup"><span data-stu-id="870e0-469">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="870e0-470">Při použití služby s vymezeným oborem v `Invoke` `InvokeAsync` middleware, vložte službu do metody nebo.</span><span class="sxs-lookup"><span data-stu-id="870e0-470">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="870e0-471">Nevstřikovat prostřednictvím [vstřikování konstruktoru,](xref:mvc/controllers/dependency-injection#constructor-injection) protože nutí službu chovat jako singleton.</span><span class="sxs-lookup"><span data-stu-id="870e0-471">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="870e0-472">Další informace naleznete v tématu <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="870e0-472">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="870e0-473">Singleton</span><span class="sxs-lookup"><span data-stu-id="870e0-473">Singleton</span></span>

<span data-ttu-id="870e0-474">Služby životnosti<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>Singleton ( ) jsou vytvořeny při `Startup.ConfigureServices` prvním požadavku (nebo při spuštění a instance je zadána s registrací služby).</span><span class="sxs-lookup"><span data-stu-id="870e0-474">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="870e0-475">Každý následující požadavek používá stejnou instanci.</span><span class="sxs-lookup"><span data-stu-id="870e0-475">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="870e0-476">Pokud aplikace vyžaduje singleton chování, povolení kontejneru služby ke správě životnosti služby se doporučuje.</span><span class="sxs-lookup"><span data-stu-id="870e0-476">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="870e0-477">Neimplementujte návrhový vzor singleton a zadejte uživatelský kód pro správu životnosti objektu ve třídě.</span><span class="sxs-lookup"><span data-stu-id="870e0-477">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="870e0-478">Je nebezpečné vyřešit službu s rozsahem z singletonu.</span><span class="sxs-lookup"><span data-stu-id="870e0-478">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="870e0-479">Může způsobit, že služba má nesprávný stav při zpracování následných požadavků.</span><span class="sxs-lookup"><span data-stu-id="870e0-479">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="870e0-480">Metody registrace služby</span><span class="sxs-lookup"><span data-stu-id="870e0-480">Service registration methods</span></span>

<span data-ttu-id="870e0-481">Metody rozšíření registrace služby nabízejí přetížení, které jsou užitečné v konkrétních scénářích.</span><span class="sxs-lookup"><span data-stu-id="870e0-481">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="870e0-482">Metoda</span><span class="sxs-lookup"><span data-stu-id="870e0-482">Method</span></span> | <span data-ttu-id="870e0-483">Automaticky</span><span class="sxs-lookup"><span data-stu-id="870e0-483">Automatic</span></span><br><span data-ttu-id="870e0-484">objekt</span><span class="sxs-lookup"><span data-stu-id="870e0-484">object</span></span><br><span data-ttu-id="870e0-485">K dispozici</span><span class="sxs-lookup"><span data-stu-id="870e0-485">disposal</span></span> | <span data-ttu-id="870e0-486">Několik</span><span class="sxs-lookup"><span data-stu-id="870e0-486">Multiple</span></span><br><span data-ttu-id="870e0-487">implementace</span><span class="sxs-lookup"><span data-stu-id="870e0-487">implementations</span></span> | <span data-ttu-id="870e0-488">Předat args</span><span class="sxs-lookup"><span data-stu-id="870e0-488">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="870e0-489">Příklad:</span><span class="sxs-lookup"><span data-stu-id="870e0-489">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="870e0-490">Ano</span><span class="sxs-lookup"><span data-stu-id="870e0-490">Yes</span></span> | <span data-ttu-id="870e0-491">Ano</span><span class="sxs-lookup"><span data-stu-id="870e0-491">Yes</span></span> | <span data-ttu-id="870e0-492">Ne</span><span class="sxs-lookup"><span data-stu-id="870e0-492">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="870e0-493">Příklady:</span><span class="sxs-lookup"><span data-stu-id="870e0-493">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="870e0-494">Ano</span><span class="sxs-lookup"><span data-stu-id="870e0-494">Yes</span></span> | <span data-ttu-id="870e0-495">Ano</span><span class="sxs-lookup"><span data-stu-id="870e0-495">Yes</span></span> | <span data-ttu-id="870e0-496">Ano</span><span class="sxs-lookup"><span data-stu-id="870e0-496">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="870e0-497">Příklad:</span><span class="sxs-lookup"><span data-stu-id="870e0-497">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="870e0-498">Ano</span><span class="sxs-lookup"><span data-stu-id="870e0-498">Yes</span></span> | <span data-ttu-id="870e0-499">Ne</span><span class="sxs-lookup"><span data-stu-id="870e0-499">No</span></span> | <span data-ttu-id="870e0-500">Ne</span><span class="sxs-lookup"><span data-stu-id="870e0-500">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="870e0-501">Příklady:</span><span class="sxs-lookup"><span data-stu-id="870e0-501">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="870e0-502">Ne</span><span class="sxs-lookup"><span data-stu-id="870e0-502">No</span></span> | <span data-ttu-id="870e0-503">Ano</span><span class="sxs-lookup"><span data-stu-id="870e0-503">Yes</span></span> | <span data-ttu-id="870e0-504">Ano</span><span class="sxs-lookup"><span data-stu-id="870e0-504">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="870e0-505">Příklady:</span><span class="sxs-lookup"><span data-stu-id="870e0-505">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="870e0-506">Ne</span><span class="sxs-lookup"><span data-stu-id="870e0-506">No</span></span> | <span data-ttu-id="870e0-507">Ne</span><span class="sxs-lookup"><span data-stu-id="870e0-507">No</span></span> | <span data-ttu-id="870e0-508">Ano</span><span class="sxs-lookup"><span data-stu-id="870e0-508">Yes</span></span> |

<span data-ttu-id="870e0-509">Další informace o likvidaci typů naleznete v části [Likvidace služeb.](#disposal-of-services)</span><span class="sxs-lookup"><span data-stu-id="870e0-509">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="870e0-510">Běžný scénář pro více implementací je [zesměšňování typů pro testování](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="870e0-510">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="870e0-511">`TryAdd{LIFETIME}`metody zaregistrujte službu pouze v případě, že již není registrována implementace.</span><span class="sxs-lookup"><span data-stu-id="870e0-511">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="870e0-512">V následujícím příkladu se první `MyDependency` `IMyDependency`řádek registruje pro .</span><span class="sxs-lookup"><span data-stu-id="870e0-512">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="870e0-513">Druhý řádek nemá žádný `IMyDependency` vliv, protože již má registrovanou implementaci:</span><span class="sxs-lookup"><span data-stu-id="870e0-513">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="870e0-514">Další informace naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="870e0-514">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="870e0-515">[Metody TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) zaregistrujte službu pouze v případě, že již neexistuje implementace *stejného typu*.</span><span class="sxs-lookup"><span data-stu-id="870e0-515">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="870e0-516">Více služeb jsou `IEnumerable<{SERVICE}>`vyřešeny prostřednictvím .</span><span class="sxs-lookup"><span data-stu-id="870e0-516">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="870e0-517">Při registraci služeb vývojář chce přidat instanci pouze v případě, že jeden ze stejného typu ještě nebyl přidán.</span><span class="sxs-lookup"><span data-stu-id="870e0-517">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="870e0-518">Obecně tato metoda se používá autoři knihovny, aby se zabránilo registraci dvou kopií instance v kontejneru.</span><span class="sxs-lookup"><span data-stu-id="870e0-518">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="870e0-519">V následujícím příkladu se první `MyDep` `IMyDep1`řádek registruje pro .</span><span class="sxs-lookup"><span data-stu-id="870e0-519">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="870e0-520">Druhý řádek se `MyDep` `IMyDep2`registruje pro .</span><span class="sxs-lookup"><span data-stu-id="870e0-520">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="870e0-521">Třetí řádek nemá žádný `IMyDep1` účinek, protože `MyDep`již má registrovanou implementaci :</span><span class="sxs-lookup"><span data-stu-id="870e0-521">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="870e0-522">Chování vstřikování konstruktoru</span><span class="sxs-lookup"><span data-stu-id="870e0-522">Constructor injection behavior</span></span>

<span data-ttu-id="870e0-523">Služby lze vyřešit dvěma mechanismy:</span><span class="sxs-lookup"><span data-stu-id="870e0-523">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="870e0-524"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>&ndash; Umožňuje vytváření objektů bez registrace služby v kontejneru vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="870e0-524"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities> &ndash; Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="870e0-525">`ActivatorUtilities`používá se s abstrakcemi směřujícími k uživatelům, jako jsou pomocné spoje značek, řadiče MVC a pořadače modelů.</span><span class="sxs-lookup"><span data-stu-id="870e0-525">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="870e0-526">Konstruktory mohou přijímat argumenty, které nejsou poskytovány vkládání závislostí, ale argumenty musí přiřadit výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="870e0-526">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="870e0-527">Pokud jsou služby `IServiceProvider` vyřešeny `ActivatorUtilities`nebo , [vstřikování konstruktoru](xref:mvc/controllers/dependency-injection#constructor-injection) vyžaduje *veřejný* konstruktor.</span><span class="sxs-lookup"><span data-stu-id="870e0-527">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="870e0-528">Pokud jsou služby vyřešeny `ActivatorUtilities`, [vstřikování konstruktoru](xref:mvc/controllers/dependency-injection#constructor-injection) vyžaduje, aby existoval pouze jeden příslušný konstruktor.</span><span class="sxs-lookup"><span data-stu-id="870e0-528">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="870e0-529">Přetížení konstruktoru jsou podporovány, ale může existovat pouze jedno přetížení, jehož argumenty mohou být splněny vkládáním závislostí.</span><span class="sxs-lookup"><span data-stu-id="870e0-529">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="870e0-530">Kontexty rozhraní entity</span><span class="sxs-lookup"><span data-stu-id="870e0-530">Entity Framework contexts</span></span>

<span data-ttu-id="870e0-531">Kontexty entity Framework jsou obvykle přidány do kontejneru služby pomocí [životnosti oboru,](#service-lifetimes) protože operace databáze webové aplikace jsou obvykle vymezeny na požadavek klienta.</span><span class="sxs-lookup"><span data-stu-id="870e0-531">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="870e0-532">Výchozí doba života je vymezena, pokud životnost není [zadána AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) přetížení při registraci kontextu databáze.</span><span class="sxs-lookup"><span data-stu-id="870e0-532">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="870e0-533">Služby dané životnosti by neměly používat kontext databáze s kratší životností než služba.</span><span class="sxs-lookup"><span data-stu-id="870e0-533">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="870e0-534">Možnosti životnosti a registrace</span><span class="sxs-lookup"><span data-stu-id="870e0-534">Lifetime and registration options</span></span>

<span data-ttu-id="870e0-535">Chcete-li demonstrovat rozdíl mezi možností životnosti a registrace, zvažte následující rozhraní, která představují úkoly jako operaci s jedinečným identifikátorem . `OperationId`</span><span class="sxs-lookup"><span data-stu-id="870e0-535">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="870e0-536">V závislosti na tom, jak je nakonfigurována životnost provozní služby pro následující rozhraní, kontejner poskytuje buď stejnou nebo jinou instanci služby na vyžádání třídou:</span><span class="sxs-lookup"><span data-stu-id="870e0-536">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="870e0-537">Rozhraní jsou implementovány `Operation` ve třídě.</span><span class="sxs-lookup"><span data-stu-id="870e0-537">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="870e0-538">Konstruktor `Operation` generuje identifikátor GUID, pokud není zadán:</span><span class="sxs-lookup"><span data-stu-id="870e0-538">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="870e0-539">Je `OperationService` registrován, který závisí na `Operation` jednotlivých typech.</span><span class="sxs-lookup"><span data-stu-id="870e0-539">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="870e0-540">Pokud `OperationService` je požadováno prostřednictvím vkládání závislostí, obdrží buď novou instanci každé služby nebo existující instance na základě životnosti závislé služby.</span><span class="sxs-lookup"><span data-stu-id="870e0-540">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="870e0-541">Při přechodných služeb jsou vytvořeny na vyžádání `OperationId` z `IOperationTransient` kontejneru, `OperationId` služby `OperationService`se liší od .</span><span class="sxs-lookup"><span data-stu-id="870e0-541">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="870e0-542">`OperationService`obdrží novou instanci `IOperationTransient` třídy.</span><span class="sxs-lookup"><span data-stu-id="870e0-542">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="870e0-543">Nová instance poskytuje jiný `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="870e0-543">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="870e0-544">Při scoped služby jsou vytvořeny `OperationId` na `IOperationScoped` požadavek klienta, `OperationService` služby je stejný jako v rámci požadavku klienta.</span><span class="sxs-lookup"><span data-stu-id="870e0-544">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="870e0-545">Napříč požadavky klientů obě služby sdílejí jinou `OperationId` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="870e0-545">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="870e0-546">Při singleton a singleton instance služby jsou vytvořeny jednou a používají `OperationId` se ve všech požadavcích klientů a všechny služby, je konstantní ve všech požadavcích na služby.</span><span class="sxs-lookup"><span data-stu-id="870e0-546">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="870e0-547">V `Startup.ConfigureServices`, každý typ je přidán do kontejneru podle jeho pojmenované životnosti:</span><span class="sxs-lookup"><span data-stu-id="870e0-547">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="870e0-548">Služba `IOperationSingletonInstance` používá konkrétní instanci se známým ID aplikace `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="870e0-548">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="870e0-549">Je jasné, kdy je tento typ používán (jeho IDENTIFIKÁTOR GUID je všechny nuly).</span><span class="sxs-lookup"><span data-stu-id="870e0-549">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="870e0-550">Ukázková aplikace ukazuje životnost objektů v rámci jednotlivých požadavků a mezi nimi.</span><span class="sxs-lookup"><span data-stu-id="870e0-550">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="870e0-551">Ukázkové `IndexModel` aplikace požaduje každý druh `IOperation` typu `OperationService`a .</span><span class="sxs-lookup"><span data-stu-id="870e0-551">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="870e0-552">Stránka pak zobrazí všechny `OperationId` hodnoty třídy modelu stránky a služby prostřednictvím přiřazení vlastností:</span><span class="sxs-lookup"><span data-stu-id="870e0-552">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="870e0-553">Dva následující výstupy ukazují výsledky dvou požadavků:</span><span class="sxs-lookup"><span data-stu-id="870e0-553">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="870e0-554">**První žádost:**</span><span class="sxs-lookup"><span data-stu-id="870e0-554">**First request:**</span></span>

<span data-ttu-id="870e0-555">Provoz řídicí jednotky:</span><span class="sxs-lookup"><span data-stu-id="870e0-555">Controller operations:</span></span>

<span data-ttu-id="870e0-556">Přechodné: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="870e0-556">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="870e0-557">Rozsah: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="870e0-557">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="870e0-558">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="870e0-558">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="870e0-559">Instance: 00000000-0000-0000-0000-000000000</span><span class="sxs-lookup"><span data-stu-id="870e0-559">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="870e0-560">`OperationService`Operace:</span><span class="sxs-lookup"><span data-stu-id="870e0-560">`OperationService` operations:</span></span>

<span data-ttu-id="870e0-561">Přechodné: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="870e0-561">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="870e0-562">Rozsah: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="870e0-562">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="870e0-563">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="870e0-563">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="870e0-564">Instance: 00000000-0000-0000-0000-000000000</span><span class="sxs-lookup"><span data-stu-id="870e0-564">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="870e0-565">**Druhý požadavek:**</span><span class="sxs-lookup"><span data-stu-id="870e0-565">**Second request:**</span></span>

<span data-ttu-id="870e0-566">Provoz řídicí jednotky:</span><span class="sxs-lookup"><span data-stu-id="870e0-566">Controller operations:</span></span>

<span data-ttu-id="870e0-567">Přechodné: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="870e0-567">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="870e0-568">Rozsah: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="870e0-568">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="870e0-569">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="870e0-569">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="870e0-570">Instance: 00000000-0000-0000-0000-000000000</span><span class="sxs-lookup"><span data-stu-id="870e0-570">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="870e0-571">`OperationService`Operace:</span><span class="sxs-lookup"><span data-stu-id="870e0-571">`OperationService` operations:</span></span>

<span data-ttu-id="870e0-572">Přechodné: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="870e0-572">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="870e0-573">Rozsah: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="870e0-573">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="870e0-574">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="870e0-574">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="870e0-575">Instance: 00000000-0000-0000-0000-000000000</span><span class="sxs-lookup"><span data-stu-id="870e0-575">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="870e0-576">Sledujte, `OperationId` které z hodnot se liší v rámci požadavku a mezi požadavky:</span><span class="sxs-lookup"><span data-stu-id="870e0-576">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="870e0-577">*Přechodné* objekty se vždy liší.</span><span class="sxs-lookup"><span data-stu-id="870e0-577">*Transient* objects are always different.</span></span> <span data-ttu-id="870e0-578">Přechodná `OperationId` hodnota pro první a druhé požadavky klienta `OperationService` se liší pro operace i napříč požadavky klientů.</span><span class="sxs-lookup"><span data-stu-id="870e0-578">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="870e0-579">Pro každý požadavek na službu a požadavek klienta je k dispozici nová instance.</span><span class="sxs-lookup"><span data-stu-id="870e0-579">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="870e0-580">*Scoped* objekty jsou stejné v rámci požadavku klienta, ale liší se mezi požadavky klienta.</span><span class="sxs-lookup"><span data-stu-id="870e0-580">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="870e0-581">*Singleton* objekty jsou stejné pro každý objekt a `Operation` každý požadavek `Startup.ConfigureServices`bez ohledu na to, zda je k dispozici instance v .</span><span class="sxs-lookup"><span data-stu-id="870e0-581">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="870e0-582">Volejte služby z hlavního</span><span class="sxs-lookup"><span data-stu-id="870e0-582">Call services from main</span></span>

<span data-ttu-id="870e0-583">Vytvořte <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> s [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) vyřešit službu s oborem v rámci oboru aplikace.</span><span class="sxs-lookup"><span data-stu-id="870e0-583">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="870e0-584">Tento přístup je užitečný pro přístup ke službě s vymezeným oborem při spuštění ke spuštění úloh inicializace.</span><span class="sxs-lookup"><span data-stu-id="870e0-584">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="870e0-585">Následující příklad ukazuje, jak získat `MyScopedService` kontext `Program.Main`pro in :</span><span class="sxs-lookup"><span data-stu-id="870e0-585">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="870e0-586">Ověření oboru</span><span class="sxs-lookup"><span data-stu-id="870e0-586">Scope validation</span></span>

<span data-ttu-id="870e0-587">Když je aplikace spuštěná ve vývojovém prostředí, provádí výchozí poskytovatel služeb kontroly, aby ověřil, že:</span><span class="sxs-lookup"><span data-stu-id="870e0-587">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="870e0-588">Služby s oborem nejsou přímo ani nepřímo vyřešeny od kořenového poskytovatele služeb.</span><span class="sxs-lookup"><span data-stu-id="870e0-588">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="870e0-589">Oborové služby nejsou přímo nebo nepřímo vloženy do singletons.</span><span class="sxs-lookup"><span data-stu-id="870e0-589">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="870e0-590">Kořenový poskytovatel služeb <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> je vytvořen při volání.</span><span class="sxs-lookup"><span data-stu-id="870e0-590">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="870e0-591">Životnost kořenového poskytovatele služeb odpovídá životnosti aplikace nebo serveru, když poskytovatel začne s aplikací a je uvolněn, když se aplikace vypne.</span><span class="sxs-lookup"><span data-stu-id="870e0-591">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="870e0-592">Služby s vymezeným oborem jsou uvolněny kontejnerem, který je vytvořil.</span><span class="sxs-lookup"><span data-stu-id="870e0-592">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="870e0-593">Pokud je služba s vymezeným oborem vytvořena v kořenovém kontejneru, životnost služby je efektivně povýšena na singleton, protože je uvolněna pouze kořenovým kontejnerem při vypnutí aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="870e0-593">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="870e0-594">Ověřování oborů služby zachytí tyto situace, když `BuildServiceProvider` je volána.</span><span class="sxs-lookup"><span data-stu-id="870e0-594">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="870e0-595">Další informace naleznete v tématu <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="870e0-595">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="870e0-596">Vyžádat si služby</span><span class="sxs-lookup"><span data-stu-id="870e0-596">Request Services</span></span>

<span data-ttu-id="870e0-597">Služby, které jsou k `HttpContext` dispozici v rámci ASP.NET základní požadavek z jsou vystaveny prostřednictvím [Kolekce HttpContext.RequestServices.](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices)</span><span class="sxs-lookup"><span data-stu-id="870e0-597">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="870e0-598">Služby Žádosti představují služby nakonfigurované a požadované jako součást aplikace.</span><span class="sxs-lookup"><span data-stu-id="870e0-598">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="870e0-599">Když objekty určují závislosti, jsou splněny typy `RequestServices`nalezenými v , nikoli `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="870e0-599">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="870e0-600">Obecně platí, že aplikace by neměla používat tyto vlastnosti přímo.</span><span class="sxs-lookup"><span data-stu-id="870e0-600">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="870e0-601">Místo toho požádejte o typy, které vyžadují třídy prostřednictvím konstruktorů třídy a povolit rozhraní rozhraní vstříkne závislosti.</span><span class="sxs-lookup"><span data-stu-id="870e0-601">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="870e0-602">To dává třídy, které jsou snadněji testovat.</span><span class="sxs-lookup"><span data-stu-id="870e0-602">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="870e0-603">Upřednostňovat požadování závislostí jako parametry `RequestServices` konstruktoru pro přístup k kolekci.</span><span class="sxs-lookup"><span data-stu-id="870e0-603">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="870e0-604">Návrhové služby pro vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="870e0-604">Design services for dependency injection</span></span>

<span data-ttu-id="870e0-605">Osvědčenými postupy jsou:</span><span class="sxs-lookup"><span data-stu-id="870e0-605">Best practices are to:</span></span>

* <span data-ttu-id="870e0-606">Navrhnout služby pro použití vkládání závislostí k získání jejich závislosti.</span><span class="sxs-lookup"><span data-stu-id="870e0-606">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="870e0-607">Vyhněte se stavové, statické třídy a členy.</span><span class="sxs-lookup"><span data-stu-id="870e0-607">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="870e0-608">Navrhněte aplikace tak, aby místo toho používaly služby singleton, které se vyhýbají vytváření globálního stavu.</span><span class="sxs-lookup"><span data-stu-id="870e0-608">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="870e0-609">Vyhněte se přímé konkretizovat závislé třídy v rámci služeb.</span><span class="sxs-lookup"><span data-stu-id="870e0-609">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="870e0-610">Přímá instance propojuje kód s konkrétní implementací.</span><span class="sxs-lookup"><span data-stu-id="870e0-610">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="870e0-611">Třídy aplikací jsou malé, dobře zapracované a snadno testované.</span><span class="sxs-lookup"><span data-stu-id="870e0-611">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="870e0-612">Pokud se zdá, že třída má příliš mnoho injekčně závislých, je to obecně známkou toho, že třída má příliš mnoho odpovědností a porušuje [zásadu jedné odpovědnosti (SRP).](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)</span><span class="sxs-lookup"><span data-stu-id="870e0-612">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="870e0-613">Pokus o refaktorování třídy přesunutím některé z jeho odpovědností do nové třídy.</span><span class="sxs-lookup"><span data-stu-id="870e0-613">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="870e0-614">Mějte na paměti, že třídy modelu stránky Razor Pages a třídy kontroleru MVC by se měly zaměřit na obavy o ui.</span><span class="sxs-lookup"><span data-stu-id="870e0-614">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="870e0-615">Obchodní pravidla a podrobnosti implementace přístupu k údajům by měly být uchovávány ve třídách odpovídajících těmto [samostatným obavám](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="870e0-615">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="870e0-616">Likvidace služeb</span><span class="sxs-lookup"><span data-stu-id="870e0-616">Disposal of services</span></span>

<span data-ttu-id="870e0-617">Kontejner volá <xref:System.IDisposable.Dispose*> typy, které <xref:System.IDisposable> vytvoří.</span><span class="sxs-lookup"><span data-stu-id="870e0-617">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="870e0-618">Pokud je instance přidána do kontejneru podle uživatelského kódu, není automaticky uvolněna.</span><span class="sxs-lookup"><span data-stu-id="870e0-618">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="870e0-619">V následujícím příkladu jsou služby vytvořeny kontejnerem služby a uvolněny automaticky:</span><span class="sxs-lookup"><span data-stu-id="870e0-619">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="870e0-620">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="870e0-620">In the following example:</span></span>

* <span data-ttu-id="870e0-621">Instance služby nejsou vytvořeny kontejnerem služby.</span><span class="sxs-lookup"><span data-stu-id="870e0-621">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="870e0-622">Zamýšlené životnosti nejsou známy v rámci.</span><span class="sxs-lookup"><span data-stu-id="870e0-622">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="870e0-623">Rozhraní framework nevyřazené služby automaticky.</span><span class="sxs-lookup"><span data-stu-id="870e0-623">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="870e0-624">Pokud služby nejsou explicitně uvolněny v kódu vývojáře, přetrvávají, dokud se aplikace nevypne.</span><span class="sxs-lookup"><span data-stu-id="870e0-624">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

## <a name="default-service-container-replacement"></a><span data-ttu-id="870e0-625">Výchozí nahrazení kontejneru služby</span><span class="sxs-lookup"><span data-stu-id="870e0-625">Default service container replacement</span></span>

<span data-ttu-id="870e0-626">Integrovaný kontejner služeb je navržen tak, aby sloužil potřebám architektury a většiny spotřebitelských aplikací.</span><span class="sxs-lookup"><span data-stu-id="870e0-626">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="870e0-627">Doporučujeme používat předdefinovaný kontejner, pokud nepotřebujete určitou funkci, kterou integrovaný kontejner nepodporuje, například:</span><span class="sxs-lookup"><span data-stu-id="870e0-627">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="870e0-628">Vstřikování majetku</span><span class="sxs-lookup"><span data-stu-id="870e0-628">Property injection</span></span>
* <span data-ttu-id="870e0-629">Injekce na základě názvu</span><span class="sxs-lookup"><span data-stu-id="870e0-629">Injection based on name</span></span>
* <span data-ttu-id="870e0-630">Dětské kontejnery</span><span class="sxs-lookup"><span data-stu-id="870e0-630">Child containers</span></span>
* <span data-ttu-id="870e0-631">Vlastní správa životnosti</span><span class="sxs-lookup"><span data-stu-id="870e0-631">Custom lifetime management</span></span>
* <span data-ttu-id="870e0-632">`Func<T>`podpora opožděné inicializace</span><span class="sxs-lookup"><span data-stu-id="870e0-632">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="870e0-633">Registrace založená na úmluvách</span><span class="sxs-lookup"><span data-stu-id="870e0-633">Convention-based registration</span></span>

<span data-ttu-id="870e0-634">Následující kontejnery třetích stran lze použít s aplikacemi ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="870e0-634">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="870e0-635">Autofac</span><span class="sxs-lookup"><span data-stu-id="870e0-635">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="870e0-636">DryIoc</span><span class="sxs-lookup"><span data-stu-id="870e0-636">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="870e0-637">Milost</span><span class="sxs-lookup"><span data-stu-id="870e0-637">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="870e0-638">Lehkývstřik</span><span class="sxs-lookup"><span data-stu-id="870e0-638">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="870e0-639">Lamar</span><span class="sxs-lookup"><span data-stu-id="870e0-639">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="870e0-640">Skrýš</span><span class="sxs-lookup"><span data-stu-id="870e0-640">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="870e0-641">Unity</span><span class="sxs-lookup"><span data-stu-id="870e0-641">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="870e0-642">Bezpečnost vlákna</span><span class="sxs-lookup"><span data-stu-id="870e0-642">Thread safety</span></span>

<span data-ttu-id="870e0-643">Vytvořte služby singleton bezpečné pro přístup z více vláken.</span><span class="sxs-lookup"><span data-stu-id="870e0-643">Create thread-safe singleton services.</span></span> <span data-ttu-id="870e0-644">Pokud je služba singleton závislá na přechodné službě, může přechodná služba také vyžadovat bezpečnost podprocesu v závislosti na tom, jak je používána singletonem.</span><span class="sxs-lookup"><span data-stu-id="870e0-644">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="870e0-645">Metoda výroby jedné služby, jako je například druhý argument [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), nemusí být bezpečná pro přístup z více vláken.</span><span class="sxs-lookup"><span data-stu-id="870e0-645">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="870e0-646">Stejně jako`static`typ ( ) konstruktor, je zaručeno, že bude volán jednou jedním vláknem.</span><span class="sxs-lookup"><span data-stu-id="870e0-646">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="870e0-647">Doporučení</span><span class="sxs-lookup"><span data-stu-id="870e0-647">Recommendations</span></span>

* <span data-ttu-id="870e0-648">`async/await`a `Task` řešení služby založené na tom není podporováno.</span><span class="sxs-lookup"><span data-stu-id="870e0-648">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="870e0-649">C# nepodporuje asynchronní konstruktory; proto je doporučenývzor použít asynchronní metody po synchronní řešení služby.</span><span class="sxs-lookup"><span data-stu-id="870e0-649">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="870e0-650">Vyhněte se ukládání dat a konfigurace přímo v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="870e0-650">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="870e0-651">Například nákupní košík uživatele by obvykle neměl být přidán do kontejneru služeb.</span><span class="sxs-lookup"><span data-stu-id="870e0-651">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="870e0-652">Konfigurace by měla používat [vzor voleb](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="870e0-652">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="870e0-653">Podobně se vyhněte "držitel dat" objekty, které existují pouze umožnit přístup k některé jiné objekty.</span><span class="sxs-lookup"><span data-stu-id="870e0-653">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="870e0-654">Je lepší požádat o skutečnou položku prostřednictvím DI.</span><span class="sxs-lookup"><span data-stu-id="870e0-654">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="870e0-655">Vyhněte se statickému přístupu ke službám (například staticky zadáváním [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) pro použití jinde).</span><span class="sxs-lookup"><span data-stu-id="870e0-655">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="870e0-656">Nepoužívejte *vzor lokátoru služby*, který kombinuje [inverzi strategií řízení.](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)</span><span class="sxs-lookup"><span data-stu-id="870e0-656">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

  * <span data-ttu-id="870e0-657">Nevyvolávejte <xref:System.IServiceProvider.GetService*> získat instanci služby, pokud můžete použít DI místo:</span><span class="sxs-lookup"><span data-stu-id="870e0-657">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="870e0-658">**Nesprávné:**</span><span class="sxs-lookup"><span data-stu-id="870e0-658">**Incorrect:**</span></span>

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

    <span data-ttu-id="870e0-659">**Správně**:</span><span class="sxs-lookup"><span data-stu-id="870e0-659">**Correct**:</span></span>

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

  * <span data-ttu-id="870e0-660">Vyhněte se vkládání továrny, která <xref:System.IServiceProvider.GetService*>řeší závislosti za běhu pomocí .</span><span class="sxs-lookup"><span data-stu-id="870e0-660">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>

* <span data-ttu-id="870e0-661">Vyhněte `HttpContext` se statickému přístupu (například [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="870e0-661">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="870e0-662">Stejně jako všechny sady doporučení, může dojít k situacím, kdy je vyžadováno ignorování doporučení.</span><span class="sxs-lookup"><span data-stu-id="870e0-662">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="870e0-663">Výjimky jsou&mdash;vzácné většinou zvláštní případy v rámci samotném.</span><span class="sxs-lookup"><span data-stu-id="870e0-663">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="870e0-664">DI je *alternativou* ke vzorům statického/globálního přístupu k objektům.</span><span class="sxs-lookup"><span data-stu-id="870e0-664">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="870e0-665">Je možné, že nebudete moci realizovat výhody DI, pokud jej smícháte s přístupem ke statickému objektu.</span><span class="sxs-lookup"><span data-stu-id="870e0-665">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="870e0-666">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="870e0-666">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="870e0-667">Zápis čistého kódu v ASP.NET jádra s vkládáním závislostí (MSDN)</span><span class="sxs-lookup"><span data-stu-id="870e0-667">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="870e0-668">Princip explicitních závislostí</span><span class="sxs-lookup"><span data-stu-id="870e0-668">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="870e0-669">Inverze řídicích kontejnerů a vzor vstřikování závislostí (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="870e0-669">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="870e0-670">Jak zaregistrovat službu s více rozhraními v ASP.NET Core DI</span><span class="sxs-lookup"><span data-stu-id="870e0-670">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

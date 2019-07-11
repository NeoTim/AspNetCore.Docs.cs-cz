---
title: Vkládání závislostí v ASP.NET Core
author: guardrex
description: Zjistěte, jak ASP.NET Core implementuje vkládání závislostí a jak se používá.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/09/2019
uid: fundamentals/dependency-injection
ms.openlocfilehash: f1282e9bf34a96e9495f35dcb51974594c938fde
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67814803"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="5a7ae-103">Vkládání závislostí v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5a7ae-103">Dependency injection in ASP.NET Core</span></span>

<span data-ttu-id="5a7ae-104">Podle [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="5a7ae-104">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="5a7ae-105">ASP.NET Core podporuje návrhový vzor vkládání závislostí (Dependency Injection, DI), což je technika používaná pro dosažení [inverze řízení (Inversion of Control, IOC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) mezi třídami a jejich závislostmi.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="5a7ae-106">Další informace specifické pro vkládání závislostí do kontrolerů MVC najdete v tématu <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="5a7ae-107">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5a7ae-107">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="5a7ae-108">Přehled vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="5a7ae-108">Overview of dependency injection</span></span>

<span data-ttu-id="5a7ae-109">*Závislost* je libovolný objekt, který je vyžadován jiným objektem.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-109">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="5a7ae-110">Prohlédněte si následující třídu `MyDependency` s metodou `WriteMessage`, na které závisí jiné třídy v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="5a7ae-110">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="5a7ae-111">Pro zpřístupnění metody `WriteMessage` v jiné třídě je možné vytvořit instanci třídy `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-111">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="5a7ae-112">Třída `MyDependency` je závislostí třídy `IndexModel`:</span><span class="sxs-lookup"><span data-stu-id="5a7ae-112">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="5a7ae-113">Třída vytváří instanci třídy `MyDependency`, na které přímo závisí.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-113">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="5a7ae-114">Přímé závislosti v kódu (jako ta v předchozím příkladu) jsou problematické a měli byste se jim vyhnout z následujících důvodů:</span><span class="sxs-lookup"><span data-stu-id="5a7ae-114">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="5a7ae-115">Chcete-li nahradit `MyDependency` jinou implementací, musí být modifikována třída, která na ní závisí.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-115">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="5a7ae-116">Pokud má třída `MyDependency` sama závislosti, musí být taktéž nakonfigurovány v dané třídě.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-116">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="5a7ae-117">Ve velkých projektech s více třídami závislých na `MyDependency` je tak konfigurační kód roztroušen na různých místech aplikace.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-117">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="5a7ae-118">Tento způsob implementace je obtížný na jednotkové testování.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-118">This implementation is difficult to unit test.</span></span> <span data-ttu-id="5a7ae-119">Aplikace by měla používat mock nebo stub třídy `MyDependency`, což však není možné s tímto přístupem.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-119">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="5a7ae-120">Vkládání závislostí řeší tyto problémy prostřednictvím:</span><span class="sxs-lookup"><span data-stu-id="5a7ae-120">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="5a7ae-121">Použití rozhraní nebo základní třídy abstraktní implementace závislostí.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-121">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="5a7ae-122">Registrace závislosti v kontejneru služeb.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-122">Registration of the dependency in a service container.</span></span> <span data-ttu-id="5a7ae-123">ASP.NET Core nabízí integrovanou službu kontejneru, <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-123">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="5a7ae-124">Služby jsou registrované v metodě `Startup.ConfigureServices` aplikace.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-124">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="5a7ae-125">*Vkládání* služeb do konstruktoru třídy, ve které se používá.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-125">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="5a7ae-126">Framework přebírá zodpovědnost za vytváření instancí závislostí a jejich uvolňování, kdy již nejsou dále potřebné.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-126">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="5a7ae-127">V [ukázkové aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) definuje rozhraní `IMyDependency` metody, které poskytuje služba aplikaci:</span><span class="sxs-lookup"><span data-stu-id="5a7ae-127">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="5a7ae-128">Toto rozhraní je implementováno konkrétním typem `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="5a7ae-128">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="5a7ae-129">`MyDependency` Požadavky <xref:Microsoft.Extensions.Logging.ILogger`1> 've svém konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-129">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="5a7ae-130">Není neobvyklé používat zřetězené vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-130">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="5a7ae-131">Každá požadovaná závislost může v zápětí požadovat své vlastní závislosti.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-131">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="5a7ae-132">Kontejner řeší závislosti v grafu a vrací plně vyřešené služby.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-132">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="5a7ae-133">Množina závislostí, které musí být rozhodnuty, se obvykle označuje jako *strom závislostí*, *graf závislostí*, nebo *graf objektů*.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-133">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="5a7ae-134">`IMyDependency` a `ILogger<TCategoryName>` musí být zaregistrovány v kontejneru služeb.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-134">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="5a7ae-135">`IMyDependency` je zaregistrovaný v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-135">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="5a7ae-136">`ILogger<TCategoryName>` je registrován infrastrukturou pro abstrakci protokolování, takže je [službou poskytovanou frameworkem](#framework-provided-services) registrovanou ve výchozím nastavení frameworkem.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-136">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="5a7ae-137">Odstraňuje kontejner `ILogger<TCategoryName>` s využitím [(Obecné) otevřete typy](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), tím eliminuje nutnost zaregistrovat každý [(Obecné) konstruovaný typ.](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="5a7ae-137">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<T>), typeof(Logger<T>));
```

<span data-ttu-id="5a7ae-138">V ukázkové aplikaci je služba `IMyDependency` registrována s konkrétním typem `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-138">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="5a7ae-139">Registrace specifikuje rámec životnosti služby na životnost jednoho požadavku.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-139">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="5a7ae-140">[Životnosti služby](#service-lifetimes) jsou popsány dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-140">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="5a7ae-141">Každá rozšiřující metoda `services.Add{SERVICE_NAME}` přidává (a potenciálně konfiguruje) služby.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-141">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="5a7ae-142">Například `services.AddMvc()` přidává služby Stránek Razor a MVC.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-142">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="5a7ae-143">Doporučujeme, aby v aplikacích byla tato konvence dodržena.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-143">We recommended that apps follow this convention.</span></span> <span data-ttu-id="5a7ae-144">Rozšiřující metody v umístění <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> obor názvů pro zapouzdření skupiny registrací služby.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-144">Place extension methods in the <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="5a7ae-145">Pokud konstruktor služby vyžaduje [předdefinovaný typ](/dotnet/csharp/language-reference/keywords/built-in-types-table), jako `string`, typ lze vloženy pomocí [konfigurace](xref:fundamentals/configuration/index) nebo [možnosti vzor](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="5a7ae-145">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="5a7ae-146">Instance služby je požadována prostřednictvím konstruktoru třídy, kde se služba využívá a přiřazuje do privátního pole.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-146">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="5a7ae-147">Toto pole se používá pro přístup ke službě podle potřeby v rámci celé třídy.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-147">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="5a7ae-148">V ukázkové aplikaci je požadována instance `IMyDependency` a posléze použita k volání metody `WriteMessage` dané služby:</span><span class="sxs-lookup"><span data-stu-id="5a7ae-148">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="framework-provided-services"></a><span data-ttu-id="5a7ae-149">Služby poskytované frameworkem</span><span class="sxs-lookup"><span data-stu-id="5a7ae-149">Framework-provided services</span></span>

<span data-ttu-id="5a7ae-150">Metoda `Startup.ConfigureServices` zodpovídá za definování služeb používaných aplikací, včetně funkcí platformy, jako je například Entity Framework Core a ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-150">The `Startup.ConfigureServices` method is responsible for defining the services the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="5a7ae-151">Zpočátku jsou v `IServiceCollection`, který je poskytován metodě `ConfigureServices`, definovány následující služby (v závislosti na [konfiguraci hostitele](xref:fundamentals/index#host)):</span><span class="sxs-lookup"><span data-stu-id="5a7ae-151">Initially, the `IServiceCollection` provided to `ConfigureServices` has the following services defined (depending on [how the host was configured](xref:fundamentals/index#host)):</span></span>

| <span data-ttu-id="5a7ae-152">Typ služby</span><span class="sxs-lookup"><span data-stu-id="5a7ae-152">Service Type</span></span> | <span data-ttu-id="5a7ae-153">Životnost</span><span class="sxs-lookup"><span data-stu-id="5a7ae-153">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="5a7ae-154">Přechodná</span><span class="sxs-lookup"><span data-stu-id="5a7ae-154">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="5a7ae-155">Singleton</span><span class="sxs-lookup"><span data-stu-id="5a7ae-155">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="5a7ae-156">Singleton</span><span class="sxs-lookup"><span data-stu-id="5a7ae-156">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="5a7ae-157">Singleton</span><span class="sxs-lookup"><span data-stu-id="5a7ae-157">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="5a7ae-158">Přechodná</span><span class="sxs-lookup"><span data-stu-id="5a7ae-158">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="5a7ae-159">Singleton</span><span class="sxs-lookup"><span data-stu-id="5a7ae-159">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="5a7ae-160">Přechodná</span><span class="sxs-lookup"><span data-stu-id="5a7ae-160">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="5a7ae-161">Singleton</span><span class="sxs-lookup"><span data-stu-id="5a7ae-161">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="5a7ae-162">Singleton</span><span class="sxs-lookup"><span data-stu-id="5a7ae-162">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="5a7ae-163">Singleton</span><span class="sxs-lookup"><span data-stu-id="5a7ae-163">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="5a7ae-164">Přechodná</span><span class="sxs-lookup"><span data-stu-id="5a7ae-164">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="5a7ae-165">Singleton</span><span class="sxs-lookup"><span data-stu-id="5a7ae-165">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="5a7ae-166">Singleton</span><span class="sxs-lookup"><span data-stu-id="5a7ae-166">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="5a7ae-167">Singleton</span><span class="sxs-lookup"><span data-stu-id="5a7ae-167">Singleton</span></span> |

<span data-ttu-id="5a7ae-168">Pokud je dostupná rozšiřující metoda rozšiřující kolekci služeb o registraci služby (případě jejích závislých služeb, je-li to požadováno), je zvykem použít jedinou rozšiřující metodu `Add{SERVICE_NAME}` k registraci všech služeb vyžadovaných danou službu.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-168">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="5a7ae-169">Následující kód je příklad toho, jak přidat další služby do kontejneru pomocí metody rozšíření [AddDbContext\<TContext >](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext), <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>, a <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*>:</span><span class="sxs-lookup"><span data-stu-id="5a7ae-169">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext), <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>, and <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*>:</span></span>

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

<span data-ttu-id="5a7ae-170">Další informace najdete v tématu <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> třídy v dokumentaci k rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-170">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="5a7ae-171">Životnost služby</span><span class="sxs-lookup"><span data-stu-id="5a7ae-171">Service lifetimes</span></span>

<span data-ttu-id="5a7ae-172">Zvolte odpovídající životnost pro každou registrovanou službu.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-172">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="5a7ae-173">Služby ASP.NET Core můžete nakonfigurovat s následujícími životnostmi:</span><span class="sxs-lookup"><span data-stu-id="5a7ae-173">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="5a7ae-174">Přechodná</span><span class="sxs-lookup"><span data-stu-id="5a7ae-174">Transient</span></span>

<span data-ttu-id="5a7ae-175">Přechodná doba platnosti služby (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) se vytváří pokaždé, když jste vyžádaný z kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-175">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="5a7ae-176">Tato životnost je vhodná pro jednoduché, bezstavové služby.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-176">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="5a7ae-177">Obor</span><span class="sxs-lookup"><span data-stu-id="5a7ae-177">Scoped</span></span>

<span data-ttu-id="5a7ae-178">Vymezené služby životního cyklu (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) se vytvoří jednou za žádost klienta (připojení).</span><span class="sxs-lookup"><span data-stu-id="5a7ae-178">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="5a7ae-179">Při použití služby s vymezenou živostností v middlewaru vkládejte službu do metody `Invoke` nebo `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-179">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="5a7ae-180">Nevkládejte službu prostřednictvím konstruktoru, protože se služba bude chovat se jako singleton.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-180">Don't inject via constructor injection because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="5a7ae-181">Další informace naleznete v tématu <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-181">For more information, see <xref:fundamentals/middleware/index>.</span></span>

### <a name="singleton"></a><span data-ttu-id="5a7ae-182">Singleton</span><span class="sxs-lookup"><span data-stu-id="5a7ae-182">Singleton</span></span>

<span data-ttu-id="5a7ae-183">Deklarace služeb typu singleton životnost (<xref:Microsoft.AspNet.OData.Builder.ODataModelBuilder.AddSingleton*>) se vytvoří při prvním jste žádali (nebo když `Startup.ConfigureServices` spuštění a instance je zadán s registrací služby).</span><span class="sxs-lookup"><span data-stu-id="5a7ae-183">Singleton lifetime services (<xref:Microsoft.AspNet.OData.Builder.ODataModelBuilder.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="5a7ae-184">Každý další požadavek použije stejnou instanci.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-184">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="5a7ae-185">Pokud je v aplikaci vyžadováno chování ve stylu návrhového vzoru Singleton, doporučuje se použít kontejner služeb ke správě životnosti takového objektu.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-185">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="5a7ae-186">Neposkytujte vlastní implementaci návrhového vzoru Singleton a umožněte tak uživatelskému kódu spravovat životnost objektu v rámci třídy.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-186">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="5a7ae-187">Je nebezpečné získávat vymezené služby ze singletonů.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-187">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="5a7ae-188">Může to způsobit nesprávný stav služby při zpracování následujících požadavků.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-188">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="5a7ae-189">Metody registrace služby</span><span class="sxs-lookup"><span data-stu-id="5a7ae-189">Service registration methods</span></span>

<span data-ttu-id="5a7ae-190">Každá metoda rozšíření registrace služby nabízí přetížení, které jsou užitečné v konkrétních scénářích.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-190">Each service registration extension method offers overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="5a7ae-191">Metoda</span><span class="sxs-lookup"><span data-stu-id="5a7ae-191">Method</span></span> | <span data-ttu-id="5a7ae-192">Automatické</span><span class="sxs-lookup"><span data-stu-id="5a7ae-192">Automatic</span></span><br><span data-ttu-id="5a7ae-193">odkazy objektů</span><span class="sxs-lookup"><span data-stu-id="5a7ae-193">object</span></span><br><span data-ttu-id="5a7ae-194">vyřazení</span><span class="sxs-lookup"><span data-stu-id="5a7ae-194">disposal</span></span> | <span data-ttu-id="5a7ae-195">Několik</span><span class="sxs-lookup"><span data-stu-id="5a7ae-195">Multiple</span></span><br><span data-ttu-id="5a7ae-196">implementace</span><span class="sxs-lookup"><span data-stu-id="5a7ae-196">implementations</span></span> | <span data-ttu-id="5a7ae-197">Předání argumentů</span><span class="sxs-lookup"><span data-stu-id="5a7ae-197">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="5a7ae-198">Příklad:</span><span class="sxs-lookup"><span data-stu-id="5a7ae-198">Example:</span></span><br>`services.AddScoped<IMyDep, MyDep>();` | <span data-ttu-id="5a7ae-199">Ano</span><span class="sxs-lookup"><span data-stu-id="5a7ae-199">Yes</span></span> | <span data-ttu-id="5a7ae-200">Ano</span><span class="sxs-lookup"><span data-stu-id="5a7ae-200">Yes</span></span> | <span data-ttu-id="5a7ae-201">Ne</span><span class="sxs-lookup"><span data-stu-id="5a7ae-201">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="5a7ae-202">Příklady:</span><span class="sxs-lookup"><span data-stu-id="5a7ae-202">Examples:</span></span><br>`services.AddScoped<IMyDep>(sp => new MyDep());`<br>`services.AddScoped<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="5a7ae-203">Ano</span><span class="sxs-lookup"><span data-stu-id="5a7ae-203">Yes</span></span> | <span data-ttu-id="5a7ae-204">Ano</span><span class="sxs-lookup"><span data-stu-id="5a7ae-204">Yes</span></span> | <span data-ttu-id="5a7ae-205">Ano</span><span class="sxs-lookup"><span data-stu-id="5a7ae-205">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="5a7ae-206">Příklad:</span><span class="sxs-lookup"><span data-stu-id="5a7ae-206">Example:</span></span><br>`services.AddScoped<MyDep>();` | <span data-ttu-id="5a7ae-207">Ano</span><span class="sxs-lookup"><span data-stu-id="5a7ae-207">Yes</span></span> | <span data-ttu-id="5a7ae-208">Ne</span><span class="sxs-lookup"><span data-stu-id="5a7ae-208">No</span></span> | <span data-ttu-id="5a7ae-209">Ne</span><span class="sxs-lookup"><span data-stu-id="5a7ae-209">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="5a7ae-210">Příklady:</span><span class="sxs-lookup"><span data-stu-id="5a7ae-210">Examples:</span></span><br>`services.AddScoped<IMyDep>(new MyDep());`<br>`services.AddScoped<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="5a7ae-211">Ne</span><span class="sxs-lookup"><span data-stu-id="5a7ae-211">No</span></span> | <span data-ttu-id="5a7ae-212">Ano</span><span class="sxs-lookup"><span data-stu-id="5a7ae-212">Yes</span></span> | <span data-ttu-id="5a7ae-213">Ano</span><span class="sxs-lookup"><span data-stu-id="5a7ae-213">Yes</span></span> |
| `Add{LIFETIME}(new {IMPLEMENTATION})`<br><span data-ttu-id="5a7ae-214">Příklady:</span><span class="sxs-lookup"><span data-stu-id="5a7ae-214">Examples:</span></span><br>`services.AddScoped(new MyDep());`<br>`services.AddScoped(new MyDep("A string!"));` | <span data-ttu-id="5a7ae-215">Ne</span><span class="sxs-lookup"><span data-stu-id="5a7ae-215">No</span></span> | <span data-ttu-id="5a7ae-216">Ne</span><span class="sxs-lookup"><span data-stu-id="5a7ae-216">No</span></span> | <span data-ttu-id="5a7ae-217">Ano</span><span class="sxs-lookup"><span data-stu-id="5a7ae-217">Yes</span></span> |

<span data-ttu-id="5a7ae-218">Další informace o vyřazení typu, najdete v článku [vyřazení služby](#disposal-of-services) oddílu.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-218">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="5a7ae-219">Je běžným scénářem, více implementací [napodobování typy pro testování](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="5a7ae-219">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="5a7ae-220">`TryAdd{LIFETIME}` metody pouze registraci služby, pokud ještě není zaregistrované implementace.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-220">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="5a7ae-221">V následujícím příkladu první řádek registruje `MyDependency` pro `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-221">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="5a7ae-222">Druhý řádek nemá žádný vliv, protože `IMyDependency` už má registrovanou implementaci:</span><span class="sxs-lookup"><span data-stu-id="5a7ae-222">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="5a7ae-223">Další informace naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="5a7ae-223">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="5a7ae-224">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) metody zaregistrovat službu pouze, pokud ještě není implementace *stejného typu*.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-224">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="5a7ae-225">Několik služeb, které jsou vyřešeny prostřednictvím `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-225">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="5a7ae-226">Při registraci služby, Vývojář pouze chce přidat instanci, pokud jeden z stejného typu už se nepřidal.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-226">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="5a7ae-227">Obecně platí tato metoda se používá autory knihoven, aby registraci dvě kopie instance v kontejneru.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-227">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="5a7ae-228">V následujícím příkladu první řádek registruje `MyDep` pro `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-228">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="5a7ae-229">Druhý řádek registruje `MyDep` pro `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-229">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="5a7ae-230">Třetí řádek nemá žádný vliv, protože `IMyDep1` již má registrovaný provádění `MyDep`:</span><span class="sxs-lookup"><span data-stu-id="5a7ae-230">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="5a7ae-231">Chování vkládání pomocí konstruktoru</span><span class="sxs-lookup"><span data-stu-id="5a7ae-231">Constructor injection behavior</span></span>

<span data-ttu-id="5a7ae-232">Služby mohou být řešeny pomocí dvou mechanismů:</span><span class="sxs-lookup"><span data-stu-id="5a7ae-232">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="5a7ae-233"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities> &ndash; Umožňuje vytvoření objektu bez registrace služby v kontejneru pro vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-233"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities> &ndash; Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="5a7ae-234">`ActivatorUtilities` se používá s uživatelsky orientovanými abstrakcemi, jako jsou například Tag Helpery, kontrolery MVC a bindery modelů.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-234">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="5a7ae-235">Konstruktory mohou přijímat argumenty, které nejsou poskytovány v rámci vkládání závislostí, ale takové argumenty musí mít přiřazené výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-235">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="5a7ae-236">Pokud jsou služby řešeny pomocí `IServiceProvider` nebo `ActivatorUtilities`, pak je vyžadován *veřejný* konstruktor.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-236">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, constructor injection requires a *public* constructor.</span></span>

<span data-ttu-id="5a7ae-237">Když jsou vyřešeny služby `ActivatorUtilities`, vkládání konstruktor vyžaduje, že pouze jeden použít konstruktor existuje.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-237">When services are resolved by `ActivatorUtilities`, constructor injection requires that only one applicable constructor exists.</span></span> <span data-ttu-id="5a7ae-238">Přetížení konstruktoru je podporováno, ale může existovat pouze jedno přetížením, jehož argumenty jsou splnitelné vkládáním závislostí.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-238">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="5a7ae-239">Kontexty Entity Frameworku</span><span class="sxs-lookup"><span data-stu-id="5a7ae-239">Entity Framework contexts</span></span>

<span data-ttu-id="5a7ae-240">Entity Framework kontexty jsou obvykle přidány do služby kontejneru pomocí [s vymezeným oborem životnost](#service-lifetimes) protože operací databáze webové aplikace jsou obvykle vymezené požadavku klienta.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-240">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="5a7ae-241">Výchozí doba života má obor, pokud se nezadá životnost [AddDbContext\<TContext >](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) přetížení při registraci kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-241">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="5a7ae-242">Služby danou dobu života neměli používat kontext databáze s životností kratší než služba.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-242">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="5a7ae-243">Životnosti a možnosti registrace</span><span class="sxs-lookup"><span data-stu-id="5a7ae-243">Lifetime and registration options</span></span>

<span data-ttu-id="5a7ae-244">Na ukázku rozdílů mezi životnostmi a možnostmi registrace si prohlédněte následující rozhraní reprezentující úlohy jako operace s jedinečným identifikátorem `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-244">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="5a7ae-245">V závislosti na tom, jak je životnost této služby nakonfigurována pro následující rozhraní, poskytne kontejner stejnou nebo rozdílnou instanci služby během vyžádání třídou:</span><span class="sxs-lookup"><span data-stu-id="5a7ae-245">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="5a7ae-246">Rozhraní jsou implementovány ve třídě `Operation`.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-246">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="5a7ae-247">Konstruktor `Operation` vytvoří identifikátor GUID, pokud není explicitně poskytnut:</span><span class="sxs-lookup"><span data-stu-id="5a7ae-247">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="5a7ae-248">Služba `OperationService` je zaregistrována tak, aby závisela na jednotlivých typech tříd `Operation`.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-248">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="5a7ae-249">Když je `OperationService` vyžádána pomocí vkládání závislostí, obdrží buď novou nebo stávající instanci třídy jednotlivých služeb v závislosti na životnosti závislých služeb.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-249">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="5a7ae-250">Při vytváření přechodné služby na požádání v kontejneru `OperationId` z `IOperationTransient` služby se liší od `OperationId` z `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-250">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="5a7ae-251">`OperationService` obdrží novou instanci třídy `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-251">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="5a7ae-252">Nová instance implikuje rozdílné `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-252">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="5a7ae-253">Při vytváření služby s vymezeným oborem každý požadavek klienta `OperationId` z `IOperationScoped` služba je stejné jako u `OperationService` v požadavku klienta.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-253">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="5a7ae-254">Mezi požadavky klientů, obě služby sdílené složky jiný `OperationId` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-254">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="5a7ae-255">Když jsou služby typu singleton a instanci typu singleton vytvořit jednou a použít v rámci všech požadavků klientů a všemi službami, `OperationId` je konstantní napříč všemi požadavky služby.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-255">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="5a7ae-256">V `Startup.ConfigureServices` je každý typ přidán do kontejneru na základě svého pojmenování podle životnosti:</span><span class="sxs-lookup"><span data-stu-id="5a7ae-256">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="5a7ae-257">Služba `IOperationSingletonInstance` používá konkrétní instanci se známým ID nastaveným na `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-257">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="5a7ae-258">Je pak zřejmé, kdy je tento typ použit (jeho identifikátor GUID obsahuje samé nuly).</span><span class="sxs-lookup"><span data-stu-id="5a7ae-258">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="5a7ae-259">Ukázková aplikace demonstruje živostnosti objektů v rámci jednotlivých požadavků a mezi nimi.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-259">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="5a7ae-260">Třída `IndexModel` ukázkové aplikace vyžaduje každý druh typu `IOperation` a službu `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-260">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="5a7ae-261">Stránka následně vypisuje všechny hodnoty `OperationId` modelu stránky a služby prostřednictvím přiřazených vlastností:</span><span class="sxs-lookup"><span data-stu-id="5a7ae-261">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="5a7ae-262">Následující výpis ukazuje výsledek dvou HTTP požadavků:</span><span class="sxs-lookup"><span data-stu-id="5a7ae-262">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="5a7ae-263">**První požadavek:**</span><span class="sxs-lookup"><span data-stu-id="5a7ae-263">**First request:**</span></span>

<span data-ttu-id="5a7ae-264">Operace kontroleru:</span><span class="sxs-lookup"><span data-stu-id="5a7ae-264">Controller operations:</span></span>

<span data-ttu-id="5a7ae-265">Transient: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="5a7ae-265">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="5a7ae-266">Nastavit rozsah: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="5a7ae-266">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="5a7ae-267">Jednotlivý prvek: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="5a7ae-267">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="5a7ae-268">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="5a7ae-268">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="5a7ae-269">Operace `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="5a7ae-269">`OperationService` operations:</span></span>

<span data-ttu-id="5a7ae-270">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="5a7ae-270">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="5a7ae-271">Nastavit rozsah: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="5a7ae-271">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="5a7ae-272">Jednotlivý prvek: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="5a7ae-272">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="5a7ae-273">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="5a7ae-273">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="5a7ae-274">**Druhý požadavek:**</span><span class="sxs-lookup"><span data-stu-id="5a7ae-274">**Second request:**</span></span>

<span data-ttu-id="5a7ae-275">Operace kontroleru:</span><span class="sxs-lookup"><span data-stu-id="5a7ae-275">Controller operations:</span></span>

<span data-ttu-id="5a7ae-276">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="5a7ae-276">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="5a7ae-277">Nastavit rozsah: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="5a7ae-277">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="5a7ae-278">Jednotlivý prvek: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="5a7ae-278">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="5a7ae-279">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="5a7ae-279">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="5a7ae-280">Operace `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="5a7ae-280">`OperationService` operations:</span></span>

<span data-ttu-id="5a7ae-281">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="5a7ae-281">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="5a7ae-282">Nastavit rozsah: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="5a7ae-282">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="5a7ae-283">Jednotlivý prvek: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="5a7ae-283">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="5a7ae-284">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="5a7ae-284">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="5a7ae-285">Všimněte si, které hodnoty `OperationId` se liší v rámci požadavku a mezi požadavky:</span><span class="sxs-lookup"><span data-stu-id="5a7ae-285">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="5a7ae-286">Objekty s *přechodnou* životností jsou vždy rozdílné.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-286">*Transient* objects are always different.</span></span> <span data-ttu-id="5a7ae-287">Přechodná `OperationId` hodnotu prvního a druhého klienta požadavky se liší pro obě `OperationService` operace napříč požadavky klientů.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-287">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="5a7ae-288">Novou instanci se poskytuje pro každou žádost o službu a požadavek klienta.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-288">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="5a7ae-289">*Obor* objekty jsou stejné v požadavku klienta, ale jiné napříč požadavky klienta.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-289">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="5a7ae-290">Objekty se živostností typu *singleton jsou* stejné pro všechny objekty a všechny požadavky bez ohledu na to, jestli je instance `Operation` poskytnuta v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-290">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="5a7ae-291">Volání služeb z main</span><span class="sxs-lookup"><span data-stu-id="5a7ae-291">Call services from main</span></span>

<span data-ttu-id="5a7ae-292">Vytvoření <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> s [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) vyřešit vymezené služby v rámci oboru aplikace.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-292">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="5a7ae-293">Tento způsob je užitečný pro přístup k službám s vymezenou životností při provádění inicializačních úloh během spuštění.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-293">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="5a7ae-294">Následující příklad ukazuje, jak získat kontext pro `MyScopedService` v `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="5a7ae-294">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="5a7ae-295">Ověření rámce životnosti</span><span class="sxs-lookup"><span data-stu-id="5a7ae-295">Scope validation</span></span>

<span data-ttu-id="5a7ae-296">Pokud aplikace běží ve vývojovém prostředí, výchozí poskytovatel služeb provádí kontroly pro ověření toho, že:</span><span class="sxs-lookup"><span data-stu-id="5a7ae-296">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="5a7ae-297">Služby s vymezenou živostností nejsou přímo nebo nepřímo rozhodovány z kořenového poskytovatele služeb.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-297">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="5a7ae-298">Služby s vymezenou živostností nejsou přímo nebo nepřímo vkládány do singletonů.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-298">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="5a7ae-299">Poskytovatel služeb root je vytvořen, když <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> je volána.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-299">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="5a7ae-300">Životnost kořenového poskytovatele služeb odpovídá životnosti aplikace/serveru, tedy poskytovatel vzniká se startem aplikace a je uvolněn při ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-300">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="5a7ae-301">Služby s vymezenou životností jsou uvolňovány kontejnerem, který je vytvořil.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-301">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="5a7ae-302">Pokud jsou služby s vymezenou životností vytvořeny v kořenovém kontejneru, životnost služby je efektivně povýšen na singleton, protože je uvolněn pomocí kořenového kontejneru pouze při vypnutí serveru/aplikace.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-302">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="5a7ae-303">Validací rámce životnosti služeb ošetřuje tyto situace při volání `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-303">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="5a7ae-304">Další informace naleznete v tématu <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-304">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="5a7ae-305">Služby požadavků</span><span class="sxs-lookup"><span data-stu-id="5a7ae-305">Request Services</span></span>

<span data-ttu-id="5a7ae-306">Služby dostupné v rámci požadavků ASP.NET Core z `HttpContext` jsou zpřístupněny prostřednictvím kolekce [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="5a7ae-306">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="5a7ae-307">Služby požadavků představují služby nakonfigurované a požadováné v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-307">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="5a7ae-308">Pokud objekty specifikují závislosti, jsou řešeny pomocí typů dostupných v `RequestServices`, nikoli `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-308">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="5a7ae-309">Aplikace by obvykle neměly používat tyto vlastnosti přímo.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-309">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="5a7ae-310">Místo toho získávejte požadované typy pomocí konstruktoru třídy a nechte framework vložit odpovídající závislosti.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-310">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="5a7ae-311">To poskytuje třídy, které usnadňuje testování.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-311">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="5a7ae-312">Pro přístup ke kolekci `RequestServices` upřednostněte získávání závislostí jako parametr konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-312">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="5a7ae-313">Návrh služeb pro vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="5a7ae-313">Design services for dependency injection</span></span>

<span data-ttu-id="5a7ae-314">Osvědčené postupy jsou následující:</span><span class="sxs-lookup"><span data-stu-id="5a7ae-314">Best practices are to:</span></span>

* <span data-ttu-id="5a7ae-315">Navrhujte služby tak, aby využívaly vkládání závislostí pro získání jejich závislostí.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-315">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="5a7ae-316">Vyhněte se volání stavová a statické metody.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-316">Avoid stateful, static method calls.</span></span>
* <span data-ttu-id="5a7ae-317">Vyhněte se přímému vytváření instancí závislých tříd v rámci služeb.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-317">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="5a7ae-318">Přímé vytváření instancí způsobuje přímou závislost na konkrétní implementaci.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-318">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="5a7ae-319">Malé, skvěle a snadno otestované, vytvořit třídy aplikace.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-319">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="5a7ae-320">Pokud má třída nepřiměřeně mnoho vložených závislostí, je to obecně znakem toho, že má třída příliš mnoho zodpovědností a porušuje tak [zásadu jediné zodpovědnosti (Single Responsibility Principle, SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="5a7ae-320">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="5a7ae-321">V tom případě se pokuste refaktorovat třídu tak, že některé z jeho zodpovědností přesunete do nové třídy.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-321">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="5a7ae-322">Mějte na paměti, že třídy modelů stránek Razor a třídy kontrolerů MVC by měly být zaměřeny na aspekty uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-322">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="5a7ae-323">Business pravidla a konkrétní implementace přístupu k datům by měly být v odpovídajících třídách respektující [princip oddělení zopovědnosti](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="5a7ae-323">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="5a7ae-324">Uvolnění služeb</span><span class="sxs-lookup"><span data-stu-id="5a7ae-324">Disposal of services</span></span>

<span data-ttu-id="5a7ae-325">Kontejner volá metodu <xref:System.IDisposable.Dispose*> u všech typů, které vytvořil a které implementují rozhraní <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-325">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="5a7ae-326">Instance přidané do kontejneru pomocí uživatelské kódu nejsou automaticky uvolňovány.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-326">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

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

## <a name="default-service-container-replacement"></a><span data-ttu-id="5a7ae-327">Nahrazení výchozího kontejneru služeb</span><span class="sxs-lookup"><span data-stu-id="5a7ae-327">Default service container replacement</span></span>

<span data-ttu-id="5a7ae-328">Integrovaný kontejner služeb je primárně určen pro naplnění potřeb frameworku a většiny uživatelských aplikací.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-328">The built-in service container is meant to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="5a7ae-329">Doporučujeme používat integrovaný kontejner, dokud nebudete potřebovat specifické funkce nepodporované kontejnerem.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-329">We recommend using the built-in container unless you need a specific feature that it doesn't support.</span></span> <span data-ttu-id="5a7ae-330">Některé z funkcí podporovaných v kontejnerech 3. stran neobsažených ve výchozím kontejneru jsou:</span><span class="sxs-lookup"><span data-stu-id="5a7ae-330">Some of the features supported in 3rd party containers not found in the built-in container:</span></span>

* <span data-ttu-id="5a7ae-331">Vkládání pomocí vlastností</span><span class="sxs-lookup"><span data-stu-id="5a7ae-331">Property injection</span></span>
* <span data-ttu-id="5a7ae-332">Vkládání podle názvu</span><span class="sxs-lookup"><span data-stu-id="5a7ae-332">Injection based on name</span></span>
* <span data-ttu-id="5a7ae-333">Vnořené kontejnery</span><span class="sxs-lookup"><span data-stu-id="5a7ae-333">Child containers</span></span>
* <span data-ttu-id="5a7ae-334">Vlastní správa životnosti</span><span class="sxs-lookup"><span data-stu-id="5a7ae-334">Custom lifetime management</span></span>
* <span data-ttu-id="5a7ae-335">Podpora `Func<T>` pro línou inicializaci</span><span class="sxs-lookup"><span data-stu-id="5a7ae-335">`Func<T>` support for lazy initialization</span></span>

<span data-ttu-id="5a7ae-336">Pro seznam některých kontejnerů podporujících adaptéry, vizte [Soubor readme.md ke Vkládání závislostí](https://github.com/aspnet/Extensions/tree/master/src/DependencyInjection).</span><span class="sxs-lookup"><span data-stu-id="5a7ae-336">See the [Dependency Injection readme.md file](https://github.com/aspnet/Extensions/tree/master/src/DependencyInjection) for a list of some of the containers that support adapters.</span></span>

<span data-ttu-id="5a7ae-337">Následující příklad nahrazuje integrovaný kontejner kontejnerem [Autofac](https://autofac.org/):</span><span class="sxs-lookup"><span data-stu-id="5a7ae-337">The following sample replaces the built-in container with [Autofac](https://autofac.org/):</span></span>

* <span data-ttu-id="5a7ae-338">Nainstalujte odpovídající balíčky kontejneru:</span><span class="sxs-lookup"><span data-stu-id="5a7ae-338">Install the appropriate container package(s):</span></span>

  * [<span data-ttu-id="5a7ae-339">Autofac</span><span class="sxs-lookup"><span data-stu-id="5a7ae-339">Autofac</span></span>](https://www.nuget.org/packages/Autofac/)
  * [<span data-ttu-id="5a7ae-340">Autofac.Extensions.DependencyInjection</span><span class="sxs-lookup"><span data-stu-id="5a7ae-340">Autofac.Extensions.DependencyInjection</span></span>](https://www.nuget.org/packages/Autofac.Extensions.DependencyInjection/)

* <span data-ttu-id="5a7ae-341">Nakonfigurujte kontejner v `Startup.ConfigureServices` a vraťte `IServiceProvider`:</span><span class="sxs-lookup"><span data-stu-id="5a7ae-341">Configure the container in `Startup.ConfigureServices` and return an `IServiceProvider`:</span></span>

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

    <span data-ttu-id="5a7ae-342">`Startup.ConfigureServices` musí vracet `IServiceProvider` pro použití kontejneru 3. stran.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-342">To use a 3rd party container, `Startup.ConfigureServices` must return `IServiceProvider`.</span></span>

* <span data-ttu-id="5a7ae-343">Konfigurace Autofacu v `DefaultModule`:</span><span class="sxs-lookup"><span data-stu-id="5a7ae-343">Configure Autofac in `DefaultModule`:</span></span>

    ```csharp
    public class DefaultModule : Module
    {
        protected override void Load(ContainerBuilder builder)
        {
            builder.RegisterType<CharacterRepository>().As<ICharacterRepository>();
        }
    }
    ```

<span data-ttu-id="5a7ae-344">Za běhu je použit Autofac pro rozhodování typů a vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-344">At runtime, Autofac is used to resolve types and inject dependencies.</span></span> <span data-ttu-id="5a7ae-345">Další informace o používání Autofac s ASP.NET Core naleznete v [dokumentaci Autofac](https://docs.autofac.org/en/latest/integration/aspnetcore.html).</span><span class="sxs-lookup"><span data-stu-id="5a7ae-345">To learn more about using Autofac with ASP.NET Core, see the [Autofac documentation](https://docs.autofac.org/en/latest/integration/aspnetcore.html).</span></span>

### <a name="thread-safety"></a><span data-ttu-id="5a7ae-346">Bezpečný přístup z více vláken</span><span class="sxs-lookup"><span data-stu-id="5a7ae-346">Thread safety</span></span>

<span data-ttu-id="5a7ae-347">Vytvoření deklarace služeb typu singleton bezpečné pro vlákna.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-347">Create thread-safe singleton services.</span></span> <span data-ttu-id="5a7ae-348">Pokud služby typu singleton obsahuje závislost na přechodné služby, přechodné služba může také požadovat bezpečnost vlákna v závislosti, jak se používají v typu singleton.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-348">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="5a7ae-349">Metoda factory jedné služby, jako je například druhý argument [AddSingleton\<TService > (IServiceCollection, Func\<IServiceProvider, TService >)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), nemusí být bezpečné pro vlákna.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-349">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="5a7ae-350">Stejně jako u typového (`static`) konstruktoru je zaručeno, že je volán jednou jediným vláknem.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-350">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="5a7ae-351">Doporučení</span><span class="sxs-lookup"><span data-stu-id="5a7ae-351">Recommendations</span></span>

* <span data-ttu-id="5a7ae-352">`async/await` a `Task` závislosti služby rozlišení není podporováno.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-352">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="5a7ae-353">C#nepodporuje asynchronní konstruktory; doporučený model je proto používání asynchronních metod po synchronně překladu služby.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-353">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="5a7ae-354">Vyhněte se ukládání dat a konfigurace přímo do kontejneru služeb.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-354">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="5a7ae-355">Například nákupní košík uživatele by neměl být přidáván do kontejneru služeb.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-355">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="5a7ae-356">Konfigurace by měla používat [vzor možností (options pattern)](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="5a7ae-356">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="5a7ae-357">Podobně se vystříhejte použití objektů "držící data", jejichž jediným účelem je poskytnutí přístupu k některému jinému objektu.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-357">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="5a7ae-358">Je lepší požádat o skutečné položky prostřednictvím DI.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-358">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="5a7ae-359">Vyhněte se statickému přístupu ke službám (například staticky typovaným [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) pro použití jinde).</span><span class="sxs-lookup"><span data-stu-id="5a7ae-359">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="5a7ae-360">Vyhněte se použití *služby lokátoru vzor*.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-360">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="5a7ae-361">Není třeba vyvolat <xref:System.IServiceProvider.GetService*> při DI místo toho můžete získat instanci služby:</span><span class="sxs-lookup"><span data-stu-id="5a7ae-361">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="5a7ae-362">**Nesprávný:**</span><span class="sxs-lookup"><span data-stu-id="5a7ae-362">**Incorrect:**</span></span>

  ```csharp
  public void MyMethod()
  {
      var options = 
          _services.GetService<IOptionsMonitor<MyOptions>>();
      var option = options.CurrentValue.Option;

      ...
  }
  ```

  <span data-ttu-id="5a7ae-363">**Správné**:</span><span class="sxs-lookup"><span data-stu-id="5a7ae-363">**Correct**:</span></span>

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

* <span data-ttu-id="5a7ae-364">Další variantou Lokátor služby, aby se vkládá objekt factory, který řeší závislosti za běhu.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-364">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="5a7ae-365">Obě tyto postupy kombinace [ovládacího prvku inverzi](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategie.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-365">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

* <span data-ttu-id="5a7ae-366">Vyhněte se statickému přístupu k `HttpContext` (například [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="5a7ae-366">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="5a7ae-367">Stejně jako u všech doporučení mohou nastat situace, ve kterých je možné tato doporučení ignorovat.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-367">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="5a7ae-368">Výjimky se vyskytují jen vzácně &mdash; nejčastěji jsou to speciální případy uvnitř frameworku samotného.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-368">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="5a7ae-369">DI je *alternativní* na vzorech přístupu statická/globální objekt.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-369">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="5a7ae-370">Nebudete moci využít výhod DI, jsou-li zkombinovány s přístupem statický objekt.</span><span class="sxs-lookup"><span data-stu-id="5a7ae-370">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5a7ae-371">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="5a7ae-371">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="5a7ae-372">Psaní čistého kódu v ASP.NET Core pomocí vkládání závislostí (MSDN)</span><span class="sxs-lookup"><span data-stu-id="5a7ae-372">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="5a7ae-373">Princip explicitních závislostí</span><span class="sxs-lookup"><span data-stu-id="5a7ae-373">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="5a7ae-374">Kontejner inverze závislostí a vzor vkládání závislostí (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="5a7ae-374">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="5a7ae-375">Postup pro registraci služeb s více rozhraními v ASP.NET Core DI</span><span class="sxs-lookup"><span data-stu-id="5a7ae-375">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

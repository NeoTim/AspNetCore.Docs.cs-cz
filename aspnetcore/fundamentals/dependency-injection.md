---
title: Vkládání závislostí v ASP.NET Core
author: guardrex
description: Zjistěte, jak ASP.NET Core implementuje vkládání závislostí a jak se používá.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/06/2019
uid: fundamentals/dependency-injection
ms.openlocfilehash: 27ae8ac979c267c666d6d63f4d1dd862ff20edba
ms.sourcegitcommit: 2eb605f4f20ac4dd9de6c3b3e3453e108a357a21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68819858"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="0c460-103">Vkládání závislostí v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0c460-103">Dependency injection in ASP.NET Core</span></span>

<span data-ttu-id="0c460-104">[Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com)a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="0c460-104">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="0c460-105">ASP.NET Core podporuje návrhový vzor vkládání závislostí (Dependency Injection, DI), což je technika používaná pro dosažení [inverze řízení (Inversion of Control, IOC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) mezi třídami a jejich závislostmi.</span><span class="sxs-lookup"><span data-stu-id="0c460-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="0c460-106">Další informace, které jsou specifické pro vkládání závislostí v rámci <xref:mvc/controllers/dependency-injection>řadičů MVC, najdete v tématu.</span><span class="sxs-lookup"><span data-stu-id="0c460-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="0c460-107">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0c460-107">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="0c460-108">Přehled vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="0c460-108">Overview of dependency injection</span></span>

<span data-ttu-id="0c460-109">*Závislost* je libovolný objekt, který je vyžadován jiným objektem.</span><span class="sxs-lookup"><span data-stu-id="0c460-109">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="0c460-110">Prohlédněte si následující třídu `MyDependency` s metodou `WriteMessage`, na které závisí jiné třídy v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="0c460-110">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="0c460-111">Pro zpřístupnění metody `WriteMessage` v jiné třídě je možné vytvořit instanci třídy `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="0c460-111">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="0c460-112">Třída `MyDependency` je závislostí třídy `IndexModel`:</span><span class="sxs-lookup"><span data-stu-id="0c460-112">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="0c460-113">Třída vytváří instanci třídy `MyDependency`, na které přímo závisí.</span><span class="sxs-lookup"><span data-stu-id="0c460-113">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="0c460-114">Přímé závislosti v kódu (jako ta v předchozím příkladu) jsou problematické a měli byste se jim vyhnout z následujících důvodů:</span><span class="sxs-lookup"><span data-stu-id="0c460-114">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="0c460-115">Chcete-li nahradit `MyDependency` jinou implementací, musí být modifikována třída, která na ní závisí.</span><span class="sxs-lookup"><span data-stu-id="0c460-115">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="0c460-116">Pokud má třída `MyDependency` sama závislosti, musí být taktéž nakonfigurovány v dané třídě.</span><span class="sxs-lookup"><span data-stu-id="0c460-116">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="0c460-117">Ve velkých projektech s více třídami závislých na `MyDependency` je tak konfigurační kód roztroušen na různých místech aplikace.</span><span class="sxs-lookup"><span data-stu-id="0c460-117">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="0c460-118">Tento způsob implementace je obtížný na jednotkové testování.</span><span class="sxs-lookup"><span data-stu-id="0c460-118">This implementation is difficult to unit test.</span></span> <span data-ttu-id="0c460-119">Aplikace by měla používat mock nebo stub třídy `MyDependency`, což však není možné s tímto přístupem.</span><span class="sxs-lookup"><span data-stu-id="0c460-119">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="0c460-120">Vkládání závislostí řeší tyto problémy prostřednictvím:</span><span class="sxs-lookup"><span data-stu-id="0c460-120">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="0c460-121">Použití rozhraní nebo základní třídy k abstrakci implementace závislosti.</span><span class="sxs-lookup"><span data-stu-id="0c460-121">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="0c460-122">Registrace závislosti v kontejneru služeb.</span><span class="sxs-lookup"><span data-stu-id="0c460-122">Registration of the dependency in a service container.</span></span> <span data-ttu-id="0c460-123">ASP.NET Core poskytuje integrovaný kontejner <xref:System.IServiceProvider>služeb.</span><span class="sxs-lookup"><span data-stu-id="0c460-123">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="0c460-124">Služby jsou registrované v metodě `Startup.ConfigureServices` aplikace.</span><span class="sxs-lookup"><span data-stu-id="0c460-124">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="0c460-125">*Vkládání* služeb do konstruktoru třídy, ve které se používá.</span><span class="sxs-lookup"><span data-stu-id="0c460-125">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="0c460-126">Framework přebírá zodpovědnost za vytváření instancí závislostí a jejich uvolňování, kdy již nejsou dále potřebné.</span><span class="sxs-lookup"><span data-stu-id="0c460-126">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="0c460-127">V [ukázkové aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) definuje rozhraní `IMyDependency` metody, které poskytuje služba aplikaci:</span><span class="sxs-lookup"><span data-stu-id="0c460-127">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="0c460-128">Toto rozhraní je implementováno konkrétním typem `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="0c460-128">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="0c460-129">`MyDependency`vyžádá <xref:Microsoft.Extensions.Logging.ILogger`1> v jeho konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="0c460-129">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="0c460-130">Není neobvyklé používat zřetězené vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="0c460-130">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="0c460-131">Každá požadovaná závislost může v zápětí požadovat své vlastní závislosti.</span><span class="sxs-lookup"><span data-stu-id="0c460-131">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="0c460-132">Kontejner řeší závislosti v grafu a vrací plně vyřešené služby.</span><span class="sxs-lookup"><span data-stu-id="0c460-132">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="0c460-133">Množina závislostí, které musí být rozhodnuty, se obvykle označuje jako *strom závislostí*, *graf závislostí*, nebo *graf objektů*.</span><span class="sxs-lookup"><span data-stu-id="0c460-133">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="0c460-134">`IMyDependency` a `ILogger<TCategoryName>` musí být zaregistrovány v kontejneru služeb.</span><span class="sxs-lookup"><span data-stu-id="0c460-134">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="0c460-135">`IMyDependency`je zaregistrován v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="0c460-135">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0c460-136">`ILogger<TCategoryName>` je registrován infrastrukturou pro abstrakci protokolování, takže je [službou poskytovanou frameworkem](#framework-provided-services) registrovanou ve výchozím nastavení frameworkem.</span><span class="sxs-lookup"><span data-stu-id="0c460-136">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="0c460-137">Kontejner řeší `ILogger<TCategoryName>` s využitím [(generických) otevřených typů](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), čímž eliminuje nutnost registrovat každý [(generický) konstruovaný typ](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="0c460-137">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<T>), typeof(Logger<T>));
```

<span data-ttu-id="0c460-138">V ukázkové aplikaci je služba `IMyDependency` registrována s konkrétním typem `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="0c460-138">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="0c460-139">Registrace specifikuje rámec životnosti služby na životnost jednoho požadavku.</span><span class="sxs-lookup"><span data-stu-id="0c460-139">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="0c460-140">[Životnosti služby](#service-lifetimes) jsou popsány dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="0c460-140">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="0c460-141">Každá rozšiřující metoda `services.Add{SERVICE_NAME}` přidává (a potenciálně konfiguruje) služby.</span><span class="sxs-lookup"><span data-stu-id="0c460-141">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="0c460-142">Například `services.AddMvc()` přidává služby Stránek Razor a MVC.</span><span class="sxs-lookup"><span data-stu-id="0c460-142">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="0c460-143">Doporučujeme, aby v aplikacích byla tato konvence dodržena.</span><span class="sxs-lookup"><span data-stu-id="0c460-143">We recommended that apps follow this convention.</span></span> <span data-ttu-id="0c460-144">Umístěte rozšiřující metody do jmenného prostoru [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) pro zapouzdření skupin registrací služeb.</span><span class="sxs-lookup"><span data-stu-id="0c460-144">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="0c460-145">Pokud konstruktor služby vyžaduje [vestavěný typ](/dotnet/csharp/language-reference/keywords/built-in-types-table), například `string`,, může být typ vložen pomocí [Konfigurace](xref:fundamentals/configuration/index) nebo [vzoru možností](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="0c460-145">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="0c460-146">Instance služby je požadována prostřednictvím konstruktoru třídy, kde se služba využívá a přiřazuje do privátního pole.</span><span class="sxs-lookup"><span data-stu-id="0c460-146">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="0c460-147">Toto pole se používá pro přístup ke službě podle potřeby v rámci celé třídy.</span><span class="sxs-lookup"><span data-stu-id="0c460-147">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="0c460-148">V ukázkové aplikaci je požadována instance `IMyDependency` a posléze použita k volání metody `WriteMessage` dané služby:</span><span class="sxs-lookup"><span data-stu-id="0c460-148">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="framework-provided-services"></a><span data-ttu-id="0c460-149">Služby poskytované frameworkem</span><span class="sxs-lookup"><span data-stu-id="0c460-149">Framework-provided services</span></span>

<span data-ttu-id="0c460-150">Metoda `Startup.ConfigureServices` zodpovídá za definování služeb používaných aplikací, včetně funkcí platformy, jako je například Entity Framework Core a ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="0c460-150">The `Startup.ConfigureServices` method is responsible for defining the services the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="0c460-151">Zpočátku jsou v `IServiceCollection`, který je poskytován metodě `ConfigureServices`, definovány následující služby (v závislosti na [konfiguraci hostitele](xref:fundamentals/index#host)):</span><span class="sxs-lookup"><span data-stu-id="0c460-151">Initially, the `IServiceCollection` provided to `ConfigureServices` has the following services defined (depending on [how the host was configured](xref:fundamentals/index#host)):</span></span>

| <span data-ttu-id="0c460-152">Typ služby</span><span class="sxs-lookup"><span data-stu-id="0c460-152">Service Type</span></span> | <span data-ttu-id="0c460-153">Životnost</span><span class="sxs-lookup"><span data-stu-id="0c460-153">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="0c460-154">Přechodná</span><span class="sxs-lookup"><span data-stu-id="0c460-154">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="0c460-155">Singleton</span><span class="sxs-lookup"><span data-stu-id="0c460-155">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="0c460-156">Singleton</span><span class="sxs-lookup"><span data-stu-id="0c460-156">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="0c460-157">Singleton</span><span class="sxs-lookup"><span data-stu-id="0c460-157">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="0c460-158">Přechodná</span><span class="sxs-lookup"><span data-stu-id="0c460-158">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="0c460-159">Singleton</span><span class="sxs-lookup"><span data-stu-id="0c460-159">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="0c460-160">Přechodná</span><span class="sxs-lookup"><span data-stu-id="0c460-160">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="0c460-161">Singleton</span><span class="sxs-lookup"><span data-stu-id="0c460-161">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="0c460-162">Singleton</span><span class="sxs-lookup"><span data-stu-id="0c460-162">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="0c460-163">Singleton</span><span class="sxs-lookup"><span data-stu-id="0c460-163">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="0c460-164">Přechodná</span><span class="sxs-lookup"><span data-stu-id="0c460-164">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="0c460-165">Singleton</span><span class="sxs-lookup"><span data-stu-id="0c460-165">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="0c460-166">Singleton</span><span class="sxs-lookup"><span data-stu-id="0c460-166">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="0c460-167">Singleton</span><span class="sxs-lookup"><span data-stu-id="0c460-167">Singleton</span></span> |

<span data-ttu-id="0c460-168">Pokud je dostupná rozšiřující metoda rozšiřující kolekci služeb o registraci služby (případě jejích závislých služeb, je-li to požadováno), je zvykem použít jedinou rozšiřující metodu `Add{SERVICE_NAME}` k registraci všech služeb vyžadovaných danou službu.</span><span class="sxs-lookup"><span data-stu-id="0c460-168">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="0c460-169">Následující kód je příkladem přidání dalších služeb do kontejneru pomocí metod rozšíření [\<AddDbContext TContext >](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext), <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>a <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*>:</span><span class="sxs-lookup"><span data-stu-id="0c460-169">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext), <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>, and <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*>:</span></span>

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

<span data-ttu-id="0c460-170">Další informace najdete <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> ve třídě v dokumentaci k rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="0c460-170">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="0c460-171">Životnost služeb</span><span class="sxs-lookup"><span data-stu-id="0c460-171">Service lifetimes</span></span>

<span data-ttu-id="0c460-172">Zvolte odpovídající životnost pro každou registrovanou službu.</span><span class="sxs-lookup"><span data-stu-id="0c460-172">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="0c460-173">Služby ASP.NET Core můžete nakonfigurovat s následujícími životnostmi:</span><span class="sxs-lookup"><span data-stu-id="0c460-173">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="0c460-174">Přechodná</span><span class="sxs-lookup"><span data-stu-id="0c460-174">Transient</span></span>

<span data-ttu-id="0c460-175">Služba přechodných životností (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) se vytvoří pokaždé, když se požadují z kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="0c460-175">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="0c460-176">Tato životnost je vhodná pro jednoduché, bezstavové služby.</span><span class="sxs-lookup"><span data-stu-id="0c460-176">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="0c460-177">Obor</span><span class="sxs-lookup"><span data-stu-id="0c460-177">Scoped</span></span>

<span data-ttu-id="0c460-178">Oborové služby ()<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>s rozsahem platnosti () se vytvoří jednou za požadavek klienta (připojení).</span><span class="sxs-lookup"><span data-stu-id="0c460-178">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="0c460-179">Při použití služby s vymezenou živostností v middlewaru vkládejte službu do metody `Invoke` nebo `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="0c460-179">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="0c460-180">Nevkládejte službu prostřednictvím konstruktoru, protože se služba bude chovat se jako singleton.</span><span class="sxs-lookup"><span data-stu-id="0c460-180">Don't inject via constructor injection because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="0c460-181">Další informace naleznete v tématu <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="0c460-181">For more information, see <xref:fundamentals/middleware/index>.</span></span>

### <a name="singleton"></a><span data-ttu-id="0c460-182">Singleton</span><span class="sxs-lookup"><span data-stu-id="0c460-182">Singleton</span></span>

<span data-ttu-id="0c460-183">Služba singleton životnosti<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>() je vytvořena při prvním vyžádání (nebo při `Startup.ConfigureServices` spuštění a instance je určena pro registraci služby).</span><span class="sxs-lookup"><span data-stu-id="0c460-183">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="0c460-184">Každý další požadavek použije stejnou instanci.</span><span class="sxs-lookup"><span data-stu-id="0c460-184">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="0c460-185">Pokud je v aplikaci vyžadováno chování ve stylu návrhového vzoru Singleton, doporučuje se použít kontejner služeb ke správě životnosti takového objektu.</span><span class="sxs-lookup"><span data-stu-id="0c460-185">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="0c460-186">Neposkytujte vlastní implementaci návrhového vzoru Singleton a umožněte tak uživatelskému kódu spravovat životnost objektu v rámci třídy.</span><span class="sxs-lookup"><span data-stu-id="0c460-186">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="0c460-187">Je nebezpečné získávat vymezené služby ze singletonů.</span><span class="sxs-lookup"><span data-stu-id="0c460-187">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="0c460-188">Může to způsobit nesprávný stav služby při zpracování následujících požadavků.</span><span class="sxs-lookup"><span data-stu-id="0c460-188">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="0c460-189">Metody registrace služby</span><span class="sxs-lookup"><span data-stu-id="0c460-189">Service registration methods</span></span>

<span data-ttu-id="0c460-190">Každá metoda rozšíření registrace služby nabízí přetížení, které jsou užitečné v konkrétních scénářích.</span><span class="sxs-lookup"><span data-stu-id="0c460-190">Each service registration extension method offers overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="0c460-191">Metoda</span><span class="sxs-lookup"><span data-stu-id="0c460-191">Method</span></span> | <span data-ttu-id="0c460-192">Automatické</span><span class="sxs-lookup"><span data-stu-id="0c460-192">Automatic</span></span><br><span data-ttu-id="0c460-193">odkazy objektů</span><span class="sxs-lookup"><span data-stu-id="0c460-193">object</span></span><br><span data-ttu-id="0c460-194">odvod</span><span class="sxs-lookup"><span data-stu-id="0c460-194">disposal</span></span> | <span data-ttu-id="0c460-195">Několik</span><span class="sxs-lookup"><span data-stu-id="0c460-195">Multiple</span></span><br><span data-ttu-id="0c460-196">implementace</span><span class="sxs-lookup"><span data-stu-id="0c460-196">implementations</span></span> | <span data-ttu-id="0c460-197">Pass – argumenty</span><span class="sxs-lookup"><span data-stu-id="0c460-197">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="0c460-198">Příklad:</span><span class="sxs-lookup"><span data-stu-id="0c460-198">Example:</span></span><br>`services.AddScoped<IMyDep, MyDep>();` | <span data-ttu-id="0c460-199">Ano</span><span class="sxs-lookup"><span data-stu-id="0c460-199">Yes</span></span> | <span data-ttu-id="0c460-200">Ano</span><span class="sxs-lookup"><span data-stu-id="0c460-200">Yes</span></span> | <span data-ttu-id="0c460-201">Ne</span><span class="sxs-lookup"><span data-stu-id="0c460-201">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="0c460-202">Příklady:</span><span class="sxs-lookup"><span data-stu-id="0c460-202">Examples:</span></span><br>`services.AddScoped<IMyDep>(sp => new MyDep());`<br>`services.AddScoped<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="0c460-203">Ano</span><span class="sxs-lookup"><span data-stu-id="0c460-203">Yes</span></span> | <span data-ttu-id="0c460-204">Ano</span><span class="sxs-lookup"><span data-stu-id="0c460-204">Yes</span></span> | <span data-ttu-id="0c460-205">Ano</span><span class="sxs-lookup"><span data-stu-id="0c460-205">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="0c460-206">Příklad:</span><span class="sxs-lookup"><span data-stu-id="0c460-206">Example:</span></span><br>`services.AddScoped<MyDep>();` | <span data-ttu-id="0c460-207">Ano</span><span class="sxs-lookup"><span data-stu-id="0c460-207">Yes</span></span> | <span data-ttu-id="0c460-208">Ne</span><span class="sxs-lookup"><span data-stu-id="0c460-208">No</span></span> | <span data-ttu-id="0c460-209">Ne</span><span class="sxs-lookup"><span data-stu-id="0c460-209">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="0c460-210">Příklady:</span><span class="sxs-lookup"><span data-stu-id="0c460-210">Examples:</span></span><br>`services.AddScoped<IMyDep>(new MyDep());`<br>`services.AddScoped<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="0c460-211">Ne</span><span class="sxs-lookup"><span data-stu-id="0c460-211">No</span></span> | <span data-ttu-id="0c460-212">Ano</span><span class="sxs-lookup"><span data-stu-id="0c460-212">Yes</span></span> | <span data-ttu-id="0c460-213">Ano</span><span class="sxs-lookup"><span data-stu-id="0c460-213">Yes</span></span> |
| `Add{LIFETIME}(new {IMPLEMENTATION})`<br><span data-ttu-id="0c460-214">Příklady:</span><span class="sxs-lookup"><span data-stu-id="0c460-214">Examples:</span></span><br>`services.AddScoped(new MyDep());`<br>`services.AddScoped(new MyDep("A string!"));` | <span data-ttu-id="0c460-215">Ne</span><span class="sxs-lookup"><span data-stu-id="0c460-215">No</span></span> | <span data-ttu-id="0c460-216">Ne</span><span class="sxs-lookup"><span data-stu-id="0c460-216">No</span></span> | <span data-ttu-id="0c460-217">Ano</span><span class="sxs-lookup"><span data-stu-id="0c460-217">Yes</span></span> |

<span data-ttu-id="0c460-218">Další informace o vyřazení typů najdete v části věnované [vyřazení služeb](#disposal-of-services) .</span><span class="sxs-lookup"><span data-stu-id="0c460-218">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="0c460-219">Běžným scénářem pro více implementací je vytvoření [typů pro testování](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="0c460-219">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="0c460-220">`TryAdd{LIFETIME}`metody registrují službu jenom v případě, že už není zaregistrovaná implementace.</span><span class="sxs-lookup"><span data-stu-id="0c460-220">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="0c460-221">V následujícím příkladu se první řádek registruje `MyDependency` pro. `IMyDependency`</span><span class="sxs-lookup"><span data-stu-id="0c460-221">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="0c460-222">Druhý řádek nemá žádný účinek, protože `IMyDependency` už má registrovanou implementaci:</span><span class="sxs-lookup"><span data-stu-id="0c460-222">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="0c460-223">Další informace naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="0c460-223">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="0c460-224">Metody [TryAddEnumerable (ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) registrují službu pouze v případě, že již není implementace *stejného typu*.</span><span class="sxs-lookup"><span data-stu-id="0c460-224">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="0c460-225">Několik služeb je vyřešeno `IEnumerable<{SERVICE}>`prostřednictvím.</span><span class="sxs-lookup"><span data-stu-id="0c460-225">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="0c460-226">Při registraci služeb chce vývojář přidat instanci jenom v případě, že už jeden ze stejného typu není přidaný.</span><span class="sxs-lookup"><span data-stu-id="0c460-226">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="0c460-227">Obecně je tato metoda používána autory knihovny k tomu, aby nedocházelo k registraci dvou kopií instance v kontejneru.</span><span class="sxs-lookup"><span data-stu-id="0c460-227">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="0c460-228">V následujícím příkladu se první řádek registruje `MyDep` pro. `IMyDep1`</span><span class="sxs-lookup"><span data-stu-id="0c460-228">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="0c460-229">Druhý řádek se `MyDep` registruje `IMyDep2`pro.</span><span class="sxs-lookup"><span data-stu-id="0c460-229">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="0c460-230">Třetí řádek nemá žádný vliv, protože `IMyDep1` už má registrovanou `MyDep`implementaci:</span><span class="sxs-lookup"><span data-stu-id="0c460-230">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="0c460-231">Chování vkládání pomocí konstruktoru</span><span class="sxs-lookup"><span data-stu-id="0c460-231">Constructor injection behavior</span></span>

<span data-ttu-id="0c460-232">Služby mohou být řešeny pomocí dvou mechanismů:</span><span class="sxs-lookup"><span data-stu-id="0c460-232">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="0c460-233"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>&ndash; Povoluje vytvoření objektu bez registrace služby v kontejneru pro vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="0c460-233"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities> &ndash; Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="0c460-234">`ActivatorUtilities` se používá s uživatelsky orientovanými abstrakcemi, jako jsou například Tag Helpery, kontrolery MVC a bindery modelů.</span><span class="sxs-lookup"><span data-stu-id="0c460-234">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="0c460-235">Konstruktory mohou přijímat argumenty, které nejsou poskytovány v rámci vkládání závislostí, ale takové argumenty musí mít přiřazené výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="0c460-235">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="0c460-236">Pokud jsou služby řešeny pomocí `IServiceProvider` nebo `ActivatorUtilities`, pak je vyžadován *veřejný* konstruktor.</span><span class="sxs-lookup"><span data-stu-id="0c460-236">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, constructor injection requires a *public* constructor.</span></span>

<span data-ttu-id="0c460-237">Pokud jsou služby řešeny pomocí `ActivatorUtilities`, konstruktorová injekce vyžaduje, aby existoval pouze jediný použitelný konstruktor.</span><span class="sxs-lookup"><span data-stu-id="0c460-237">When services are resolved by `ActivatorUtilities`, constructor injection requires that only one applicable constructor exists.</span></span> <span data-ttu-id="0c460-238">Přetížení konstruktoru je podporováno, ale může existovat pouze jedno přetížení, jehož argumenty jsou splnitelné vkládáním závislostí.</span><span class="sxs-lookup"><span data-stu-id="0c460-238">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="0c460-239">Kontexty Entity Frameworku</span><span class="sxs-lookup"><span data-stu-id="0c460-239">Entity Framework contexts</span></span>

<span data-ttu-id="0c460-240">Kontexty Entity Frameworku jsou obvykle přidány do kontejneru služeb s [vymezenou životností](#service-lifetimes), protože operace webové aplikace nad databází jsou obvykle vymezeny na požadavek klienta.</span><span class="sxs-lookup"><span data-stu-id="0c460-240">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="0c460-241">Výchozí doba životnosti je vymezená, pokud není specifikována v přetížení metody [AddDbContext\<TContext >](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) při registraci databázového kontextu.</span><span class="sxs-lookup"><span data-stu-id="0c460-241">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="0c460-242">Služby s danou životností by neměly používat databázový kontext s životností kratší, než je životnost služby.</span><span class="sxs-lookup"><span data-stu-id="0c460-242">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="0c460-243">Životnosti a možnosti registrace</span><span class="sxs-lookup"><span data-stu-id="0c460-243">Lifetime and registration options</span></span>

<span data-ttu-id="0c460-244">Na ukázku rozdílů mezi životnostmi a možnostmi registrace si prohlédněte následující rozhraní reprezentující úlohy jako operace s jedinečným identifikátorem `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="0c460-244">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="0c460-245">V závislosti na tom, jak je životnost této služby nakonfigurována pro následující rozhraní, poskytne kontejner stejnou nebo rozdílnou instanci služby během vyžádání třídou:</span><span class="sxs-lookup"><span data-stu-id="0c460-245">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="0c460-246">Rozhraní jsou implementovány ve třídě `Operation`.</span><span class="sxs-lookup"><span data-stu-id="0c460-246">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="0c460-247">Konstruktor `Operation` vytvoří identifikátor GUID, pokud není explicitně poskytnut:</span><span class="sxs-lookup"><span data-stu-id="0c460-247">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="0c460-248">Služba `OperationService` je zaregistrována tak, aby závisela na jednotlivých typech tříd `Operation`.</span><span class="sxs-lookup"><span data-stu-id="0c460-248">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="0c460-249">Když je `OperationService` vyžádána pomocí vkládání závislostí, obdrží buď novou nebo stávající instanci třídy jednotlivých služeb v závislosti na životnosti závislých služeb.</span><span class="sxs-lookup"><span data-stu-id="0c460-249">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="0c460-250">Když se v případě vyžádání z kontejneru `OperationId` vytvoří přechodné služby, `IOperationTransient` `OperationService`liší `OperationId` se od služby.</span><span class="sxs-lookup"><span data-stu-id="0c460-250">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="0c460-251">`OperationService` obdrží novou instanci třídy `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="0c460-251">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="0c460-252">Nová instance implikuje rozdílné `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="0c460-252">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="0c460-253">Když se vytvoří vymezené služby pro jednotlivé požadavky klienta, `OperationId` `IOperationScoped` služba je stejná jako `OperationService` v rámci žádosti klienta.</span><span class="sxs-lookup"><span data-stu-id="0c460-253">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="0c460-254">V rámci požadavků klientů obě služby sdílejí jinou `OperationId` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="0c460-254">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="0c460-255">Pokud se služby instance singleton a singleton vytvoří jednou a použije se pro všechny požadavky klientů a všechny služby, je `OperationId` v rámci všech žádostí o službu konstantní.</span><span class="sxs-lookup"><span data-stu-id="0c460-255">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="0c460-256">V `Startup.ConfigureServices` je každý typ přidán do kontejneru na základě svého pojmenování podle životnosti:</span><span class="sxs-lookup"><span data-stu-id="0c460-256">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="0c460-257">Služba `IOperationSingletonInstance` používá konkrétní instanci se známým ID nastaveným na `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="0c460-257">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="0c460-258">Je pak zřejmé, kdy je tento typ použit (jeho identifikátor GUID obsahuje samé nuly).</span><span class="sxs-lookup"><span data-stu-id="0c460-258">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="0c460-259">Ukázková aplikace demonstruje živostnosti objektů v rámci jednotlivých požadavků a mezi nimi.</span><span class="sxs-lookup"><span data-stu-id="0c460-259">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="0c460-260">Třída `IndexModel` ukázkové aplikace vyžaduje každý druh typu `IOperation` a službu `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="0c460-260">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="0c460-261">Stránka následně vypisuje všechny hodnoty `OperationId` modelu stránky a služby prostřednictvím přiřazených vlastností:</span><span class="sxs-lookup"><span data-stu-id="0c460-261">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="0c460-262">Následující výpis ukazuje výsledek dvou HTTP požadavků:</span><span class="sxs-lookup"><span data-stu-id="0c460-262">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="0c460-263">**První požadavek:**</span><span class="sxs-lookup"><span data-stu-id="0c460-263">**First request:**</span></span>

<span data-ttu-id="0c460-264">Operace kontroleru:</span><span class="sxs-lookup"><span data-stu-id="0c460-264">Controller operations:</span></span>

<span data-ttu-id="0c460-265">Transient: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="0c460-265">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="0c460-266">Obor 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="0c460-266">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="0c460-267">Singleton 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="0c460-267">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="0c460-268">Případě 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="0c460-268">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="0c460-269">Operace `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="0c460-269">`OperationService` operations:</span></span>

<span data-ttu-id="0c460-270">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="0c460-270">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="0c460-271">Obor 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="0c460-271">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="0c460-272">Singleton 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="0c460-272">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="0c460-273">Případě 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="0c460-273">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="0c460-274">**Druhý požadavek:**</span><span class="sxs-lookup"><span data-stu-id="0c460-274">**Second request:**</span></span>

<span data-ttu-id="0c460-275">Operace kontroleru:</span><span class="sxs-lookup"><span data-stu-id="0c460-275">Controller operations:</span></span>

<span data-ttu-id="0c460-276">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="0c460-276">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="0c460-277">Obor 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="0c460-277">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="0c460-278">Singleton 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="0c460-278">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="0c460-279">Případě 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="0c460-279">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="0c460-280">Operace `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="0c460-280">`OperationService` operations:</span></span>

<span data-ttu-id="0c460-281">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="0c460-281">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="0c460-282">Obor 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="0c460-282">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="0c460-283">Singleton 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="0c460-283">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="0c460-284">Případě 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="0c460-284">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="0c460-285">Všimněte si, které hodnoty `OperationId` se liší v rámci požadavku a mezi požadavky:</span><span class="sxs-lookup"><span data-stu-id="0c460-285">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="0c460-286">Objekty s *přechodnou* životností jsou vždy rozdílné.</span><span class="sxs-lookup"><span data-stu-id="0c460-286">*Transient* objects are always different.</span></span> <span data-ttu-id="0c460-287">Přechodná `OperationId` hodnota pro první i druhý požadavek klienta se liší pro obě `OperationService` operace i mezi požadavky klientů.</span><span class="sxs-lookup"><span data-stu-id="0c460-287">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="0c460-288">Každé žádosti o službu a žádosti klienta se poskytne nová instance.</span><span class="sxs-lookup"><span data-stu-id="0c460-288">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="0c460-289">*Vymezené* objekty jsou v požadavku klienta stejné, ale v rámci požadavků klientů se liší.</span><span class="sxs-lookup"><span data-stu-id="0c460-289">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="0c460-290">Objekty se živostností typu *singleton jsou* stejné pro všechny objekty a všechny požadavky bez ohledu na to, jestli je instance `Operation` poskytnuta v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="0c460-290">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="0c460-291">Volání služeb z main</span><span class="sxs-lookup"><span data-stu-id="0c460-291">Call services from main</span></span>

<span data-ttu-id="0c460-292">Vytvořte pomocí [IServiceScopeFactory. CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) pro řešení oboru služby v rámci rozsahu aplikace. <xref:Microsoft.Extensions.DependencyInjection.IServiceScope></span><span class="sxs-lookup"><span data-stu-id="0c460-292">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="0c460-293">Tento způsob je užitečný pro přístup k službám s vymezenou životností při provádění inicializačních úloh během spuštění.</span><span class="sxs-lookup"><span data-stu-id="0c460-293">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="0c460-294">Následující příklad ukazuje, jak získat kontext pro `MyScopedService`: `Program.Main`</span><span class="sxs-lookup"><span data-stu-id="0c460-294">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="0c460-295">Ověření rámce životnosti</span><span class="sxs-lookup"><span data-stu-id="0c460-295">Scope validation</span></span>

<span data-ttu-id="0c460-296">Pokud aplikace běží ve vývojovém prostředí, výchozí poskytovatel služeb provádí kontroly pro ověření toho, že:</span><span class="sxs-lookup"><span data-stu-id="0c460-296">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="0c460-297">Služby s vymezenou živostností nejsou přímo nebo nepřímo rozhodovány z kořenového poskytovatele služeb.</span><span class="sxs-lookup"><span data-stu-id="0c460-297">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="0c460-298">Služby s vymezenou živostností nejsou přímo nebo nepřímo vkládány do singletonů.</span><span class="sxs-lookup"><span data-stu-id="0c460-298">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="0c460-299">Poskytovatel kořenové služby se vytvoří, když <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> se zavolá.</span><span class="sxs-lookup"><span data-stu-id="0c460-299">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="0c460-300">Životnost kořenového poskytovatele služeb odpovídá životnosti aplikace/serveru, tedy poskytovatel vzniká se startem aplikace a je uvolněn při ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="0c460-300">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="0c460-301">Služby s vymezenou životností jsou uvolňovány kontejnerem, který je vytvořil.</span><span class="sxs-lookup"><span data-stu-id="0c460-301">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="0c460-302">Pokud jsou služby s vymezenou životností vytvořeny v kořenovém kontejneru, životnost služby je efektivně povýšen na singleton, protože je uvolněn pomocí kořenového kontejneru pouze při vypnutí serveru/aplikace.</span><span class="sxs-lookup"><span data-stu-id="0c460-302">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="0c460-303">Validací rámce životnosti služeb ošetřuje tyto situace při volání `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="0c460-303">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="0c460-304">Další informace naleznete v tématu <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="0c460-304">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="0c460-305">Služby požadavků</span><span class="sxs-lookup"><span data-stu-id="0c460-305">Request Services</span></span>

<span data-ttu-id="0c460-306">Služby dostupné v rámci požadavků ASP.NET Core z `HttpContext` jsou zpřístupněny prostřednictvím kolekce [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="0c460-306">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="0c460-307">Služby požadavků představují služby nakonfigurované a požadováné v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="0c460-307">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="0c460-308">Pokud objekty specifikují závislosti, jsou řešeny pomocí typů dostupných v `RequestServices`, nikoli `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="0c460-308">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="0c460-309">Aplikace by obvykle neměly používat tyto vlastnosti přímo.</span><span class="sxs-lookup"><span data-stu-id="0c460-309">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="0c460-310">Místo toho získávejte požadované typy pomocí konstruktoru třídy a nechte framework vložit odpovídající závislosti.</span><span class="sxs-lookup"><span data-stu-id="0c460-310">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="0c460-311">To poskytuje třídy, které jsou snáze testovány.</span><span class="sxs-lookup"><span data-stu-id="0c460-311">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="0c460-312">Pro přístup ke kolekci `RequestServices` upřednostněte získávání závislostí jako parametr konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="0c460-312">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="0c460-313">Návrh služeb pro vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="0c460-313">Design services for dependency injection</span></span>

<span data-ttu-id="0c460-314">Osvědčené postupy:</span><span class="sxs-lookup"><span data-stu-id="0c460-314">Best practices are to:</span></span>

* <span data-ttu-id="0c460-315">Navrhujte služby tak, aby využívaly vkládání závislostí pro získání jejich závislostí.</span><span class="sxs-lookup"><span data-stu-id="0c460-315">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="0c460-316">Vyhněte se stavovým a statickým voláním metody.</span><span class="sxs-lookup"><span data-stu-id="0c460-316">Avoid stateful, static method calls.</span></span>
* <span data-ttu-id="0c460-317">Vyhněte se přímému vytváření instancí závislých tříd v rámci služeb.</span><span class="sxs-lookup"><span data-stu-id="0c460-317">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="0c460-318">Přímé vytváření instancí způsobuje přímou závislost na konkrétní implementaci.</span><span class="sxs-lookup"><span data-stu-id="0c460-318">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="0c460-319">Vytvářejte třídy aplikací malými, dobře faktoringně a snadno testované.</span><span class="sxs-lookup"><span data-stu-id="0c460-319">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="0c460-320">Pokud má třída nepřiměřeně mnoho vložených závislostí, je to obecně znakem toho, že má třída příliš mnoho zodpovědností a porušuje tak [zásadu jediné zodpovědnosti (Single Responsibility Principle, SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="0c460-320">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="0c460-321">V tom případě se pokuste refaktorovat třídu tak, že některé z jeho zodpovědností přesunete do nové třídy.</span><span class="sxs-lookup"><span data-stu-id="0c460-321">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="0c460-322">Mějte na paměti, že třídy modelů stránek Razor a třídy kontrolerů MVC by měly být zaměřeny na aspekty uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="0c460-322">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="0c460-323">Business pravidla a konkrétní implementace přístupu k datům by měly být v odpovídajících třídách respektující [princip oddělení zopovědnosti](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="0c460-323">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="0c460-324">Uvolnění služeb</span><span class="sxs-lookup"><span data-stu-id="0c460-324">Disposal of services</span></span>

<span data-ttu-id="0c460-325">Kontejner volá metodu <xref:System.IDisposable.Dispose*> u všech typů, které vytvořil a které implementují rozhraní <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="0c460-325">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="0c460-326">Instance přidané do kontejneru pomocí uživatelské kódu nejsou automaticky uvolňovány.</span><span class="sxs-lookup"><span data-stu-id="0c460-326">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

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

## <a name="default-service-container-replacement"></a><span data-ttu-id="0c460-327">Nahrazení výchozího kontejneru služeb</span><span class="sxs-lookup"><span data-stu-id="0c460-327">Default service container replacement</span></span>

<span data-ttu-id="0c460-328">Integrovaný kontejner služeb je primárně určen pro naplnění potřeb frameworku a většiny uživatelských aplikací.</span><span class="sxs-lookup"><span data-stu-id="0c460-328">The built-in service container is meant to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="0c460-329">Doporučujeme používat integrovaný kontejner, dokud nebudete potřebovat specifické funkce nepodporované kontejnerem.</span><span class="sxs-lookup"><span data-stu-id="0c460-329">We recommend using the built-in container unless you need a specific feature that it doesn't support.</span></span> <span data-ttu-id="0c460-330">Některé z funkcí podporovaných v kontejnerech 3. stran neobsažených ve výchozím kontejneru jsou:</span><span class="sxs-lookup"><span data-stu-id="0c460-330">Some of the features supported in 3rd party containers not found in the built-in container:</span></span>

* <span data-ttu-id="0c460-331">Vkládání pomocí vlastností</span><span class="sxs-lookup"><span data-stu-id="0c460-331">Property injection</span></span>
* <span data-ttu-id="0c460-332">Vkládání na základě názvu</span><span class="sxs-lookup"><span data-stu-id="0c460-332">Injection based on name</span></span>
* <span data-ttu-id="0c460-333">Vnořené kontejnery</span><span class="sxs-lookup"><span data-stu-id="0c460-333">Child containers</span></span>
* <span data-ttu-id="0c460-334">Vlastní správa životnosti</span><span class="sxs-lookup"><span data-stu-id="0c460-334">Custom lifetime management</span></span>
* <span data-ttu-id="0c460-335">Podpora `Func<T>` pro línou inicializaci</span><span class="sxs-lookup"><span data-stu-id="0c460-335">`Func<T>` support for lazy initialization</span></span>

<span data-ttu-id="0c460-336">Pro seznam některých kontejnerů podporujících adaptéry, vizte [Soubor readme.md ke Vkládání závislostí](https://github.com/aspnet/Extensions/tree/master/src/DependencyInjection).</span><span class="sxs-lookup"><span data-stu-id="0c460-336">See the [Dependency Injection readme.md file](https://github.com/aspnet/Extensions/tree/master/src/DependencyInjection) for a list of some of the containers that support adapters.</span></span>

<span data-ttu-id="0c460-337">Následující příklad nahrazuje integrovaný kontejner kontejnerem [Autofac](https://autofac.org/):</span><span class="sxs-lookup"><span data-stu-id="0c460-337">The following sample replaces the built-in container with [Autofac](https://autofac.org/):</span></span>

* <span data-ttu-id="0c460-338">Nainstalujte odpovídající balíčky kontejneru:</span><span class="sxs-lookup"><span data-stu-id="0c460-338">Install the appropriate container package(s):</span></span>

  * [<span data-ttu-id="0c460-339">Autofac</span><span class="sxs-lookup"><span data-stu-id="0c460-339">Autofac</span></span>](https://www.nuget.org/packages/Autofac/)
  * [<span data-ttu-id="0c460-340">Autofac.Extensions.DependencyInjection</span><span class="sxs-lookup"><span data-stu-id="0c460-340">Autofac.Extensions.DependencyInjection</span></span>](https://www.nuget.org/packages/Autofac.Extensions.DependencyInjection/)

* <span data-ttu-id="0c460-341">Nakonfigurujte kontejner v `Startup.ConfigureServices` a vraťte `IServiceProvider`:</span><span class="sxs-lookup"><span data-stu-id="0c460-341">Configure the container in `Startup.ConfigureServices` and return an `IServiceProvider`:</span></span>

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

    <span data-ttu-id="0c460-342">`Startup.ConfigureServices` musí vracet `IServiceProvider` pro použití kontejneru 3. stran.</span><span class="sxs-lookup"><span data-stu-id="0c460-342">To use a 3rd party container, `Startup.ConfigureServices` must return `IServiceProvider`.</span></span>

* <span data-ttu-id="0c460-343">Konfigurace Autofacu v `DefaultModule`:</span><span class="sxs-lookup"><span data-stu-id="0c460-343">Configure Autofac in `DefaultModule`:</span></span>

    ```csharp
    public class DefaultModule : Module
    {
        protected override void Load(ContainerBuilder builder)
        {
            builder.RegisterType<CharacterRepository>().As<ICharacterRepository>();
        }
    }
    ```

<span data-ttu-id="0c460-344">Za běhu je použit Autofac pro rozhodování typů a vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="0c460-344">At runtime, Autofac is used to resolve types and inject dependencies.</span></span> <span data-ttu-id="0c460-345">Další informace o používání Autofac s ASP.NET Core naleznete v [dokumentaci Autofac](https://docs.autofac.org/en/latest/integration/aspnetcore.html).</span><span class="sxs-lookup"><span data-stu-id="0c460-345">To learn more about using Autofac with ASP.NET Core, see the [Autofac documentation](https://docs.autofac.org/en/latest/integration/aspnetcore.html).</span></span>

### <a name="thread-safety"></a><span data-ttu-id="0c460-346">Bezpečný přístup z více vláken</span><span class="sxs-lookup"><span data-stu-id="0c460-346">Thread safety</span></span>

<span data-ttu-id="0c460-347">Vytvořte služby, které jsou v bezpečí pro přístup z více vláken.</span><span class="sxs-lookup"><span data-stu-id="0c460-347">Create thread-safe singleton services.</span></span> <span data-ttu-id="0c460-348">Pokud má služba typu Singleton závislost na přechodné službě, přechodná služba může také vyžadovat zabezpečení vlákna v závislosti na tom, jak je používá typ singleton.</span><span class="sxs-lookup"><span data-stu-id="0c460-348">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="0c460-349">Výrobní metoda samostatné služby, jako je například druhý argument pro [AddSingleton\<TService > (\<IServiceCollection, Func IServiceProvider, TService >)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), nemusí být bezpečná pro přístup z více vláken.</span><span class="sxs-lookup"><span data-stu-id="0c460-349">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="0c460-350">Stejně jako u typového (`static`) konstruktoru je zaručeno, že je volán jednou jediným vláknem.</span><span class="sxs-lookup"><span data-stu-id="0c460-350">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="0c460-351">Doporučení</span><span class="sxs-lookup"><span data-stu-id="0c460-351">Recommendations</span></span>

* <span data-ttu-id="0c460-352">`async/await`řešení `Task` služeb a se nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="0c460-352">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="0c460-353">C#nepodporuje asynchronní konstruktory; Proto je doporučeným vzorem použití asynchronních metod po synchronním překladu služby.</span><span class="sxs-lookup"><span data-stu-id="0c460-353">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="0c460-354">Vyhněte se ukládání dat a konfigurace přímo do kontejneru služeb.</span><span class="sxs-lookup"><span data-stu-id="0c460-354">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="0c460-355">Například nákupní košík uživatele by neměl být přidáván do kontejneru služeb.</span><span class="sxs-lookup"><span data-stu-id="0c460-355">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="0c460-356">Konfigurace by měla používat [vzor možností (options pattern)](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="0c460-356">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="0c460-357">Podobně se vystříhejte použití objektů "držící data", jejichž jediným účelem je poskytnutí přístupu k některému jinému objektu.</span><span class="sxs-lookup"><span data-stu-id="0c460-357">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="0c460-358">Je lepší žádat o skutečnou položku přes DI.</span><span class="sxs-lookup"><span data-stu-id="0c460-358">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="0c460-359">Vyhněte se statickému přístupu ke službám (například staticky typovaným [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) pro použití jinde).</span><span class="sxs-lookup"><span data-stu-id="0c460-359">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="0c460-360">Vyhněte se použití *vzoru lokátoru služby*.</span><span class="sxs-lookup"><span data-stu-id="0c460-360">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="0c460-361">Například Nevolejte <xref:System.IServiceProvider.GetService*> pro získání instance služby, když můžete místo toho použít di:</span><span class="sxs-lookup"><span data-stu-id="0c460-361">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="0c460-362">**Špatný**</span><span class="sxs-lookup"><span data-stu-id="0c460-362">**Incorrect:**</span></span>

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

  <span data-ttu-id="0c460-363">**Správné**:</span><span class="sxs-lookup"><span data-stu-id="0c460-363">**Correct**:</span></span>

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

* <span data-ttu-id="0c460-364">Další změnou lokátoru služby, aby se zabránilo, je vložení továrny, která vyřeší závislosti za běhu.</span><span class="sxs-lookup"><span data-stu-id="0c460-364">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="0c460-365">Oba tyto postupy kombinují [inverze strategií řízení](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .</span><span class="sxs-lookup"><span data-stu-id="0c460-365">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

* <span data-ttu-id="0c460-366">Vyhněte se statickému přístupu k `HttpContext` (například [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="0c460-366">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="0c460-367">Stejně jako u všech doporučení mohou nastat situace, ve kterých je možné tato doporučení ignorovat.</span><span class="sxs-lookup"><span data-stu-id="0c460-367">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="0c460-368">Výjimky se vyskytují jen vzácně &mdash; nejčastěji jsou to speciální případy uvnitř frameworku samotného.</span><span class="sxs-lookup"><span data-stu-id="0c460-368">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="0c460-369">DI je *alternativou* ke vzorům statických nebo globálních přístupů k objektům.</span><span class="sxs-lookup"><span data-stu-id="0c460-369">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="0c460-370">Je možné, že nebudete moci využít výhody DI, pokud je kombinujete se statickým přístupem k objektům.</span><span class="sxs-lookup"><span data-stu-id="0c460-370">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0c460-371">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="0c460-371">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="0c460-372">Psaní čistého kódu v ASP.NET Core pomocí vkládání závislostí (MSDN)</span><span class="sxs-lookup"><span data-stu-id="0c460-372">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="0c460-373">Princip explicitních závislostí</span><span class="sxs-lookup"><span data-stu-id="0c460-373">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="0c460-374">Kontejner inverze závislostí a vzor vkládání závislostí (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="0c460-374">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="0c460-375">Postup pro registraci služeb s více rozhraními v ASP.NET Core DI</span><span class="sxs-lookup"><span data-stu-id="0c460-375">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

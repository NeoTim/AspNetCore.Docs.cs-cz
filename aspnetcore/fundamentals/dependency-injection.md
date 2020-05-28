---
<span data-ttu-id="467f3-101">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="467f3-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="467f3-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467f3-102">'Blazor'</span></span>
- <span data-ttu-id="467f3-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467f3-103">'Identity'</span></span>
- <span data-ttu-id="467f3-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467f3-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="467f3-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467f3-105">'Razor'</span></span>
- <span data-ttu-id="467f3-106">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="467f3-106">'SignalR' uid:</span></span> 

---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="467f3-107">Injektáž závislostí v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="467f3-107">Dependency injection in ASP.NET Core</span></span>

<span data-ttu-id="467f3-108">[Steve Smith](https://ardalis.com/) a [Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="467f3-108">By [Steve Smith](https://ardalis.com/) and [Scott Addie](https://scottaddie.com)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="467f3-109">ASP.NET Core podporuje vzor návrhu softwaru pro vkládání závislostí (DI), což je technika pro dosažení [inverze ovládacího prvku (IOC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) mezi třídami a jejich závislostmi.</span><span class="sxs-lookup"><span data-stu-id="467f3-109">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="467f3-110">Další informace, které jsou specifické pro vkládání závislostí v rámci řadičů MVC, najdete v tématu <xref:mvc/controllers/dependency-injection> .</span><span class="sxs-lookup"><span data-stu-id="467f3-110">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="467f3-111">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="467f3-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="467f3-112">Přehled injektáže závislosti</span><span class="sxs-lookup"><span data-stu-id="467f3-112">Overview of dependency injection</span></span>

<span data-ttu-id="467f3-113">*Závislost* je libovolný objekt, který vyžaduje jiný objekt.</span><span class="sxs-lookup"><span data-stu-id="467f3-113">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="467f3-114">Projděte si následující `MyDependency` třídu s `WriteMessage` metodou, na které jsou závislé jiné třídy v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="467f3-114">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="467f3-115">Instance `MyDependency` třídy může být vytvořena, aby byla `WriteMessage` Metoda k dispozici pro třídu.</span><span class="sxs-lookup"><span data-stu-id="467f3-115">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="467f3-116">`MyDependency`Třída je závislostí `IndexModel` třídy:</span><span class="sxs-lookup"><span data-stu-id="467f3-116">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="467f3-117">Třída vytvoří a přímo závisí na `MyDependency` instanci.</span><span class="sxs-lookup"><span data-stu-id="467f3-117">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="467f3-118">Závislosti kódu (například předchozí příklad) jsou problematické a je třeba se jim vyhnout z následujících důvodů:</span><span class="sxs-lookup"><span data-stu-id="467f3-118">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="467f3-119">Chcete-li nahradit `MyDependency` jinou implementací, třída musí být upravena.</span><span class="sxs-lookup"><span data-stu-id="467f3-119">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="467f3-120">Pokud `MyDependency` má závislosti, musí být nakonfigurovány třídou.</span><span class="sxs-lookup"><span data-stu-id="467f3-120">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="467f3-121">Ve velkém projektu s více třídami, v závislosti na tom `MyDependency` , je kód konfigurace rozptýlen napříč aplikací.</span><span class="sxs-lookup"><span data-stu-id="467f3-121">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="467f3-122">Tato implementace je obtížná pro testování částí.</span><span class="sxs-lookup"><span data-stu-id="467f3-122">This implementation is difficult to unit test.</span></span> <span data-ttu-id="467f3-123">Aplikace by měla používat třídu typu "nebo zástupné procedury" `MyDependency` , což není u tohoto přístupu možné.</span><span class="sxs-lookup"><span data-stu-id="467f3-123">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="467f3-124">Vkládání závislostí řeší tyto problémy prostřednictvím:</span><span class="sxs-lookup"><span data-stu-id="467f3-124">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="467f3-125">Použití rozhraní nebo základní třídy k abstrakci implementace závislosti.</span><span class="sxs-lookup"><span data-stu-id="467f3-125">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="467f3-126">Registrace závislosti v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="467f3-126">Registration of the dependency in a service container.</span></span> <span data-ttu-id="467f3-127">ASP.NET Core poskytuje integrovaný kontejner služeb <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="467f3-127">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="467f3-128">Služby jsou registrovány v metodě aplikace `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="467f3-128">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="467f3-129">*Vložení* služby do konstruktoru třídy, kde se používá.</span><span class="sxs-lookup"><span data-stu-id="467f3-129">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="467f3-130">Rozhraní přebírá zodpovědnost za vytvoření instance závislosti a její odstranění, když už ji nepotřebujete.</span><span class="sxs-lookup"><span data-stu-id="467f3-130">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="467f3-131">V [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) `IMyDependency` definuje rozhraní metodu, kterou služba poskytuje aplikaci:</span><span class="sxs-lookup"><span data-stu-id="467f3-131">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="467f3-132">Toto rozhraní je implementováno konkrétním typem `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="467f3-132">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="467f3-133">`MyDependency`vyžádá <xref:Microsoft.Extensions.Logging.ILogger`1> v jeho konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="467f3-133">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="467f3-134">Není neobvyklé používat vkládání závislostí v zřetězeném způsobem.</span><span class="sxs-lookup"><span data-stu-id="467f3-134">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="467f3-135">Každá požadovaná závislost zase vyžádá své vlastní závislosti.</span><span class="sxs-lookup"><span data-stu-id="467f3-135">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="467f3-136">Kontejner vyřeší závislosti v grafu a vrátí plně přeloženou službu.</span><span class="sxs-lookup"><span data-stu-id="467f3-136">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="467f3-137">Kolektivní sada závislostí, které je třeba vyřešit, se obvykle označuje jako *strom závislosti*, *graf závislosti*nebo *graf objektů*.</span><span class="sxs-lookup"><span data-stu-id="467f3-137">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="467f3-138">`IMyDependency`a `ILogger<TCategoryName>` musí být registrována v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="467f3-138">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="467f3-139">`IMyDependency`je zaregistrován v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="467f3-139">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="467f3-140">`ILogger<TCategoryName>`je zaregistrované v infrastruktuře abstrakce protokolování, takže se jedná o [službu](#framework-provided-services) , která je zaregistrovaná ve výchozím rozhraní.</span><span class="sxs-lookup"><span data-stu-id="467f3-140">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="467f3-141">Kontejner se překládá `ILogger<TCategoryName>` tím, že využije [(Obecné) otevřené typy](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)a eliminuje nutnost registrace každého [(obecného) konstruovaného typu](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="467f3-141">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="467f3-142">V ukázkové aplikaci `IMyDependency` je služba zaregistrovaná u konkrétního typu `MyDependency` .</span><span class="sxs-lookup"><span data-stu-id="467f3-142">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="467f3-143">Registruje rozsah platnosti služby po dobu životnosti jediného požadavku.</span><span class="sxs-lookup"><span data-stu-id="467f3-143">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="467f3-144">[Doby platnosti služeb](#service-lifetimes) jsou popsány dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="467f3-144">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="467f3-145">Jednotlivé `services.Add{SERVICE_NAME}` metody rozšíření přidávají (a případně nakonfigurují) služby.</span><span class="sxs-lookup"><span data-stu-id="467f3-145">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="467f3-146">Například `services.AddMvc()` přidá Razor stránky služeb a MVC vyžaduje.</span><span class="sxs-lookup"><span data-stu-id="467f3-146">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="467f3-147">Doporučujeme, aby aplikace dodržovaly tuto konvenci.</span><span class="sxs-lookup"><span data-stu-id="467f3-147">We recommended that apps follow this convention.</span></span> <span data-ttu-id="467f3-148">Do oboru názvů [Microsoft. Extensions. DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) umístěte metody rozšíření pro zapouzdření skupin registrací služby.</span><span class="sxs-lookup"><span data-stu-id="467f3-148">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="467f3-149">Pokud konstruktor služby vyžaduje [vestavěný typ](/dotnet/csharp/language-reference/keywords/built-in-types-table), například `string` ,, může být typ vložen pomocí [Konfigurace](xref:fundamentals/configuration/index) nebo [vzoru možností](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="467f3-149">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="467f3-150">Instance služby je požadována prostřednictvím konstruktoru třídy, kde je služba používána a přiřazena k soukromému poli.</span><span class="sxs-lookup"><span data-stu-id="467f3-150">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="467f3-151">Pole se používá pro přístup ke službě podle potřeby v celé třídě.</span><span class="sxs-lookup"><span data-stu-id="467f3-151">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="467f3-152">V ukázkové aplikaci `IMyDependency` je instance vyžádána a používána pro volání `WriteMessage` metody služby:</span><span class="sxs-lookup"><span data-stu-id="467f3-152">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="467f3-153">Služby vložené do spuštění</span><span class="sxs-lookup"><span data-stu-id="467f3-153">Services injected into Startup</span></span>

<span data-ttu-id="467f3-154">`Startup`Při použití obecného hostitele () mohou být do konstruktoru vloženy pouze následující typy služeb <xref:Microsoft.Extensions.Hosting.IHostBuilder> :</span><span class="sxs-lookup"><span data-stu-id="467f3-154">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="467f3-155">Služby je možné vložit do `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="467f3-155">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="467f3-156">Další informace naleznete v tématu <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="467f3-156">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="467f3-157">Služby poskytované rozhraním</span><span class="sxs-lookup"><span data-stu-id="467f3-157">Framework-provided services</span></span>

<span data-ttu-id="467f3-158">`Startup.ConfigureServices`Metoda zodpovídá za definování služeb, které aplikace používá, včetně funkcí platformy, jako je například Entity Framework Core a ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="467f3-158">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="467f3-159">Zpočátku `IServiceCollection` `ConfigureServices` služba má služby definované rozhraním v závislosti na [tom, jak byl hostitel nakonfigurovaný](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="467f3-159">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="467f3-160">Pro aplikaci, která je založená na šabloně ASP.NET Core, není běžné, že mají stovky služeb zaregistrovaných v rámci rozhraní.</span><span class="sxs-lookup"><span data-stu-id="467f3-160">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="467f3-161">V následující tabulce je uvedena malá ukázka služeb registrovaných v rozhraní.</span><span class="sxs-lookup"><span data-stu-id="467f3-161">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="467f3-162">Typ služby</span><span class="sxs-lookup"><span data-stu-id="467f3-162">Service Type</span></span> | <span data-ttu-id="467f3-163">Doba platnosti</span><span class="sxs-lookup"><span data-stu-id="467f3-163">Lifetime</span></span> |
| ---
<span data-ttu-id="467f3-164">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="467f3-164">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="467f3-165">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467f3-165">'Blazor'</span></span>
- <span data-ttu-id="467f3-166">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467f3-166">'Identity'</span></span>
- <span data-ttu-id="467f3-167">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467f3-167">'Let's Encrypt'</span></span>
- <span data-ttu-id="467f3-168">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467f3-168">'Razor'</span></span>
- <span data-ttu-id="467f3-169">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="467f3-169">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467f3-170">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="467f3-170">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="467f3-171">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467f3-171">'Blazor'</span></span>
- <span data-ttu-id="467f3-172">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467f3-172">'Identity'</span></span>
- <span data-ttu-id="467f3-173">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467f3-173">'Let's Encrypt'</span></span>
- <span data-ttu-id="467f3-174">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467f3-174">'Razor'</span></span>
- <span data-ttu-id="467f3-175">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="467f3-175">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467f3-176">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="467f3-176">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="467f3-177">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467f3-177">'Blazor'</span></span>
- <span data-ttu-id="467f3-178">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467f3-178">'Identity'</span></span>
- <span data-ttu-id="467f3-179">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467f3-179">'Let's Encrypt'</span></span>
- <span data-ttu-id="467f3-180">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467f3-180">'Razor'</span></span>
- <span data-ttu-id="467f3-181">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="467f3-181">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467f3-182">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="467f3-182">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="467f3-183">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467f3-183">'Blazor'</span></span>
- <span data-ttu-id="467f3-184">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467f3-184">'Identity'</span></span>
- <span data-ttu-id="467f3-185">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467f3-185">'Let's Encrypt'</span></span>
- <span data-ttu-id="467f3-186">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467f3-186">'Razor'</span></span>
- <span data-ttu-id="467f3-187">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="467f3-187">'SignalR' uid:</span></span> 

<span data-ttu-id="467f3-188">------ | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="467f3-188">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="467f3-189">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467f3-189">'Blazor'</span></span>
- <span data-ttu-id="467f3-190">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467f3-190">'Identity'</span></span>
- <span data-ttu-id="467f3-191">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467f3-191">'Let's Encrypt'</span></span>
- <span data-ttu-id="467f3-192">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467f3-192">'Razor'</span></span>
- <span data-ttu-id="467f3-193">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="467f3-193">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467f3-194">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="467f3-194">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="467f3-195">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467f3-195">'Blazor'</span></span>
- <span data-ttu-id="467f3-196">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467f3-196">'Identity'</span></span>
- <span data-ttu-id="467f3-197">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467f3-197">'Let's Encrypt'</span></span>
- <span data-ttu-id="467f3-198">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467f3-198">'Razor'</span></span>
- <span data-ttu-id="467f3-199">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="467f3-199">'SignalR' uid:</span></span> 

<span data-ttu-id="467f3-200">---- | | <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Přechodný | | `IHostApplicationLifetime` | Singleton | | `IWebHostEnvironment` | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Přechodný | | <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Přechodný | | <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Přechodný | | <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Singleton |</span><span class="sxs-lookup"><span data-stu-id="467f3-200">---- | | <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Transient | | `IHostApplicationLifetime` | Singleton | | `IWebHostEnvironment` | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Transient | | <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Transient | | <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Transient | | <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Singleton |</span></span>

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="467f3-201">Registrace dalších služeb pomocí rozšiřujících metod</span><span class="sxs-lookup"><span data-stu-id="467f3-201">Register additional services with extension methods</span></span>

<span data-ttu-id="467f3-202">Pokud je k dispozici metoda rozšíření kolekce služeb pro registraci služby (a jejích závislých služeb, v případě potřeby), konvence používá jedinou `Add{SERVICE_NAME}` metodu rozšíření k registraci všech služeb vyžadovaných touto službou.</span><span class="sxs-lookup"><span data-stu-id="467f3-202">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="467f3-203">Následující kód je příkladem přidání dalších služeb do kontejneru pomocí metod rozšíření [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) a <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> :</span><span class="sxs-lookup"><span data-stu-id="467f3-203">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="467f3-204">Další informace najdete <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> ve třídě v dokumentaci k rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="467f3-204">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="467f3-205">Životnost služeb</span><span class="sxs-lookup"><span data-stu-id="467f3-205">Service lifetimes</span></span>

<span data-ttu-id="467f3-206">Vyberte odpovídající dobu života pro každou registrovanou službu.</span><span class="sxs-lookup"><span data-stu-id="467f3-206">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="467f3-207">Služby ASP.NET Core Services je možné nakonfigurovat s následujícími životnostmi:</span><span class="sxs-lookup"><span data-stu-id="467f3-207">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="467f3-208">Dočasný</span><span class="sxs-lookup"><span data-stu-id="467f3-208">Transient</span></span>

<span data-ttu-id="467f3-209">Služba přechodných životností ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*> ) se vytvoří pokaždé, když se požadují z kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="467f3-209">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="467f3-210">Tato životnost funguje nejlépe pro odlehčené bezstavové služby.</span><span class="sxs-lookup"><span data-stu-id="467f3-210">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="467f3-211">Obor</span><span class="sxs-lookup"><span data-stu-id="467f3-211">Scoped</span></span>

<span data-ttu-id="467f3-212">Oborové služby () s rozsahem platnosti ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*> ) se vytvoří jednou za požadavek klienta (připojení).</span><span class="sxs-lookup"><span data-stu-id="467f3-212">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="467f3-213">Při použití oboru služby v middlewaru založit službu do `Invoke` `InvokeAsync` metody nebo.</span><span class="sxs-lookup"><span data-stu-id="467f3-213">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="467f3-214">Nepoužívejte [vkládání pomocí injektáže konstruktoru](xref:mvc/controllers/dependency-injection#constructor-injection) , protože vynutí, aby se služba chovala jako typ singleton.</span><span class="sxs-lookup"><span data-stu-id="467f3-214">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="467f3-215">Další informace naleznete v tématu <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="467f3-215">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="467f3-216">Singleton</span><span class="sxs-lookup"><span data-stu-id="467f3-216">Singleton</span></span>

<span data-ttu-id="467f3-217">Služba singleton životnosti ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*> ) je vytvořena při prvním vyžádání (nebo při `Startup.ConfigureServices` spuštění a instance je určena pro registraci služby).</span><span class="sxs-lookup"><span data-stu-id="467f3-217">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="467f3-218">Každý další požadavek používá stejnou instanci.</span><span class="sxs-lookup"><span data-stu-id="467f3-218">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="467f3-219">Pokud aplikace vyžaduje chování singleton, doporučuje se povolit kontejneru služby spravovat dobu života služby.</span><span class="sxs-lookup"><span data-stu-id="467f3-219">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="467f3-220">Neimplementujte vzor návrhu singleton a poskytněte uživatelský kód pro správu životního cyklu objektu ve třídě.</span><span class="sxs-lookup"><span data-stu-id="467f3-220">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="467f3-221">Není bezpečné přeložit oborovou službu z typu singleton.</span><span class="sxs-lookup"><span data-stu-id="467f3-221">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="467f3-222">Může dojít k tomu, že služba má při zpracování dalších požadavků špatný stav.</span><span class="sxs-lookup"><span data-stu-id="467f3-222">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="467f3-223">Metody registrace služby</span><span class="sxs-lookup"><span data-stu-id="467f3-223">Service registration methods</span></span>

<span data-ttu-id="467f3-224">Metody rozšíření registrace služby nabízejí přetížení, která jsou užitečná pro konkrétní scénáře.</span><span class="sxs-lookup"><span data-stu-id="467f3-224">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="467f3-225">Metoda</span><span class="sxs-lookup"><span data-stu-id="467f3-225">Method</span></span> | <span data-ttu-id="467f3-226">Automaticky</span><span class="sxs-lookup"><span data-stu-id="467f3-226">Automatic</span></span><br><span data-ttu-id="467f3-227">odkazy objektů</span><span class="sxs-lookup"><span data-stu-id="467f3-227">object</span></span><br><span data-ttu-id="467f3-228">odvod</span><span class="sxs-lookup"><span data-stu-id="467f3-228">disposal</span></span> | <span data-ttu-id="467f3-229">Několik</span><span class="sxs-lookup"><span data-stu-id="467f3-229">Multiple</span></span><br><span data-ttu-id="467f3-230">implementace</span><span class="sxs-lookup"><span data-stu-id="467f3-230">implementations</span></span> | <span data-ttu-id="467f3-231">Pass – argumenty</span><span class="sxs-lookup"><span data-stu-id="467f3-231">Pass args</span></span> |
| ---
<span data-ttu-id="467f3-232">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="467f3-232">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="467f3-233">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467f3-233">'Blazor'</span></span>
- <span data-ttu-id="467f3-234">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467f3-234">'Identity'</span></span>
- <span data-ttu-id="467f3-235">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467f3-235">'Let's Encrypt'</span></span>
- <span data-ttu-id="467f3-236">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467f3-236">'Razor'</span></span>
- <span data-ttu-id="467f3-237">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="467f3-237">'SignalR' uid:</span></span> 

<span data-ttu-id="467f3-238">--- | :---název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="467f3-238">--- | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="467f3-239">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467f3-239">'Blazor'</span></span>
- <span data-ttu-id="467f3-240">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467f3-240">'Identity'</span></span>
- <span data-ttu-id="467f3-241">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467f3-241">'Let's Encrypt'</span></span>
- <span data-ttu-id="467f3-242">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467f3-242">'Razor'</span></span>
- <span data-ttu-id="467f3-243">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="467f3-243">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467f3-244">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="467f3-244">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="467f3-245">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467f3-245">'Blazor'</span></span>
- <span data-ttu-id="467f3-246">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467f3-246">'Identity'</span></span>
- <span data-ttu-id="467f3-247">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467f3-247">'Let's Encrypt'</span></span>
- <span data-ttu-id="467f3-248">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467f3-248">'Razor'</span></span>
- <span data-ttu-id="467f3-249">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="467f3-249">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467f3-250">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="467f3-250">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="467f3-251">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467f3-251">'Blazor'</span></span>
- <span data-ttu-id="467f3-252">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467f3-252">'Identity'</span></span>
- <span data-ttu-id="467f3-253">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467f3-253">'Let's Encrypt'</span></span>
- <span data-ttu-id="467f3-254">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467f3-254">'Razor'</span></span>
- <span data-ttu-id="467f3-255">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="467f3-255">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467f3-256">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="467f3-256">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="467f3-257">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467f3-257">'Blazor'</span></span>
- <span data-ttu-id="467f3-258">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467f3-258">'Identity'</span></span>
- <span data-ttu-id="467f3-259">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467f3-259">'Let's Encrypt'</span></span>
- <span data-ttu-id="467f3-260">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467f3-260">'Razor'</span></span>
- <span data-ttu-id="467f3-261">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="467f3-261">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467f3-262">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="467f3-262">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="467f3-263">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467f3-263">'Blazor'</span></span>
- <span data-ttu-id="467f3-264">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467f3-264">'Identity'</span></span>
- <span data-ttu-id="467f3-265">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467f3-265">'Let's Encrypt'</span></span>
- <span data-ttu-id="467f3-266">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467f3-266">'Razor'</span></span>
- <span data-ttu-id="467f3-267">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="467f3-267">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467f3-268">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="467f3-268">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="467f3-269">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467f3-269">'Blazor'</span></span>
- <span data-ttu-id="467f3-270">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467f3-270">'Identity'</span></span>
- <span data-ttu-id="467f3-271">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467f3-271">'Let's Encrypt'</span></span>
- <span data-ttu-id="467f3-272">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467f3-272">'Razor'</span></span>
- <span data-ttu-id="467f3-273">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="467f3-273">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467f3-274">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="467f3-274">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="467f3-275">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467f3-275">'Blazor'</span></span>
- <span data-ttu-id="467f3-276">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467f3-276">'Identity'</span></span>
- <span data-ttu-id="467f3-277">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467f3-277">'Let's Encrypt'</span></span>
- <span data-ttu-id="467f3-278">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467f3-278">'Razor'</span></span>
- <span data-ttu-id="467f3-279">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="467f3-279">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467f3-280">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="467f3-280">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="467f3-281">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467f3-281">'Blazor'</span></span>
- <span data-ttu-id="467f3-282">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467f3-282">'Identity'</span></span>
- <span data-ttu-id="467f3-283">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467f3-283">'Let's Encrypt'</span></span>
- <span data-ttu-id="467f3-284">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467f3-284">'Razor'</span></span>
- <span data-ttu-id="467f3-285">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="467f3-285">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467f3-286">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="467f3-286">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="467f3-287">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467f3-287">'Blazor'</span></span>
- <span data-ttu-id="467f3-288">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467f3-288">'Identity'</span></span>
- <span data-ttu-id="467f3-289">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467f3-289">'Let's Encrypt'</span></span>
- <span data-ttu-id="467f3-290">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467f3-290">'Razor'</span></span>
- <span data-ttu-id="467f3-291">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="467f3-291">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467f3-292">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="467f3-292">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="467f3-293">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467f3-293">'Blazor'</span></span>
- <span data-ttu-id="467f3-294">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467f3-294">'Identity'</span></span>
- <span data-ttu-id="467f3-295">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467f3-295">'Let's Encrypt'</span></span>
- <span data-ttu-id="467f3-296">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467f3-296">'Razor'</span></span>
- <span data-ttu-id="467f3-297">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="467f3-297">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467f3-298">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="467f3-298">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="467f3-299">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467f3-299">'Blazor'</span></span>
- <span data-ttu-id="467f3-300">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467f3-300">'Identity'</span></span>
- <span data-ttu-id="467f3-301">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467f3-301">'Let's Encrypt'</span></span>
- <span data-ttu-id="467f3-302">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467f3-302">'Razor'</span></span>
- <span data-ttu-id="467f3-303">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="467f3-303">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467f3-304">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="467f3-304">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="467f3-305">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467f3-305">'Blazor'</span></span>
- <span data-ttu-id="467f3-306">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467f3-306">'Identity'</span></span>
- <span data-ttu-id="467f3-307">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467f3-307">'Let's Encrypt'</span></span>
- <span data-ttu-id="467f3-308">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467f3-308">'Razor'</span></span>
- <span data-ttu-id="467f3-309">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="467f3-309">'SignalR' uid:</span></span> 

<span data-ttu-id="467f3-310">---------------: | :---název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="467f3-310">---------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="467f3-311">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467f3-311">'Blazor'</span></span>
- <span data-ttu-id="467f3-312">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467f3-312">'Identity'</span></span>
- <span data-ttu-id="467f3-313">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467f3-313">'Let's Encrypt'</span></span>
- <span data-ttu-id="467f3-314">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467f3-314">'Razor'</span></span>
- <span data-ttu-id="467f3-315">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="467f3-315">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467f3-316">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="467f3-316">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="467f3-317">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467f3-317">'Blazor'</span></span>
- <span data-ttu-id="467f3-318">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467f3-318">'Identity'</span></span>
- <span data-ttu-id="467f3-319">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467f3-319">'Let's Encrypt'</span></span>
- <span data-ttu-id="467f3-320">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467f3-320">'Razor'</span></span>
- <span data-ttu-id="467f3-321">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="467f3-321">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467f3-322">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="467f3-322">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="467f3-323">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467f3-323">'Blazor'</span></span>
- <span data-ttu-id="467f3-324">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467f3-324">'Identity'</span></span>
- <span data-ttu-id="467f3-325">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467f3-325">'Let's Encrypt'</span></span>
- <span data-ttu-id="467f3-326">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467f3-326">'Razor'</span></span>
- <span data-ttu-id="467f3-327">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="467f3-327">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467f3-328">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="467f3-328">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="467f3-329">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467f3-329">'Blazor'</span></span>
- <span data-ttu-id="467f3-330">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467f3-330">'Identity'</span></span>
- <span data-ttu-id="467f3-331">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467f3-331">'Let's Encrypt'</span></span>
- <span data-ttu-id="467f3-332">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467f3-332">'Razor'</span></span>
- <span data-ttu-id="467f3-333">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="467f3-333">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467f3-334">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="467f3-334">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="467f3-335">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467f3-335">'Blazor'</span></span>
- <span data-ttu-id="467f3-336">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467f3-336">'Identity'</span></span>
- <span data-ttu-id="467f3-337">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467f3-337">'Let's Encrypt'</span></span>
- <span data-ttu-id="467f3-338">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467f3-338">'Razor'</span></span>
- <span data-ttu-id="467f3-339">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="467f3-339">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467f3-340">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="467f3-340">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="467f3-341">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467f3-341">'Blazor'</span></span>
- <span data-ttu-id="467f3-342">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467f3-342">'Identity'</span></span>
- <span data-ttu-id="467f3-343">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467f3-343">'Let's Encrypt'</span></span>
- <span data-ttu-id="467f3-344">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467f3-344">'Razor'</span></span>
- <span data-ttu-id="467f3-345">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="467f3-345">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467f3-346">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="467f3-346">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="467f3-347">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467f3-347">'Blazor'</span></span>
- <span data-ttu-id="467f3-348">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467f3-348">'Identity'</span></span>
- <span data-ttu-id="467f3-349">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467f3-349">'Let's Encrypt'</span></span>
- <span data-ttu-id="467f3-350">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467f3-350">'Razor'</span></span>
- <span data-ttu-id="467f3-351">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="467f3-351">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467f3-352">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="467f3-352">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="467f3-353">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467f3-353">'Blazor'</span></span>
- <span data-ttu-id="467f3-354">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467f3-354">'Identity'</span></span>
- <span data-ttu-id="467f3-355">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467f3-355">'Let's Encrypt'</span></span>
- <span data-ttu-id="467f3-356">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467f3-356">'Razor'</span></span>
- <span data-ttu-id="467f3-357">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="467f3-357">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467f3-358">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="467f3-358">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="467f3-359">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467f3-359">'Blazor'</span></span>
- <span data-ttu-id="467f3-360">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467f3-360">'Identity'</span></span>
- <span data-ttu-id="467f3-361">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467f3-361">'Let's Encrypt'</span></span>
- <span data-ttu-id="467f3-362">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467f3-362">'Razor'</span></span>
- <span data-ttu-id="467f3-363">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="467f3-363">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467f3-364">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="467f3-364">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="467f3-365">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467f3-365">'Blazor'</span></span>
- <span data-ttu-id="467f3-366">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467f3-366">'Identity'</span></span>
- <span data-ttu-id="467f3-367">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467f3-367">'Let's Encrypt'</span></span>
- <span data-ttu-id="467f3-368">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467f3-368">'Razor'</span></span>
- <span data-ttu-id="467f3-369">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="467f3-369">'SignalR' uid:</span></span> 

<span data-ttu-id="467f3-370">-------------: | :---název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="467f3-370">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="467f3-371">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467f3-371">'Blazor'</span></span>
- <span data-ttu-id="467f3-372">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467f3-372">'Identity'</span></span>
- <span data-ttu-id="467f3-373">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467f3-373">'Let's Encrypt'</span></span>
- <span data-ttu-id="467f3-374">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467f3-374">'Razor'</span></span>
- <span data-ttu-id="467f3-375">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="467f3-375">'SignalR' uid:</span></span> 

<span data-ttu-id="467f3-376">----: | | `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`</span><span class="sxs-lookup"><span data-stu-id="467f3-376">----: | | `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`</span></span><br><span data-ttu-id="467f3-377">Příklad:</span><span class="sxs-lookup"><span data-stu-id="467f3-377">Example:</span></span><br><span data-ttu-id="467f3-378">`services.AddSingleton<IMyDep, MyDep>();`| Ano | Ano | Žádné | |`Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="467f3-378">`services.AddSingleton<IMyDep, MyDep>();` | Yes | Yes | No | | `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="467f3-379">Příklady:</span><span class="sxs-lookup"><span data-stu-id="467f3-379">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br><span data-ttu-id="467f3-380">`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));`| Ano | Ano | Ano | |`Add{LIFETIME}<{IMPLEMENTATION}>()`</span><span class="sxs-lookup"><span data-stu-id="467f3-380">`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | Yes | Yes | Yes | | `Add{LIFETIME}<{IMPLEMENTATION}>()`</span></span><br><span data-ttu-id="467f3-381">Příklad:</span><span class="sxs-lookup"><span data-stu-id="467f3-381">Example:</span></span><br><span data-ttu-id="467f3-382">`services.AddSingleton<MyDep>();`| Ano | Žádné | Žádné | |`AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="467f3-382">`services.AddSingleton<MyDep>();` | Yes | No | No | | `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="467f3-383">Příklady:</span><span class="sxs-lookup"><span data-stu-id="467f3-383">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br><span data-ttu-id="467f3-384">`services.AddSingleton<IMyDep>(new MyDep("A string!"));`| Žádné | Ano | Ano | |`AddSingleton(new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="467f3-384">`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | No | Yes | Yes | | `AddSingleton(new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="467f3-385">Příklady:</span><span class="sxs-lookup"><span data-stu-id="467f3-385">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br><span data-ttu-id="467f3-386">`services.AddSingleton(new MyDep("A string!"));`| Žádné | Žádné | Ano |</span><span class="sxs-lookup"><span data-stu-id="467f3-386">`services.AddSingleton(new MyDep("A string!"));` | No | No | Yes |</span></span>

<span data-ttu-id="467f3-387">Další informace o vyřazení typů najdete v části věnované [vyřazení služeb](#disposal-of-services) .</span><span class="sxs-lookup"><span data-stu-id="467f3-387">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="467f3-388">Běžným scénářem pro více implementací je vytvoření [typů pro testování](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="467f3-388">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="467f3-389">`TryAdd{LIFETIME}`metody registrují službu jenom v případě, že už není zaregistrovaná implementace.</span><span class="sxs-lookup"><span data-stu-id="467f3-389">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="467f3-390">V následujícím příkladu se první řádek registruje `MyDependency` pro `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="467f3-390">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="467f3-391">Druhý řádek nemá žádný účinek, protože `IMyDependency` už má registrovanou implementaci:</span><span class="sxs-lookup"><span data-stu-id="467f3-391">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="467f3-392">Další informace naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="467f3-392">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="467f3-393">Metody [TryAddEnumerable (ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) registrují službu pouze v případě, že již není implementace *stejného typu*.</span><span class="sxs-lookup"><span data-stu-id="467f3-393">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="467f3-394">Několik služeb je vyřešeno prostřednictvím `IEnumerable<{SERVICE}>` .</span><span class="sxs-lookup"><span data-stu-id="467f3-394">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="467f3-395">Při registraci služeb chce vývojář přidat instanci jenom v případě, že už jeden ze stejného typu není přidaný.</span><span class="sxs-lookup"><span data-stu-id="467f3-395">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="467f3-396">Obecně je tato metoda používána autory knihovny k tomu, aby nedocházelo k registraci dvou kopií instance v kontejneru.</span><span class="sxs-lookup"><span data-stu-id="467f3-396">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="467f3-397">V následujícím příkladu se první řádek registruje `MyDep` pro `IMyDep1` .</span><span class="sxs-lookup"><span data-stu-id="467f3-397">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="467f3-398">Druhý řádek se registruje `MyDep` pro `IMyDep2` .</span><span class="sxs-lookup"><span data-stu-id="467f3-398">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="467f3-399">Třetí řádek nemá žádný vliv, protože `IMyDep1` už má registrovanou implementaci `MyDep` :</span><span class="sxs-lookup"><span data-stu-id="467f3-399">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="467f3-400">Chování injektáže konstruktoru</span><span class="sxs-lookup"><span data-stu-id="467f3-400">Constructor injection behavior</span></span>

<span data-ttu-id="467f3-401">Služby je možné vyřešit pomocí dvou mechanismů:</span><span class="sxs-lookup"><span data-stu-id="467f3-401">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="467f3-402"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Povoluje vytvoření objektu bez registrace služby v kontejneru vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="467f3-402"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="467f3-403">`ActivatorUtilities`se používá s uživateli s přístupem k uživatelům, jako jsou například rutiny značek, řadiče MVC a pořadače modelů.</span><span class="sxs-lookup"><span data-stu-id="467f3-403">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="467f3-404">Konstruktory mohou přijímat argumenty, které nejsou poskytovány vkládáním závislostí, ale argumenty musí přiřadit výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="467f3-404">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="467f3-405">Když jsou služby vyřešeny `IServiceProvider` nebo `ActivatorUtilities` , [Injektáže konstruktoru](xref:mvc/controllers/dependency-injection#constructor-injection) vyžaduje *veřejný* konstruktor.</span><span class="sxs-lookup"><span data-stu-id="467f3-405">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="467f3-406">Když jsou služby vyřešeny nástrojem `ActivatorUtilities` , [Injektáže konstruktoru](xref:mvc/controllers/dependency-injection#constructor-injection) vyžaduje, aby existoval pouze jeden příslušný konstruktor.</span><span class="sxs-lookup"><span data-stu-id="467f3-406">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="467f3-407">Přetížení konstruktoru jsou podporovaná, ale může existovat jenom jedno přetížení, jehož argumenty můžou být splněné vkládáním závislostí.</span><span class="sxs-lookup"><span data-stu-id="467f3-407">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="467f3-408">Entity Framework kontexty</span><span class="sxs-lookup"><span data-stu-id="467f3-408">Entity Framework contexts</span></span>

<span data-ttu-id="467f3-409">Entity Framework kontexty se obvykle přidávají do kontejneru služby pomocí [rozsahu životnosti](#service-lifetimes) , protože operace databáze webové aplikace jsou obvykle vymezeny na požadavky klienta.</span><span class="sxs-lookup"><span data-stu-id="467f3-409">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="467f3-410">Výchozí doba života je vymezena v případě, že při registraci kontextu databáze není zadána doba života při [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) přetížení.</span><span class="sxs-lookup"><span data-stu-id="467f3-410">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="467f3-411">Služby pro danou dobu života by neměly používat kontext databáze s kratší dobou životnosti než služba.</span><span class="sxs-lookup"><span data-stu-id="467f3-411">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="467f3-412">Možnosti života a registrace</span><span class="sxs-lookup"><span data-stu-id="467f3-412">Lifetime and registration options</span></span>

<span data-ttu-id="467f3-413">Chcete-li předvést rozdíl mezi možnostmi životního cyklu a registrací, zvažte následující rozhraní, která představují úlohy jako operaci s jedinečným identifikátorem `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="467f3-413">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="467f3-414">V závislosti na tom, jak je životnost provozní služby nakonfigurována pro následující rozhraní, kontejner poskytuje buď stejnou nebo jinou instanci služby, když je vyžádána třídou:</span><span class="sxs-lookup"><span data-stu-id="467f3-414">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="467f3-415">Rozhraní jsou implementována ve `Operation` třídě.</span><span class="sxs-lookup"><span data-stu-id="467f3-415">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="467f3-416">`Operation`Konstruktor generuje identifikátor GUID, pokud není dodán:</span><span class="sxs-lookup"><span data-stu-id="467f3-416">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="467f3-417">`OperationService`Je zaregistrován, který závisí na každém z ostatních `Operation` typů.</span><span class="sxs-lookup"><span data-stu-id="467f3-417">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="467f3-418">Když `OperationService` je požadavek proveden prostřednictvím injektáže závislosti, obdrží buď novou instanci každé služby, nebo existující instanci na základě životnosti závislé služby.</span><span class="sxs-lookup"><span data-stu-id="467f3-418">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="467f3-419">Když se v případě vyžádání z kontejneru vytvoří přechodné služby, `OperationId` liší se od `IOperationTransient` služby `OperationId` `OperationService` .</span><span class="sxs-lookup"><span data-stu-id="467f3-419">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="467f3-420">`OperationService`přijme novou instanci `IOperationTransient` třídy.</span><span class="sxs-lookup"><span data-stu-id="467f3-420">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="467f3-421">Nová instance vrací jinou instanci `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="467f3-421">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="467f3-422">Když se vytvoří vymezené služby pro jednotlivé požadavky klienta, `OperationId` `IOperationScoped` služba je stejná jako `OperationService` v rámci žádosti klienta.</span><span class="sxs-lookup"><span data-stu-id="467f3-422">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="467f3-423">V rámci požadavků klientů obě služby sdílejí jinou `OperationId` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="467f3-423">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="467f3-424">Pokud se služby instance singleton a singleton vytvoří jednou a použije se pro všechny požadavky klientů a všechny služby, `OperationId` je v rámci všech žádostí o službu konstantní.</span><span class="sxs-lookup"><span data-stu-id="467f3-424">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="467f3-425">V `Startup.ConfigureServices` je každý typ přidán do kontejneru podle jeho pojmenované životnosti:</span><span class="sxs-lookup"><span data-stu-id="467f3-425">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="467f3-426">`IOperationSingletonInstance`Služba používá konkrétní instanci se známým ID `Guid.Empty` .</span><span class="sxs-lookup"><span data-stu-id="467f3-426">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="467f3-427">Je jasné, že pokud se tento typ používá (jeho identifikátor GUID je všechny nuly).</span><span class="sxs-lookup"><span data-stu-id="467f3-427">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="467f3-428">Ukázková aplikace ukazuje dobu života objektu v rámci a mezi jednotlivými požadavky.</span><span class="sxs-lookup"><span data-stu-id="467f3-428">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="467f3-429">Ukázková aplikace `IndexModel` požaduje každý druh `IOperation` typu a `OperationService` .</span><span class="sxs-lookup"><span data-stu-id="467f3-429">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="467f3-430">Stránka potom zobrazí všechny hodnoty třídy modelu stránky a `OperationId` hodnoty služby prostřednictvím přiřazení vlastností:</span><span class="sxs-lookup"><span data-stu-id="467f3-430">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="467f3-431">Výsledkem dvou následujících výstupů jsou výsledky dvou požadavků:</span><span class="sxs-lookup"><span data-stu-id="467f3-431">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="467f3-432">**První požadavek:**</span><span class="sxs-lookup"><span data-stu-id="467f3-432">**First request:**</span></span>

<span data-ttu-id="467f3-433">Operace kontroleru:</span><span class="sxs-lookup"><span data-stu-id="467f3-433">Controller operations:</span></span>

<span data-ttu-id="467f3-434">Přechodný: d233e165-f417-469b-A866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="467f3-434">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="467f3-435">Vymezené: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="467f3-435">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="467f3-436">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="467f3-436">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="467f3-437">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="467f3-437">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="467f3-438">`OperationService`Operations</span><span class="sxs-lookup"><span data-stu-id="467f3-438">`OperationService` operations:</span></span>

<span data-ttu-id="467f3-439">Přechodný: c6b049eb-1318-4E31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="467f3-439">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="467f3-440">Vymezené: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="467f3-440">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="467f3-441">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="467f3-441">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="467f3-442">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="467f3-442">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="467f3-443">**Druhá žádost:**</span><span class="sxs-lookup"><span data-stu-id="467f3-443">**Second request:**</span></span>

<span data-ttu-id="467f3-444">Operace kontroleru:</span><span class="sxs-lookup"><span data-stu-id="467f3-444">Controller operations:</span></span>

<span data-ttu-id="467f3-445">Přechodný: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="467f3-445">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="467f3-446">Vymezené: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="467f3-446">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="467f3-447">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="467f3-447">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="467f3-448">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="467f3-448">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="467f3-449">`OperationService`Operations</span><span class="sxs-lookup"><span data-stu-id="467f3-449">`OperationService` operations:</span></span>

<span data-ttu-id="467f3-450">Přechodný: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="467f3-450">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="467f3-451">Vymezené: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="467f3-451">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="467f3-452">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="467f3-452">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="467f3-453">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="467f3-453">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="467f3-454">Pozor, které `OperationId` hodnoty se liší v rámci požadavku a mezi požadavky:</span><span class="sxs-lookup"><span data-stu-id="467f3-454">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="467f3-455">*Přechodné* objekty jsou vždy odlišné.</span><span class="sxs-lookup"><span data-stu-id="467f3-455">*Transient* objects are always different.</span></span> <span data-ttu-id="467f3-456">Přechodná `OperationId` hodnota pro první i druhý požadavek klienta se liší pro obě `OperationService` operace i mezi požadavky klientů.</span><span class="sxs-lookup"><span data-stu-id="467f3-456">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="467f3-457">Každé žádosti o službu a žádosti klienta se poskytne nová instance.</span><span class="sxs-lookup"><span data-stu-id="467f3-457">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="467f3-458">*Vymezené* objekty jsou v požadavku klienta stejné, ale v rámci požadavků klientů se liší.</span><span class="sxs-lookup"><span data-stu-id="467f3-458">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="467f3-459">Objekty *singleton* jsou stejné pro každý objekt a každý požadavek bez ohledu na to, zda `Operation` je instance uvedena v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="467f3-459">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="467f3-460">Volat služby z Main</span><span class="sxs-lookup"><span data-stu-id="467f3-460">Call services from main</span></span>

<span data-ttu-id="467f3-461">Vytvořte <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> pomocí [IServiceScopeFactory. CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) pro řešení oboru služby v rámci rozsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="467f3-461">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="467f3-462">Tento přístup je užitečný pro přístup k oboruované službě při spuštění za účelem spouštění úloh inicializace.</span><span class="sxs-lookup"><span data-stu-id="467f3-462">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="467f3-463">Následující příklad ukazuje, jak získat kontext pro `MyScopedService` `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="467f3-463">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="467f3-464">Ověřování oboru</span><span class="sxs-lookup"><span data-stu-id="467f3-464">Scope validation</span></span>

<span data-ttu-id="467f3-465">Když je aplikace spuštěná ve vývojovém prostředí a volá [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) , aby se vytvořila hostitel, použije výchozí poskytovatel služeb kontroly, aby ověřil, že:</span><span class="sxs-lookup"><span data-stu-id="467f3-465">When the app is running in the Development environment and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="467f3-466">Oborové služby nejsou přímo nebo nepřímo vyřešeny od poskytovatele kořenové služby.</span><span class="sxs-lookup"><span data-stu-id="467f3-466">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="467f3-467">Oborové služby nejsou přímo nebo nepřímo vložené do singleton.</span><span class="sxs-lookup"><span data-stu-id="467f3-467">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="467f3-468">Poskytovatel kořenové služby se vytvoří, když <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> se zavolá.</span><span class="sxs-lookup"><span data-stu-id="467f3-468">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="467f3-469">Doba života poskytovatele kořenové služby odpovídá době životnosti aplikace nebo serveru, když se poskytovatel spustí s aplikací a je uvolněný při ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="467f3-469">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="467f3-470">Oborové služby jsou uvolněny kontejnerem, který je vytvořil.</span><span class="sxs-lookup"><span data-stu-id="467f3-470">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="467f3-471">Pokud je v kořenovém kontejneru vytvořena vymezená služba, doba života služby je efektivně povýšena na typ singleton, protože je uvolněna pouze kořenovým kontejnerem při vypnutí aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="467f3-471">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="467f3-472">Při ověřování oborů služeb se tyto situace zachytí `BuildServiceProvider` .</span><span class="sxs-lookup"><span data-stu-id="467f3-472">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="467f3-473">Další informace naleznete v tématu <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="467f3-473">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="467f3-474">Žádosti o služby</span><span class="sxs-lookup"><span data-stu-id="467f3-474">Request Services</span></span>

<span data-ttu-id="467f3-475">Služby, které jsou k dispozici v rámci žádosti o ASP.NET Core, `HttpContext` jsou zpřístupněny prostřednictvím kolekce [HttpContext. RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) .</span><span class="sxs-lookup"><span data-stu-id="467f3-475">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="467f3-476">Žádosti o služby reprezentují služby nakonfigurované a požadované v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="467f3-476">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="467f3-477">Když objekty určují závislosti, jsou splněny typy nalezenými v `RequestServices` , ne `ApplicationServices` .</span><span class="sxs-lookup"><span data-stu-id="467f3-477">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="467f3-478">Obecně platí, že aplikace by tyto vlastnosti neměla používat přímo.</span><span class="sxs-lookup"><span data-stu-id="467f3-478">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="467f3-479">Místo toho požádejte o typy, které třídy vyžadují prostřednictvím konstruktorů třídy, a umožněte rozhraní vkládat závislosti.</span><span class="sxs-lookup"><span data-stu-id="467f3-479">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span> <span data-ttu-id="467f3-480">To poskytuje třídy, které jsou snáze testovány.</span><span class="sxs-lookup"><span data-stu-id="467f3-480">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="467f3-481">Preferovat požadavky na závislosti jako parametry konstruktoru pro přístup ke `RequestServices` kolekci.</span><span class="sxs-lookup"><span data-stu-id="467f3-481">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="467f3-482">Navrhnout služby pro vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="467f3-482">Design services for dependency injection</span></span>

<span data-ttu-id="467f3-483">Osvědčené postupy:</span><span class="sxs-lookup"><span data-stu-id="467f3-483">Best practices are to:</span></span>

* <span data-ttu-id="467f3-484">Navrhněte služby pro použití injektáže závislosti k získání jejich závislostí.</span><span class="sxs-lookup"><span data-stu-id="467f3-484">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="467f3-485">Vyhněte se stavovým, statickým třídám a členům.</span><span class="sxs-lookup"><span data-stu-id="467f3-485">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="467f3-486">Navrhněte aplikace, aby místo toho používaly služby s jedním prvkem, což se vyhne vytváření globálního stavu.</span><span class="sxs-lookup"><span data-stu-id="467f3-486">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="467f3-487">Vyhněte se přímému vytváření instancí závislých tříd v rámci služeb.</span><span class="sxs-lookup"><span data-stu-id="467f3-487">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="467f3-488">Přímá instance Couples kód na konkrétní implementaci.</span><span class="sxs-lookup"><span data-stu-id="467f3-488">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="467f3-489">Vytvářejte třídy aplikací malými, dobře faktoringně a snadno testované.</span><span class="sxs-lookup"><span data-stu-id="467f3-489">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="467f3-490">Pokud se zdá, že třída má příliš mnoho vložených závislostí, obvykle se jedná o znaménko, že třída má příliš mnoho zodpovědnosti a je v rozporu s [principem jediné zodpovědnosti (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="467f3-490">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="467f3-491">Pokuste se Refaktorovat třídu přesunutím některých odpovědností do nové třídy.</span><span class="sxs-lookup"><span data-stu-id="467f3-491">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="467f3-492">Mějte na paměti, že Razor stránky tříd modelu stránky a třídy KONTROLERU MVC by se měly zaměřit na uživatelské rozhraní.</span><span class="sxs-lookup"><span data-stu-id="467f3-492">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="467f3-493">Obchodní pravidla a podrobnosti implementace přístupu k datům by měly být uchovávány ve třídách, které jsou vhodné pro tyto [samostatné obavy](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="467f3-493">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="467f3-494">Vyřazení služeb</span><span class="sxs-lookup"><span data-stu-id="467f3-494">Disposal of services</span></span>

<span data-ttu-id="467f3-495">Kontejner volá <xref:System.IDisposable.Dispose*> typy, které <xref:System.IDisposable> vytvoří.</span><span class="sxs-lookup"><span data-stu-id="467f3-495">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="467f3-496">Pokud je instance přidána do kontejneru pomocí uživatelského kódu, nebude uvolněna automaticky.</span><span class="sxs-lookup"><span data-stu-id="467f3-496">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="467f3-497">V následujícím příkladu jsou služby vytvořeny pomocí kontejneru služby a automaticky odstraněny:</span><span class="sxs-lookup"><span data-stu-id="467f3-497">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="467f3-498">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="467f3-498">In the following example:</span></span>

* <span data-ttu-id="467f3-499">Instance služby nejsou vytvořeny kontejnerem služby.</span><span class="sxs-lookup"><span data-stu-id="467f3-499">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="467f3-500">Zamýšlené životnosti služeb nejsou rozhraním známy.</span><span class="sxs-lookup"><span data-stu-id="467f3-500">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="467f3-501">Rozhraní neodstraní služby automaticky.</span><span class="sxs-lookup"><span data-stu-id="467f3-501">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="467f3-502">Pokud se služby explicitně neodstraní v kódu pro vývojáře, zůstanou zachovány, dokud se aplikace neukončí.</span><span class="sxs-lookup"><span data-stu-id="467f3-502">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

<span data-ttu-id="467f3-503">Diskuzi o možnostech vyřazení služeb najdete [v článku čtyři způsoby odstranění IDisposable v ASP.NET Core](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/).</span><span class="sxs-lookup"><span data-stu-id="467f3-503">For a discussion of service disposal options, see [Four ways to dispose IDisposables in ASP.NET Core](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/).</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="467f3-504">Výchozí nahrazení kontejneru služby</span><span class="sxs-lookup"><span data-stu-id="467f3-504">Default service container replacement</span></span>

<span data-ttu-id="467f3-505">Integrovaný kontejner služeb je navržený tak, aby sloužil potřebám architektury a většině spotřebitelských aplikací.</span><span class="sxs-lookup"><span data-stu-id="467f3-505">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="467f3-506">Pokud nepotřebujete určitou funkci, kterou integrovaný kontejner nepodporuje, doporučujeme použít vestavěný kontejner, například:</span><span class="sxs-lookup"><span data-stu-id="467f3-506">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="467f3-507">Vkládání vlastností</span><span class="sxs-lookup"><span data-stu-id="467f3-507">Property injection</span></span>
* <span data-ttu-id="467f3-508">Vkládání na základě názvu</span><span class="sxs-lookup"><span data-stu-id="467f3-508">Injection based on name</span></span>
* <span data-ttu-id="467f3-509">Podřízené kontejnery</span><span class="sxs-lookup"><span data-stu-id="467f3-509">Child containers</span></span>
* <span data-ttu-id="467f3-510">Vlastní Správa životního cyklu</span><span class="sxs-lookup"><span data-stu-id="467f3-510">Custom lifetime management</span></span>
* <span data-ttu-id="467f3-511">`Func<T>`Podpora opožděné inicializace</span><span class="sxs-lookup"><span data-stu-id="467f3-511">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="467f3-512">Registrace založená na konvencích</span><span class="sxs-lookup"><span data-stu-id="467f3-512">Convention-based registration</span></span>

<span data-ttu-id="467f3-513">S ASP.NET Coremi aplikacemi je možné používat následující kontejnery třetích stran:</span><span class="sxs-lookup"><span data-stu-id="467f3-513">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="467f3-514">Autofac</span><span class="sxs-lookup"><span data-stu-id="467f3-514">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="467f3-515">DryIoc</span><span class="sxs-lookup"><span data-stu-id="467f3-515">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="467f3-516">Integrit</span><span class="sxs-lookup"><span data-stu-id="467f3-516">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="467f3-517">LightInject</span><span class="sxs-lookup"><span data-stu-id="467f3-517">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="467f3-518">Lamar</span><span class="sxs-lookup"><span data-stu-id="467f3-518">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="467f3-519">Stashbox</span><span class="sxs-lookup"><span data-stu-id="467f3-519">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="467f3-520">Unity</span><span class="sxs-lookup"><span data-stu-id="467f3-520">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="467f3-521">Bezpečnost vlákna</span><span class="sxs-lookup"><span data-stu-id="467f3-521">Thread safety</span></span>

<span data-ttu-id="467f3-522">Vytvořte služby, které jsou v bezpečí pro přístup z více vláken.</span><span class="sxs-lookup"><span data-stu-id="467f3-522">Create thread-safe singleton services.</span></span> <span data-ttu-id="467f3-523">Pokud má služba typu Singleton závislost na přechodné službě, přechodná služba může také vyžadovat zabezpečení vlákna v závislosti na tom, jak je používá typ singleton.</span><span class="sxs-lookup"><span data-stu-id="467f3-523">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="467f3-524">Výrobní metoda samostatné služby, jako je například druhý argument pro [AddSingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), nemusí být bezpečná pro přístup z více vláken.</span><span class="sxs-lookup"><span data-stu-id="467f3-524">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="467f3-525">Podobně jako konstruktor typu ( `static` ), je zaručeno, že bude volán jednou vláknem.</span><span class="sxs-lookup"><span data-stu-id="467f3-525">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="467f3-526">Doporučení</span><span class="sxs-lookup"><span data-stu-id="467f3-526">Recommendations</span></span>

* <span data-ttu-id="467f3-527">`async/await``Task`řešení služeb a se nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="467f3-527">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="467f3-528">Jazyk C# nepodporuje asynchronní konstruktory; Proto je doporučeným vzorem použití asynchronních metod po synchronním překladu služby.</span><span class="sxs-lookup"><span data-stu-id="467f3-528">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="467f3-529">Neukládejte data a konfiguraci přímo do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="467f3-529">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="467f3-530">Například nákupní košík uživatele by neměl být obvykle přidán do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="467f3-530">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="467f3-531">Konfigurace by měla používat [vzor možností](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="467f3-531">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="467f3-532">Podobně nepoužívejte objekty "držitel dat", které existují pouze k povolení přístupu k jinému objektu.</span><span class="sxs-lookup"><span data-stu-id="467f3-532">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="467f3-533">Je lepší žádat o skutečnou položku přes DI.</span><span class="sxs-lookup"><span data-stu-id="467f3-533">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="467f3-534">Vyhněte se statickému přístupu ke službám (například statickému psaní [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) pro použití jinde).</span><span class="sxs-lookup"><span data-stu-id="467f3-534">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="467f3-535">Vyhněte se použití *vzoru lokátoru služby*.</span><span class="sxs-lookup"><span data-stu-id="467f3-535">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="467f3-536">Například Nevolejte <xref:System.IServiceProvider.GetService*> pro získání instance služby, když můžete místo toho použít di:</span><span class="sxs-lookup"><span data-stu-id="467f3-536">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="467f3-537">**Špatný**</span><span class="sxs-lookup"><span data-stu-id="467f3-537">**Incorrect:**</span></span>

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

  <span data-ttu-id="467f3-538">**Správné**:</span><span class="sxs-lookup"><span data-stu-id="467f3-538">**Correct**:</span></span>

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

* <span data-ttu-id="467f3-539">Další změnou lokátoru služby, aby se zabránilo, je vložení továrny, která vyřeší závislosti za běhu.</span><span class="sxs-lookup"><span data-stu-id="467f3-539">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="467f3-540">Oba tyto postupy kombinují [inverze strategií řízení](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .</span><span class="sxs-lookup"><span data-stu-id="467f3-540">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

* <span data-ttu-id="467f3-541">Vyhněte se statickému přístupu k `HttpContext` (například [IHttpContextAccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="467f3-541">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="467f3-542">Podobně jako u všech sad doporučení se může stát, že se vyžaduje ignorování doporučení.</span><span class="sxs-lookup"><span data-stu-id="467f3-542">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="467f3-543">Výjimky jsou zřídka &mdash; většinou zvláštní případy v rámci samotného rozhraní.</span><span class="sxs-lookup"><span data-stu-id="467f3-543">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="467f3-544">DI je *alternativou* ke vzorům statických nebo globálních přístupů k objektům.</span><span class="sxs-lookup"><span data-stu-id="467f3-544">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="467f3-545">Je možné, že nebudete moci využít výhody DI, pokud je kombinujete se statickým přístupem k objektům.</span><span class="sxs-lookup"><span data-stu-id="467f3-545">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="467f3-546">Doporučené vzory pro víceklientské architektury v DI</span><span class="sxs-lookup"><span data-stu-id="467f3-546">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="467f3-547">[Sadu jader](https://github.com/OrchardCMS/OrchardCore) nabízí víceklientské architektury.</span><span class="sxs-lookup"><span data-stu-id="467f3-547">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) provides multi-tenancy.</span></span> <span data-ttu-id="467f3-548">Další informace najdete v dokumentaci k sadě [sad](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="467f3-548">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="467f3-549">V ukázkových aplikacích v tématu najdete https://github.com/OrchardCMS/OrchardCore.Samples příklady vytváření modulárních a víceklientské aplikací s využitím jenom sady Core Core Framework bez jakýchkoli funkcí specifických pro CMS.</span><span class="sxs-lookup"><span data-stu-id="467f3-549">See the samples apps at https://github.com/OrchardCMS/OrchardCore.Samples for examples of how to build modular and multi-tenant apps using just Orchard Core Framework without any of the CMS specific features.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="467f3-550">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="467f3-550">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="467f3-551">Zápis čistého kódu v ASP.NET Core pomocí injektáže závislosti (MSDN)</span><span class="sxs-lookup"><span data-stu-id="467f3-551">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="467f3-552">Explicitní závislosti – princip</span><span class="sxs-lookup"><span data-stu-id="467f3-552">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="467f3-553">Inverze řídicích kontejnerů a vzor injektáže závislosti (Martin Fowlera)</span><span class="sxs-lookup"><span data-stu-id="467f3-553">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="467f3-554">Jak zaregistrovat službu s více rozhraními v ASP.NET Core DI</span><span class="sxs-lookup"><span data-stu-id="467f3-554">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="467f3-555">ASP.NET Core podporuje vzor návrhu softwaru pro vkládání závislostí (DI), což je technika pro dosažení [inverze ovládacího prvku (IOC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) mezi třídami a jejich závislostmi.</span><span class="sxs-lookup"><span data-stu-id="467f3-555">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="467f3-556">Další informace, které jsou specifické pro vkládání závislostí v rámci řadičů MVC, najdete v tématu <xref:mvc/controllers/dependency-injection> .</span><span class="sxs-lookup"><span data-stu-id="467f3-556">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="467f3-557">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="467f3-557">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="467f3-558">Přehled injektáže závislosti</span><span class="sxs-lookup"><span data-stu-id="467f3-558">Overview of dependency injection</span></span>

<span data-ttu-id="467f3-559">*Závislost* je libovolný objekt, který vyžaduje jiný objekt.</span><span class="sxs-lookup"><span data-stu-id="467f3-559">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="467f3-560">Projděte si následující `MyDependency` třídu s `WriteMessage` metodou, na které jsou závislé jiné třídy v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="467f3-560">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="467f3-561">Instance `MyDependency` třídy může být vytvořena, aby byla `WriteMessage` Metoda k dispozici pro třídu.</span><span class="sxs-lookup"><span data-stu-id="467f3-561">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="467f3-562">`MyDependency`Třída je závislostí `IndexModel` třídy:</span><span class="sxs-lookup"><span data-stu-id="467f3-562">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="467f3-563">Třída vytvoří a přímo závisí na `MyDependency` instanci.</span><span class="sxs-lookup"><span data-stu-id="467f3-563">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="467f3-564">Závislosti kódu (například předchozí příklad) jsou problematické a je třeba se jim vyhnout z následujících důvodů:</span><span class="sxs-lookup"><span data-stu-id="467f3-564">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="467f3-565">Chcete-li nahradit `MyDependency` jinou implementací, třída musí být upravena.</span><span class="sxs-lookup"><span data-stu-id="467f3-565">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="467f3-566">Pokud `MyDependency` má závislosti, musí být nakonfigurovány třídou.</span><span class="sxs-lookup"><span data-stu-id="467f3-566">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="467f3-567">Ve velkém projektu s více třídami, v závislosti na tom `MyDependency` , je kód konfigurace rozptýlen napříč aplikací.</span><span class="sxs-lookup"><span data-stu-id="467f3-567">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="467f3-568">Tato implementace je obtížná pro testování částí.</span><span class="sxs-lookup"><span data-stu-id="467f3-568">This implementation is difficult to unit test.</span></span> <span data-ttu-id="467f3-569">Aplikace by měla používat třídu typu "nebo zástupné procedury" `MyDependency` , což není u tohoto přístupu možné.</span><span class="sxs-lookup"><span data-stu-id="467f3-569">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="467f3-570">Vkládání závislostí řeší tyto problémy prostřednictvím:</span><span class="sxs-lookup"><span data-stu-id="467f3-570">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="467f3-571">Použití rozhraní nebo základní třídy k abstrakci implementace závislosti.</span><span class="sxs-lookup"><span data-stu-id="467f3-571">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="467f3-572">Registrace závislosti v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="467f3-572">Registration of the dependency in a service container.</span></span> <span data-ttu-id="467f3-573">ASP.NET Core poskytuje integrovaný kontejner služeb <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="467f3-573">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="467f3-574">Služby jsou registrovány v metodě aplikace `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="467f3-574">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="467f3-575">*Vložení* služby do konstruktoru třídy, kde se používá.</span><span class="sxs-lookup"><span data-stu-id="467f3-575">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="467f3-576">Rozhraní přebírá zodpovědnost za vytvoření instance závislosti a její odstranění, když už ji nepotřebujete.</span><span class="sxs-lookup"><span data-stu-id="467f3-576">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="467f3-577">V [ukázkové aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) `IMyDependency` definuje rozhraní metodu, kterou služba poskytuje aplikaci:</span><span class="sxs-lookup"><span data-stu-id="467f3-577">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="467f3-578">Toto rozhraní je implementováno konkrétním typem `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="467f3-578">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="467f3-579">`MyDependency`vyžádá <xref:Microsoft.Extensions.Logging.ILogger`1> v jeho konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="467f3-579">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="467f3-580">Není neobvyklé používat vkládání závislostí v zřetězeném způsobem.</span><span class="sxs-lookup"><span data-stu-id="467f3-580">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="467f3-581">Každá požadovaná závislost zase vyžádá své vlastní závislosti.</span><span class="sxs-lookup"><span data-stu-id="467f3-581">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="467f3-582">Kontejner vyřeší závislosti v grafu a vrátí plně přeloženou službu.</span><span class="sxs-lookup"><span data-stu-id="467f3-582">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="467f3-583">Kolektivní sada závislostí, které je třeba vyřešit, se obvykle označuje jako *strom závislosti*, *graf závislosti*nebo *graf objektů*.</span><span class="sxs-lookup"><span data-stu-id="467f3-583">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="467f3-584">`IMyDependency`a `ILogger<TCategoryName>` musí být registrována v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="467f3-584">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="467f3-585">`IMyDependency`je zaregistrován v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="467f3-585">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="467f3-586">`ILogger<TCategoryName>`je zaregistrované v infrastruktuře abstrakce protokolování, takže se jedná o [službu](#framework-provided-services) , která je zaregistrovaná ve výchozím rozhraní.</span><span class="sxs-lookup"><span data-stu-id="467f3-586">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="467f3-587">Kontejner se překládá `ILogger<TCategoryName>` tím, že využije [(Obecné) otevřené typy](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)a eliminuje nutnost registrace každého [(obecného) konstruovaného typu](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="467f3-587">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="467f3-588">V ukázkové aplikaci `IMyDependency` je služba zaregistrovaná u konkrétního typu `MyDependency` .</span><span class="sxs-lookup"><span data-stu-id="467f3-588">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="467f3-589">Registruje rozsah platnosti služby po dobu životnosti jediného požadavku.</span><span class="sxs-lookup"><span data-stu-id="467f3-589">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="467f3-590">[Doby platnosti služeb](#service-lifetimes) jsou popsány dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="467f3-590">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="467f3-591">Jednotlivé `services.Add{SERVICE_NAME}` metody rozšíření přidávají (a případně nakonfigurují) služby.</span><span class="sxs-lookup"><span data-stu-id="467f3-591">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="467f3-592">Například `services.AddMvc()` přidá Razor stránky služeb a MVC vyžaduje.</span><span class="sxs-lookup"><span data-stu-id="467f3-592">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="467f3-593">Doporučujeme, aby aplikace dodržovaly tuto konvenci.</span><span class="sxs-lookup"><span data-stu-id="467f3-593">We recommended that apps follow this convention.</span></span> <span data-ttu-id="467f3-594">Do oboru názvů [Microsoft. Extensions. DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) umístěte metody rozšíření pro zapouzdření skupin registrací služby.</span><span class="sxs-lookup"><span data-stu-id="467f3-594">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="467f3-595">Pokud konstruktor služby vyžaduje [vestavěný typ](/dotnet/csharp/language-reference/keywords/built-in-types-table), například `string` ,, může být typ vložen pomocí [Konfigurace](xref:fundamentals/configuration/index) nebo [vzoru možností](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="467f3-595">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="467f3-596">Instance služby je požadována prostřednictvím konstruktoru třídy, kde je služba používána a přiřazena k soukromému poli.</span><span class="sxs-lookup"><span data-stu-id="467f3-596">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="467f3-597">Pole se používá pro přístup ke službě podle potřeby v celé třídě.</span><span class="sxs-lookup"><span data-stu-id="467f3-597">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="467f3-598">V ukázkové aplikaci `IMyDependency` je instance vyžádána a používána pro volání `WriteMessage` metody služby:</span><span class="sxs-lookup"><span data-stu-id="467f3-598">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="467f3-599">Služby vložené do spuštění</span><span class="sxs-lookup"><span data-stu-id="467f3-599">Services injected into Startup</span></span>

<span data-ttu-id="467f3-600">`Startup`Při použití obecného hostitele () mohou být do konstruktoru vloženy pouze následující typy služeb <xref:Microsoft.Extensions.Hosting.IHostBuilder> :</span><span class="sxs-lookup"><span data-stu-id="467f3-600">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="467f3-601">Služby je možné vložit do `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="467f3-601">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="467f3-602">Další informace naleznete v tématu <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="467f3-602">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="467f3-603">Služby poskytované rozhraním</span><span class="sxs-lookup"><span data-stu-id="467f3-603">Framework-provided services</span></span>

<span data-ttu-id="467f3-604">`Startup.ConfigureServices`Metoda zodpovídá za definování služeb, které aplikace používá, včetně funkcí platformy, jako je například Entity Framework Core a ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="467f3-604">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="467f3-605">Zpočátku `IServiceCollection` `ConfigureServices` služba má služby definované rozhraním v závislosti na [tom, jak byl hostitel nakonfigurovaný](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="467f3-605">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="467f3-606">Pro aplikaci, která je založená na šabloně ASP.NET Core, není běžné, že mají stovky služeb zaregistrovaných v rámci rozhraní.</span><span class="sxs-lookup"><span data-stu-id="467f3-606">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="467f3-607">V následující tabulce je uvedena malá ukázka služeb registrovaných v rozhraní.</span><span class="sxs-lookup"><span data-stu-id="467f3-607">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="467f3-608">Typ služby</span><span class="sxs-lookup"><span data-stu-id="467f3-608">Service Type</span></span> | <span data-ttu-id="467f3-609">Doba platnosti</span><span class="sxs-lookup"><span data-stu-id="467f3-609">Lifetime</span></span> |
| ---
<span data-ttu-id="467f3-610">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="467f3-610">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="467f3-611">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467f3-611">'Blazor'</span></span>
- <span data-ttu-id="467f3-612">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467f3-612">'Identity'</span></span>
- <span data-ttu-id="467f3-613">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467f3-613">'Let's Encrypt'</span></span>
- <span data-ttu-id="467f3-614">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467f3-614">'Razor'</span></span>
- <span data-ttu-id="467f3-615">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="467f3-615">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467f3-616">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="467f3-616">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="467f3-617">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467f3-617">'Blazor'</span></span>
- <span data-ttu-id="467f3-618">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467f3-618">'Identity'</span></span>
- <span data-ttu-id="467f3-619">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467f3-619">'Let's Encrypt'</span></span>
- <span data-ttu-id="467f3-620">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467f3-620">'Razor'</span></span>
- <span data-ttu-id="467f3-621">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="467f3-621">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467f3-622">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="467f3-622">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="467f3-623">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467f3-623">'Blazor'</span></span>
- <span data-ttu-id="467f3-624">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467f3-624">'Identity'</span></span>
- <span data-ttu-id="467f3-625">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467f3-625">'Let's Encrypt'</span></span>
- <span data-ttu-id="467f3-626">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467f3-626">'Razor'</span></span>
- <span data-ttu-id="467f3-627">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="467f3-627">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467f3-628">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="467f3-628">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="467f3-629">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467f3-629">'Blazor'</span></span>
- <span data-ttu-id="467f3-630">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467f3-630">'Identity'</span></span>
- <span data-ttu-id="467f3-631">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467f3-631">'Let's Encrypt'</span></span>
- <span data-ttu-id="467f3-632">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467f3-632">'Razor'</span></span>
- <span data-ttu-id="467f3-633">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="467f3-633">'SignalR' uid:</span></span> 

<span data-ttu-id="467f3-634">------ | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="467f3-634">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="467f3-635">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467f3-635">'Blazor'</span></span>
- <span data-ttu-id="467f3-636">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467f3-636">'Identity'</span></span>
- <span data-ttu-id="467f3-637">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467f3-637">'Let's Encrypt'</span></span>
- <span data-ttu-id="467f3-638">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467f3-638">'Razor'</span></span>
- <span data-ttu-id="467f3-639">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="467f3-639">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467f3-640">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="467f3-640">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="467f3-641">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467f3-641">'Blazor'</span></span>
- <span data-ttu-id="467f3-642">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467f3-642">'Identity'</span></span>
- <span data-ttu-id="467f3-643">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467f3-643">'Let's Encrypt'</span></span>
- <span data-ttu-id="467f3-644">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467f3-644">'Razor'</span></span>
- <span data-ttu-id="467f3-645">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="467f3-645">'SignalR' uid:</span></span> 

<span data-ttu-id="467f3-646">---- | | <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Přechodný | | <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Přechodný | | <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Přechodný | | <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Přechodný | | <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Singleton |</span><span class="sxs-lookup"><span data-stu-id="467f3-646">---- | | <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Transient | | <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Transient | | <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Transient | | <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Transient | | <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Singleton |</span></span>

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="467f3-647">Registrace dalších služeb pomocí rozšiřujících metod</span><span class="sxs-lookup"><span data-stu-id="467f3-647">Register additional services with extension methods</span></span>

<span data-ttu-id="467f3-648">Pokud je k dispozici metoda rozšíření kolekce služeb pro registraci služby (a jejích závislých služeb, v případě potřeby), konvence používá jedinou `Add{SERVICE_NAME}` metodu rozšíření k registraci všech služeb vyžadovaných touto službou.</span><span class="sxs-lookup"><span data-stu-id="467f3-648">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="467f3-649">Následující kód je příkladem přidání dalších služeb do kontejneru pomocí metod rozšíření [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) a <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> :</span><span class="sxs-lookup"><span data-stu-id="467f3-649">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="467f3-650">Další informace najdete <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> ve třídě v dokumentaci k rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="467f3-650">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="467f3-651">Životnost služeb</span><span class="sxs-lookup"><span data-stu-id="467f3-651">Service lifetimes</span></span>

<span data-ttu-id="467f3-652">Vyberte odpovídající dobu života pro každou registrovanou službu.</span><span class="sxs-lookup"><span data-stu-id="467f3-652">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="467f3-653">Služby ASP.NET Core Services je možné nakonfigurovat s následujícími životnostmi:</span><span class="sxs-lookup"><span data-stu-id="467f3-653">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="467f3-654">Dočasný</span><span class="sxs-lookup"><span data-stu-id="467f3-654">Transient</span></span>

<span data-ttu-id="467f3-655">Služba přechodných životností ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*> ) se vytvoří pokaždé, když se požadují z kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="467f3-655">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="467f3-656">Tato životnost funguje nejlépe pro odlehčené bezstavové služby.</span><span class="sxs-lookup"><span data-stu-id="467f3-656">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="467f3-657">Obor</span><span class="sxs-lookup"><span data-stu-id="467f3-657">Scoped</span></span>

<span data-ttu-id="467f3-658">Oborové služby () s rozsahem platnosti ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*> ) se vytvoří jednou za požadavek klienta (připojení).</span><span class="sxs-lookup"><span data-stu-id="467f3-658">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="467f3-659">Při použití oboru služby v middlewaru založit službu do `Invoke` `InvokeAsync` metody nebo.</span><span class="sxs-lookup"><span data-stu-id="467f3-659">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="467f3-660">Nepoužívejte [vkládání pomocí injektáže konstruktoru](xref:mvc/controllers/dependency-injection#constructor-injection) , protože vynutí, aby se služba chovala jako typ singleton.</span><span class="sxs-lookup"><span data-stu-id="467f3-660">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="467f3-661">Další informace naleznete v tématu <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="467f3-661">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="467f3-662">Singleton</span><span class="sxs-lookup"><span data-stu-id="467f3-662">Singleton</span></span>

<span data-ttu-id="467f3-663">Služba singleton životnosti ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*> ) je vytvořena při prvním vyžádání (nebo při `Startup.ConfigureServices` spuštění a instance je určena pro registraci služby).</span><span class="sxs-lookup"><span data-stu-id="467f3-663">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="467f3-664">Každý další požadavek používá stejnou instanci.</span><span class="sxs-lookup"><span data-stu-id="467f3-664">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="467f3-665">Pokud aplikace vyžaduje chování singleton, doporučuje se povolit kontejneru služby spravovat dobu života služby.</span><span class="sxs-lookup"><span data-stu-id="467f3-665">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="467f3-666">Neimplementujte vzor návrhu singleton a poskytněte uživatelský kód pro správu životního cyklu objektu ve třídě.</span><span class="sxs-lookup"><span data-stu-id="467f3-666">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="467f3-667">Není bezpečné přeložit oborovou službu z typu singleton.</span><span class="sxs-lookup"><span data-stu-id="467f3-667">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="467f3-668">Může dojít k tomu, že služba má při zpracování dalších požadavků špatný stav.</span><span class="sxs-lookup"><span data-stu-id="467f3-668">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="467f3-669">Metody registrace služby</span><span class="sxs-lookup"><span data-stu-id="467f3-669">Service registration methods</span></span>

<span data-ttu-id="467f3-670">Metody rozšíření registrace služby nabízejí přetížení, která jsou užitečná pro konkrétní scénáře.</span><span class="sxs-lookup"><span data-stu-id="467f3-670">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="467f3-671">Metoda</span><span class="sxs-lookup"><span data-stu-id="467f3-671">Method</span></span> | <span data-ttu-id="467f3-672">Automaticky</span><span class="sxs-lookup"><span data-stu-id="467f3-672">Automatic</span></span><br><span data-ttu-id="467f3-673">odkazy objektů</span><span class="sxs-lookup"><span data-stu-id="467f3-673">object</span></span><br><span data-ttu-id="467f3-674">odvod</span><span class="sxs-lookup"><span data-stu-id="467f3-674">disposal</span></span> | <span data-ttu-id="467f3-675">Několik</span><span class="sxs-lookup"><span data-stu-id="467f3-675">Multiple</span></span><br><span data-ttu-id="467f3-676">implementace</span><span class="sxs-lookup"><span data-stu-id="467f3-676">implementations</span></span> | <span data-ttu-id="467f3-677">Pass – argumenty</span><span class="sxs-lookup"><span data-stu-id="467f3-677">Pass args</span></span> |
| ---
<span data-ttu-id="467f3-678">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="467f3-678">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="467f3-679">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467f3-679">'Blazor'</span></span>
- <span data-ttu-id="467f3-680">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467f3-680">'Identity'</span></span>
- <span data-ttu-id="467f3-681">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467f3-681">'Let's Encrypt'</span></span>
- <span data-ttu-id="467f3-682">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467f3-682">'Razor'</span></span>
- <span data-ttu-id="467f3-683">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="467f3-683">'SignalR' uid:</span></span> 

<span data-ttu-id="467f3-684">--- | :---název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="467f3-684">--- | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="467f3-685">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467f3-685">'Blazor'</span></span>
- <span data-ttu-id="467f3-686">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467f3-686">'Identity'</span></span>
- <span data-ttu-id="467f3-687">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467f3-687">'Let's Encrypt'</span></span>
- <span data-ttu-id="467f3-688">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467f3-688">'Razor'</span></span>
- <span data-ttu-id="467f3-689">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="467f3-689">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467f3-690">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="467f3-690">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="467f3-691">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467f3-691">'Blazor'</span></span>
- <span data-ttu-id="467f3-692">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467f3-692">'Identity'</span></span>
- <span data-ttu-id="467f3-693">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467f3-693">'Let's Encrypt'</span></span>
- <span data-ttu-id="467f3-694">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467f3-694">'Razor'</span></span>
- <span data-ttu-id="467f3-695">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="467f3-695">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467f3-696">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="467f3-696">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="467f3-697">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467f3-697">'Blazor'</span></span>
- <span data-ttu-id="467f3-698">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467f3-698">'Identity'</span></span>
- <span data-ttu-id="467f3-699">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467f3-699">'Let's Encrypt'</span></span>
- <span data-ttu-id="467f3-700">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467f3-700">'Razor'</span></span>
- <span data-ttu-id="467f3-701">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="467f3-701">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467f3-702">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="467f3-702">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="467f3-703">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467f3-703">'Blazor'</span></span>
- <span data-ttu-id="467f3-704">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467f3-704">'Identity'</span></span>
- <span data-ttu-id="467f3-705">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467f3-705">'Let's Encrypt'</span></span>
- <span data-ttu-id="467f3-706">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467f3-706">'Razor'</span></span>
- <span data-ttu-id="467f3-707">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="467f3-707">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467f3-708">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="467f3-708">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="467f3-709">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467f3-709">'Blazor'</span></span>
- <span data-ttu-id="467f3-710">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467f3-710">'Identity'</span></span>
- <span data-ttu-id="467f3-711">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467f3-711">'Let's Encrypt'</span></span>
- <span data-ttu-id="467f3-712">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467f3-712">'Razor'</span></span>
- <span data-ttu-id="467f3-713">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="467f3-713">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467f3-714">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="467f3-714">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="467f3-715">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467f3-715">'Blazor'</span></span>
- <span data-ttu-id="467f3-716">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467f3-716">'Identity'</span></span>
- <span data-ttu-id="467f3-717">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467f3-717">'Let's Encrypt'</span></span>
- <span data-ttu-id="467f3-718">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467f3-718">'Razor'</span></span>
- <span data-ttu-id="467f3-719">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="467f3-719">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467f3-720">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="467f3-720">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="467f3-721">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467f3-721">'Blazor'</span></span>
- <span data-ttu-id="467f3-722">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467f3-722">'Identity'</span></span>
- <span data-ttu-id="467f3-723">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467f3-723">'Let's Encrypt'</span></span>
- <span data-ttu-id="467f3-724">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467f3-724">'Razor'</span></span>
- <span data-ttu-id="467f3-725">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="467f3-725">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467f3-726">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="467f3-726">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="467f3-727">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467f3-727">'Blazor'</span></span>
- <span data-ttu-id="467f3-728">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467f3-728">'Identity'</span></span>
- <span data-ttu-id="467f3-729">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467f3-729">'Let's Encrypt'</span></span>
- <span data-ttu-id="467f3-730">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467f3-730">'Razor'</span></span>
- <span data-ttu-id="467f3-731">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="467f3-731">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467f3-732">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="467f3-732">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="467f3-733">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467f3-733">'Blazor'</span></span>
- <span data-ttu-id="467f3-734">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467f3-734">'Identity'</span></span>
- <span data-ttu-id="467f3-735">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467f3-735">'Let's Encrypt'</span></span>
- <span data-ttu-id="467f3-736">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467f3-736">'Razor'</span></span>
- <span data-ttu-id="467f3-737">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="467f3-737">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467f3-738">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="467f3-738">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="467f3-739">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467f3-739">'Blazor'</span></span>
- <span data-ttu-id="467f3-740">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467f3-740">'Identity'</span></span>
- <span data-ttu-id="467f3-741">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467f3-741">'Let's Encrypt'</span></span>
- <span data-ttu-id="467f3-742">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467f3-742">'Razor'</span></span>
- <span data-ttu-id="467f3-743">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="467f3-743">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467f3-744">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="467f3-744">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="467f3-745">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467f3-745">'Blazor'</span></span>
- <span data-ttu-id="467f3-746">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467f3-746">'Identity'</span></span>
- <span data-ttu-id="467f3-747">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467f3-747">'Let's Encrypt'</span></span>
- <span data-ttu-id="467f3-748">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467f3-748">'Razor'</span></span>
- <span data-ttu-id="467f3-749">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="467f3-749">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467f3-750">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="467f3-750">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="467f3-751">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467f3-751">'Blazor'</span></span>
- <span data-ttu-id="467f3-752">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467f3-752">'Identity'</span></span>
- <span data-ttu-id="467f3-753">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467f3-753">'Let's Encrypt'</span></span>
- <span data-ttu-id="467f3-754">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467f3-754">'Razor'</span></span>
- <span data-ttu-id="467f3-755">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="467f3-755">'SignalR' uid:</span></span> 

<span data-ttu-id="467f3-756">---------------: | :---název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="467f3-756">---------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="467f3-757">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467f3-757">'Blazor'</span></span>
- <span data-ttu-id="467f3-758">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467f3-758">'Identity'</span></span>
- <span data-ttu-id="467f3-759">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467f3-759">'Let's Encrypt'</span></span>
- <span data-ttu-id="467f3-760">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467f3-760">'Razor'</span></span>
- <span data-ttu-id="467f3-761">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="467f3-761">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467f3-762">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="467f3-762">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="467f3-763">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467f3-763">'Blazor'</span></span>
- <span data-ttu-id="467f3-764">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467f3-764">'Identity'</span></span>
- <span data-ttu-id="467f3-765">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467f3-765">'Let's Encrypt'</span></span>
- <span data-ttu-id="467f3-766">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467f3-766">'Razor'</span></span>
- <span data-ttu-id="467f3-767">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="467f3-767">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467f3-768">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="467f3-768">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="467f3-769">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467f3-769">'Blazor'</span></span>
- <span data-ttu-id="467f3-770">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467f3-770">'Identity'</span></span>
- <span data-ttu-id="467f3-771">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467f3-771">'Let's Encrypt'</span></span>
- <span data-ttu-id="467f3-772">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467f3-772">'Razor'</span></span>
- <span data-ttu-id="467f3-773">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="467f3-773">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467f3-774">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="467f3-774">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="467f3-775">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467f3-775">'Blazor'</span></span>
- <span data-ttu-id="467f3-776">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467f3-776">'Identity'</span></span>
- <span data-ttu-id="467f3-777">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467f3-777">'Let's Encrypt'</span></span>
- <span data-ttu-id="467f3-778">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467f3-778">'Razor'</span></span>
- <span data-ttu-id="467f3-779">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="467f3-779">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467f3-780">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="467f3-780">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="467f3-781">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467f3-781">'Blazor'</span></span>
- <span data-ttu-id="467f3-782">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467f3-782">'Identity'</span></span>
- <span data-ttu-id="467f3-783">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467f3-783">'Let's Encrypt'</span></span>
- <span data-ttu-id="467f3-784">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467f3-784">'Razor'</span></span>
- <span data-ttu-id="467f3-785">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="467f3-785">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467f3-786">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="467f3-786">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="467f3-787">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467f3-787">'Blazor'</span></span>
- <span data-ttu-id="467f3-788">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467f3-788">'Identity'</span></span>
- <span data-ttu-id="467f3-789">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467f3-789">'Let's Encrypt'</span></span>
- <span data-ttu-id="467f3-790">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467f3-790">'Razor'</span></span>
- <span data-ttu-id="467f3-791">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="467f3-791">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467f3-792">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="467f3-792">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="467f3-793">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467f3-793">'Blazor'</span></span>
- <span data-ttu-id="467f3-794">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467f3-794">'Identity'</span></span>
- <span data-ttu-id="467f3-795">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467f3-795">'Let's Encrypt'</span></span>
- <span data-ttu-id="467f3-796">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467f3-796">'Razor'</span></span>
- <span data-ttu-id="467f3-797">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="467f3-797">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467f3-798">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="467f3-798">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="467f3-799">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467f3-799">'Blazor'</span></span>
- <span data-ttu-id="467f3-800">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467f3-800">'Identity'</span></span>
- <span data-ttu-id="467f3-801">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467f3-801">'Let's Encrypt'</span></span>
- <span data-ttu-id="467f3-802">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467f3-802">'Razor'</span></span>
- <span data-ttu-id="467f3-803">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="467f3-803">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467f3-804">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="467f3-804">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="467f3-805">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467f3-805">'Blazor'</span></span>
- <span data-ttu-id="467f3-806">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467f3-806">'Identity'</span></span>
- <span data-ttu-id="467f3-807">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467f3-807">'Let's Encrypt'</span></span>
- <span data-ttu-id="467f3-808">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467f3-808">'Razor'</span></span>
- <span data-ttu-id="467f3-809">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="467f3-809">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467f3-810">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="467f3-810">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="467f3-811">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467f3-811">'Blazor'</span></span>
- <span data-ttu-id="467f3-812">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467f3-812">'Identity'</span></span>
- <span data-ttu-id="467f3-813">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467f3-813">'Let's Encrypt'</span></span>
- <span data-ttu-id="467f3-814">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467f3-814">'Razor'</span></span>
- <span data-ttu-id="467f3-815">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="467f3-815">'SignalR' uid:</span></span> 

<span data-ttu-id="467f3-816">-------------: | :---název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="467f3-816">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="467f3-817">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467f3-817">'Blazor'</span></span>
- <span data-ttu-id="467f3-818">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467f3-818">'Identity'</span></span>
- <span data-ttu-id="467f3-819">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467f3-819">'Let's Encrypt'</span></span>
- <span data-ttu-id="467f3-820">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467f3-820">'Razor'</span></span>
- <span data-ttu-id="467f3-821">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="467f3-821">'SignalR' uid:</span></span> 

<span data-ttu-id="467f3-822">----: | | `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`</span><span class="sxs-lookup"><span data-stu-id="467f3-822">----: | | `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`</span></span><br><span data-ttu-id="467f3-823">Příklad:</span><span class="sxs-lookup"><span data-stu-id="467f3-823">Example:</span></span><br><span data-ttu-id="467f3-824">`services.AddSingleton<IMyDep, MyDep>();`| Ano | Ano | Žádné | |`Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="467f3-824">`services.AddSingleton<IMyDep, MyDep>();` | Yes | Yes | No | | `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="467f3-825">Příklady:</span><span class="sxs-lookup"><span data-stu-id="467f3-825">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br><span data-ttu-id="467f3-826">`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));`| Ano | Ano | Ano | |`Add{LIFETIME}<{IMPLEMENTATION}>()`</span><span class="sxs-lookup"><span data-stu-id="467f3-826">`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | Yes | Yes | Yes | | `Add{LIFETIME}<{IMPLEMENTATION}>()`</span></span><br><span data-ttu-id="467f3-827">Příklad:</span><span class="sxs-lookup"><span data-stu-id="467f3-827">Example:</span></span><br><span data-ttu-id="467f3-828">`services.AddSingleton<MyDep>();`| Ano | Žádné | Žádné | |`AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="467f3-828">`services.AddSingleton<MyDep>();` | Yes | No | No | | `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="467f3-829">Příklady:</span><span class="sxs-lookup"><span data-stu-id="467f3-829">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br><span data-ttu-id="467f3-830">`services.AddSingleton<IMyDep>(new MyDep("A string!"));`| Žádné | Ano | Ano | |`AddSingleton(new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="467f3-830">`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | No | Yes | Yes | | `AddSingleton(new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="467f3-831">Příklady:</span><span class="sxs-lookup"><span data-stu-id="467f3-831">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br><span data-ttu-id="467f3-832">`services.AddSingleton(new MyDep("A string!"));`| Žádné | Žádné | Ano |</span><span class="sxs-lookup"><span data-stu-id="467f3-832">`services.AddSingleton(new MyDep("A string!"));` | No | No | Yes |</span></span>

<span data-ttu-id="467f3-833">Další informace o vyřazení typů najdete v části věnované [vyřazení služeb](#disposal-of-services) .</span><span class="sxs-lookup"><span data-stu-id="467f3-833">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="467f3-834">Běžným scénářem pro více implementací je vytvoření [typů pro testování](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="467f3-834">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="467f3-835">`TryAdd{LIFETIME}`metody registrují službu jenom v případě, že už není zaregistrovaná implementace.</span><span class="sxs-lookup"><span data-stu-id="467f3-835">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="467f3-836">V následujícím příkladu se první řádek registruje `MyDependency` pro `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="467f3-836">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="467f3-837">Druhý řádek nemá žádný účinek, protože `IMyDependency` už má registrovanou implementaci:</span><span class="sxs-lookup"><span data-stu-id="467f3-837">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="467f3-838">Další informace naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="467f3-838">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="467f3-839">Metody [TryAddEnumerable (ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) registrují službu pouze v případě, že již není implementace *stejného typu*.</span><span class="sxs-lookup"><span data-stu-id="467f3-839">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="467f3-840">Několik služeb je vyřešeno prostřednictvím `IEnumerable<{SERVICE}>` .</span><span class="sxs-lookup"><span data-stu-id="467f3-840">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="467f3-841">Při registraci služeb chce vývojář přidat instanci jenom v případě, že už jeden ze stejného typu není přidaný.</span><span class="sxs-lookup"><span data-stu-id="467f3-841">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="467f3-842">Obecně je tato metoda používána autory knihovny k tomu, aby nedocházelo k registraci dvou kopií instance v kontejneru.</span><span class="sxs-lookup"><span data-stu-id="467f3-842">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="467f3-843">V následujícím příkladu se první řádek registruje `MyDep` pro `IMyDep1` .</span><span class="sxs-lookup"><span data-stu-id="467f3-843">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="467f3-844">Druhý řádek se registruje `MyDep` pro `IMyDep2` .</span><span class="sxs-lookup"><span data-stu-id="467f3-844">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="467f3-845">Třetí řádek nemá žádný vliv, protože `IMyDep1` už má registrovanou implementaci `MyDep` :</span><span class="sxs-lookup"><span data-stu-id="467f3-845">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="467f3-846">Chování injektáže konstruktoru</span><span class="sxs-lookup"><span data-stu-id="467f3-846">Constructor injection behavior</span></span>

<span data-ttu-id="467f3-847">Služby je možné vyřešit pomocí dvou mechanismů:</span><span class="sxs-lookup"><span data-stu-id="467f3-847">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="467f3-848"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Povoluje vytvoření objektu bez registrace služby v kontejneru vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="467f3-848"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="467f3-849">`ActivatorUtilities`se používá s uživateli s přístupem k uživatelům, jako jsou například rutiny značek, řadiče MVC a pořadače modelů.</span><span class="sxs-lookup"><span data-stu-id="467f3-849">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="467f3-850">Konstruktory mohou přijímat argumenty, které nejsou poskytovány vkládáním závislostí, ale argumenty musí přiřadit výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="467f3-850">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="467f3-851">Když jsou služby vyřešeny `IServiceProvider` nebo `ActivatorUtilities` , [Injektáže konstruktoru](xref:mvc/controllers/dependency-injection#constructor-injection) vyžaduje *veřejný* konstruktor.</span><span class="sxs-lookup"><span data-stu-id="467f3-851">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="467f3-852">Když jsou služby vyřešeny nástrojem `ActivatorUtilities` , [Injektáže konstruktoru](xref:mvc/controllers/dependency-injection#constructor-injection) vyžaduje, aby existoval pouze jeden příslušný konstruktor.</span><span class="sxs-lookup"><span data-stu-id="467f3-852">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="467f3-853">Přetížení konstruktoru jsou podporovaná, ale může existovat jenom jedno přetížení, jehož argumenty můžou být splněné vkládáním závislostí.</span><span class="sxs-lookup"><span data-stu-id="467f3-853">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="467f3-854">Entity Framework kontexty</span><span class="sxs-lookup"><span data-stu-id="467f3-854">Entity Framework contexts</span></span>

<span data-ttu-id="467f3-855">Entity Framework kontexty se obvykle přidávají do kontejneru služby pomocí [rozsahu životnosti](#service-lifetimes) , protože operace databáze webové aplikace jsou obvykle vymezeny na požadavky klienta.</span><span class="sxs-lookup"><span data-stu-id="467f3-855">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="467f3-856">Výchozí doba života je vymezena v případě, že při registraci kontextu databáze není zadána doba života při [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) přetížení.</span><span class="sxs-lookup"><span data-stu-id="467f3-856">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="467f3-857">Služby pro danou dobu života by neměly používat kontext databáze s kratší dobou životnosti než služba.</span><span class="sxs-lookup"><span data-stu-id="467f3-857">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="467f3-858">Možnosti života a registrace</span><span class="sxs-lookup"><span data-stu-id="467f3-858">Lifetime and registration options</span></span>

<span data-ttu-id="467f3-859">Chcete-li předvést rozdíl mezi možnostmi životního cyklu a registrací, zvažte následující rozhraní, která představují úlohy jako operaci s jedinečným identifikátorem `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="467f3-859">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="467f3-860">V závislosti na tom, jak je životnost provozní služby nakonfigurována pro následující rozhraní, kontejner poskytuje buď stejnou nebo jinou instanci služby, když je vyžádána třídou:</span><span class="sxs-lookup"><span data-stu-id="467f3-860">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="467f3-861">Rozhraní jsou implementována ve `Operation` třídě.</span><span class="sxs-lookup"><span data-stu-id="467f3-861">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="467f3-862">`Operation`Konstruktor generuje identifikátor GUID, pokud není dodán:</span><span class="sxs-lookup"><span data-stu-id="467f3-862">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="467f3-863">`OperationService`Je zaregistrován, který závisí na každém z ostatních `Operation` typů.</span><span class="sxs-lookup"><span data-stu-id="467f3-863">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="467f3-864">Když `OperationService` je požadavek proveden prostřednictvím injektáže závislosti, obdrží buď novou instanci každé služby, nebo existující instanci na základě životnosti závislé služby.</span><span class="sxs-lookup"><span data-stu-id="467f3-864">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="467f3-865">Když se v případě vyžádání z kontejneru vytvoří přechodné služby, `OperationId` liší se od `IOperationTransient` služby `OperationId` `OperationService` .</span><span class="sxs-lookup"><span data-stu-id="467f3-865">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="467f3-866">`OperationService`přijme novou instanci `IOperationTransient` třídy.</span><span class="sxs-lookup"><span data-stu-id="467f3-866">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="467f3-867">Nová instance vrací jinou instanci `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="467f3-867">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="467f3-868">Když se vytvoří vymezené služby pro jednotlivé požadavky klienta, `OperationId` `IOperationScoped` služba je stejná jako `OperationService` v rámci žádosti klienta.</span><span class="sxs-lookup"><span data-stu-id="467f3-868">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="467f3-869">V rámci požadavků klientů obě služby sdílejí jinou `OperationId` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="467f3-869">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="467f3-870">Pokud se služby instance singleton a singleton vytvoří jednou a použije se pro všechny požadavky klientů a všechny služby, `OperationId` je v rámci všech žádostí o službu konstantní.</span><span class="sxs-lookup"><span data-stu-id="467f3-870">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="467f3-871">V `Startup.ConfigureServices` je každý typ přidán do kontejneru podle jeho pojmenované životnosti:</span><span class="sxs-lookup"><span data-stu-id="467f3-871">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="467f3-872">`IOperationSingletonInstance`Služba používá konkrétní instanci se známým ID `Guid.Empty` .</span><span class="sxs-lookup"><span data-stu-id="467f3-872">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="467f3-873">Je jasné, že pokud se tento typ používá (jeho identifikátor GUID je všechny nuly).</span><span class="sxs-lookup"><span data-stu-id="467f3-873">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="467f3-874">Ukázková aplikace ukazuje dobu života objektu v rámci a mezi jednotlivými požadavky.</span><span class="sxs-lookup"><span data-stu-id="467f3-874">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="467f3-875">Ukázková aplikace `IndexModel` požaduje každý druh `IOperation` typu a `OperationService` .</span><span class="sxs-lookup"><span data-stu-id="467f3-875">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="467f3-876">Stránka potom zobrazí všechny hodnoty třídy modelu stránky a `OperationId` hodnoty služby prostřednictvím přiřazení vlastností:</span><span class="sxs-lookup"><span data-stu-id="467f3-876">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="467f3-877">Výsledkem dvou následujících výstupů jsou výsledky dvou požadavků:</span><span class="sxs-lookup"><span data-stu-id="467f3-877">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="467f3-878">**První požadavek:**</span><span class="sxs-lookup"><span data-stu-id="467f3-878">**First request:**</span></span>

<span data-ttu-id="467f3-879">Operace kontroleru:</span><span class="sxs-lookup"><span data-stu-id="467f3-879">Controller operations:</span></span>

<span data-ttu-id="467f3-880">Přechodný: d233e165-f417-469b-A866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="467f3-880">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="467f3-881">Vymezené: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="467f3-881">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="467f3-882">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="467f3-882">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="467f3-883">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="467f3-883">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="467f3-884">`OperationService`Operations</span><span class="sxs-lookup"><span data-stu-id="467f3-884">`OperationService` operations:</span></span>

<span data-ttu-id="467f3-885">Přechodný: c6b049eb-1318-4E31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="467f3-885">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="467f3-886">Vymezené: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="467f3-886">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="467f3-887">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="467f3-887">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="467f3-888">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="467f3-888">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="467f3-889">**Druhá žádost:**</span><span class="sxs-lookup"><span data-stu-id="467f3-889">**Second request:**</span></span>

<span data-ttu-id="467f3-890">Operace kontroleru:</span><span class="sxs-lookup"><span data-stu-id="467f3-890">Controller operations:</span></span>

<span data-ttu-id="467f3-891">Přechodný: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="467f3-891">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="467f3-892">Vymezené: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="467f3-892">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="467f3-893">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="467f3-893">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="467f3-894">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="467f3-894">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="467f3-895">`OperationService`Operations</span><span class="sxs-lookup"><span data-stu-id="467f3-895">`OperationService` operations:</span></span>

<span data-ttu-id="467f3-896">Přechodný: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="467f3-896">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="467f3-897">Vymezené: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="467f3-897">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="467f3-898">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="467f3-898">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="467f3-899">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="467f3-899">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="467f3-900">Pozor, které `OperationId` hodnoty se liší v rámci požadavku a mezi požadavky:</span><span class="sxs-lookup"><span data-stu-id="467f3-900">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="467f3-901">*Přechodné* objekty jsou vždy odlišné.</span><span class="sxs-lookup"><span data-stu-id="467f3-901">*Transient* objects are always different.</span></span> <span data-ttu-id="467f3-902">Přechodná `OperationId` hodnota pro první i druhý požadavek klienta se liší pro obě `OperationService` operace i mezi požadavky klientů.</span><span class="sxs-lookup"><span data-stu-id="467f3-902">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="467f3-903">Každé žádosti o službu a žádosti klienta se poskytne nová instance.</span><span class="sxs-lookup"><span data-stu-id="467f3-903">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="467f3-904">*Vymezené* objekty jsou v požadavku klienta stejné, ale v rámci požadavků klientů se liší.</span><span class="sxs-lookup"><span data-stu-id="467f3-904">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="467f3-905">Objekty *singleton* jsou stejné pro každý objekt a každý požadavek bez ohledu na to, zda `Operation` je instance uvedena v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="467f3-905">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="467f3-906">Volat služby z Main</span><span class="sxs-lookup"><span data-stu-id="467f3-906">Call services from main</span></span>

<span data-ttu-id="467f3-907">Vytvořte <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> pomocí [IServiceScopeFactory. CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) pro řešení oboru služby v rámci rozsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="467f3-907">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="467f3-908">Tento přístup je užitečný pro přístup k oboruované službě při spuštění za účelem spouštění úloh inicializace.</span><span class="sxs-lookup"><span data-stu-id="467f3-908">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="467f3-909">Následující příklad ukazuje, jak získat kontext pro `MyScopedService` `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="467f3-909">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="467f3-910">Ověřování oboru</span><span class="sxs-lookup"><span data-stu-id="467f3-910">Scope validation</span></span>

<span data-ttu-id="467f3-911">Když je aplikace spuštěná ve vývojovém prostředí, provede výchozí poskytovatel služeb kontroly, aby ověřil, že:</span><span class="sxs-lookup"><span data-stu-id="467f3-911">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="467f3-912">Oborové služby nejsou přímo nebo nepřímo vyřešeny od poskytovatele kořenové služby.</span><span class="sxs-lookup"><span data-stu-id="467f3-912">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="467f3-913">Oborové služby nejsou přímo nebo nepřímo vložené do singleton.</span><span class="sxs-lookup"><span data-stu-id="467f3-913">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="467f3-914">Poskytovatel kořenové služby se vytvoří, když <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> se zavolá.</span><span class="sxs-lookup"><span data-stu-id="467f3-914">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="467f3-915">Doba života poskytovatele kořenové služby odpovídá době životnosti aplikace nebo serveru, když se poskytovatel spustí s aplikací a je uvolněný při ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="467f3-915">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="467f3-916">Oborové služby jsou uvolněny kontejnerem, který je vytvořil.</span><span class="sxs-lookup"><span data-stu-id="467f3-916">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="467f3-917">Pokud je v kořenovém kontejneru vytvořena vymezená služba, doba života služby je efektivně povýšena na typ singleton, protože je uvolněna pouze kořenovým kontejnerem při vypnutí aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="467f3-917">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="467f3-918">Při ověřování oborů služeb se tyto situace zachytí `BuildServiceProvider` .</span><span class="sxs-lookup"><span data-stu-id="467f3-918">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="467f3-919">Další informace naleznete v tématu <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="467f3-919">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="467f3-920">Žádosti o služby</span><span class="sxs-lookup"><span data-stu-id="467f3-920">Request Services</span></span>

<span data-ttu-id="467f3-921">Služby, které jsou k dispozici v rámci žádosti o ASP.NET Core, `HttpContext` jsou zpřístupněny prostřednictvím kolekce [HttpContext. RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) .</span><span class="sxs-lookup"><span data-stu-id="467f3-921">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="467f3-922">Žádosti o služby reprezentují služby nakonfigurované a požadované v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="467f3-922">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="467f3-923">Když objekty určují závislosti, jsou splněny typy nalezenými v `RequestServices` , ne `ApplicationServices` .</span><span class="sxs-lookup"><span data-stu-id="467f3-923">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="467f3-924">Obecně platí, že aplikace by tyto vlastnosti neměla používat přímo.</span><span class="sxs-lookup"><span data-stu-id="467f3-924">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="467f3-925">Místo toho požádejte o typy, které třídy vyžadují prostřednictvím konstruktorů třídy, a umožněte rozhraní vložit závislosti.</span><span class="sxs-lookup"><span data-stu-id="467f3-925">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="467f3-926">To poskytuje třídy, které jsou snáze testovány.</span><span class="sxs-lookup"><span data-stu-id="467f3-926">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="467f3-927">Preferovat požadavky na závislosti jako parametry konstruktoru pro přístup ke `RequestServices` kolekci.</span><span class="sxs-lookup"><span data-stu-id="467f3-927">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="467f3-928">Navrhnout služby pro vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="467f3-928">Design services for dependency injection</span></span>

<span data-ttu-id="467f3-929">Osvědčené postupy:</span><span class="sxs-lookup"><span data-stu-id="467f3-929">Best practices are to:</span></span>

* <span data-ttu-id="467f3-930">Navrhněte služby pro použití injektáže závislosti k získání jejich závislostí.</span><span class="sxs-lookup"><span data-stu-id="467f3-930">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="467f3-931">Vyhněte se stavovým, statickým třídám a členům.</span><span class="sxs-lookup"><span data-stu-id="467f3-931">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="467f3-932">Navrhněte aplikace, aby místo toho používaly služby s jedním prvkem, což se vyhne vytváření globálního stavu.</span><span class="sxs-lookup"><span data-stu-id="467f3-932">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="467f3-933">Vyhněte se přímému vytváření instancí závislých tříd v rámci služeb.</span><span class="sxs-lookup"><span data-stu-id="467f3-933">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="467f3-934">Přímá instance Couples kód na konkrétní implementaci.</span><span class="sxs-lookup"><span data-stu-id="467f3-934">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="467f3-935">Vytvářejte třídy aplikací malými, dobře faktoringně a snadno testované.</span><span class="sxs-lookup"><span data-stu-id="467f3-935">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="467f3-936">Pokud se zdá, že třída má příliš mnoho vložených závislostí, obvykle se jedná o znaménko, že třída má příliš mnoho zodpovědnosti a je v rozporu s [principem jediné zodpovědnosti (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="467f3-936">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="467f3-937">Pokuste se Refaktorovat třídu přesunutím některých odpovědností do nové třídy.</span><span class="sxs-lookup"><span data-stu-id="467f3-937">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="467f3-938">Mějte na paměti, že Razor stránky tříd modelu stránky a třídy KONTROLERU MVC by se měly zaměřit na uživatelské rozhraní.</span><span class="sxs-lookup"><span data-stu-id="467f3-938">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="467f3-939">Obchodní pravidla a podrobnosti implementace přístupu k datům by měly být uchovávány ve třídách, které jsou vhodné pro tyto [samostatné obavy](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="467f3-939">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="467f3-940">Vyřazení služeb</span><span class="sxs-lookup"><span data-stu-id="467f3-940">Disposal of services</span></span>

<span data-ttu-id="467f3-941">Kontejner volá <xref:System.IDisposable.Dispose*> typy, které <xref:System.IDisposable> vytvoří.</span><span class="sxs-lookup"><span data-stu-id="467f3-941">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="467f3-942">Pokud je instance přidána do kontejneru pomocí uživatelského kódu, nebude uvolněna automaticky.</span><span class="sxs-lookup"><span data-stu-id="467f3-942">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="467f3-943">V následujícím příkladu jsou služby vytvořeny pomocí kontejneru služby a automaticky odstraněny:</span><span class="sxs-lookup"><span data-stu-id="467f3-943">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="467f3-944">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="467f3-944">In the following example:</span></span>

* <span data-ttu-id="467f3-945">Instance služby nejsou vytvořeny kontejnerem služby.</span><span class="sxs-lookup"><span data-stu-id="467f3-945">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="467f3-946">Zamýšlené životnosti služeb nejsou rozhraním známy.</span><span class="sxs-lookup"><span data-stu-id="467f3-946">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="467f3-947">Rozhraní neodstraní služby automaticky.</span><span class="sxs-lookup"><span data-stu-id="467f3-947">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="467f3-948">Pokud se služby explicitně neodstraní v kódu pro vývojáře, zůstanou zachovány, dokud se aplikace neukončí.</span><span class="sxs-lookup"><span data-stu-id="467f3-948">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

## <a name="default-service-container-replacement"></a><span data-ttu-id="467f3-949">Výchozí nahrazení kontejneru služby</span><span class="sxs-lookup"><span data-stu-id="467f3-949">Default service container replacement</span></span>

<span data-ttu-id="467f3-950">Integrovaný kontejner služeb je navržený tak, aby sloužil potřebám architektury a většině spotřebitelských aplikací.</span><span class="sxs-lookup"><span data-stu-id="467f3-950">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="467f3-951">Pokud nepotřebujete určitou funkci, kterou integrovaný kontejner nepodporuje, doporučujeme použít vestavěný kontejner, například:</span><span class="sxs-lookup"><span data-stu-id="467f3-951">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="467f3-952">Vkládání vlastností</span><span class="sxs-lookup"><span data-stu-id="467f3-952">Property injection</span></span>
* <span data-ttu-id="467f3-953">Vkládání na základě názvu</span><span class="sxs-lookup"><span data-stu-id="467f3-953">Injection based on name</span></span>
* <span data-ttu-id="467f3-954">Podřízené kontejnery</span><span class="sxs-lookup"><span data-stu-id="467f3-954">Child containers</span></span>
* <span data-ttu-id="467f3-955">Vlastní Správa životního cyklu</span><span class="sxs-lookup"><span data-stu-id="467f3-955">Custom lifetime management</span></span>
* <span data-ttu-id="467f3-956">`Func<T>`Podpora opožděné inicializace</span><span class="sxs-lookup"><span data-stu-id="467f3-956">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="467f3-957">Registrace založená na konvencích</span><span class="sxs-lookup"><span data-stu-id="467f3-957">Convention-based registration</span></span>

<span data-ttu-id="467f3-958">S ASP.NET Coremi aplikacemi je možné používat následující kontejnery třetích stran:</span><span class="sxs-lookup"><span data-stu-id="467f3-958">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="467f3-959">Autofac</span><span class="sxs-lookup"><span data-stu-id="467f3-959">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="467f3-960">DryIoc</span><span class="sxs-lookup"><span data-stu-id="467f3-960">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="467f3-961">Integrit</span><span class="sxs-lookup"><span data-stu-id="467f3-961">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="467f3-962">LightInject</span><span class="sxs-lookup"><span data-stu-id="467f3-962">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="467f3-963">Lamar</span><span class="sxs-lookup"><span data-stu-id="467f3-963">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="467f3-964">Stashbox</span><span class="sxs-lookup"><span data-stu-id="467f3-964">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="467f3-965">Unity</span><span class="sxs-lookup"><span data-stu-id="467f3-965">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="467f3-966">Bezpečnost vlákna</span><span class="sxs-lookup"><span data-stu-id="467f3-966">Thread safety</span></span>

<span data-ttu-id="467f3-967">Vytvořte služby, které jsou v bezpečí pro přístup z více vláken.</span><span class="sxs-lookup"><span data-stu-id="467f3-967">Create thread-safe singleton services.</span></span> <span data-ttu-id="467f3-968">Pokud má služba typu Singleton závislost na přechodné službě, přechodná služba může také vyžadovat zabezpečení vlákna v závislosti na tom, jak je používá typ singleton.</span><span class="sxs-lookup"><span data-stu-id="467f3-968">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="467f3-969">Výrobní metoda samostatné služby, jako je například druhý argument pro [AddSingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), nemusí být bezpečná pro přístup z více vláken.</span><span class="sxs-lookup"><span data-stu-id="467f3-969">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="467f3-970">Podobně jako konstruktor typu ( `static` ), je zaručeno, že bude volán jednou vláknem.</span><span class="sxs-lookup"><span data-stu-id="467f3-970">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="467f3-971">Doporučení</span><span class="sxs-lookup"><span data-stu-id="467f3-971">Recommendations</span></span>

* <span data-ttu-id="467f3-972">`async/await``Task`řešení služeb a se nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="467f3-972">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="467f3-973">Jazyk C# nepodporuje asynchronní konstruktory; Proto je doporučeným vzorem použití asynchronních metod po synchronním překladu služby.</span><span class="sxs-lookup"><span data-stu-id="467f3-973">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="467f3-974">Neukládejte data a konfiguraci přímo do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="467f3-974">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="467f3-975">Například nákupní košík uživatele by neměl být obvykle přidán do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="467f3-975">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="467f3-976">Konfigurace by měla používat [vzor možností](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="467f3-976">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="467f3-977">Podobně nepoužívejte objekty "držitel dat", které existují pouze k povolení přístupu k jinému objektu.</span><span class="sxs-lookup"><span data-stu-id="467f3-977">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="467f3-978">Je lepší žádat o skutečnou položku přes DI.</span><span class="sxs-lookup"><span data-stu-id="467f3-978">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="467f3-979">Vyhněte se statickému přístupu ke službám (například statickému psaní [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) pro použití jinde).</span><span class="sxs-lookup"><span data-stu-id="467f3-979">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="467f3-980">Vyhněte se použití *vzoru lokátoru služby*, který kombinuje [inverzi strategií řízení](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .</span><span class="sxs-lookup"><span data-stu-id="467f3-980">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

  * <span data-ttu-id="467f3-981">Nevolejte <xref:System.IServiceProvider.GetService*> pro získání instance služby, když můžete místo toho použít di:</span><span class="sxs-lookup"><span data-stu-id="467f3-981">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="467f3-982">**Špatný**</span><span class="sxs-lookup"><span data-stu-id="467f3-982">**Incorrect:**</span></span>

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

    <span data-ttu-id="467f3-983">**Správné**:</span><span class="sxs-lookup"><span data-stu-id="467f3-983">**Correct**:</span></span>

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

  * <span data-ttu-id="467f3-984">Vyhněte se vkládání továrny, která řeší závislosti za běhu pomocí <xref:System.IServiceProvider.GetService*> .</span><span class="sxs-lookup"><span data-stu-id="467f3-984">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>

* <span data-ttu-id="467f3-985">Vyhněte se statickému přístupu k `HttpContext` (například [IHttpContextAccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="467f3-985">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="467f3-986">Podobně jako u všech sad doporučení se může stát, že se vyžaduje ignorování doporučení.</span><span class="sxs-lookup"><span data-stu-id="467f3-986">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="467f3-987">Výjimky jsou zřídka &mdash; většinou zvláštní případy v rámci samotného rozhraní.</span><span class="sxs-lookup"><span data-stu-id="467f3-987">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="467f3-988">DI je *alternativou* ke vzorům statických nebo globálních přístupů k objektům.</span><span class="sxs-lookup"><span data-stu-id="467f3-988">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="467f3-989">Je možné, že nebudete moci využít výhody DI, pokud je kombinujete se statickým přístupem k objektům.</span><span class="sxs-lookup"><span data-stu-id="467f3-989">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="467f3-990">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="467f3-990">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="467f3-991">Zápis čistého kódu v ASP.NET Core pomocí injektáže závislosti (MSDN)</span><span class="sxs-lookup"><span data-stu-id="467f3-991">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="467f3-992">Explicitní závislosti – princip</span><span class="sxs-lookup"><span data-stu-id="467f3-992">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="467f3-993">Inverze řídicích kontejnerů a vzor injektáže závislosti (Martin Fowlera)</span><span class="sxs-lookup"><span data-stu-id="467f3-993">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="467f3-994">Jak zaregistrovat službu s více rozhraními v ASP.NET Core DI</span><span class="sxs-lookup"><span data-stu-id="467f3-994">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

---
title: Vkládání závislostí v ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak ASP.NET Core implementuje vkládání závislostí a jak ho používat.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/21/2020
no-loc:
- ASP.NET Core Identity
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/dependency-injection
ms.openlocfilehash: 2d002e075f9d57654589b540e522307c363d9660
ms.sourcegitcommit: 4cce99cbd44372fd4575e8da8c0f4345949f4d9a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/31/2020
ms.locfileid: "89153542"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="f51b0-103">Vkládání závislostí v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f51b0-103">Dependency injection in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f51b0-104">Od [Kirka Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com)a [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="f51b0-104">By [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="f51b0-105">ASP.NET Core podporuje vzor návrhu softwaru pro vkládání závislostí (DI), což je technika pro dosažení [inverze ovládacího prvku (IOC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) mezi třídami a jejich závislostmi.</span><span class="sxs-lookup"><span data-stu-id="f51b0-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="f51b0-106">Další informace, které jsou specifické pro vkládání závislostí v rámci řadičů MVC, najdete v tématu <xref:mvc/controllers/dependency-injection> .</span><span class="sxs-lookup"><span data-stu-id="f51b0-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="f51b0-107">Další informace o vkládání závislostí z možností naleznete v tématu <xref:fundamentals/configuration/options> .</span><span class="sxs-lookup"><span data-stu-id="f51b0-107">For more information on dependency injection of options, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="f51b0-108">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f51b0-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="f51b0-109">Přehled injektáže závislosti</span><span class="sxs-lookup"><span data-stu-id="f51b0-109">Overview of dependency injection</span></span>

<span data-ttu-id="f51b0-110">*Závislost* je objekt, na kterém je závislý jiný objekt.</span><span class="sxs-lookup"><span data-stu-id="f51b0-110">A *dependency* is an object that another object depends on.</span></span> <span data-ttu-id="f51b0-111">Projděte si následující `MyDependency` třídu s `WriteMessage` metodou, na které jsou závislé jiné třídy:</span><span class="sxs-lookup"><span data-stu-id="f51b0-111">Examine the following `MyDependency` class with a `WriteMessage` method that other classes depend on:</span></span>

```csharp
public class MyDependency
{
    public void WriteMessage(string message)
    {
        Console.WriteLine($"MyDependency.WriteMessage called. Message: {message}");
    }
}
```

<span data-ttu-id="f51b0-112">Třída může vytvořit instanci `MyDependency` třídy, aby bylo možné využít její `WriteMessage` metodu.</span><span class="sxs-lookup"><span data-stu-id="f51b0-112">A class can create an instance of the `MyDependency` class to make use of its `WriteMessage` method.</span></span> <span data-ttu-id="f51b0-113">V následujícím příkladu `MyDependency` je třída závislostí `IndexModel` třídy:</span><span class="sxs-lookup"><span data-stu-id="f51b0-113">In the following example, the `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

```csharp
public class IndexModel : PageModel
{
    private readonly MyDependency _dependency = new MyDependency();

    public void OnGet()
    {
        _dependency.WriteMessage("IndexModel.OnGet created this message.");
    }
}
```

<span data-ttu-id="f51b0-114">Třída vytvoří a přímo závisí na `MyDependency` třídě.</span><span class="sxs-lookup"><span data-stu-id="f51b0-114">The class creates and directly depends on the `MyDependency` class.</span></span> <span data-ttu-id="f51b0-115">Závislosti kódu, jako v předchozím příkladu, jsou problematické a je třeba se jim vyhnout z následujících důvodů:</span><span class="sxs-lookup"><span data-stu-id="f51b0-115">Code dependencies, such as in the previous example, are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="f51b0-116">Chcete-li nahradit `MyDependency` jinou implementací, `IndexModel` Třída musí být upravena.</span><span class="sxs-lookup"><span data-stu-id="f51b0-116">To replace `MyDependency` with a different implementation, the `IndexModel` class must be modified.</span></span>
* <span data-ttu-id="f51b0-117">Pokud `MyDependency` má závislosti, musí být také nakonfigurovány `IndexModel` třídou.</span><span class="sxs-lookup"><span data-stu-id="f51b0-117">If `MyDependency` has dependencies, they must also be configured by the `IndexModel` class.</span></span> <span data-ttu-id="f51b0-118">Ve velkém projektu s více třídami, v závislosti na tom `MyDependency` , je kód konfigurace rozptýlen napříč aplikací.</span><span class="sxs-lookup"><span data-stu-id="f51b0-118">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="f51b0-119">Tato implementace je obtížná pro testování částí.</span><span class="sxs-lookup"><span data-stu-id="f51b0-119">This implementation is difficult to unit test.</span></span> <span data-ttu-id="f51b0-120">Aplikace by měla používat třídu typu "nebo zástupné procedury" `MyDependency` , což není u tohoto přístupu možné.</span><span class="sxs-lookup"><span data-stu-id="f51b0-120">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="f51b0-121">Vkládání závislostí řeší tyto problémy prostřednictvím:</span><span class="sxs-lookup"><span data-stu-id="f51b0-121">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="f51b0-122">Použití rozhraní nebo základní třídy k abstrakci implementace závislosti.</span><span class="sxs-lookup"><span data-stu-id="f51b0-122">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="f51b0-123">Registrace závislosti v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="f51b0-123">Registration of the dependency in a service container.</span></span> <span data-ttu-id="f51b0-124">ASP.NET Core poskytuje integrovaný kontejner služeb <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="f51b0-124">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="f51b0-125">Služby jsou obvykle registrovány v metodě aplikace `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="f51b0-125">Services are typically registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="f51b0-126">*Vložení* služby do konstruktoru třídy, kde se používá.</span><span class="sxs-lookup"><span data-stu-id="f51b0-126">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="f51b0-127">Rozhraní přebírá zodpovědnost za vytvoření instance závislosti a její odstranění, když už ji nepotřebujete.</span><span class="sxs-lookup"><span data-stu-id="f51b0-127">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="f51b0-128">V [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) `IMyDependency` definuje rozhraní `WriteMessage` metodu:</span><span class="sxs-lookup"><span data-stu-id="f51b0-128">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="f51b0-129">Toto rozhraní je implementováno konkrétním typem `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="f51b0-129">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="f51b0-130">Ukázková aplikace registruje `IMyDependency` službu pomocí konkrétního typu `MyDependency` .</span><span class="sxs-lookup"><span data-stu-id="f51b0-130">The sample app registers the `IMyDependency` service with the concrete type `MyDependency`.</span></span> <span data-ttu-id="f51b0-131"><xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A>Metoda registruje službu s rozsahem životnosti, životností jediného požadavku.</span><span class="sxs-lookup"><span data-stu-id="f51b0-131">The <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> method registers the service with a scoped lifetime, the lifetime of a single request.</span></span> <span data-ttu-id="f51b0-132">[Doby platnosti služeb](#service-lifetimes) jsou popsány dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="f51b0-132">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency.cs?name=snippet1)]

<span data-ttu-id="f51b0-133">V ukázkové aplikaci `IMyDependency` se vyžádá služba a použije se k volání `WriteMessage` metody:</span><span class="sxs-lookup"><span data-stu-id="f51b0-133">In the sample app, the `IMyDependency` service is requested and used to call the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index2.cshtml.cs?name=snippet1)]

<span data-ttu-id="f51b0-134">Pomocí vzoru DI se kontroler:</span><span class="sxs-lookup"><span data-stu-id="f51b0-134">By using the DI pattern, the controller:</span></span>

* <span data-ttu-id="f51b0-135">Nepoužívá konkrétní typ `MyDependency` , pouze rozhraní, které `IMyDependency` implementuje.</span><span class="sxs-lookup"><span data-stu-id="f51b0-135">Doesn't use the concrete type `MyDependency`, only the `IMyDependency` interface it implements.</span></span> <span data-ttu-id="f51b0-136">Díky tomu je možné snadno změnit implementaci, kterou kontroler používá bez změny kontroleru.</span><span class="sxs-lookup"><span data-stu-id="f51b0-136">That makes it easy to change the implementation that the controller uses without modifying the controller.</span></span>
* <span data-ttu-id="f51b0-137">Nevytvoří instanci, která je `MyDependency` vytvořena kontejnerem di.</span><span class="sxs-lookup"><span data-stu-id="f51b0-137">Doesn't create an instance of `MyDependency`, it's created by the DI container.</span></span>

<span data-ttu-id="f51b0-138">Implementaci `IMyDependency` rozhraní lze zlepšit pomocí integrovaného protokolovacího rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="f51b0-138">The implementation of the `IMyDependency` interface can be improved by using the built-in logging API:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet2)]

<span data-ttu-id="f51b0-139">Aktualizovaná `ConfigureServices` Metoda registruje novou `IMyDependency` implementaci:</span><span class="sxs-lookup"><span data-stu-id="f51b0-139">The updated `ConfigureServices` method registers the new `IMyDependency` implementation:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency2.cs?name=snippet1)]

<span data-ttu-id="f51b0-140">`MyDependency2` závisí na <xref:Microsoft.Extensions.Logging.ILogger%601> tom, které požadavky v konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="f51b0-140">`MyDependency2` depends on <xref:Microsoft.Extensions.Logging.ILogger%601>, which it requests in the constructor.</span></span> <span data-ttu-id="f51b0-141">`ILogger<TCategoryName>` je [služba poskytovaná rozhraním](#framework-provided-services).</span><span class="sxs-lookup"><span data-stu-id="f51b0-141">`ILogger<TCategoryName>` is a [framework-provided service](#framework-provided-services).</span></span>

<span data-ttu-id="f51b0-142">Není neobvyklé používat vkládání závislostí v zřetězeném způsobem.</span><span class="sxs-lookup"><span data-stu-id="f51b0-142">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="f51b0-143">Každá požadovaná závislost zase vyžádá své vlastní závislosti.</span><span class="sxs-lookup"><span data-stu-id="f51b0-143">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="f51b0-144">Kontejner vyřeší závislosti v grafu a vrátí plně přeloženou službu.</span><span class="sxs-lookup"><span data-stu-id="f51b0-144">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="f51b0-145">Kolektivní sada závislostí, které je třeba vyřešit, se obvykle označuje jako *strom závislosti*, *graf závislosti*nebo *graf objektů*.</span><span class="sxs-lookup"><span data-stu-id="f51b0-145">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="f51b0-146">Kontejner se překládá `ILogger<TCategoryName>` tím, že využije [(Obecné) otevřené typy](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)a eliminuje nutnost registrovat každý [(obecný) konstruovaný typ](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span><span class="sxs-lookup"><span data-stu-id="f51b0-146">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span></span>

<span data-ttu-id="f51b0-147">V terminologii injektáže závislostí služba:</span><span class="sxs-lookup"><span data-stu-id="f51b0-147">In dependency injection terminology, a service:</span></span>

* <span data-ttu-id="f51b0-148">Je obvykle objekt, který poskytuje službu jiným objektům, jako je například `IMyDependency` služba.</span><span class="sxs-lookup"><span data-stu-id="f51b0-148">Is typically an object that provides a service to other objects, such as the `IMyDependency` service.</span></span>
* <span data-ttu-id="f51b0-149">Nesouvisí s webovou službou, i když služba může používat webovou službu.</span><span class="sxs-lookup"><span data-stu-id="f51b0-149">Is not related to a web service, although the service may use a web service.</span></span>

<span data-ttu-id="f51b0-150">Rozhraní poskytuje robustní systém [protokolování](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="f51b0-150">The framework provides a robust [logging](xref:fundamentals/logging/index) system.</span></span> <span data-ttu-id="f51b0-151">`IMyDependency`Implementace uvedené v předchozích příkladech byly zapsány k demonstraci základního di, nikoli k implementaci protokolování.</span><span class="sxs-lookup"><span data-stu-id="f51b0-151">The `IMyDependency` implementations shown in the preceding examples were written to demonstrate basic DI, not to implement logging.</span></span> <span data-ttu-id="f51b0-152">Většina aplikací by neměla potřebovat zapisovat protokolovací nástroje.</span><span class="sxs-lookup"><span data-stu-id="f51b0-152">Most apps shouldn't need to write loggers.</span></span> <span data-ttu-id="f51b0-153">Následující kód ukazuje použití výchozího protokolování, které nevyžaduje registraci žádné služby v nástroji `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f51b0-153">The following code demonstrates using the default logging, which doesn't require any services to be registered in `ConfigureServices`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="f51b0-154">Pomocí předchozího kódu není nutné aktualizovat `ConfigureServices` , protože [protokolování](xref:fundamentals/logging/index) je poskytováno rozhraním.</span><span class="sxs-lookup"><span data-stu-id="f51b0-154">Using the preceding code, there is no need to update `ConfigureServices`, because [logging](xref:fundamentals/logging/index) is provided by the framework.</span></span>

## <a name="services-injected-into-startup"></a><span data-ttu-id="f51b0-155">Služby vložené do spuštění</span><span class="sxs-lookup"><span data-stu-id="f51b0-155">Services injected into Startup</span></span>

<span data-ttu-id="f51b0-156">Služby lze vložit do `Startup` konstruktoru a do `Startup.Configure` metody.</span><span class="sxs-lookup"><span data-stu-id="f51b0-156">Services can be injected into the `Startup` constructor and the `Startup.Configure` method.</span></span>

<span data-ttu-id="f51b0-157">`Startup`Při použití obecného hostitele () mohou být do konstruktoru vloženy pouze následující služby <xref:Microsoft.Extensions.Hosting.IHostBuilder> :</span><span class="sxs-lookup"><span data-stu-id="f51b0-157">Only the following services can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="f51b0-158">Kterákoli služba zaregistrovaná v kontejneru DI může být vložená do `Startup.Configure` metody:</span><span class="sxs-lookup"><span data-stu-id="f51b0-158">Any service registered with the DI container can be injected into the `Startup.Configure` method:</span></span>

```csharp
public void Configure(IApplicationBuilder app, ILogger<Startup> logger)
{
    ...
}
```

<span data-ttu-id="f51b0-159">Další informace najdete v tématu <xref:fundamentals/startup> a [Konfigurace přístupu při spuštění](xref:fundamentals/configuration/index#access-configuration-in-startup).</span><span class="sxs-lookup"><span data-stu-id="f51b0-159">For more information, see <xref:fundamentals/startup> and [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup).</span></span>

## <a name="register-groups-of-services-with-extension-methods"></a><span data-ttu-id="f51b0-160">Registrace skupin služeb s metodami rozšíření</span><span class="sxs-lookup"><span data-stu-id="f51b0-160">Register groups of services with extension methods</span></span>

<span data-ttu-id="f51b0-161">Rozhraní ASP.NET Core Framework používá konvenci pro registraci skupiny souvisejících služeb.</span><span class="sxs-lookup"><span data-stu-id="f51b0-161">The ASP.NET Core framework uses a convention for registering a group of related services.</span></span> <span data-ttu-id="f51b0-162">Konvence je určena k `Add{GROUP_NAME}` registraci všech služeb vyžadovaných funkcí rozhraní pomocí jediné metody rozšíření.</span><span class="sxs-lookup"><span data-stu-id="f51b0-162">The convention is to use a single `Add{GROUP_NAME}` extension method to register all of the services required by a framework feature.</span></span> <span data-ttu-id="f51b0-163">Například <Microsoft. Extensions. DependencyInjection. MvcServiceCollectionExtensions. AddControllers> rozšiřující metoda registruje služby požadované pro řadiče MVC.</span><span class="sxs-lookup"><span data-stu-id="f51b0-163">For example, the <Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers> extension method registers the services required for MVC controllers.</span></span>

<span data-ttu-id="f51b0-164">Následující kód je vygenerován Razor šablonou stránky pomocí individuálních uživatelských účtů a ukazuje, jak přidat další služby do kontejneru pomocí metod rozšíření <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> a <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A> :</span><span class="sxs-lookup"><span data-stu-id="f51b0-164">The following code is generated by the Razor Pages template using individual user accounts and shows how to add additional services to the container using the extension methods <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A>:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupEF.cs?name=snippet)]

[!INCLUDE[](~/includes/combine-di.md)]

## <a name="service-lifetimes"></a><span data-ttu-id="f51b0-165">Životnost služeb</span><span class="sxs-lookup"><span data-stu-id="f51b0-165">Service lifetimes</span></span>

<span data-ttu-id="f51b0-166">Služby je možné zaregistrovat s jednou z následujících dob života:</span><span class="sxs-lookup"><span data-stu-id="f51b0-166">Services can be registered with one of the following lifetimes:</span></span>

* <span data-ttu-id="f51b0-167">Dočasný</span><span class="sxs-lookup"><span data-stu-id="f51b0-167">Transient</span></span>
* <span data-ttu-id="f51b0-168">Obor</span><span class="sxs-lookup"><span data-stu-id="f51b0-168">Scoped</span></span>
* <span data-ttu-id="f51b0-169">Singleton</span><span class="sxs-lookup"><span data-stu-id="f51b0-169">Singleton</span></span>

<span data-ttu-id="f51b0-170">V následujících částech jsou popsány všechny předchozí životnosti.</span><span class="sxs-lookup"><span data-stu-id="f51b0-170">The following sections describe each of the preceding lifetimes.</span></span> <span data-ttu-id="f51b0-171">Vyberte odpovídající dobu života pro každou registrovanou službu.</span><span class="sxs-lookup"><span data-stu-id="f51b0-171">Choose an appropriate lifetime for each registered service.</span></span> 

### <a name="transient"></a><span data-ttu-id="f51b0-172">Dočasný</span><span class="sxs-lookup"><span data-stu-id="f51b0-172">Transient</span></span>

<span data-ttu-id="f51b0-173">Dočasné služby životnosti se vytváří pokaždé, když jsou požadovány z kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="f51b0-173">Transient lifetime services are created each time they're requested from the service container.</span></span> <span data-ttu-id="f51b0-174">Tato životnost funguje nejlépe pro odlehčené bezstavové služby.</span><span class="sxs-lookup"><span data-stu-id="f51b0-174">This lifetime works best for lightweight, stateless services.</span></span> <span data-ttu-id="f51b0-175">Zaregistrujte přechodné služby pomocí <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient%2A> .</span><span class="sxs-lookup"><span data-stu-id="f51b0-175">Register transient services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient%2A>.</span></span>

<span data-ttu-id="f51b0-176">V aplikacích, které zpracovávají požadavky, jsou přechodné služby na konci žádosti zrušené.</span><span class="sxs-lookup"><span data-stu-id="f51b0-176">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="f51b0-177">Obor</span><span class="sxs-lookup"><span data-stu-id="f51b0-177">Scoped</span></span>

<span data-ttu-id="f51b0-178">Služby životnosti v oboru jsou vytvářeny jednou za požadavek klienta (připojení).</span><span class="sxs-lookup"><span data-stu-id="f51b0-178">Scoped lifetime services are created once per client request (connection).</span></span> <span data-ttu-id="f51b0-179">Zaregistrujte obor služeb pomocí <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> .</span><span class="sxs-lookup"><span data-stu-id="f51b0-179">Register scoped services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A>.</span></span>

<span data-ttu-id="f51b0-180">V aplikacích, které zpracovávají požadavky, se vymezené služby na konci žádosti odstraní.</span><span class="sxs-lookup"><span data-stu-id="f51b0-180">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

<span data-ttu-id="f51b0-181">Při použití Entity Framework Core <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> metoda rozšíření registruje `DbContext` typy s vymezenou životností ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="f51b0-181">When using Entity Framework Core, the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension method registers `DbContext` types with a scoped lifetime by default.</span></span>

<span data-ttu-id="f51b0-182">Neprovádějte ***Překlad oboru*** služby z typu singleton.</span><span class="sxs-lookup"><span data-stu-id="f51b0-182">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="f51b0-183">Může dojít k tomu, že služba má při zpracování dalších požadavků špatný stav.</span><span class="sxs-lookup"><span data-stu-id="f51b0-183">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="f51b0-184">Je to v pořádku:</span><span class="sxs-lookup"><span data-stu-id="f51b0-184">It's fine to:</span></span>

* <span data-ttu-id="f51b0-185">Vyřešte službu typu Singleton z oboru nebo přechodné služby.</span><span class="sxs-lookup"><span data-stu-id="f51b0-185">Resolve a singleton service from a scoped or transient service.</span></span>
* <span data-ttu-id="f51b0-186">Vyřešte oborovou službu z jiné vymezené nebo přechodné služby.</span><span class="sxs-lookup"><span data-stu-id="f51b0-186">Resolve a scoped service from another scoped or transient service.</span></span>

<span data-ttu-id="f51b0-187">Ve výchozím nastavení ve vývojovém prostředí vyřeší služba z jiné služby s delší životností výjimku.</span><span class="sxs-lookup"><span data-stu-id="f51b0-187">By default, in the development environment, resolving a service from another service with a longer lifetime throws an exception.</span></span> <span data-ttu-id="f51b0-188">Další informace najdete v tématu [ověřování oboru](#sv).</span><span class="sxs-lookup"><span data-stu-id="f51b0-188">For more information, see [Scope validation](#sv).</span></span>

<span data-ttu-id="f51b0-189">Pokud chcete používat vymezené služby v middlewaru, použijte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="f51b0-189">To use scoped services in middleware, use one of the following approaches:</span></span>

* <span data-ttu-id="f51b0-190">Službu zahájíte do služby nebo do metody middlewaru `Invoke` `InvokeAsync` .</span><span class="sxs-lookup"><span data-stu-id="f51b0-190">Inject the service into the middleware's `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="f51b0-191">Použití [Injektáže konstruktoru](xref:mvc/controllers/dependency-injection#constructor-injection) vyvolá výjimku za běhu, protože vynutí, aby se služba s oborem chovala jako typ singleton.</span><span class="sxs-lookup"><span data-stu-id="f51b0-191">Using [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) throws a runtime exception because it forces the scoped service to behave like a singleton.</span></span> <span data-ttu-id="f51b0-192">Ukázka v části [Možnosti životního cyklu a registrace](#lifetime-and-registration-options) demonstruje `InvokeAsync` přístup.</span><span class="sxs-lookup"><span data-stu-id="f51b0-192">The sample in the [Lifetime and registration options](#lifetime-and-registration-options) section demonstrates the `InvokeAsync` approach.</span></span>
* <span data-ttu-id="f51b0-193">Použijte [middleware založený na továrně](xref:fundamentals/middleware/extensibility).</span><span class="sxs-lookup"><span data-stu-id="f51b0-193">Use [Factory-based middleware](xref:fundamentals/middleware/extensibility).</span></span> <span data-ttu-id="f51b0-194">Middleware registrované pomocí tohoto přístupu se aktivují na žádost klienta (připojení), která umožňuje vkládání oboru služeb do metody middlewaru `InvokeAsync` .</span><span class="sxs-lookup"><span data-stu-id="f51b0-194">Middleware registered using this approach is activated per client request (connection), which allows scoped services to be injected into the middleware's `InvokeAsync` method.</span></span>

<span data-ttu-id="f51b0-195">Další informace naleznete v tématu <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="f51b0-195">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="f51b0-196">Singleton</span><span class="sxs-lookup"><span data-stu-id="f51b0-196">Singleton</span></span>

<span data-ttu-id="f51b0-197">Služba životnosti singleton je vytvořena buď:</span><span class="sxs-lookup"><span data-stu-id="f51b0-197">Singleton lifetime services are created either:</span></span>

* <span data-ttu-id="f51b0-198">Při prvním vyžádání.</span><span class="sxs-lookup"><span data-stu-id="f51b0-198">The first time they're requested.</span></span>
* <span data-ttu-id="f51b0-199">Vývojář při poskytování instance implementace přímo do kontejneru.</span><span class="sxs-lookup"><span data-stu-id="f51b0-199">By the developer, when providing an implementation instance directly to the container.</span></span> <span data-ttu-id="f51b0-200">Tento přístup je potřeba jenom zřídka.</span><span class="sxs-lookup"><span data-stu-id="f51b0-200">This approach is rarely needed.</span></span>

<span data-ttu-id="f51b0-201">Každý další požadavek používá stejnou instanci.</span><span class="sxs-lookup"><span data-stu-id="f51b0-201">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="f51b0-202">Pokud aplikace vyžaduje chování singleton, umožněte kontejneru služby spravovat dobu života služby.</span><span class="sxs-lookup"><span data-stu-id="f51b0-202">If the app requires singleton behavior, allow the service container to manage the service's lifetime.</span></span> <span data-ttu-id="f51b0-203">Neimplementujte vzor návrhu singleton a poskytněte kód pro odstranění typu singleton.</span><span class="sxs-lookup"><span data-stu-id="f51b0-203">Don't implement the singleton design pattern and provide code to dispose of the singleton.</span></span> <span data-ttu-id="f51b0-204">Služby by nikdy neměly být odstraněny kódem, který službu vyřešil z kontejneru.</span><span class="sxs-lookup"><span data-stu-id="f51b0-204">Services should never be disposed by code that resolved the service from the container.</span></span> <span data-ttu-id="f51b0-205">Pokud je typ nebo objekt pro vytváření zaregistrován jako singleton, kontejner odstraní singleton automaticky.</span><span class="sxs-lookup"><span data-stu-id="f51b0-205">If a type or factory is registered as a singleton, the container disposes the singleton automatically.</span></span>

<span data-ttu-id="f51b0-206">Registrovat služby s jedním prvkem pomocí <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A> .</span><span class="sxs-lookup"><span data-stu-id="f51b0-206">Register singleton services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A>.</span></span> <span data-ttu-id="f51b0-207">Služby s jedním prvkem musí být bezpečné pro přístup z více vláken a často se používají ve bezstavových službách.</span><span class="sxs-lookup"><span data-stu-id="f51b0-207">Singleton services must be thread safe and are often used in stateless services.</span></span>

<span data-ttu-id="f51b0-208">V aplikacích, které zpracovávají požadavky, jsou nejednoznačné služby uvolněny při <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> vyřazení aplikace z vypnutí.</span><span class="sxs-lookup"><span data-stu-id="f51b0-208">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed on application shutdown.</span></span> <span data-ttu-id="f51b0-209">Vzhledem k tomu, že paměť není uvolněna, dokud nebude aplikace vypnutá, zvažte použití paměti u služby s jedním prvkem.</span><span class="sxs-lookup"><span data-stu-id="f51b0-209">Because memory is not released until the app is shut down, consider memory use with a singleton service.</span></span>

> [!WARNING]
> <span data-ttu-id="f51b0-210">Neprovádějte ***Překlad oboru*** služby z typu singleton.</span><span class="sxs-lookup"><span data-stu-id="f51b0-210">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="f51b0-211">Může dojít k tomu, že služba má při zpracování dalších požadavků špatný stav.</span><span class="sxs-lookup"><span data-stu-id="f51b0-211">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="f51b0-212">Je dobré vyřešit službu typu Singleton z oboru nebo přechodné služby.</span><span class="sxs-lookup"><span data-stu-id="f51b0-212">It's fine to resolve a singleton service from a scoped or transient service.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="f51b0-213">Metody registrace služby</span><span class="sxs-lookup"><span data-stu-id="f51b0-213">Service registration methods</span></span>

<span data-ttu-id="f51b0-214">Rozhraní poskytuje metody rozšíření pro registraci služby, které jsou užitečné v konkrétních scénářích:</span><span class="sxs-lookup"><span data-stu-id="f51b0-214">The framework provides service registration extension methods that are useful in specific scenarios:</span></span>

<!-- Review: Auto disposal at end of app lifetime is not what you think of auto disposal  -->

| <span data-ttu-id="f51b0-215">Metoda</span><span class="sxs-lookup"><span data-stu-id="f51b0-215">Method</span></span>                                                                                                                                                                              | <span data-ttu-id="f51b0-216">Automaticky</span><span class="sxs-lookup"><span data-stu-id="f51b0-216">Automatic</span></span><br><span data-ttu-id="f51b0-217">object</span><span class="sxs-lookup"><span data-stu-id="f51b0-217">object</span></span><br><span data-ttu-id="f51b0-218">odvod</span><span class="sxs-lookup"><span data-stu-id="f51b0-218">disposal</span></span> | <span data-ttu-id="f51b0-219">Několik</span><span class="sxs-lookup"><span data-stu-id="f51b0-219">Multiple</span></span><br><span data-ttu-id="f51b0-220">implementace</span><span class="sxs-lookup"><span data-stu-id="f51b0-220">implementations</span></span> | <span data-ttu-id="f51b0-221">Pass – argumenty</span><span class="sxs-lookup"><span data-stu-id="f51b0-221">Pass args</span></span> |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------:|:---------------------------:|:---------:|
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="f51b0-222">Příklad:</span><span class="sxs-lookup"><span data-stu-id="f51b0-222">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();`                                                                             | <span data-ttu-id="f51b0-223">Ano</span><span class="sxs-lookup"><span data-stu-id="f51b0-223">Yes</span></span>                             | <span data-ttu-id="f51b0-224">Ano</span><span class="sxs-lookup"><span data-stu-id="f51b0-224">Yes</span></span>                         | <span data-ttu-id="f51b0-225">Ne</span><span class="sxs-lookup"><span data-stu-id="f51b0-225">No</span></span>        |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="f51b0-226">Příklady:</span><span class="sxs-lookup"><span data-stu-id="f51b0-226">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep(99));` | <span data-ttu-id="f51b0-227">Ano</span><span class="sxs-lookup"><span data-stu-id="f51b0-227">Yes</span></span>                             | <span data-ttu-id="f51b0-228">Ano</span><span class="sxs-lookup"><span data-stu-id="f51b0-228">Yes</span></span>                         | <span data-ttu-id="f51b0-229">Ano</span><span class="sxs-lookup"><span data-stu-id="f51b0-229">Yes</span></span>       |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="f51b0-230">Příklad:</span><span class="sxs-lookup"><span data-stu-id="f51b0-230">Example:</span></span><br>`services.AddSingleton<MyDep>();`                                                                                                | <span data-ttu-id="f51b0-231">Ano</span><span class="sxs-lookup"><span data-stu-id="f51b0-231">Yes</span></span>                             | <span data-ttu-id="f51b0-232">Ne</span><span class="sxs-lookup"><span data-stu-id="f51b0-232">No</span></span>                          | <span data-ttu-id="f51b0-233">Ne</span><span class="sxs-lookup"><span data-stu-id="f51b0-233">No</span></span>        |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="f51b0-234">Příklady:</span><span class="sxs-lookup"><span data-stu-id="f51b0-234">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep(99));`                    | <span data-ttu-id="f51b0-235">Ne</span><span class="sxs-lookup"><span data-stu-id="f51b0-235">No</span></span>                              | <span data-ttu-id="f51b0-236">Ano</span><span class="sxs-lookup"><span data-stu-id="f51b0-236">Yes</span></span>                         | <span data-ttu-id="f51b0-237">Ano</span><span class="sxs-lookup"><span data-stu-id="f51b0-237">Yes</span></span>       |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="f51b0-238">Příklady:</span><span class="sxs-lookup"><span data-stu-id="f51b0-238">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep(99));`                                               | <span data-ttu-id="f51b0-239">Ne</span><span class="sxs-lookup"><span data-stu-id="f51b0-239">No</span></span>                              | <span data-ttu-id="f51b0-240">Ne</span><span class="sxs-lookup"><span data-stu-id="f51b0-240">No</span></span>                          | <span data-ttu-id="f51b0-241">Ano</span><span class="sxs-lookup"><span data-stu-id="f51b0-241">Yes</span></span>       |

<span data-ttu-id="f51b0-242">Další informace o vyřazení typů najdete v části věnované [vyřazení služeb](#disposal-of-services) .</span><span class="sxs-lookup"><span data-stu-id="f51b0-242">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="f51b0-243">Při napodobování [typů pro testování](xref:test/integration-tests#inject-mock-services)je běžné použít více implementací.</span><span class="sxs-lookup"><span data-stu-id="f51b0-243">It's common to use multiple implementations when [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="f51b0-244">Rozhraní poskytuje také `TryAdd{LIFETIME}` metody rozšíření, které registrují službu pouze v případě, že již není zaregistrována implementace.</span><span class="sxs-lookup"><span data-stu-id="f51b0-244">The framework also provides `TryAdd{LIFETIME}` extension methods, which register the service only if there isn't already an implementation registered.</span></span>

<span data-ttu-id="f51b0-245">V následujícím příkladu je volání `AddSingleton` registrováno `MyDependency` jako implementace pro `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="f51b0-245">In the following example, the call to `AddSingleton` registers `MyDependency` as an implementation for `IMyDependency`.</span></span> <span data-ttu-id="f51b0-246">Volání `TryAddSingleton` nemá žádný účinek, protože `IMyDependency` již má registrovanou implementaci:</span><span class="sxs-lookup"><span data-stu-id="f51b0-246">The call to `TryAddSingleton` has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="f51b0-247">Další informace naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="f51b0-247">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton%2A>

<span data-ttu-id="f51b0-248">Metody [TryAddEnumerable (ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable%2A) registrují službu pouze v případě, že již neexistují implementace *stejného typu*.</span><span class="sxs-lookup"><span data-stu-id="f51b0-248">The [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable%2A) methods register the service only if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="f51b0-249">Několik služeb je vyřešeno prostřednictvím `IEnumerable<{SERVICE}>` .</span><span class="sxs-lookup"><span data-stu-id="f51b0-249">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="f51b0-250">Při registraci služeb by měl vývojář přidat instanci, pokud už jeden ze stejného typu není přidaný.</span><span class="sxs-lookup"><span data-stu-id="f51b0-250">When registering services, the developer should add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="f51b0-251">Obecně autoři knihovny používají `TryAddEnumerable` k tomu, aby nedocházelo k registraci více kopií implementace v kontejneru.</span><span class="sxs-lookup"><span data-stu-id="f51b0-251">Generally, library authors use `TryAddEnumerable` to avoid registering multiple copies of an implementation in the container.</span></span>

<span data-ttu-id="f51b0-252">V následujícím příkladu se první volání `TryAddEnumerable` registruje `MyDependency` jako implementace pro `IMyDependency1` .</span><span class="sxs-lookup"><span data-stu-id="f51b0-252">In the following example, the first call to `TryAddEnumerable` registers `MyDependency` as an implementation for `IMyDependency1`.</span></span> <span data-ttu-id="f51b0-253">Druhý registr volání `MyDependency` pro `IMyDependency2` .</span><span class="sxs-lookup"><span data-stu-id="f51b0-253">The second call registers `MyDependency` for `IMyDependency2`.</span></span> <span data-ttu-id="f51b0-254">Třetí volání nemá žádný účinek, protože `IMyDependency1` už má registrovanou implementaci `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="f51b0-254">The third call has no effect because `IMyDependency1` already has a registered implementation of `MyDependency`:</span></span>

```csharp
public interface IMyDependency1 { }
public interface IMyDependency2 { }

public class MyDependency : IMyDependency1, IMyDependency2 { }

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency1, MyDependency>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency2, MyDependency>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency1, MyDependency>());
```

<span data-ttu-id="f51b0-255">Registrace služby je obecně závislá na nezávislém pořadí s výjimkou registrace více implementací stejného typu.</span><span class="sxs-lookup"><span data-stu-id="f51b0-255">Service registration is generally order independent except when registering multiple implementations of the same type.</span></span>

<span data-ttu-id="f51b0-256">`IServiceCollection` je kolekce <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> objektů.</span><span class="sxs-lookup"><span data-stu-id="f51b0-256">`IServiceCollection` is a collection of <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> objects.</span></span> <span data-ttu-id="f51b0-257">Následující příklad ukazuje, jak zaregistrovat službu vytvořením a přidáním `ServiceDescriptor` :</span><span class="sxs-lookup"><span data-stu-id="f51b0-257">The following example shows how to register a service by creating and adding a `ServiceDescriptor`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup5.cs?name=snippet)]

<span data-ttu-id="f51b0-258">Předdefinované `Add{LIFETIME}` metody používají stejný přístup.</span><span class="sxs-lookup"><span data-stu-id="f51b0-258">The built-in `Add{LIFETIME}` methods use the same approach.</span></span> <span data-ttu-id="f51b0-259">Podívejte se například na [zdrojový kód AddScoped](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span><span class="sxs-lookup"><span data-stu-id="f51b0-259">For example, see the [AddScoped source code](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span></span>

### <a name="constructor-injection-behavior"></a><span data-ttu-id="f51b0-260">Chování injektáže konstruktoru</span><span class="sxs-lookup"><span data-stu-id="f51b0-260">Constructor injection behavior</span></span>

<span data-ttu-id="f51b0-261">Služby je možné vyřešit pomocí:</span><span class="sxs-lookup"><span data-stu-id="f51b0-261">Services can be resolved by using:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="f51b0-262"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span><span class="sxs-lookup"><span data-stu-id="f51b0-262"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span></span>
  * <span data-ttu-id="f51b0-263">Vytvoří objekty, které nejsou registrovány v kontejneru.</span><span class="sxs-lookup"><span data-stu-id="f51b0-263">Creates objects that aren't registered in the container.</span></span>
  * <span data-ttu-id="f51b0-264">Používá se s funkcemi architektury, jako jsou například [pomocníka značek](xref:mvc/views/tag-helpers/intro), řadiče MVC a [pořadače modelů](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="f51b0-264">Used with framework features, such as [Tag Helpers](xref:mvc/views/tag-helpers/intro), MVC controllers, and [model binders](xref:mvc/models/model-binding).</span></span>

<span data-ttu-id="f51b0-265">Konstruktory mohou přijímat argumenty, které nejsou poskytovány vkládáním závislostí, ale argumenty musí přiřadit výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="f51b0-265">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="f51b0-266">Když jsou služby vyřešeny `IServiceProvider` nebo `ActivatorUtilities` , [Injektáže konstruktoru](xref:mvc/controllers/dependency-injection#constructor-injection) vyžaduje *veřejný* konstruktor.</span><span class="sxs-lookup"><span data-stu-id="f51b0-266">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="f51b0-267">Když jsou služby vyřešeny nástrojem `ActivatorUtilities` , [Injektáže konstruktoru](xref:mvc/controllers/dependency-injection#constructor-injection) vyžaduje, aby existoval pouze jeden příslušný konstruktor.</span><span class="sxs-lookup"><span data-stu-id="f51b0-267">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="f51b0-268">Přetížení konstruktoru jsou podporovaná, ale může existovat jenom jedno přetížení, jehož argumenty můžou být splněné vkládáním závislostí.</span><span class="sxs-lookup"><span data-stu-id="f51b0-268">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="f51b0-269">Entity Framework kontexty</span><span class="sxs-lookup"><span data-stu-id="f51b0-269">Entity Framework contexts</span></span>

<span data-ttu-id="f51b0-270">Ve výchozím nastavení se Entity Framework kontexty přidávají do kontejneru služby pomocí [rozsahu životnosti](#service-lifetimes) , protože operace databáze webové aplikace jsou normálně vymezeny na žádost klienta.</span><span class="sxs-lookup"><span data-stu-id="f51b0-270">By default, Entity Framework contexts are added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="f51b0-271">Chcete-li použít jinou dobu života, určete dobu života pomocí <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> přetížení.</span><span class="sxs-lookup"><span data-stu-id="f51b0-271">To use a different lifetime, specify the lifetime by using an <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> overload.</span></span> <span data-ttu-id="f51b0-272">Služby pro danou dobu života by neměly používat kontext databáze s dobou životnosti, která je kratší než doba života služby.</span><span class="sxs-lookup"><span data-stu-id="f51b0-272">Services of a given lifetime shouldn't use a database context with a lifetime that's shorter than the service's lifetime.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="f51b0-273">Možnosti života a registrace</span><span class="sxs-lookup"><span data-stu-id="f51b0-273">Lifetime and registration options</span></span>

<span data-ttu-id="f51b0-274">Chcete-li předvést rozdíl mezi životností služeb a jejich možnostmi registrace, zvažte následující rozhraní, která představují úlohu jako operaci s identifikátorem `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="f51b0-274">To demonstrate the difference between service lifetimes and their registration options, consider the following interfaces that represent a task as an operation with an identifier, `OperationId`.</span></span> <span data-ttu-id="f51b0-275">V závislosti na tom, jak je doba platnosti služby operace nakonfigurovaná pro následující rozhraní, kontejner poskytuje buď stejné nebo jiné instance služby, když ho požaduje třída:</span><span class="sxs-lookup"><span data-stu-id="f51b0-275">Depending on how the lifetime of an operation's service is configured for the following interfaces, the container provides either the same or different instances of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="f51b0-276">Následující `Operation` Třída implementuje všechna předchozí rozhraní.</span><span class="sxs-lookup"><span data-stu-id="f51b0-276">The following `Operation` class implements all of the preceding interfaces.</span></span> <span data-ttu-id="f51b0-277">`Operation`Konstruktor generuje identifikátor GUID a ukládá poslední 4 znaky ve `OperationId` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="f51b0-277">The `Operation` constructor generates a GUID and stores the last 4 characters in the `OperationId` property:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<!--
An `OperationService` is registered that depends on each of the other `Operation` types. When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.

* When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`. `OperationService` receives a new instance of the `IOperationTransient` class. The new instance yields a different `OperationId`.
* When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request. Across client requests, both services share a different `OperationId` value.
* When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]
-->

<span data-ttu-id="f51b0-278">`Startup.ConfigureServices`Metoda vytvoří více registrací `Operation` třídy podle pojmenovaných životností:</span><span class="sxs-lookup"><span data-stu-id="f51b0-278">The `Startup.ConfigureServices` method creates multiple registrations of the `Operation` class according to the named lifetimes:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="f51b0-279">Ukázková aplikace ukazuje dobu života objektu v rámci i mezi požadavky.</span><span class="sxs-lookup"><span data-stu-id="f51b0-279">The sample app demonstrates object lifetimes both within and between requests.</span></span> <span data-ttu-id="f51b0-280">`IndexModel`A middleware vyžádá každý druh `IOperation` typu a protokoluje `OperationId` pro každý typ:</span><span class="sxs-lookup"><span data-stu-id="f51b0-280">The `IndexModel` and the middleware request each kind of `IOperation` type and log the `OperationId` for each:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="f51b0-281">Podobně jako rozhraní `IndexModel` , middleware řeší stejné služby:</span><span class="sxs-lookup"><span data-stu-id="f51b0-281">Similar to the `IndexModel`, the middleware resolves the same services:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet)]

<span data-ttu-id="f51b0-282">Oborové služby musí být vyřešené v `InvokeAsync` metodě:</span><span class="sxs-lookup"><span data-stu-id="f51b0-282">Scoped services must be resolved in the `InvokeAsync` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet2&highlight=2)]

<span data-ttu-id="f51b0-283">Výstup protokolovacího nástroje ukazuje:</span><span class="sxs-lookup"><span data-stu-id="f51b0-283">The logger output shows:</span></span>

* <span data-ttu-id="f51b0-284">*Přechodné* objekty jsou vždy odlišné.</span><span class="sxs-lookup"><span data-stu-id="f51b0-284">*Transient* objects are always different.</span></span> <span data-ttu-id="f51b0-285">Přechodná `OperationId` hodnota se liší v `IndexModel` a v middlewaru.</span><span class="sxs-lookup"><span data-stu-id="f51b0-285">The transient `OperationId` value is different in the `IndexModel` and in the middleware.</span></span>
* <span data-ttu-id="f51b0-286">*Vymezené* objekty jsou pro každý požadavek stejné, ale v každé žádosti se liší.</span><span class="sxs-lookup"><span data-stu-id="f51b0-286">*Scoped* objects are the same for each request but different across each request.</span></span>
* <span data-ttu-id="f51b0-287">Objekty *singleton* jsou pro každý požadavek stejné.</span><span class="sxs-lookup"><span data-stu-id="f51b0-287">*Singleton* objects are the same for every request.</span></span>

<span data-ttu-id="f51b0-288">Chcete-li snížit výstup protokolování, nastavte v *appsettings.Development.jsv* souboru "Logging: LogLevel: Microsoft: Error":</span><span class="sxs-lookup"><span data-stu-id="f51b0-288">To reduce the logging output, set "Logging:LogLevel:Microsoft:Error" in the *appsettings.Development.json* file:</span></span>

[!code-json[](dependency-injection/samples/3.x/DependencyInjectionSample/appsettings.Development.json?highlight=7)]

## <a name="call-services-from-main"></a><span data-ttu-id="f51b0-289">Volat služby z Main</span><span class="sxs-lookup"><span data-stu-id="f51b0-289">Call services from main</span></span>

<span data-ttu-id="f51b0-290">Vytvořte <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> pomocí [IServiceScopeFactory. CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) pro řešení oboru služby v rámci rozsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="f51b0-290">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="f51b0-291">Tento přístup je užitečný pro přístup k oboruované službě při spuštění za účelem spouštění úloh inicializace.</span><span class="sxs-lookup"><span data-stu-id="f51b0-291">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span>

<span data-ttu-id="f51b0-292">Následující příklad ukazuje, jak přistupovat k oboru `IMyDependency` služby a volat jeho `WriteMessage` metodu v `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="f51b0-292">The following example shows how to access the scoped `IMyDependency` service and call its `WriteMessage` method in `Program.Main`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Program.cs?name=snippet)]

<a name="sv"></a>

## <a name="scope-validation"></a><span data-ttu-id="f51b0-293">Ověřování oboru</span><span class="sxs-lookup"><span data-stu-id="f51b0-293">Scope validation</span></span>

<span data-ttu-id="f51b0-294">Když je aplikace spuštěná ve [vývojovém prostředí](xref:fundamentals/environments) a volá [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) , aby se vytvořila hostitel, použije výchozí poskytovatel služeb kontroly, aby ověřil, že:</span><span class="sxs-lookup"><span data-stu-id="f51b0-294">When the app runs in the [Development environment](xref:fundamentals/environments) and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="f51b0-295">Oborové služby se nevyřešily od poskytovatele kořenové služby.</span><span class="sxs-lookup"><span data-stu-id="f51b0-295">Scoped services aren't resolved from the root service provider.</span></span>
* <span data-ttu-id="f51b0-296">Oborové služby se nevkládají do singleton.</span><span class="sxs-lookup"><span data-stu-id="f51b0-296">Scoped services aren't injected into singletons.</span></span>

<span data-ttu-id="f51b0-297">Poskytovatel kořenové služby se vytvoří, když <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> se zavolá.</span><span class="sxs-lookup"><span data-stu-id="f51b0-297">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> is called.</span></span> <span data-ttu-id="f51b0-298">Doba života poskytovatele kořenové služby odpovídá době životnosti aplikace, když poskytovatel spustí aplikaci a je uvolněna při ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="f51b0-298">The root service provider's lifetime corresponds to the app's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="f51b0-299">Oborové služby jsou uvolněny kontejnerem, který je vytvořil.</span><span class="sxs-lookup"><span data-stu-id="f51b0-299">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="f51b0-300">Pokud je v kořenovém kontejneru vytvořena vymezená služba, doba života služby je efektivně povýšena na typ singleton, protože je uvolněna pouze kořenovým kontejnerem, když aplikace vypíná.</span><span class="sxs-lookup"><span data-stu-id="f51b0-300">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when the app shuts down.</span></span> <span data-ttu-id="f51b0-301">Při ověřování oborů služeb se tyto situace zachytí `BuildServiceProvider` .</span><span class="sxs-lookup"><span data-stu-id="f51b0-301">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="f51b0-302">Další informace najdete v tématu [ověřování oboru](xref:fundamentals/host/web-host#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="f51b0-302">For more information, see [Scope validation](xref:fundamentals/host/web-host#scope-validation).</span></span>

## <a name="request-services"></a><span data-ttu-id="f51b0-303">Žádosti o služby</span><span class="sxs-lookup"><span data-stu-id="f51b0-303">Request Services</span></span>

<span data-ttu-id="f51b0-304">Služby, které jsou k dispozici v rámci žádosti o ASP.NET Core, jsou zpřístupněny prostřednictvím kolekce [HttpContext. RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) .</span><span class="sxs-lookup"><span data-stu-id="f51b0-304">The services available within an ASP.NET Core request are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span> <span data-ttu-id="f51b0-305">Pokud jsou služby požadovány zevnitř žádosti, služby a jejich závislosti jsou řešeny z `RequestServices` kolekce.</span><span class="sxs-lookup"><span data-stu-id="f51b0-305">When services are requested from inside of a request, the services and their dependencies are resolved from the `RequestServices` collection.</span></span>

<span data-ttu-id="f51b0-306">Architektura vytvoří obor na žádost a `RequestServices` zpřístupní poskytovatele vymezeného rozsahu.</span><span class="sxs-lookup"><span data-stu-id="f51b0-306">The framework creates a scope per request and `RequestServices` exposes the scoped service provider.</span></span> <span data-ttu-id="f51b0-307">Všechny oborové služby platí, pokud je žádost aktivní.</span><span class="sxs-lookup"><span data-stu-id="f51b0-307">All scoped services are valid for as long as the request is active.</span></span>

> [!NOTE]
> <span data-ttu-id="f51b0-308">Preferovat požadavky na závislosti jako parametry konstruktoru pro překlad služeb z `RequestServices` kolekce.</span><span class="sxs-lookup"><span data-stu-id="f51b0-308">Prefer requesting dependencies as constructor parameters to resolving services from the `RequestServices` collection.</span></span> <span data-ttu-id="f51b0-309">Výsledkem jsou třídy, které jsou snáze testovány.</span><span class="sxs-lookup"><span data-stu-id="f51b0-309">This results in classes that are easier to test.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="f51b0-310">Navrhnout služby pro vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="f51b0-310">Design services for dependency injection</span></span>

<span data-ttu-id="f51b0-311">Při navrhování služeb pro vkládání závislostí:</span><span class="sxs-lookup"><span data-stu-id="f51b0-311">When designing services for dependency injection:</span></span>

* <span data-ttu-id="f51b0-312">Vyhněte se stavovým, statickým třídám a členům.</span><span class="sxs-lookup"><span data-stu-id="f51b0-312">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="f51b0-313">Vyhněte se vytváření globálního stavu tím, že aplikace navrhujete, aby místo toho používaly služby singleton.</span><span class="sxs-lookup"><span data-stu-id="f51b0-313">Avoid creating global state by designing apps to use singleton services instead.</span></span>
* <span data-ttu-id="f51b0-314">Vyhněte se přímému vytváření instancí závislých tříd v rámci služeb.</span><span class="sxs-lookup"><span data-stu-id="f51b0-314">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="f51b0-315">Přímá instance Couples kód na konkrétní implementaci.</span><span class="sxs-lookup"><span data-stu-id="f51b0-315">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="f51b0-316">Vytvářejte služby s malým, dobře faktoringem a snadnou otestováním.</span><span class="sxs-lookup"><span data-stu-id="f51b0-316">Make services small, well-factored, and easily tested.</span></span>

<span data-ttu-id="f51b0-317">Pokud má třída mnoho vložených závislostí, může se jednat o znaménko, že třída má příliš mnoho zodpovědnosti a je v rozporu s [principem jediné zodpovědnosti (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="f51b0-317">If a class has a lot of injected dependencies, it might be a sign that the class has too many responsibilities and violates the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="f51b0-318">Pokuste se Refaktorovat třídu přesunutím některých jeho odpovědností do nových tříd.</span><span class="sxs-lookup"><span data-stu-id="f51b0-318">Attempt to refactor the class by moving some of its responsibilities into new classes.</span></span> <span data-ttu-id="f51b0-319">Mějte na paměti, že Razor stránky tříd modelu stránky a třídy KONTROLERU MVC by se měly zaměřit na uživatelské rozhraní.</span><span class="sxs-lookup"><span data-stu-id="f51b0-319">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="f51b0-320">Vyřazení služeb</span><span class="sxs-lookup"><span data-stu-id="f51b0-320">Disposal of services</span></span>

<span data-ttu-id="f51b0-321">Kontejner volá <xref:System.IDisposable.Dispose%2A> typy, které <xref:System.IDisposable> vytvoří.</span><span class="sxs-lookup"><span data-stu-id="f51b0-321">The container calls <xref:System.IDisposable.Dispose%2A> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="f51b0-322">Služby vyřešené z kontejneru by nikdy neměly být odstraněny vývojářem.</span><span class="sxs-lookup"><span data-stu-id="f51b0-322">Services resolved from the container should never be disposed by the developer.</span></span> <span data-ttu-id="f51b0-323">Pokud je typ nebo objekt pro vytváření zaregistrován jako singleton, kontejner odstraní singleton automaticky.</span><span class="sxs-lookup"><span data-stu-id="f51b0-323">If a type or factory is registered as a singleton, the container disposes the singleton automatically.</span></span>

<span data-ttu-id="f51b0-324">V následujícím příkladu jsou služby vytvořeny pomocí kontejneru služby a automaticky odstraněny:</span><span class="sxs-lookup"><span data-stu-id="f51b0-324">In the following example, the services are created by the service container and disposed automatically:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Services/Service1.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="f51b0-325">Konzola ladění po každé aktualizaci stránky indexu zobrazí následující výstup:</span><span class="sxs-lookup"><span data-stu-id="f51b0-325">The debug console shows the following output after each refresh of the Index page:</span></span>

```console
Service1: IndexModel.OnGet
Service2: IndexModel.OnGet
Service3: IndexModel.OnGet
Service1.Dispose
```

### <a name="services-not-created-by-the-service-container"></a><span data-ttu-id="f51b0-326">Služby, které nevytvořil kontejner služby</span><span class="sxs-lookup"><span data-stu-id="f51b0-326">Services not created by the service container</span></span>

<span data-ttu-id="f51b0-327">Uvažujte následující kód:</span><span class="sxs-lookup"><span data-stu-id="f51b0-327">Consider the following code:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup2.cs?name=snippet)]

<span data-ttu-id="f51b0-328">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="f51b0-328">In the preceding code:</span></span>

* <span data-ttu-id="f51b0-329">Instance služby nejsou vytvořeny kontejnerem služby.</span><span class="sxs-lookup"><span data-stu-id="f51b0-329">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="f51b0-330">Rozhraní neodstraní služby automaticky.</span><span class="sxs-lookup"><span data-stu-id="f51b0-330">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="f51b0-331">Vývojář zodpovídá za likvidaci služeb.</span><span class="sxs-lookup"><span data-stu-id="f51b0-331">The developer is responsible for disposing the services.</span></span>

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="f51b0-332">Doprovodné materiály k rozhraní IDisposable pro přechodné a sdílené instance</span><span class="sxs-lookup"><span data-stu-id="f51b0-332">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="f51b0-333">Přechodná, omezená doba života</span><span class="sxs-lookup"><span data-stu-id="f51b0-333">Transient, limited lifetime</span></span>

<span data-ttu-id="f51b0-334">**Scénář**</span><span class="sxs-lookup"><span data-stu-id="f51b0-334">**Scenario**</span></span>

<span data-ttu-id="f51b0-335">Aplikace vyžaduje <xref:System.IDisposable> instanci s přechodným životností pro některý z následujících scénářů:</span><span class="sxs-lookup"><span data-stu-id="f51b0-335">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="f51b0-336">Instance je vyřešena v kořenovém oboru (kořenovém kontejneru).</span><span class="sxs-lookup"><span data-stu-id="f51b0-336">The instance is resolved in the root scope (root container).</span></span>
* <span data-ttu-id="f51b0-337">Instance by měla být uvolněna před ukončením oboru.</span><span class="sxs-lookup"><span data-stu-id="f51b0-337">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="f51b0-338">**Řešení**</span><span class="sxs-lookup"><span data-stu-id="f51b0-338">**Solution**</span></span>

<span data-ttu-id="f51b0-339">Pomocí modelu továrny vytvořte instanci mimo nadřazený obor.</span><span class="sxs-lookup"><span data-stu-id="f51b0-339">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="f51b0-340">V takové situaci by aplikace měla obvykle `Create` metodu, která volá přímo konstruktor finálního typu.</span><span class="sxs-lookup"><span data-stu-id="f51b0-340">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="f51b0-341">Pokud má konečný typ jiné závislosti, továrna může:</span><span class="sxs-lookup"><span data-stu-id="f51b0-341">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="f51b0-342">Přijmout <xref:System.IServiceProvider> ve svém konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="f51b0-342">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="f51b0-343">Slouží <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> k vytvoření instance instance mimo kontejner, při použití kontejneru pro jeho závislosti.</span><span class="sxs-lookup"><span data-stu-id="f51b0-343">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside of the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="f51b0-344">Sdílená instance, omezená doba života</span><span class="sxs-lookup"><span data-stu-id="f51b0-344">Shared instance, limited lifetime</span></span>

<span data-ttu-id="f51b0-345">**Scénář**</span><span class="sxs-lookup"><span data-stu-id="f51b0-345">**Scenario**</span></span>

<span data-ttu-id="f51b0-346">Aplikace vyžaduje sdílenou <xref:System.IDisposable> instanci napříč více službami, ale <xref:System.IDisposable> instance by měla mít omezené trvání.</span><span class="sxs-lookup"><span data-stu-id="f51b0-346">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> instance should have a limited lifetime.</span></span>

<span data-ttu-id="f51b0-347">**Řešení**</span><span class="sxs-lookup"><span data-stu-id="f51b0-347">**Solution**</span></span>

<span data-ttu-id="f51b0-348">Zaregistrujte instanci s vymezenou životností.</span><span class="sxs-lookup"><span data-stu-id="f51b0-348">Register the instance with a scoped lifetime.</span></span> <span data-ttu-id="f51b0-349">Použijte <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> k vytvoření nového <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> .</span><span class="sxs-lookup"><span data-stu-id="f51b0-349">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="f51b0-350"><xref:System.IServiceProvider>Požadované služby získáte pomocí tohoto oboru.</span><span class="sxs-lookup"><span data-stu-id="f51b0-350">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="f51b0-351">Pokud už tento obor nepotřebujete, vyřadit ho.</span><span class="sxs-lookup"><span data-stu-id="f51b0-351">Dispose the scope when it's no longer needed.</span></span>

#### <a name="general-idisposable-guidelines"></a><span data-ttu-id="f51b0-352">Obecné pokyny pro IDisposable</span><span class="sxs-lookup"><span data-stu-id="f51b0-352">General IDisposable guidelines</span></span>

* <span data-ttu-id="f51b0-353">Neregistrujte <xref:System.IDisposable> instance s přechodným životností.</span><span class="sxs-lookup"><span data-stu-id="f51b0-353">Don't register <xref:System.IDisposable> instances with a transient lifetime.</span></span> <span data-ttu-id="f51b0-354">Místo toho použijte výrobní model.</span><span class="sxs-lookup"><span data-stu-id="f51b0-354">Use the factory pattern instead.</span></span>
* <span data-ttu-id="f51b0-355"><xref:System.IDisposable>V kořenovém oboru neodstraňujte instance s přechodnou nebo vymezenou životností.</span><span class="sxs-lookup"><span data-stu-id="f51b0-355">Don't resolve <xref:System.IDisposable> instances with a transient or scoped lifetime in the root scope.</span></span> <span data-ttu-id="f51b0-356">Jedinou výjimkou je, že pokud aplikace vytvoří nebo znovu vytvoří a odstraní <xref:System.IServiceProvider> , ale to není ideální vzor.</span><span class="sxs-lookup"><span data-stu-id="f51b0-356">The only exception to this is if the app creates/recreates and disposes <xref:System.IServiceProvider>, but this isn't an ideal pattern.</span></span>
* <span data-ttu-id="f51b0-357">Příjem <xref:System.IDisposable> závislosti přes di nevyžaduje, aby se příjemce sám implementoval <xref:System.IDisposable> .</span><span class="sxs-lookup"><span data-stu-id="f51b0-357">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="f51b0-358">Příjemce <xref:System.IDisposable> závislosti by neměl volat <xref:System.IDisposable.Dispose%2A> tuto závislost.</span><span class="sxs-lookup"><span data-stu-id="f51b0-358">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="f51b0-359">Použijte obory k řízení životnosti služeb.</span><span class="sxs-lookup"><span data-stu-id="f51b0-359">Use scopes to control the lifetimes of services.</span></span> <span data-ttu-id="f51b0-360">Obory nejsou hierarchické a mezi obory není žádné zvláštní připojení.</span><span class="sxs-lookup"><span data-stu-id="f51b0-360">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="f51b0-361">Výchozí nahrazení kontejneru služby</span><span class="sxs-lookup"><span data-stu-id="f51b0-361">Default service container replacement</span></span>

<span data-ttu-id="f51b0-362">Integrovaný kontejner služeb je navržený tak, aby sloužil potřebám architektury a většině spotřebitelských aplikací.</span><span class="sxs-lookup"><span data-stu-id="f51b0-362">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="f51b0-363">Pokud nepotřebujete konkrétní funkci, kterou nepodporuje, doporučujeme použít vestavěný kontejner, například:</span><span class="sxs-lookup"><span data-stu-id="f51b0-363">We recommend using the built-in container unless you need a specific feature that it doesn't support, such as:</span></span>

* <span data-ttu-id="f51b0-364">Vkládání vlastností</span><span class="sxs-lookup"><span data-stu-id="f51b0-364">Property injection</span></span>
* <span data-ttu-id="f51b0-365">Vkládání na základě názvu</span><span class="sxs-lookup"><span data-stu-id="f51b0-365">Injection based on name</span></span>
* <span data-ttu-id="f51b0-366">Podřízené kontejnery</span><span class="sxs-lookup"><span data-stu-id="f51b0-366">Child containers</span></span>
* <span data-ttu-id="f51b0-367">Vlastní Správa životního cyklu</span><span class="sxs-lookup"><span data-stu-id="f51b0-367">Custom lifetime management</span></span>
* <span data-ttu-id="f51b0-368">`Func<T>` Podpora opožděné inicializace</span><span class="sxs-lookup"><span data-stu-id="f51b0-368">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="f51b0-369">Registrace založená na konvencích</span><span class="sxs-lookup"><span data-stu-id="f51b0-369">Convention-based registration</span></span>

<span data-ttu-id="f51b0-370">U ASP.NET Corech aplikací lze použít následující kontejnery třetích stran:</span><span class="sxs-lookup"><span data-stu-id="f51b0-370">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="f51b0-371">Autofac</span><span class="sxs-lookup"><span data-stu-id="f51b0-371">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="f51b0-372">DryIoc</span><span class="sxs-lookup"><span data-stu-id="f51b0-372">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="f51b0-373">Integrit</span><span class="sxs-lookup"><span data-stu-id="f51b0-373">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="f51b0-374">LightInject</span><span class="sxs-lookup"><span data-stu-id="f51b0-374">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="f51b0-375">Lamar</span><span class="sxs-lookup"><span data-stu-id="f51b0-375">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="f51b0-376">Stashbox</span><span class="sxs-lookup"><span data-stu-id="f51b0-376">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="f51b0-377">Unity</span><span class="sxs-lookup"><span data-stu-id="f51b0-377">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

## <a name="thread-safety"></a><span data-ttu-id="f51b0-378">Bezpečnost vlákna</span><span class="sxs-lookup"><span data-stu-id="f51b0-378">Thread safety</span></span>

<span data-ttu-id="f51b0-379">Vytvořte služby, které jsou v bezpečí pro přístup z více vláken.</span><span class="sxs-lookup"><span data-stu-id="f51b0-379">Create thread-safe singleton services.</span></span> <span data-ttu-id="f51b0-380">Pokud má služba typu Singleton závislost na přechodné službě, přechodná služba může také vyžadovat zabezpečení vlákna v závislosti na tom, jak je používá typ singleton.</span><span class="sxs-lookup"><span data-stu-id="f51b0-380">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending on how it's used by the singleton.</span></span>

<span data-ttu-id="f51b0-381">Výrobní metoda samostatné služby, jako je například druhý argument pro [AddSingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A), nemusí být bezpečná pro přístup z více vláken.</span><span class="sxs-lookup"><span data-stu-id="f51b0-381">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A), doesn't need to be thread-safe.</span></span> <span data-ttu-id="f51b0-382">Podobně jako konstruktor typu ( `static` ), je zaručeno volání pouze jednou vláknem.</span><span class="sxs-lookup"><span data-stu-id="f51b0-382">Like a type (`static`) constructor, it's guaranteed to be called only once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="f51b0-383">Doporučení</span><span class="sxs-lookup"><span data-stu-id="f51b0-383">Recommendations</span></span>

* <span data-ttu-id="f51b0-384">`async/await``Task`řešení služeb založené na základech se nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="f51b0-384">`async/await` and `Task` based service resolution isn't supported.</span></span> <span data-ttu-id="f51b0-385">Vzhledem k tomu, že jazyk C# nepodporuje asynchronní konstruktory, použijte asynchronní metody po synchronním překladu služby.</span><span class="sxs-lookup"><span data-stu-id="f51b0-385">Because C# doesn't support asynchronous constructors, use asynchronous methods after synchronously resolving the service.</span></span>
* <span data-ttu-id="f51b0-386">Neukládejte data a konfiguraci přímo do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="f51b0-386">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="f51b0-387">Například nákupní košík uživatele by neměl být obvykle přidán do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="f51b0-387">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="f51b0-388">Konfigurace by měla používat [vzor možností](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="f51b0-388">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="f51b0-389">Podobně nepoužívejte objekty "držitel dat", které existují pouze k povolení přístupu k jinému objektu.</span><span class="sxs-lookup"><span data-stu-id="f51b0-389">Similarly, avoid "data holder" objects that only exist to allow access to another object.</span></span> <span data-ttu-id="f51b0-390">Je lepší žádat o skutečnou položku přes DI.</span><span class="sxs-lookup"><span data-stu-id="f51b0-390">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="f51b0-391">Vyhněte se statickému přístupu ke službám.</span><span class="sxs-lookup"><span data-stu-id="f51b0-391">Avoid static access to services.</span></span> <span data-ttu-id="f51b0-392">Vyhněte se například zachycení [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) jako statické pole nebo vlastnost pro použití jinde.</span><span class="sxs-lookup"><span data-stu-id="f51b0-392">For example, avoid capturing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) as a static field or property for use elsewhere.</span></span>
* <span data-ttu-id="f51b0-393">Snažte se DI Factory rychle a synchronně.</span><span class="sxs-lookup"><span data-stu-id="f51b0-393">Keep DI factories fast and synchronous.</span></span>
* <span data-ttu-id="f51b0-394">Vyhněte se použití *vzoru lokátoru služby*.</span><span class="sxs-lookup"><span data-stu-id="f51b0-394">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="f51b0-395">Například Nevolejte <xref:System.IServiceProvider.GetService%2A> pro získání instance služby, když můžete místo toho použít di:</span><span class="sxs-lookup"><span data-stu-id="f51b0-395">For example, don't invoke <xref:System.IServiceProvider.GetService%2A> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="f51b0-396">**Nesprávně:**</span><span class="sxs-lookup"><span data-stu-id="f51b0-396">**Incorrect:**</span></span>

    ![Nesprávný kód](dependency-injection/_static/bad.png)

  <span data-ttu-id="f51b0-398">**Správné**:</span><span class="sxs-lookup"><span data-stu-id="f51b0-398">**Correct**:</span></span>

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
* <span data-ttu-id="f51b0-399">Další změnou lokátoru služby, aby se zabránilo, je vložení továrny, která vyřeší závislosti za běhu.</span><span class="sxs-lookup"><span data-stu-id="f51b0-399">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="f51b0-400">Oba tyto postupy kombinují [inverze strategií řízení](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .</span><span class="sxs-lookup"><span data-stu-id="f51b0-400">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
* <span data-ttu-id="f51b0-401">Vyhněte se statickému přístupu k `HttpContext` (například [IHttpContextAccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="f51b0-401">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<a name="ASP0000"></a>
* <span data-ttu-id="f51b0-402">Vyhněte se volání do <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> v `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="f51b0-402">Avoid calls to <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> in `ConfigureServices`.</span></span> <span data-ttu-id="f51b0-403">`BuildServiceProvider`K volání obvykle dochází, když vývojář chce službu vyřešit `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="f51b0-403">Calling `BuildServiceProvider` typically happens when the developer wants to resolve a service in `ConfigureServices`.</span></span> <span data-ttu-id="f51b0-404">Zvažte například případ, kdy `LoginPath` je načten z konfigurace.</span><span class="sxs-lookup"><span data-stu-id="f51b0-404">For example, consider the case where the `LoginPath` is loaded from configuration.</span></span> <span data-ttu-id="f51b0-405">Vyhněte se následujícímu přístupu:</span><span class="sxs-lookup"><span data-stu-id="f51b0-405">Avoid the following approach:</span></span>

  ![Chybný kód volající BuildServiceProvider](~/fundamentals/dependency-injection/_static/badcodeX.png)

  <span data-ttu-id="f51b0-407">Na předchozím obrázku vyberte zelenou vlnovku pod nadpisem `services.BuildServiceProvider` zobrazit následující upozornění ASP0000:</span><span class="sxs-lookup"><span data-stu-id="f51b0-407">In the preceding image, selecting the green wavy line under `services.BuildServiceProvider` shows the following ASP0000 warning:</span></span>

  > <span data-ttu-id="f51b0-408">ASP0000 volání ' BuildServiceProvider ' z kódu aplikace vede k vytvoření další kopie ojedinělých služeb, které jsou vytvářeny.</span><span class="sxs-lookup"><span data-stu-id="f51b0-408">ASP0000 Calling 'BuildServiceProvider' from application code results in an additional copy of singleton services being created.</span></span> <span data-ttu-id="f51b0-409">Zvažte alternativy, jako jsou třeba závislosti, jako jsou například služby, jako jsou parametry konfigurace.</span><span class="sxs-lookup"><span data-stu-id="f51b0-409">Consider alternatives such as dependency injecting services as parameters to 'Configure'.</span></span>

  <span data-ttu-id="f51b0-410">Volání `BuildServiceProvider` vytvoří druhý kontejner, který může vytvořit roztrhané jednoznačné objekty a způsobit odkazy na grafy objektů napříč více kontejnery.</span><span class="sxs-lookup"><span data-stu-id="f51b0-410">Calling `BuildServiceProvider` creates a second container, which can create torn singletons and cause references to object graphs across multiple containers.</span></span>

  <span data-ttu-id="f51b0-411">Správný způsob, jak získat `LoginPath` , je použít integrovanou podporu vzorce možností pro di:</span><span class="sxs-lookup"><span data-stu-id="f51b0-411">A correct way to get `LoginPath` is to use the options pattern's built-in support for DI:</span></span>

  [!code-csharp[](dependency-injection/samples/3.x/AntiPattern3/Startup.cs?name=snippet)]

* <span data-ttu-id="f51b0-412">Přechodné služby za použití jsou zachyceny kontejnerem k vyřazení.</span><span class="sxs-lookup"><span data-stu-id="f51b0-412">Disposable transient services are captured by the container for disposal.</span></span> <span data-ttu-id="f51b0-413">To může způsobit nevracení paměti, pokud je vyřešeno z kontejneru nejvyšší úrovně.</span><span class="sxs-lookup"><span data-stu-id="f51b0-413">This can turn into a memory leak if resolved from the top level container.</span></span>
* <span data-ttu-id="f51b0-414">Povolte ověřování oboru, abyste se ujistili, že aplikace nemá k dispozici jednoznačné služby, které zachytí obor služeb.</span><span class="sxs-lookup"><span data-stu-id="f51b0-414">Enable scope validation to make sure the app doesn't have singletons that capture scoped services.</span></span> <span data-ttu-id="f51b0-415">Další informace najdete v tématu [ověřování oboru](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="f51b0-415">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="f51b0-416">Podobně jako u všech sad doporučení se může stát, že se vyžaduje ignorování doporučení.</span><span class="sxs-lookup"><span data-stu-id="f51b0-416">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="f51b0-417">Výjimky jsou zřídka, většinou zvláštní případy v rámci samotného rozhraní.</span><span class="sxs-lookup"><span data-stu-id="f51b0-417">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="f51b0-418">DI je *alternativou* ke vzorům statických nebo globálních přístupů k objektům.</span><span class="sxs-lookup"><span data-stu-id="f51b0-418">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="f51b0-419">Je možné, že nebudete moci využít výhody DI, pokud je kombinujete se statickým přístupem k objektům.</span><span class="sxs-lookup"><span data-stu-id="f51b0-419">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="f51b0-420">Doporučené vzory pro víceklientské architektury v DI</span><span class="sxs-lookup"><span data-stu-id="f51b0-420">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="f51b0-421">[Sadu jader](https://github.com/OrchardCMS/OrchardCore) je aplikační architektura pro vytváření modulárních aplikací s více klienty na ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f51b0-421">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) is an application framework for building modular, multi-tenant applications on ASP.NET Core.</span></span> <span data-ttu-id="f51b0-422">Další informace najdete v dokumentaci k sadě [sad](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="f51b0-422">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="f51b0-423">V [ukázkách Core Core](https://github.com/OrchardCMS/OrchardCore.Samples) najdete příklady vytváření modulárních a víceklientské aplikací s využitím jenom sady Core Core Framework bez jakýchkoli funkcí specifických pro CMS.</span><span class="sxs-lookup"><span data-stu-id="f51b0-423">See the [Orchard Core samples](https://github.com/OrchardCMS/OrchardCore.Samples) for examples of how to build modular and multi-tenant apps using just the Orchard Core Framework without any of its CMS-specific features.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="f51b0-424">Služby poskytované rozhraním</span><span class="sxs-lookup"><span data-stu-id="f51b0-424">Framework-provided services</span></span>

<span data-ttu-id="f51b0-425">`Startup.ConfigureServices`Metoda zaregistruje služby, které aplikace používá, včetně funkcí platformy, jako je například Entity Framework Core a ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="f51b0-425">The `Startup.ConfigureServices` method registers services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="f51b0-426">Zpočátku `IServiceCollection` `ConfigureServices` služba má služby definované rozhraním v závislosti na [tom, jak byl hostitel nakonfigurovaný](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="f51b0-426">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="f51b0-427">Pro aplikace založené na šablonách ASP.NET Core registruje rozhraní více než 250 služeb.</span><span class="sxs-lookup"><span data-stu-id="f51b0-427">For apps based on the ASP.NET Core templates, the framework registers more than 250 services.</span></span> 

<span data-ttu-id="f51b0-428">V následující tabulce je uveden malý vzorek těchto služeb registrovaných v rozhraní:</span><span class="sxs-lookup"><span data-stu-id="f51b0-428">The following table lists a small sample of these framework-registered services:</span></span>

| <span data-ttu-id="f51b0-429">Typ služby</span><span class="sxs-lookup"><span data-stu-id="f51b0-429">Service Type</span></span>                                                                                    | <span data-ttu-id="f51b0-430">Doba platnosti</span><span class="sxs-lookup"><span data-stu-id="f51b0-430">Lifetime</span></span>  |
|-------------------------------------------------------------------------------------------------|-----------|
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="f51b0-431">Dočasný</span><span class="sxs-lookup"><span data-stu-id="f51b0-431">Transient</span></span> |
| <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>                                    | <span data-ttu-id="f51b0-432">Singleton</span><span class="sxs-lookup"><span data-stu-id="f51b0-432">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>                                         | <span data-ttu-id="f51b0-433">Singleton</span><span class="sxs-lookup"><span data-stu-id="f51b0-433">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName>                           | <span data-ttu-id="f51b0-434">Singleton</span><span class="sxs-lookup"><span data-stu-id="f51b0-434">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName>                     | <span data-ttu-id="f51b0-435">Dočasný</span><span class="sxs-lookup"><span data-stu-id="f51b0-435">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName>                     | <span data-ttu-id="f51b0-436">Singleton</span><span class="sxs-lookup"><span data-stu-id="f51b0-436">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName>                   | <span data-ttu-id="f51b0-437">Dočasný</span><span class="sxs-lookup"><span data-stu-id="f51b0-437">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger%601?displayProperty=fullName>                        | <span data-ttu-id="f51b0-438">Singleton</span><span class="sxs-lookup"><span data-stu-id="f51b0-438">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName>                     | <span data-ttu-id="f51b0-439">Singleton</span><span class="sxs-lookup"><span data-stu-id="f51b0-439">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName>              | <span data-ttu-id="f51b0-440">Singleton</span><span class="sxs-lookup"><span data-stu-id="f51b0-440">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions%601?displayProperty=fullName>              | <span data-ttu-id="f51b0-441">Dočasný</span><span class="sxs-lookup"><span data-stu-id="f51b0-441">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions%601?displayProperty=fullName>                       | <span data-ttu-id="f51b0-442">Singleton</span><span class="sxs-lookup"><span data-stu-id="f51b0-442">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName>                             | <span data-ttu-id="f51b0-443">Singleton</span><span class="sxs-lookup"><span data-stu-id="f51b0-443">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName>                           | <span data-ttu-id="f51b0-444">Singleton</span><span class="sxs-lookup"><span data-stu-id="f51b0-444">Singleton</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="f51b0-445">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="f51b0-445">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* [<span data-ttu-id="f51b0-446">NORWEGIAN Developers Conference se vzory konferencí pro vývoj aplikací pro DI</span><span class="sxs-lookup"><span data-stu-id="f51b0-446">NDC Conference Patterns for DI app development</span></span>](https://www.youtube.com/watch?v=x-C-CNBVTaY)
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="f51b0-447">Čtyři způsoby, jak vyřadit IDisposable v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f51b0-447">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="f51b0-448">Zápis čistého kódu v ASP.NET Core pomocí injektáže závislosti (MSDN)</span><span class="sxs-lookup"><span data-stu-id="f51b0-448">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="f51b0-449">Explicitní závislosti – princip</span><span class="sxs-lookup"><span data-stu-id="f51b0-449">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="f51b0-450">Inverze řídicích kontejnerů a vzor injektáže závislosti (Martin Fowlera)</span><span class="sxs-lookup"><span data-stu-id="f51b0-450">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="f51b0-451">Jak zaregistrovat službu s více rozhraními v ASP.NET Core DI</span><span class="sxs-lookup"><span data-stu-id="f51b0-451">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f51b0-452">[Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com)a [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="f51b0-452">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="f51b0-453">ASP.NET Core podporuje vzor návrhu softwaru pro vkládání závislostí (DI), což je technika pro dosažení [inverze ovládacího prvku (IOC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) mezi třídami a jejich závislostmi.</span><span class="sxs-lookup"><span data-stu-id="f51b0-453">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="f51b0-454">Další informace, které jsou specifické pro vkládání závislostí v rámci řadičů MVC, najdete v tématu <xref:mvc/controllers/dependency-injection> .</span><span class="sxs-lookup"><span data-stu-id="f51b0-454">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="f51b0-455">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f51b0-455">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="f51b0-456">Přehled injektáže závislosti</span><span class="sxs-lookup"><span data-stu-id="f51b0-456">Overview of dependency injection</span></span>

<span data-ttu-id="f51b0-457">*Závislost* je libovolný objekt, který vyžaduje jiný objekt.</span><span class="sxs-lookup"><span data-stu-id="f51b0-457">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="f51b0-458">Projděte si následující `MyDependency` třídu s `WriteMessage` metodou, na které jsou závislé jiné třídy v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="f51b0-458">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="f51b0-459">Instance `MyDependency` třídy může být vytvořena, aby byla `WriteMessage` Metoda k dispozici pro třídu.</span><span class="sxs-lookup"><span data-stu-id="f51b0-459">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="f51b0-460">`MyDependency`Třída je závislostí `IndexModel` třídy:</span><span class="sxs-lookup"><span data-stu-id="f51b0-460">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="f51b0-461">Třída vytvoří a přímo závisí na `MyDependency` instanci.</span><span class="sxs-lookup"><span data-stu-id="f51b0-461">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="f51b0-462">Závislosti kódu (například předchozí příklad) jsou problematické a je třeba se jim vyhnout z následujících důvodů:</span><span class="sxs-lookup"><span data-stu-id="f51b0-462">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="f51b0-463">Chcete-li nahradit `MyDependency` jinou implementací, třída musí být upravena.</span><span class="sxs-lookup"><span data-stu-id="f51b0-463">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="f51b0-464">Pokud `MyDependency` má závislosti, musí být nakonfigurovány třídou.</span><span class="sxs-lookup"><span data-stu-id="f51b0-464">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="f51b0-465">Ve velkém projektu s více třídami, v závislosti na tom `MyDependency` , je kód konfigurace rozptýlen napříč aplikací.</span><span class="sxs-lookup"><span data-stu-id="f51b0-465">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="f51b0-466">Tato implementace je obtížná pro testování částí.</span><span class="sxs-lookup"><span data-stu-id="f51b0-466">This implementation is difficult to unit test.</span></span> <span data-ttu-id="f51b0-467">Aplikace by měla používat třídu typu "nebo zástupné procedury" `MyDependency` , což není u tohoto přístupu možné.</span><span class="sxs-lookup"><span data-stu-id="f51b0-467">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="f51b0-468">Vkládání závislostí řeší tyto problémy prostřednictvím:</span><span class="sxs-lookup"><span data-stu-id="f51b0-468">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="f51b0-469">Použití rozhraní nebo základní třídy k abstrakci implementace závislosti.</span><span class="sxs-lookup"><span data-stu-id="f51b0-469">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="f51b0-470">Registrace závislosti v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="f51b0-470">Registration of the dependency in a service container.</span></span> <span data-ttu-id="f51b0-471">ASP.NET Core poskytuje integrovaný kontejner služeb <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="f51b0-471">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="f51b0-472">Služby jsou registrovány v metodě aplikace `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="f51b0-472">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="f51b0-473">*Vložení* služby do konstruktoru třídy, kde se používá.</span><span class="sxs-lookup"><span data-stu-id="f51b0-473">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="f51b0-474">Rozhraní přebírá zodpovědnost za vytvoření instance závislosti a její odstranění, když už ji nepotřebujete.</span><span class="sxs-lookup"><span data-stu-id="f51b0-474">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="f51b0-475">V [ukázkové aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) `IMyDependency` definuje rozhraní metodu, kterou služba poskytuje aplikaci:</span><span class="sxs-lookup"><span data-stu-id="f51b0-475">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="f51b0-476">Toto rozhraní je implementováno konkrétním typem `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="f51b0-476">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="f51b0-477">`MyDependency` vyžádá <xref:Microsoft.Extensions.Logging.ILogger`1> v jeho konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="f51b0-477">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="f51b0-478">Není neobvyklé používat vkládání závislostí v zřetězeném způsobem.</span><span class="sxs-lookup"><span data-stu-id="f51b0-478">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="f51b0-479">Každá požadovaná závislost zase vyžádá své vlastní závislosti.</span><span class="sxs-lookup"><span data-stu-id="f51b0-479">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="f51b0-480">Kontejner vyřeší závislosti v grafu a vrátí plně přeloženou službu.</span><span class="sxs-lookup"><span data-stu-id="f51b0-480">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="f51b0-481">Kolektivní sada závislostí, které je třeba vyřešit, se obvykle označuje jako *strom závislosti*, *graf závislosti*nebo *graf objektů*.</span><span class="sxs-lookup"><span data-stu-id="f51b0-481">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="f51b0-482">`IMyDependency` a `ILogger<TCategoryName>` musí být registrována v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="f51b0-482">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="f51b0-483">`IMyDependency` je zaregistrován v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="f51b0-483">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f51b0-484">`ILogger<TCategoryName>` je zaregistrované v infrastruktuře abstrakce protokolování, takže se jedná o [službu](#framework-provided-services) , která je zaregistrovaná ve výchozím rozhraní.</span><span class="sxs-lookup"><span data-stu-id="f51b0-484">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="f51b0-485">Kontejner se překládá `ILogger<TCategoryName>` tím, že využije [(Obecné) otevřené typy](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)a eliminuje nutnost registrace každého [(obecného) konstruovaného typu](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="f51b0-485">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="f51b0-486">V ukázkové aplikaci `IMyDependency` je služba zaregistrovaná u konkrétního typu `MyDependency` .</span><span class="sxs-lookup"><span data-stu-id="f51b0-486">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="f51b0-487">Registruje rozsah platnosti služby po dobu životnosti jediného požadavku.</span><span class="sxs-lookup"><span data-stu-id="f51b0-487">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="f51b0-488">[Doby platnosti služeb](#service-lifetimes) jsou popsány dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="f51b0-488">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="f51b0-489">Jednotlivé `services.Add{SERVICE_NAME}` metody rozšíření přidávají (a případně nakonfigurují) služby.</span><span class="sxs-lookup"><span data-stu-id="f51b0-489">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="f51b0-490">Například `services.AddMvc()` přidá Razor stránky služeb a MVC vyžaduje.</span><span class="sxs-lookup"><span data-stu-id="f51b0-490">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="f51b0-491">Doporučujeme, aby aplikace dodržovaly tuto konvenci.</span><span class="sxs-lookup"><span data-stu-id="f51b0-491">We recommended that apps follow this convention.</span></span> <span data-ttu-id="f51b0-492">Do oboru názvů [Microsoft. Extensions. DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) umístěte metody rozšíření pro zapouzdření skupin registrací služby.</span><span class="sxs-lookup"><span data-stu-id="f51b0-492">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="f51b0-493">Pokud konstruktor služby vyžaduje [vestavěný typ](/dotnet/csharp/language-reference/keywords/built-in-types-table), například `string` ,, může být typ vložen pomocí [Konfigurace](xref:fundamentals/configuration/index) nebo [vzoru možností](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="f51b0-493">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="f51b0-494">Instance služby je požadována prostřednictvím konstruktoru třídy, kde je služba používána a přiřazena k soukromému poli.</span><span class="sxs-lookup"><span data-stu-id="f51b0-494">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="f51b0-495">Pole se používá pro přístup ke službě podle potřeby v celé třídě.</span><span class="sxs-lookup"><span data-stu-id="f51b0-495">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="f51b0-496">V ukázkové aplikaci `IMyDependency` je instance vyžádána a používána pro volání `WriteMessage` metody služby:</span><span class="sxs-lookup"><span data-stu-id="f51b0-496">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="f51b0-497">Služby vložené do spuštění</span><span class="sxs-lookup"><span data-stu-id="f51b0-497">Services injected into Startup</span></span>

<span data-ttu-id="f51b0-498">`Startup`Při použití obecného hostitele () mohou být do konstruktoru vloženy pouze následující typy služeb <xref:Microsoft.Extensions.Hosting.IHostBuilder> :</span><span class="sxs-lookup"><span data-stu-id="f51b0-498">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="f51b0-499">Služby je možné vložit do `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="f51b0-499">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="f51b0-500">Další informace naleznete v tématu <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="f51b0-500">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="f51b0-501">Služby poskytované rozhraním</span><span class="sxs-lookup"><span data-stu-id="f51b0-501">Framework-provided services</span></span>

<span data-ttu-id="f51b0-502">`Startup.ConfigureServices`Metoda zodpovídá za definování služeb, které aplikace používá, včetně funkcí platformy, jako je například Entity Framework Core a ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="f51b0-502">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="f51b0-503">Zpočátku `IServiceCollection` `ConfigureServices` služba má služby definované rozhraním v závislosti na [tom, jak byl hostitel nakonfigurovaný](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="f51b0-503">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="f51b0-504">Pro aplikaci, která je založená na šabloně ASP.NET Core, není běžné, že mají stovky služeb zaregistrovaných v rámci rozhraní.</span><span class="sxs-lookup"><span data-stu-id="f51b0-504">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="f51b0-505">V následující tabulce je uvedena malá ukázka služeb registrovaných v rozhraní.</span><span class="sxs-lookup"><span data-stu-id="f51b0-505">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="f51b0-506">Typ služby</span><span class="sxs-lookup"><span data-stu-id="f51b0-506">Service Type</span></span> | <span data-ttu-id="f51b0-507">Doba platnosti</span><span class="sxs-lookup"><span data-stu-id="f51b0-507">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="f51b0-508">Dočasný</span><span class="sxs-lookup"><span data-stu-id="f51b0-508">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="f51b0-509">Singleton</span><span class="sxs-lookup"><span data-stu-id="f51b0-509">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="f51b0-510">Singleton</span><span class="sxs-lookup"><span data-stu-id="f51b0-510">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="f51b0-511">Singleton</span><span class="sxs-lookup"><span data-stu-id="f51b0-511">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="f51b0-512">Dočasný</span><span class="sxs-lookup"><span data-stu-id="f51b0-512">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="f51b0-513">Singleton</span><span class="sxs-lookup"><span data-stu-id="f51b0-513">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="f51b0-514">Dočasný</span><span class="sxs-lookup"><span data-stu-id="f51b0-514">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="f51b0-515">Singleton</span><span class="sxs-lookup"><span data-stu-id="f51b0-515">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="f51b0-516">Singleton</span><span class="sxs-lookup"><span data-stu-id="f51b0-516">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="f51b0-517">Singleton</span><span class="sxs-lookup"><span data-stu-id="f51b0-517">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="f51b0-518">Dočasný</span><span class="sxs-lookup"><span data-stu-id="f51b0-518">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="f51b0-519">Singleton</span><span class="sxs-lookup"><span data-stu-id="f51b0-519">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="f51b0-520">Singleton</span><span class="sxs-lookup"><span data-stu-id="f51b0-520">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="f51b0-521">Singleton</span><span class="sxs-lookup"><span data-stu-id="f51b0-521">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="f51b0-522">Registrace dalších služeb pomocí rozšiřujících metod</span><span class="sxs-lookup"><span data-stu-id="f51b0-522">Register additional services with extension methods</span></span>

<span data-ttu-id="f51b0-523">Pokud je k dispozici metoda rozšíření kolekce služeb pro registraci služby (a jejích závislých služeb, v případě potřeby), konvence používá jedinou `Add{SERVICE_NAME}` metodu rozšíření k registraci všech služeb vyžadovaných touto službou.</span><span class="sxs-lookup"><span data-stu-id="f51b0-523">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="f51b0-524">Následující kód je příkladem přidání dalších služeb do kontejneru pomocí metod rozšíření [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) a <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> :</span><span class="sxs-lookup"><span data-stu-id="f51b0-524">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="f51b0-525">Další informace najdete <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> ve třídě v dokumentaci k rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="f51b0-525">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="f51b0-526">Životnost služeb</span><span class="sxs-lookup"><span data-stu-id="f51b0-526">Service lifetimes</span></span>

<span data-ttu-id="f51b0-527">Vyberte odpovídající dobu života pro každou registrovanou službu.</span><span class="sxs-lookup"><span data-stu-id="f51b0-527">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="f51b0-528">Služby ASP.NET Core Services je možné nakonfigurovat s následujícími životnostmi:</span><span class="sxs-lookup"><span data-stu-id="f51b0-528">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="f51b0-529">Dočasný</span><span class="sxs-lookup"><span data-stu-id="f51b0-529">Transient</span></span>

<span data-ttu-id="f51b0-530">Služba přechodných životností ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*> ) se vytvoří pokaždé, když se požadují z kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="f51b0-530">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="f51b0-531">Tato životnost funguje nejlépe pro odlehčené bezstavové služby.</span><span class="sxs-lookup"><span data-stu-id="f51b0-531">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="f51b0-532">V aplikacích, které zpracovávají požadavky, jsou přechodné služby na konci žádosti zrušené.</span><span class="sxs-lookup"><span data-stu-id="f51b0-532">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="f51b0-533">Obor</span><span class="sxs-lookup"><span data-stu-id="f51b0-533">Scoped</span></span>

<span data-ttu-id="f51b0-534">Oborové služby () s rozsahem platnosti ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*> ) se vytvoří jednou za požadavek klienta (připojení).</span><span class="sxs-lookup"><span data-stu-id="f51b0-534">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="f51b0-535">V aplikacích, které zpracovávají požadavky, se vymezené služby na konci žádosti odstraní.</span><span class="sxs-lookup"><span data-stu-id="f51b0-535">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="f51b0-536">Při použití oboru služby v middlewaru založit službu do `Invoke` `InvokeAsync` metody nebo.</span><span class="sxs-lookup"><span data-stu-id="f51b0-536">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="f51b0-537">Nepoužívejte [vkládání pomocí injektáže konstruktoru](xref:mvc/controllers/dependency-injection#constructor-injection) , protože vynutí, aby se služba chovala jako typ singleton.</span><span class="sxs-lookup"><span data-stu-id="f51b0-537">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="f51b0-538">Další informace naleznete v tématu <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="f51b0-538">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="f51b0-539">Singleton</span><span class="sxs-lookup"><span data-stu-id="f51b0-539">Singleton</span></span>

<span data-ttu-id="f51b0-540">Služba singleton životnosti ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*> ) je vytvořena při prvním vyžádání (nebo při `Startup.ConfigureServices` spuštění a instance je určena pro registraci služby).</span><span class="sxs-lookup"><span data-stu-id="f51b0-540">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="f51b0-541">Každý další požadavek používá stejnou instanci.</span><span class="sxs-lookup"><span data-stu-id="f51b0-541">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="f51b0-542">Pokud aplikace vyžaduje chování singleton, doporučuje se povolit kontejneru služby spravovat dobu života služby.</span><span class="sxs-lookup"><span data-stu-id="f51b0-542">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="f51b0-543">Neimplementujte vzor návrhu singleton a poskytněte uživatelský kód pro správu životního cyklu objektu ve třídě.</span><span class="sxs-lookup"><span data-stu-id="f51b0-543">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="f51b0-544">V aplikacích, které zpracovávají požadavky, jsou nejednoznačné služby uvolněny, když <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> je uvolněna při vypnutí aplikace.</span><span class="sxs-lookup"><span data-stu-id="f51b0-544">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="f51b0-545">Není bezpečné přeložit oborovou službu z typu singleton.</span><span class="sxs-lookup"><span data-stu-id="f51b0-545">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="f51b0-546">Může dojít k tomu, že služba má při zpracování dalších požadavků špatný stav.</span><span class="sxs-lookup"><span data-stu-id="f51b0-546">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="f51b0-547">Metody registrace služby</span><span class="sxs-lookup"><span data-stu-id="f51b0-547">Service registration methods</span></span>

<span data-ttu-id="f51b0-548">Metody rozšíření registrace služby nabízejí přetížení, která jsou užitečná pro konkrétní scénáře.</span><span class="sxs-lookup"><span data-stu-id="f51b0-548">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="f51b0-549">Metoda</span><span class="sxs-lookup"><span data-stu-id="f51b0-549">Method</span></span> | <span data-ttu-id="f51b0-550">Automaticky</span><span class="sxs-lookup"><span data-stu-id="f51b0-550">Automatic</span></span><br><span data-ttu-id="f51b0-551">object</span><span class="sxs-lookup"><span data-stu-id="f51b0-551">object</span></span><br><span data-ttu-id="f51b0-552">odvod</span><span class="sxs-lookup"><span data-stu-id="f51b0-552">disposal</span></span> | <span data-ttu-id="f51b0-553">Několik</span><span class="sxs-lookup"><span data-stu-id="f51b0-553">Multiple</span></span><br><span data-ttu-id="f51b0-554">implementace</span><span class="sxs-lookup"><span data-stu-id="f51b0-554">implementations</span></span> | <span data-ttu-id="f51b0-555">Pass – argumenty</span><span class="sxs-lookup"><span data-stu-id="f51b0-555">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="f51b0-556">Příklad:</span><span class="sxs-lookup"><span data-stu-id="f51b0-556">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="f51b0-557">Ano</span><span class="sxs-lookup"><span data-stu-id="f51b0-557">Yes</span></span> | <span data-ttu-id="f51b0-558">Ano</span><span class="sxs-lookup"><span data-stu-id="f51b0-558">Yes</span></span> | <span data-ttu-id="f51b0-559">Ne</span><span class="sxs-lookup"><span data-stu-id="f51b0-559">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="f51b0-560">Příklady:</span><span class="sxs-lookup"><span data-stu-id="f51b0-560">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="f51b0-561">Ano</span><span class="sxs-lookup"><span data-stu-id="f51b0-561">Yes</span></span> | <span data-ttu-id="f51b0-562">Ano</span><span class="sxs-lookup"><span data-stu-id="f51b0-562">Yes</span></span> | <span data-ttu-id="f51b0-563">Ano</span><span class="sxs-lookup"><span data-stu-id="f51b0-563">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="f51b0-564">Příklad:</span><span class="sxs-lookup"><span data-stu-id="f51b0-564">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="f51b0-565">Ano</span><span class="sxs-lookup"><span data-stu-id="f51b0-565">Yes</span></span> | <span data-ttu-id="f51b0-566">Ne</span><span class="sxs-lookup"><span data-stu-id="f51b0-566">No</span></span> | <span data-ttu-id="f51b0-567">Ne</span><span class="sxs-lookup"><span data-stu-id="f51b0-567">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="f51b0-568">Příklady:</span><span class="sxs-lookup"><span data-stu-id="f51b0-568">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="f51b0-569">Ne</span><span class="sxs-lookup"><span data-stu-id="f51b0-569">No</span></span> | <span data-ttu-id="f51b0-570">Ano</span><span class="sxs-lookup"><span data-stu-id="f51b0-570">Yes</span></span> | <span data-ttu-id="f51b0-571">Ano</span><span class="sxs-lookup"><span data-stu-id="f51b0-571">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="f51b0-572">Příklady:</span><span class="sxs-lookup"><span data-stu-id="f51b0-572">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="f51b0-573">Ne</span><span class="sxs-lookup"><span data-stu-id="f51b0-573">No</span></span> | <span data-ttu-id="f51b0-574">Ne</span><span class="sxs-lookup"><span data-stu-id="f51b0-574">No</span></span> | <span data-ttu-id="f51b0-575">Ano</span><span class="sxs-lookup"><span data-stu-id="f51b0-575">Yes</span></span> |

<span data-ttu-id="f51b0-576">Další informace o vyřazení typů najdete v části věnované [vyřazení služeb](#disposal-of-services) .</span><span class="sxs-lookup"><span data-stu-id="f51b0-576">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="f51b0-577">Běžným scénářem pro více implementací je vytvoření [typů pro testování](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="f51b0-577">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="f51b0-578">`TryAdd{LIFETIME}` metody registrují službu jenom v případě, že už není zaregistrovaná implementace.</span><span class="sxs-lookup"><span data-stu-id="f51b0-578">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="f51b0-579">V následujícím příkladu se první řádek registruje `MyDependency` pro `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="f51b0-579">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="f51b0-580">Druhý řádek nemá žádný účinek, protože `IMyDependency` už má registrovanou implementaci:</span><span class="sxs-lookup"><span data-stu-id="f51b0-580">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="f51b0-581">Další informace naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="f51b0-581">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="f51b0-582">Metody [TryAddEnumerable (ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) registrují službu pouze v případě, že již není implementace *stejného typu*.</span><span class="sxs-lookup"><span data-stu-id="f51b0-582">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="f51b0-583">Několik služeb je vyřešeno prostřednictvím `IEnumerable<{SERVICE}>` .</span><span class="sxs-lookup"><span data-stu-id="f51b0-583">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="f51b0-584">Při registraci služeb chce vývojář přidat instanci jenom v případě, že už jeden ze stejného typu není přidaný.</span><span class="sxs-lookup"><span data-stu-id="f51b0-584">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="f51b0-585">Obecně je tato metoda používána autory knihovny k tomu, aby nedocházelo k registraci dvou kopií instance v kontejneru.</span><span class="sxs-lookup"><span data-stu-id="f51b0-585">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="f51b0-586">V následujícím příkladu se první řádek registruje `MyDep` pro `IMyDep1` .</span><span class="sxs-lookup"><span data-stu-id="f51b0-586">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="f51b0-587">Druhý řádek se registruje `MyDep` pro `IMyDep2` .</span><span class="sxs-lookup"><span data-stu-id="f51b0-587">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="f51b0-588">Třetí řádek nemá žádný vliv, protože `IMyDep1` už má registrovanou implementaci `MyDep` :</span><span class="sxs-lookup"><span data-stu-id="f51b0-588">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="f51b0-589">Chování injektáže konstruktoru</span><span class="sxs-lookup"><span data-stu-id="f51b0-589">Constructor injection behavior</span></span>

<span data-ttu-id="f51b0-590">Služby je možné vyřešit pomocí dvou mechanismů:</span><span class="sxs-lookup"><span data-stu-id="f51b0-590">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="f51b0-591"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Povoluje vytvoření objektu bez registrace služby v kontejneru vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="f51b0-591"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="f51b0-592">`ActivatorUtilities` se používá s uživateli s přístupem k uživatelům, jako jsou například rutiny značek, řadiče MVC a pořadače modelů.</span><span class="sxs-lookup"><span data-stu-id="f51b0-592">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="f51b0-593">Konstruktory mohou přijímat argumenty, které nejsou poskytovány vkládáním závislostí, ale argumenty musí přiřadit výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="f51b0-593">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="f51b0-594">Když jsou služby vyřešeny `IServiceProvider` nebo `ActivatorUtilities` , [Injektáže konstruktoru](xref:mvc/controllers/dependency-injection#constructor-injection) vyžaduje *veřejný* konstruktor.</span><span class="sxs-lookup"><span data-stu-id="f51b0-594">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="f51b0-595">Když jsou služby vyřešeny nástrojem `ActivatorUtilities` , [Injektáže konstruktoru](xref:mvc/controllers/dependency-injection#constructor-injection) vyžaduje, aby existoval pouze jeden příslušný konstruktor.</span><span class="sxs-lookup"><span data-stu-id="f51b0-595">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="f51b0-596">Přetížení konstruktoru jsou podporovaná, ale může existovat jenom jedno přetížení, jehož argumenty můžou být splněné vkládáním závislostí.</span><span class="sxs-lookup"><span data-stu-id="f51b0-596">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="f51b0-597">Entity Framework kontexty</span><span class="sxs-lookup"><span data-stu-id="f51b0-597">Entity Framework contexts</span></span>

<span data-ttu-id="f51b0-598">Entity Framework kontexty se obvykle přidávají do kontejneru služby pomocí [rozsahu životnosti](#service-lifetimes) , protože operace databáze webové aplikace jsou obvykle vymezeny na požadavky klienta.</span><span class="sxs-lookup"><span data-stu-id="f51b0-598">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="f51b0-599">Výchozí doba života je vymezena v případě, že při registraci kontextu databáze není zadána doba života při [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) přetížení.</span><span class="sxs-lookup"><span data-stu-id="f51b0-599">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="f51b0-600">Služby pro danou dobu života by neměly používat kontext databáze s kratší dobou životnosti než služba.</span><span class="sxs-lookup"><span data-stu-id="f51b0-600">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="f51b0-601">Možnosti života a registrace</span><span class="sxs-lookup"><span data-stu-id="f51b0-601">Lifetime and registration options</span></span>

<span data-ttu-id="f51b0-602">Chcete-li předvést rozdíl mezi možnostmi životního cyklu a registrací, zvažte následující rozhraní, která představují úlohy jako operaci s jedinečným identifikátorem `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="f51b0-602">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="f51b0-603">V závislosti na tom, jak je životnost provozní služby nakonfigurována pro následující rozhraní, kontejner poskytuje buď stejnou nebo jinou instanci služby, když je vyžádána třídou:</span><span class="sxs-lookup"><span data-stu-id="f51b0-603">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="f51b0-604">Rozhraní jsou implementována ve `Operation` třídě.</span><span class="sxs-lookup"><span data-stu-id="f51b0-604">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="f51b0-605">`Operation`Konstruktor generuje identifikátor GUID, pokud není dodán:</span><span class="sxs-lookup"><span data-stu-id="f51b0-605">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="f51b0-606">`OperationService`Je zaregistrován, který závisí na každém z ostatních `Operation` typů.</span><span class="sxs-lookup"><span data-stu-id="f51b0-606">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="f51b0-607">Když `OperationService` je požadavek proveden prostřednictvím injektáže závislosti, obdrží buď novou instanci každé služby, nebo existující instanci na základě životnosti závislé služby.</span><span class="sxs-lookup"><span data-stu-id="f51b0-607">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="f51b0-608">Když se v případě vyžádání z kontejneru vytvoří přechodné služby, `OperationId` liší se od `IOperationTransient` služby `OperationId` `OperationService` .</span><span class="sxs-lookup"><span data-stu-id="f51b0-608">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="f51b0-609">`OperationService` přijme novou instanci `IOperationTransient` třídy.</span><span class="sxs-lookup"><span data-stu-id="f51b0-609">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="f51b0-610">Nová instance vrací jinou instanci `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="f51b0-610">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="f51b0-611">Když se vytvoří vymezené služby pro jednotlivé požadavky klienta, `OperationId` `IOperationScoped` služba je stejná jako `OperationService` v rámci žádosti klienta.</span><span class="sxs-lookup"><span data-stu-id="f51b0-611">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="f51b0-612">V rámci požadavků klientů obě služby sdílejí jinou `OperationId` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="f51b0-612">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="f51b0-613">Pokud se služby instance singleton a singleton vytvoří jednou a použije se pro všechny požadavky klientů a všechny služby, `OperationId` je v rámci všech žádostí o službu konstantní.</span><span class="sxs-lookup"><span data-stu-id="f51b0-613">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="f51b0-614">V `Startup.ConfigureServices` je každý typ přidán do kontejneru podle jeho pojmenované životnosti:</span><span class="sxs-lookup"><span data-stu-id="f51b0-614">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="f51b0-615">`IOperationSingletonInstance`Služba používá konkrétní instanci se známým ID `Guid.Empty` .</span><span class="sxs-lookup"><span data-stu-id="f51b0-615">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="f51b0-616">Je jasné, že pokud se tento typ používá (jeho identifikátor GUID je všechny nuly).</span><span class="sxs-lookup"><span data-stu-id="f51b0-616">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="f51b0-617">Ukázková aplikace ukazuje dobu života objektu v rámci a mezi jednotlivými požadavky.</span><span class="sxs-lookup"><span data-stu-id="f51b0-617">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="f51b0-618">Ukázková aplikace `IndexModel` požaduje každý druh `IOperation` typu a `OperationService` .</span><span class="sxs-lookup"><span data-stu-id="f51b0-618">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="f51b0-619">Stránka potom zobrazí všechny hodnoty třídy modelu stránky a `OperationId` hodnoty služby prostřednictvím přiřazení vlastností:</span><span class="sxs-lookup"><span data-stu-id="f51b0-619">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="f51b0-620">Výsledkem dvou následujících výstupů jsou výsledky dvou požadavků:</span><span class="sxs-lookup"><span data-stu-id="f51b0-620">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="f51b0-621">**První požadavek:**</span><span class="sxs-lookup"><span data-stu-id="f51b0-621">**First request:**</span></span>

<span data-ttu-id="f51b0-622">Operace kontroleru:</span><span class="sxs-lookup"><span data-stu-id="f51b0-622">Controller operations:</span></span>

<span data-ttu-id="f51b0-623">Přechodný: d233e165-f417-469b-A866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="f51b0-623">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="f51b0-624">Vymezené: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="f51b0-624">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="f51b0-625">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="f51b0-625">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="f51b0-626">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="f51b0-626">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="f51b0-627">`OperationService` Operations</span><span class="sxs-lookup"><span data-stu-id="f51b0-627">`OperationService` operations:</span></span>

<span data-ttu-id="f51b0-628">Přechodný: c6b049eb-1318-4E31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="f51b0-628">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="f51b0-629">Vymezené: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="f51b0-629">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="f51b0-630">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="f51b0-630">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="f51b0-631">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="f51b0-631">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="f51b0-632">**Druhá žádost:**</span><span class="sxs-lookup"><span data-stu-id="f51b0-632">**Second request:**</span></span>

<span data-ttu-id="f51b0-633">Operace kontroleru:</span><span class="sxs-lookup"><span data-stu-id="f51b0-633">Controller operations:</span></span>

<span data-ttu-id="f51b0-634">Přechodný: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="f51b0-634">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="f51b0-635">Vymezené: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="f51b0-635">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="f51b0-636">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="f51b0-636">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="f51b0-637">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="f51b0-637">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="f51b0-638">`OperationService` Operations</span><span class="sxs-lookup"><span data-stu-id="f51b0-638">`OperationService` operations:</span></span>

<span data-ttu-id="f51b0-639">Přechodný: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="f51b0-639">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="f51b0-640">Vymezené: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="f51b0-640">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="f51b0-641">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="f51b0-641">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="f51b0-642">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="f51b0-642">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="f51b0-643">Pozor, které `OperationId` hodnoty se liší v rámci požadavku a mezi požadavky:</span><span class="sxs-lookup"><span data-stu-id="f51b0-643">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="f51b0-644">*Přechodné* objekty jsou vždy odlišné.</span><span class="sxs-lookup"><span data-stu-id="f51b0-644">*Transient* objects are always different.</span></span> <span data-ttu-id="f51b0-645">Přechodná `OperationId` hodnota pro první i druhý požadavek klienta se liší pro obě `OperationService` operace i mezi požadavky klientů.</span><span class="sxs-lookup"><span data-stu-id="f51b0-645">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="f51b0-646">Každé žádosti o službu a žádosti klienta se poskytne nová instance.</span><span class="sxs-lookup"><span data-stu-id="f51b0-646">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="f51b0-647">*Vymezené* objekty jsou v požadavku klienta stejné, ale v rámci požadavků klientů se liší.</span><span class="sxs-lookup"><span data-stu-id="f51b0-647">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="f51b0-648">Objekty *singleton* jsou stejné pro každý objekt a každý požadavek bez ohledu na to, zda `Operation` je instance uvedena v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="f51b0-648">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="f51b0-649">Volat služby z Main</span><span class="sxs-lookup"><span data-stu-id="f51b0-649">Call services from main</span></span>

<span data-ttu-id="f51b0-650">Vytvořte <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> pomocí [IServiceScopeFactory. CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) pro řešení oboru služby v rámci rozsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="f51b0-650">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="f51b0-651">Tento přístup je užitečný pro přístup k oboruované službě při spuštění za účelem spouštění úloh inicializace.</span><span class="sxs-lookup"><span data-stu-id="f51b0-651">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="f51b0-652">Následující příklad ukazuje, jak získat kontext pro `MyScopedService` `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="f51b0-652">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="f51b0-653">Ověřování oboru</span><span class="sxs-lookup"><span data-stu-id="f51b0-653">Scope validation</span></span>

<span data-ttu-id="f51b0-654">Když je aplikace spuštěná ve vývojovém prostředí, provede výchozí poskytovatel služeb kontroly, aby ověřil, že:</span><span class="sxs-lookup"><span data-stu-id="f51b0-654">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="f51b0-655">Oborové služby nejsou přímo nebo nepřímo vyřešeny od poskytovatele kořenové služby.</span><span class="sxs-lookup"><span data-stu-id="f51b0-655">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="f51b0-656">Oborové služby nejsou přímo nebo nepřímo vložené do singleton.</span><span class="sxs-lookup"><span data-stu-id="f51b0-656">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="f51b0-657">Poskytovatel kořenové služby se vytvoří, když <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> se zavolá.</span><span class="sxs-lookup"><span data-stu-id="f51b0-657">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="f51b0-658">Doba života poskytovatele kořenové služby odpovídá době životnosti aplikace nebo serveru, když se poskytovatel spustí s aplikací a je uvolněný při ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="f51b0-658">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="f51b0-659">Oborové služby jsou uvolněny kontejnerem, který je vytvořil.</span><span class="sxs-lookup"><span data-stu-id="f51b0-659">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="f51b0-660">Pokud je v kořenovém kontejneru vytvořena vymezená služba, doba života služby je efektivně povýšena na typ singleton, protože je uvolněna pouze kořenovým kontejnerem při vypnutí aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="f51b0-660">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="f51b0-661">Při ověřování oborů služeb se tyto situace zachytí `BuildServiceProvider` .</span><span class="sxs-lookup"><span data-stu-id="f51b0-661">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="f51b0-662">Další informace naleznete v tématu <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="f51b0-662">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>   

## <a name="request-services"></a><span data-ttu-id="f51b0-663">Žádosti o služby</span><span class="sxs-lookup"><span data-stu-id="f51b0-663">Request Services</span></span>

<span data-ttu-id="f51b0-664">Služby, které jsou k dispozici v rámci žádosti o ASP.NET Core, `HttpContext` jsou zpřístupněny prostřednictvím kolekce [HttpContext. RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) .</span><span class="sxs-lookup"><span data-stu-id="f51b0-664">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="f51b0-665">Žádosti o služby reprezentují služby nakonfigurované a požadované v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="f51b0-665">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="f51b0-666">Když objekty určují závislosti, jsou splněny typy nalezenými v `RequestServices` , ne `ApplicationServices` .</span><span class="sxs-lookup"><span data-stu-id="f51b0-666">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="f51b0-667">Obecně platí, že aplikace by tyto vlastnosti neměla používat přímo.</span><span class="sxs-lookup"><span data-stu-id="f51b0-667">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="f51b0-668">Místo toho požádejte o typy, které třídy vyžadují prostřednictvím konstruktorů třídy, a umožněte rozhraní vložit závislosti.</span><span class="sxs-lookup"><span data-stu-id="f51b0-668">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="f51b0-669">To poskytuje třídy, které jsou snáze testovány.</span><span class="sxs-lookup"><span data-stu-id="f51b0-669">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="f51b0-670">Preferovat požadavky na závislosti jako parametry konstruktoru pro přístup ke `RequestServices` kolekci.</span><span class="sxs-lookup"><span data-stu-id="f51b0-670">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="f51b0-671">Navrhnout služby pro vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="f51b0-671">Design services for dependency injection</span></span>

<span data-ttu-id="f51b0-672">Osvědčené postupy:</span><span class="sxs-lookup"><span data-stu-id="f51b0-672">Best practices are to:</span></span>

* <span data-ttu-id="f51b0-673">Navrhněte služby pro použití injektáže závislosti k získání jejich závislostí.</span><span class="sxs-lookup"><span data-stu-id="f51b0-673">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="f51b0-674">Vyhněte se stavovým, statickým třídám a členům.</span><span class="sxs-lookup"><span data-stu-id="f51b0-674">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="f51b0-675">Navrhněte aplikace, aby místo toho používaly služby s jedním prvkem, což se vyhne vytváření globálního stavu.</span><span class="sxs-lookup"><span data-stu-id="f51b0-675">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="f51b0-676">Vyhněte se přímému vytváření instancí závislých tříd v rámci služeb.</span><span class="sxs-lookup"><span data-stu-id="f51b0-676">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="f51b0-677">Přímá instance Couples kód na konkrétní implementaci.</span><span class="sxs-lookup"><span data-stu-id="f51b0-677">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="f51b0-678">Vytvářejte třídy aplikací malými, dobře faktoringně a snadno testované.</span><span class="sxs-lookup"><span data-stu-id="f51b0-678">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="f51b0-679">Pokud se zdá, že třída má příliš mnoho vložených závislostí, obvykle se jedná o znaménko, že třída má příliš mnoho zodpovědnosti a je v rozporu s [principem jediné zodpovědnosti (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="f51b0-679">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="f51b0-680">Pokuste se Refaktorovat třídu přesunutím některých odpovědností do nové třídy.</span><span class="sxs-lookup"><span data-stu-id="f51b0-680">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="f51b0-681">Mějte na paměti, že Razor stránky tříd modelu stránky a třídy KONTROLERU MVC by se měly zaměřit na uživatelské rozhraní.</span><span class="sxs-lookup"><span data-stu-id="f51b0-681">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="f51b0-682">Obchodní pravidla a podrobnosti implementace přístupu k datům by měly být uchovávány ve třídách, které jsou vhodné pro tyto [samostatné obavy](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="f51b0-682">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="f51b0-683">Vyřazení služeb</span><span class="sxs-lookup"><span data-stu-id="f51b0-683">Disposal of services</span></span>

<span data-ttu-id="f51b0-684">Kontejner volá <xref:System.IDisposable.Dispose*> typy, které <xref:System.IDisposable> vytvoří.</span><span class="sxs-lookup"><span data-stu-id="f51b0-684">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="f51b0-685">Pokud je instance přidána do kontejneru pomocí uživatelského kódu, nebude uvolněna automaticky.</span><span class="sxs-lookup"><span data-stu-id="f51b0-685">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="f51b0-686">V následujícím příkladu jsou služby vytvořeny pomocí kontejneru služby a automaticky odstraněny:</span><span class="sxs-lookup"><span data-stu-id="f51b0-686">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="f51b0-687">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="f51b0-687">In the following example:</span></span>

* <span data-ttu-id="f51b0-688">Instance služby nejsou vytvořeny kontejnerem služby.</span><span class="sxs-lookup"><span data-stu-id="f51b0-688">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="f51b0-689">Zamýšlené životnosti služeb nejsou rozhraním známy.</span><span class="sxs-lookup"><span data-stu-id="f51b0-689">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="f51b0-690">Rozhraní neodstraní služby automaticky.</span><span class="sxs-lookup"><span data-stu-id="f51b0-690">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="f51b0-691">Pokud se služby explicitně neodstraní v kódu pro vývojáře, zůstanou zachovány, dokud se aplikace neukončí.</span><span class="sxs-lookup"><span data-stu-id="f51b0-691">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="f51b0-692">Doprovodné materiály k rozhraní IDisposable pro přechodné a sdílené instance</span><span class="sxs-lookup"><span data-stu-id="f51b0-692">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="f51b0-693">Přechodná, omezená doba života</span><span class="sxs-lookup"><span data-stu-id="f51b0-693">Transient, limited lifetime</span></span>

<span data-ttu-id="f51b0-694">**Scénář**</span><span class="sxs-lookup"><span data-stu-id="f51b0-694">**Scenario**</span></span>

<span data-ttu-id="f51b0-695">Aplikace vyžaduje <xref:System.IDisposable> instanci s přechodným životností pro některý z následujících scénářů:</span><span class="sxs-lookup"><span data-stu-id="f51b0-695">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="f51b0-696">Instance je vyřešena v kořenovém oboru.</span><span class="sxs-lookup"><span data-stu-id="f51b0-696">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="f51b0-697">Instance by měla být uvolněna před ukončením oboru.</span><span class="sxs-lookup"><span data-stu-id="f51b0-697">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="f51b0-698">**Řešení**</span><span class="sxs-lookup"><span data-stu-id="f51b0-698">**Solution**</span></span>

<span data-ttu-id="f51b0-699">Pomocí modelu továrny vytvořte instanci mimo nadřazený obor.</span><span class="sxs-lookup"><span data-stu-id="f51b0-699">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="f51b0-700">V takové situaci by aplikace měla obvykle `Create` metodu, která volá přímo konstruktor finálního typu.</span><span class="sxs-lookup"><span data-stu-id="f51b0-700">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="f51b0-701">Pokud má konečný typ jiné závislosti, továrna může:</span><span class="sxs-lookup"><span data-stu-id="f51b0-701">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="f51b0-702">Přijmout <xref:System.IServiceProvider> ve svém konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="f51b0-702">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="f51b0-703">Slouží <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> k vytvoření instance instance mimo kontejner, při použití kontejneru pro jeho závislosti.</span><span class="sxs-lookup"><span data-stu-id="f51b0-703">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="f51b0-704">Sdílená instance, omezená doba života</span><span class="sxs-lookup"><span data-stu-id="f51b0-704">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="f51b0-705">**Scénář**</span><span class="sxs-lookup"><span data-stu-id="f51b0-705">**Scenario**</span></span>

<span data-ttu-id="f51b0-706">Aplikace vyžaduje sdílenou <xref:System.IDisposable> instanci napříč více službami, ale <xref:System.IDisposable> měla by mít omezené trvání.</span><span class="sxs-lookup"><span data-stu-id="f51b0-706">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="f51b0-707">**Řešení**</span><span class="sxs-lookup"><span data-stu-id="f51b0-707">**Solution**</span></span>

<span data-ttu-id="f51b0-708">Zaregistrujte instanci s vymezenou životností.</span><span class="sxs-lookup"><span data-stu-id="f51b0-708">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="f51b0-709">Použijte <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> k zahájení a vytvoření nového <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> .</span><span class="sxs-lookup"><span data-stu-id="f51b0-709">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="f51b0-710"><xref:System.IServiceProvider>Požadované služby získáte pomocí tohoto oboru.</span><span class="sxs-lookup"><span data-stu-id="f51b0-710">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="f51b0-711">Vyřadit rozsah, pokud by životnost měla končit.</span><span class="sxs-lookup"><span data-stu-id="f51b0-711">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="f51b0-712">Obecné pokyny</span><span class="sxs-lookup"><span data-stu-id="f51b0-712">General Guidelines</span></span>

* <span data-ttu-id="f51b0-713">Neregistrujte <xref:System.IDisposable> instance s přechodným oborem.</span><span class="sxs-lookup"><span data-stu-id="f51b0-713">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="f51b0-714">Místo toho použijte výrobní model.</span><span class="sxs-lookup"><span data-stu-id="f51b0-714">Use the factory pattern instead.</span></span>
* <span data-ttu-id="f51b0-715">V kořenovém oboru neřešte přechodné nebo vymezené <xref:System.IDisposable> instance.</span><span class="sxs-lookup"><span data-stu-id="f51b0-715">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="f51b0-716">Jedinou obecnou výjimkou je, když aplikace vytvoří nebo znovu vytvoří a odstraní <xref:System.IServiceProvider> , což není ideální vzor.</span><span class="sxs-lookup"><span data-stu-id="f51b0-716">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="f51b0-717">Příjem <xref:System.IDisposable> závislosti přes di nevyžaduje, aby se příjemce sám implementoval <xref:System.IDisposable> .</span><span class="sxs-lookup"><span data-stu-id="f51b0-717">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="f51b0-718">Příjemce <xref:System.IDisposable> závislosti by neměl volat <xref:System.IDisposable.Dispose%2A> tuto závislost.</span><span class="sxs-lookup"><span data-stu-id="f51b0-718">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="f51b0-719">Obory by měly sloužit k řízení životnosti služeb.</span><span class="sxs-lookup"><span data-stu-id="f51b0-719">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="f51b0-720">Obory nejsou hierarchické a mezi obory není žádné zvláštní připojení.</span><span class="sxs-lookup"><span data-stu-id="f51b0-720">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="f51b0-721">Výchozí nahrazení kontejneru služby</span><span class="sxs-lookup"><span data-stu-id="f51b0-721">Default service container replacement</span></span>

<span data-ttu-id="f51b0-722">Integrovaný kontejner služeb je navržený tak, aby sloužil potřebám architektury a většině spotřebitelských aplikací.</span><span class="sxs-lookup"><span data-stu-id="f51b0-722">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="f51b0-723">Pokud nepotřebujete určitou funkci, kterou integrovaný kontejner nepodporuje, doporučujeme použít vestavěný kontejner, například:</span><span class="sxs-lookup"><span data-stu-id="f51b0-723">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="f51b0-724">Vkládání vlastností</span><span class="sxs-lookup"><span data-stu-id="f51b0-724">Property injection</span></span>
* <span data-ttu-id="f51b0-725">Vkládání na základě názvu</span><span class="sxs-lookup"><span data-stu-id="f51b0-725">Injection based on name</span></span>
* <span data-ttu-id="f51b0-726">Podřízené kontejnery</span><span class="sxs-lookup"><span data-stu-id="f51b0-726">Child containers</span></span>
* <span data-ttu-id="f51b0-727">Vlastní Správa životního cyklu</span><span class="sxs-lookup"><span data-stu-id="f51b0-727">Custom lifetime management</span></span>
* <span data-ttu-id="f51b0-728">`Func<T>` Podpora opožděné inicializace</span><span class="sxs-lookup"><span data-stu-id="f51b0-728">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="f51b0-729">Registrace založená na konvencích</span><span class="sxs-lookup"><span data-stu-id="f51b0-729">Convention-based registration</span></span>

<span data-ttu-id="f51b0-730">U ASP.NET Corech aplikací lze použít následující kontejnery třetích stran:</span><span class="sxs-lookup"><span data-stu-id="f51b0-730">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="f51b0-731">Autofac</span><span class="sxs-lookup"><span data-stu-id="f51b0-731">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="f51b0-732">DryIoc</span><span class="sxs-lookup"><span data-stu-id="f51b0-732">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="f51b0-733">Integrit</span><span class="sxs-lookup"><span data-stu-id="f51b0-733">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="f51b0-734">LightInject</span><span class="sxs-lookup"><span data-stu-id="f51b0-734">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="f51b0-735">Lamar</span><span class="sxs-lookup"><span data-stu-id="f51b0-735">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="f51b0-736">Stashbox</span><span class="sxs-lookup"><span data-stu-id="f51b0-736">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="f51b0-737">Unity</span><span class="sxs-lookup"><span data-stu-id="f51b0-737">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="f51b0-738">Bezpečnost vlákna</span><span class="sxs-lookup"><span data-stu-id="f51b0-738">Thread safety</span></span>

<span data-ttu-id="f51b0-739">Vytvořte služby, které jsou v bezpečí pro přístup z více vláken.</span><span class="sxs-lookup"><span data-stu-id="f51b0-739">Create thread-safe singleton services.</span></span> <span data-ttu-id="f51b0-740">Pokud má služba typu Singleton závislost na přechodné službě, přechodná služba může také vyžadovat zabezpečení vlákna v závislosti na tom, jak je používá typ singleton.</span><span class="sxs-lookup"><span data-stu-id="f51b0-740">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="f51b0-741">Výrobní metoda samostatné služby, jako je například druhý argument pro [AddSingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), nemusí být bezpečná pro přístup z více vláken.</span><span class="sxs-lookup"><span data-stu-id="f51b0-741">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="f51b0-742">Podobně jako konstruktor typu ( `static` ), je zaručeno, že bude volán jednou vláknem.</span><span class="sxs-lookup"><span data-stu-id="f51b0-742">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="f51b0-743">Doporučení</span><span class="sxs-lookup"><span data-stu-id="f51b0-743">Recommendations</span></span>

* <span data-ttu-id="f51b0-744">`async/await``Task`řešení služeb a se nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="f51b0-744">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="f51b0-745">Jazyk C# nepodporuje asynchronní konstruktory; Proto je doporučeným vzorem použití asynchronních metod po synchronním překladu služby.</span><span class="sxs-lookup"><span data-stu-id="f51b0-745">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="f51b0-746">Neukládejte data a konfiguraci přímo do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="f51b0-746">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="f51b0-747">Například nákupní košík uživatele by neměl být obvykle přidán do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="f51b0-747">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="f51b0-748">Konfigurace by měla používat [vzor možností](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="f51b0-748">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="f51b0-749">Podobně nepoužívejte objekty "držitel dat", které existují pouze k povolení přístupu k jinému objektu.</span><span class="sxs-lookup"><span data-stu-id="f51b0-749">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="f51b0-750">Je lepší žádat o skutečnou položku přes DI.</span><span class="sxs-lookup"><span data-stu-id="f51b0-750">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="f51b0-751">Vyhněte se statickému přístupu ke službám.</span><span class="sxs-lookup"><span data-stu-id="f51b0-751">Avoid static access to services.</span></span> <span data-ttu-id="f51b0-752">Vyhněte se například statickému psaní [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) pro použití jinde.</span><span class="sxs-lookup"><span data-stu-id="f51b0-752">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere.</span></span>

* <span data-ttu-id="f51b0-753">Vyhněte se použití *vzoru lokátoru služby*, který kombinuje [inverzi strategií řízení](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .</span><span class="sxs-lookup"><span data-stu-id="f51b0-753">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
  * <span data-ttu-id="f51b0-754">Nevolejte <xref:System.IServiceProvider.GetService*> pro získání instance služby, když můžete místo toho použít di:</span><span class="sxs-lookup"><span data-stu-id="f51b0-754">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="f51b0-755">**Nesprávně:**</span><span class="sxs-lookup"><span data-stu-id="f51b0-755">**Incorrect:**</span></span>

    ```csharp
    public class MyClass()
   
      public void MyMethod()
      {
          var optionsMonitor = 
              _services.GetService<IOptionsMonitor<MyOptions>>();
          var option = optionsMonitor.CurrentValue.Option;
   
          ...
      }
      ```
   
    <span data-ttu-id="f51b0-756">**Správné**:</span><span class="sxs-lookup"><span data-stu-id="f51b0-756">**Correct**:</span></span>

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

* <span data-ttu-id="f51b0-757">Vyhněte se vkládání továrny, která řeší závislosti za běhu pomocí <xref:System.IServiceProvider.GetService*> .</span><span class="sxs-lookup"><span data-stu-id="f51b0-757">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>
* <span data-ttu-id="f51b0-758">Vyhněte se statickému přístupu k `HttpContext` (například [IHttpContextAccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="f51b0-758">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="f51b0-759">Podobně jako u všech sad doporučení se může stát, že se vyžaduje ignorování doporučení.</span><span class="sxs-lookup"><span data-stu-id="f51b0-759">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="f51b0-760">Výjimky jsou zřídka, většinou zvláštní případy v rámci samotného rozhraní.</span><span class="sxs-lookup"><span data-stu-id="f51b0-760">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="f51b0-761">DI je *alternativou* ke vzorům statických nebo globálních přístupů k objektům.</span><span class="sxs-lookup"><span data-stu-id="f51b0-761">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="f51b0-762">Je možné, že nebudete moci využít výhody DI, pokud je kombinujete se statickým přístupem k objektům.</span><span class="sxs-lookup"><span data-stu-id="f51b0-762">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f51b0-763">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="f51b0-763">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="f51b0-764">Čtyři způsoby, jak vyřadit IDisposable v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f51b0-764">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="f51b0-765">Zápis čistého kódu v ASP.NET Core pomocí injektáže závislosti (MSDN)</span><span class="sxs-lookup"><span data-stu-id="f51b0-765">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="f51b0-766">Explicitní závislosti – princip</span><span class="sxs-lookup"><span data-stu-id="f51b0-766">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="f51b0-767">Inverze řídicích kontejnerů a vzor injektáže závislosti (Martin Fowlera)</span><span class="sxs-lookup"><span data-stu-id="f51b0-767">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="f51b0-768">Jak zaregistrovat službu s více rozhraními v ASP.NET Core DI</span><span class="sxs-lookup"><span data-stu-id="f51b0-768">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

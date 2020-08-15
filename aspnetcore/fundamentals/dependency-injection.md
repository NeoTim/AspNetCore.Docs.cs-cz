---
title: Injektáž závislostí v ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak ASP.NET Core implementuje vkládání závislostí a jak ho používat.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/21/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/dependency-injection
ms.openlocfilehash: b0ba7c7598df13413c00934a30e03681129de98a
ms.sourcegitcommit: 503b348e9046fcd969de85898394a1ea8274ec38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/14/2020
ms.locfileid: "88227576"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="4b62d-103">Injektáž závislostí v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4b62d-103">Dependency injection in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4b62d-104">Od [Kirka Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com)a [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="4b62d-104">By [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="4b62d-105">ASP.NET Core podporuje vzor návrhu softwaru pro vkládání závislostí (DI), což je technika pro dosažení [inverze ovládacího prvku (IOC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) mezi třídami a jejich závislostmi.</span><span class="sxs-lookup"><span data-stu-id="4b62d-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="4b62d-106">Další informace, které jsou specifické pro vkládání závislostí v rámci řadičů MVC, najdete v tématu <xref:mvc/controllers/dependency-injection> .</span><span class="sxs-lookup"><span data-stu-id="4b62d-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="4b62d-107">Další informace o vkládání závislostí z možností naleznete v tématu <xref:fundamentals/configuration/options> .</span><span class="sxs-lookup"><span data-stu-id="4b62d-107">For more information on dependency injection of options, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="4b62d-108">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4b62d-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="4b62d-109">Přehled injektáže závislosti</span><span class="sxs-lookup"><span data-stu-id="4b62d-109">Overview of dependency injection</span></span>

<span data-ttu-id="4b62d-110">*Závislost* je libovolný objekt, který vyžaduje jiný objekt.</span><span class="sxs-lookup"><span data-stu-id="4b62d-110">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="4b62d-111">Projděte si následující `MyDependency` třídu s `WriteMessage` metodou, na které jsou závislé jiné třídy v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="4b62d-111">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

```csharp
public class MyDependency
{
    public MyDependency()
    {
    }

    public void WriteMessage(string message)
    {
        Console.WriteLine(
            $"MyDependency.WriteMessage called. Message: {message}");
    }
}
```

<span data-ttu-id="4b62d-112">Instance `MyDependency` třídy může být vytvořena, aby byla `WriteMessage` Metoda k dispozici pro třídu.</span><span class="sxs-lookup"><span data-stu-id="4b62d-112">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="4b62d-113">`MyDependency`Třída je závislostí `IndexModel` třídy:</span><span class="sxs-lookup"><span data-stu-id="4b62d-113">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

```csharp
public class IndexModel : PageModel
{
    private readonly MyDependency _dependency = new MyDependency();

    public void OnGet()
    {
        _dependency.WriteMessage(
            "IndexModel.OnGet created this message.");
    }
}
```

<span data-ttu-id="4b62d-114">Třída vytvoří a přímo závisí na `MyDependency` instanci.</span><span class="sxs-lookup"><span data-stu-id="4b62d-114">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="4b62d-115">Závislosti kódu, jako je například předchozí příklad, jsou problematické a je třeba se jim vyhnout z následujících důvodů:</span><span class="sxs-lookup"><span data-stu-id="4b62d-115">Code dependencies, such as the previous example, are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="4b62d-116">Chcete-li nahradit `MyDependency` jinou implementací, třída musí být upravena.</span><span class="sxs-lookup"><span data-stu-id="4b62d-116">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="4b62d-117">Pokud `MyDependency` má závislosti, musí být nakonfigurovány třídou.</span><span class="sxs-lookup"><span data-stu-id="4b62d-117">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="4b62d-118">Ve velkém projektu s více třídami, v závislosti na tom `MyDependency` , je kód konfigurace rozptýlen napříč aplikací.</span><span class="sxs-lookup"><span data-stu-id="4b62d-118">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="4b62d-119">Tato implementace je obtížná pro testování částí.</span><span class="sxs-lookup"><span data-stu-id="4b62d-119">This implementation is difficult to unit test.</span></span> <span data-ttu-id="4b62d-120">Aplikace by měla používat třídu typu "nebo zástupné procedury" `MyDependency` , což není u tohoto přístupu možné.</span><span class="sxs-lookup"><span data-stu-id="4b62d-120">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="4b62d-121">Vkládání závislostí řeší tyto problémy prostřednictvím:</span><span class="sxs-lookup"><span data-stu-id="4b62d-121">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="4b62d-122">Použití rozhraní nebo základní třídy k abstrakci implementace závislosti.</span><span class="sxs-lookup"><span data-stu-id="4b62d-122">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="4b62d-123">Registrace závislosti v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="4b62d-123">Registration of the dependency in a service container.</span></span> <span data-ttu-id="4b62d-124">ASP.NET Core poskytuje integrovaný kontejner služeb <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="4b62d-124">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="4b62d-125">Služby jsou registrovány v metodě aplikace `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="4b62d-125">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="4b62d-126">*Vložení* služby do konstruktoru třídy, kde se používá.</span><span class="sxs-lookup"><span data-stu-id="4b62d-126">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="4b62d-127">Rozhraní přebírá zodpovědnost za vytvoření instance závislosti a její odstranění, když už ji nepotřebujete.</span><span class="sxs-lookup"><span data-stu-id="4b62d-127">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="4b62d-128">V [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) `IMyDependency` definuje rozhraní metodu, kterou služba poskytuje aplikaci:</span><span class="sxs-lookup"><span data-stu-id="4b62d-128">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="4b62d-129">Toto rozhraní je implementováno konkrétním typem `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="4b62d-129">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="4b62d-130">V ukázkové aplikaci `IMyDependency` je služba zaregistrovaná u konkrétního typu `MyDependency` .</span><span class="sxs-lookup"><span data-stu-id="4b62d-130">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="4b62d-131"><xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A>Metoda registruje službu s rozsahem životnosti, životností jediného požadavku.</span><span class="sxs-lookup"><span data-stu-id="4b62d-131">The <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> method registers the service with a scoped lifetime, the lifetime of a single request.</span></span> <span data-ttu-id="4b62d-132">[Doby platnosti služeb](#service-lifetimes) jsou popsány dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="4b62d-132">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency.cs?name=snippet1)]

<span data-ttu-id="4b62d-133">V ukázkové aplikaci `IMyDependency` je instance vyžádána a používána pro volání `WriteMessage` metody služby:</span><span class="sxs-lookup"><span data-stu-id="4b62d-133">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index2.cshtml.cs?name=snippet1)]

<span data-ttu-id="4b62d-134">Se vzorem DI:</span><span class="sxs-lookup"><span data-stu-id="4b62d-134">With the DI pattern:</span></span>

* <span data-ttu-id="4b62d-135">Kontroler nepoužívá konkrétní typ `MyDependency` , jenom rozhraní `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="4b62d-135">The controller doesn't use the concrete type `MyDependency`, only the interface `IMyDependency`.</span></span> <span data-ttu-id="4b62d-136">Díky tomu je možné snadno změnit implementaci, kterou kontroler používá bez změny kontroleru.</span><span class="sxs-lookup"><span data-stu-id="4b62d-136">That makes it easy to change the implementation that the controller uses without modifying the controller.</span></span>
* <span data-ttu-id="4b62d-137">Kontroler nevytvoří instanci třídy `MyDependency` , kterou vytvoří kontejner di.</span><span class="sxs-lookup"><span data-stu-id="4b62d-137">The controller doesn't create an instance of `MyDependency`, it's created by the DI container.</span></span>

<span data-ttu-id="4b62d-138">Implementaci `IMyDependency` rozhraní lze zlepšit pomocí integrovaného protokolovacího rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="4b62d-138">The implementation of the `IMyDependency` interface can be improved by using the built-in logging API:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet2)]

<span data-ttu-id="4b62d-139">Aktualizovaná `ConfigureServices` Metoda registruje novou `IMyDependency` implementaci:</span><span class="sxs-lookup"><span data-stu-id="4b62d-139">The updated `ConfigureServices` method registers the new `IMyDependency` implementation:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency2.cs?name=snippet1)]

<span data-ttu-id="4b62d-140">`MyDependency2` požaduje <xref:Microsoft.Extensions.Logging.ILogger`1> v konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="4b62d-140">`MyDependency2` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in the constructor.</span></span> <span data-ttu-id="4b62d-141">Není neobvyklé používat vkládání závislostí v zřetězeném způsobem.</span><span class="sxs-lookup"><span data-stu-id="4b62d-141">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="4b62d-142">Každá požadovaná závislost zase vyžádá své vlastní závislosti.</span><span class="sxs-lookup"><span data-stu-id="4b62d-142">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="4b62d-143">Kontejner vyřeší závislosti v grafu a vrátí plně přeloženou službu.</span><span class="sxs-lookup"><span data-stu-id="4b62d-143">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="4b62d-144">Kolektivní sada závislostí, které je třeba vyřešit, se obvykle označuje jako *strom závislosti*, *graf závislosti*nebo *graf objektů*.</span><span class="sxs-lookup"><span data-stu-id="4b62d-144">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="4b62d-145">`ILogger<TCategoryName>` je [služba poskytovaná rozhraním](#framework-provided-services).</span><span class="sxs-lookup"><span data-stu-id="4b62d-145">`ILogger<TCategoryName>` is a [framework-provided service](#framework-provided-services).</span></span>

<span data-ttu-id="4b62d-146">Kontejner se překládá `ILogger<TCategoryName>` tím, že využije [(Obecné) otevřené typy](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)a eliminuje nutnost registrovat každý [(obecný) konstruovaný typ](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span><span class="sxs-lookup"><span data-stu-id="4b62d-146">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span></span>

<span data-ttu-id="4b62d-147">V terminologii injektáže závislostí služba:</span><span class="sxs-lookup"><span data-stu-id="4b62d-147">In dependency injection terminology, a service:</span></span>

* <span data-ttu-id="4b62d-148">Je obvykle objekt, který poskytuje službu pro jiný kód v aplikaci, jako je například `IMyDependency` služba.</span><span class="sxs-lookup"><span data-stu-id="4b62d-148">Is typically an object that provides a service to other code in the app, such as the `IMyDependency` service.</span></span>
* <span data-ttu-id="4b62d-149">Nesouvisí s webovou službou, i když služba může používat webovou službu.</span><span class="sxs-lookup"><span data-stu-id="4b62d-149">Is not related to a web service, although the service may use a web service.</span></span>

<span data-ttu-id="4b62d-150">Rozhraní poskytuje robustní systém [protokolování](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="4b62d-150">The framework provides a robust [logging](xref:fundamentals/logging/index) system.</span></span> <span data-ttu-id="4b62d-151">`IMyDependency`Implementace byly zapsány za účelem demonstrace základního di, nikoli implementace protokolování.</span><span class="sxs-lookup"><span data-stu-id="4b62d-151">The `IMyDependency` implementations were written to demonstrate basic DI, not to implement logging.</span></span> <span data-ttu-id="4b62d-152">Většina aplikací by neměla potřebovat zapisovat protokolovací nástroje.</span><span class="sxs-lookup"><span data-stu-id="4b62d-152">Most apps shouldn't need to write loggers.</span></span> <span data-ttu-id="4b62d-153">Následující kód ukazuje použití výchozího protokolování, které nevyžaduje registraci žádné služby v nástroji `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="4b62d-153">The following code demonstrates using the default logging, which doesn't require any services to be registered in `ConfigureServices`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="4b62d-154">Pomocí předchozího kódu není nutné aktualizovat, `ConfigureServices` protože [protokolování](xref:fundamentals/logging/index) je poskytováno rozhraním:</span><span class="sxs-lookup"><span data-stu-id="4b62d-154">Using the preceding code, there is no need to update `ConfigureServices` because [logging](xref:fundamentals/logging/index) is provided by the framework:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages();
}
```

## <a name="services-injected-into-startup"></a><span data-ttu-id="4b62d-155">Služby vložené do spuštění</span><span class="sxs-lookup"><span data-stu-id="4b62d-155">Services injected into Startup</span></span>

<span data-ttu-id="4b62d-156">`Startup`Při použití obecného hostitele () mohou být do konstruktoru vloženy pouze následující typy služeb <xref:Microsoft.Extensions.Hosting.IHostBuilder> :</span><span class="sxs-lookup"><span data-stu-id="4b62d-156">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="4b62d-157">Služby je možné vložit do `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="4b62d-157">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="4b62d-158">Další informace najdete v tématu <xref:fundamentals/startup> a [Konfigurace přístupu při spuštění](xref:fundamentals/configuration/index#access-configuration-in-startup).</span><span class="sxs-lookup"><span data-stu-id="4b62d-158">For more information, see <xref:fundamentals/startup> and [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup).</span></span>

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="4b62d-159">Registrace dalších služeb pomocí rozšiřujících metod</span><span class="sxs-lookup"><span data-stu-id="4b62d-159">Register additional services with extension methods</span></span>

<span data-ttu-id="4b62d-160">Když je k dispozici metoda rozšíření kolekce služeb pro registraci služby:</span><span class="sxs-lookup"><span data-stu-id="4b62d-160">When a service collection extension method is available to register a service:</span></span>

* <span data-ttu-id="4b62d-161">Konvence slouží k `Add{SERVICE_NAME}` registraci všech služeb, které služba vyžaduje, pomocí jediné metody rozšíření.</span><span class="sxs-lookup"><span data-stu-id="4b62d-161">The convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span>
* <span data-ttu-id="4b62d-162">Závislé služby jsou také zaregistrovány.</span><span class="sxs-lookup"><span data-stu-id="4b62d-162">The dependent services are also registered.</span></span>

<span data-ttu-id="4b62d-163">Následující kód je vygenerován Razor šablonou stránky pomocí individuálních uživatelských účtů a ukazuje, jak přidat další služby do kontejneru pomocí metod rozšíření <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> a <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A> :</span><span class="sxs-lookup"><span data-stu-id="4b62d-163">The following code is generated by the Razor Pages template using individual user accounts and shows how to add additional services to the container using the extension methods <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A>:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupEF.cs?name=snippet)]

<span data-ttu-id="4b62d-164">Další informace naleznete v tématech <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> a <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="4b62d-164">For more information, see <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> and <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="4b62d-165">Pokyny k zápisu rozšiřující metody k registraci služeb najdete v části [kombinování kolekce služeb](#csc) .</span><span class="sxs-lookup"><span data-stu-id="4b62d-165">See the section [Combining service collection](#csc) for instructions on writing an extension method to register services.</span></span>

[!INCLUDE[](~/includes/combine-di.md)]

## <a name="service-lifetimes"></a><span data-ttu-id="4b62d-166">Životnost služeb</span><span class="sxs-lookup"><span data-stu-id="4b62d-166">Service lifetimes</span></span>

<span data-ttu-id="4b62d-167">Vyberte odpovídající dobu života pro každou registrovanou službu.</span><span class="sxs-lookup"><span data-stu-id="4b62d-167">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="4b62d-168">Služby ASP.NET Core Services je možné nakonfigurovat s následujícími životnostmi:</span><span class="sxs-lookup"><span data-stu-id="4b62d-168">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="4b62d-169">Dočasný</span><span class="sxs-lookup"><span data-stu-id="4b62d-169">Transient</span></span>

<span data-ttu-id="4b62d-170">Služba přechodných životností ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*> ) se vytvoří pokaždé, když se požadují z kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="4b62d-170">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="4b62d-171">Tato životnost funguje nejlépe pro odlehčené bezstavové služby.</span><span class="sxs-lookup"><span data-stu-id="4b62d-171">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="4b62d-172">V aplikacích, které zpracovávají požadavky, jsou přechodné služby na konci žádosti zrušené.</span><span class="sxs-lookup"><span data-stu-id="4b62d-172">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="4b62d-173">Obor</span><span class="sxs-lookup"><span data-stu-id="4b62d-173">Scoped</span></span>

<span data-ttu-id="4b62d-174">Oborové služby () s rozsahem platnosti ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*> ) se vytvoří jednou za požadavek klienta (připojení).</span><span class="sxs-lookup"><span data-stu-id="4b62d-174">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="4b62d-175">Při použití Entity Framework Core <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> metoda rozšíření registruje `DbContext` typy s vymezenou životností ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="4b62d-175">When using Entity Framework Core, the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension method registers `DbContext` types with a scoped lifetime by default.</span></span>

<span data-ttu-id="4b62d-176">V aplikacích, které zpracovávají požadavky, se vymezené služby na konci žádosti odstraní.</span><span class="sxs-lookup"><span data-stu-id="4b62d-176">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

<span data-ttu-id="4b62d-177">Používejte vymezené služby v middlewaru s jedním z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="4b62d-177">Use scoped services in middleware with one of the following approaches:</span></span>

* <span data-ttu-id="4b62d-178">Službu založit do `Invoke` metody nebo `InvokeAsync` .</span><span class="sxs-lookup"><span data-stu-id="4b62d-178">Inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="4b62d-179">Vložení pomocí [Injektáže konstruktoru](xref:mvc/controllers/dependency-injection#constructor-injection) vyvolá výjimku za běhu, protože vynutí, aby se služba chovala jako typ singleton.</span><span class="sxs-lookup"><span data-stu-id="4b62d-179">Injecting by [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) throws an exception at run time because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="4b62d-180">Ukázka v [možnostech životního cyklu a registrace](#lifetime-and-registration-options) používá `InvokeAsync` přístup.</span><span class="sxs-lookup"><span data-stu-id="4b62d-180">The sample in the [Lifetime and registration options](#lifetime-and-registration-options) uses the `InvokeAsync` approach.</span></span>
* <span data-ttu-id="4b62d-181">[Middleware založený na továrně](<xref:fundamentals/middleware/extensibility>).</span><span class="sxs-lookup"><span data-stu-id="4b62d-181">[Factory-based middleware](<xref:fundamentals/middleware/extensibility>).</span></span> <span data-ttu-id="4b62d-182"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> rozšiřující metody kontrolují, jestli implementuje registrovaný typ middlewaru <xref:Microsoft.AspNetCore.Http.IMiddleware> .</span><span class="sxs-lookup"><span data-stu-id="4b62d-182"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> extension methods check if a middleware's registered type implements <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span></span> <span data-ttu-id="4b62d-183">V takovém případě <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance zaregistrovaná v kontejneru slouží k vyřešení <xref:Microsoft.AspNetCore.Http.IMiddleware> implementace namísto použití logiky aktivace middleware založeného na konvencích.</span><span class="sxs-lookup"><span data-stu-id="4b62d-183">If it does, the <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance registered in the container is used to resolve the <xref:Microsoft.AspNetCore.Http.IMiddleware> implementation instead of using the convention-based middleware activation logic.</span></span> <span data-ttu-id="4b62d-184">Middleware je zaregistrován jako služba v oboru nebo přechodné službě v kontejneru služby aplikace.</span><span class="sxs-lookup"><span data-stu-id="4b62d-184">The middleware is registered as a scoped or transient service in the app's service container.</span></span>

<span data-ttu-id="4b62d-185">Další informace naleznete v tématu <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="4b62d-185">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

<span data-ttu-id="4b62d-186">Neprovádějte ***Překlad oboru*** služby z typu singleton.</span><span class="sxs-lookup"><span data-stu-id="4b62d-186">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="4b62d-187">Může dojít k tomu, že služba má při zpracování dalších požadavků špatný stav.</span><span class="sxs-lookup"><span data-stu-id="4b62d-187">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="4b62d-188">Je to v pořádku:</span><span class="sxs-lookup"><span data-stu-id="4b62d-188">It's fine to:</span></span>

* <span data-ttu-id="4b62d-189">Vyřešte službu typu Singleton z oboru nebo přechodné služby.</span><span class="sxs-lookup"><span data-stu-id="4b62d-189">Resolve a singleton service from a scoped or transient service.</span></span>
* <span data-ttu-id="4b62d-190">Vyřešte oborovou službu z jiné vymezené nebo přechodné služby.</span><span class="sxs-lookup"><span data-stu-id="4b62d-190">Resolve a scoped service from another scoped or transient service.</span></span>

<span data-ttu-id="4b62d-191">Ve výchozím nastavení ve vývojovém prostředí vyřeší služba z jiné služby s delší životností výjimku.</span><span class="sxs-lookup"><span data-stu-id="4b62d-191">By default, in the development environment, resolving a service from another service with longer lifetime throws an exception.</span></span> <span data-ttu-id="4b62d-192">Další informace najdete v tématu [ověřování oboru](#sv).</span><span class="sxs-lookup"><span data-stu-id="4b62d-192">For more information, see [Scope validation](#sv).</span></span>

### <a name="singleton"></a><span data-ttu-id="4b62d-193">Singleton</span><span class="sxs-lookup"><span data-stu-id="4b62d-193">Singleton</span></span>

<span data-ttu-id="4b62d-194">Vytvoří se služba životnosti singleton ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*> ):</span><span class="sxs-lookup"><span data-stu-id="4b62d-194">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created either:</span></span>

* <span data-ttu-id="4b62d-195">Při prvním vyžádání.</span><span class="sxs-lookup"><span data-stu-id="4b62d-195">The first time they're requested.</span></span>
* <span data-ttu-id="4b62d-196">Vývojář při poskytování instance implementace přímo do kontejneru.</span><span class="sxs-lookup"><span data-stu-id="4b62d-196">By the developer, when providing an implementation instance directly to the container.</span></span> <span data-ttu-id="4b62d-197">Tento přístup je potřeba jenom zřídka.</span><span class="sxs-lookup"><span data-stu-id="4b62d-197">This approach is rarely needed.</span></span>

<span data-ttu-id="4b62d-198">Každý další požadavek používá stejnou instanci.</span><span class="sxs-lookup"><span data-stu-id="4b62d-198">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="4b62d-199">Pokud aplikace vyžaduje chování singleton, umožněte kontejneru služby spravovat dobu života služby.</span><span class="sxs-lookup"><span data-stu-id="4b62d-199">If the app requires singleton behavior, allow the service container to manage the service's lifetime.</span></span> <span data-ttu-id="4b62d-200">Neimplementujte vzor návrhu singleton a poskytněte kód pro odstranění typu singleton.</span><span class="sxs-lookup"><span data-stu-id="4b62d-200">Don't implement the singleton design pattern and provide code to dispose of the singleton.</span></span> <span data-ttu-id="4b62d-201">Služby by nikdy neměly být odstraněny kódem, který službu vyřešil z kontejneru.</span><span class="sxs-lookup"><span data-stu-id="4b62d-201">Services should never be disposed by code that resolved the service from a container.</span></span> <span data-ttu-id="4b62d-202">Pokud je typ nebo objekt pro vytváření zaregistrován jako singleton, kontejner automaticky odstraní singleton.</span><span class="sxs-lookup"><span data-stu-id="4b62d-202">If a type or factory is registered as a singleton, the container will dispose the singleton automatically.</span></span>

<span data-ttu-id="4b62d-203">Služby s jedním prvkem musí být bezpečné pro přístup z více vláken a často se používají ve bezstavových službách.</span><span class="sxs-lookup"><span data-stu-id="4b62d-203">Singleton services must be thread safe and are often used in stateless services.</span></span>

<span data-ttu-id="4b62d-204">V aplikacích, které zpracovávají požadavky, jsou nejednoznačné služby uvolněny při <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> vyřazení aplikace z vypnutí.</span><span class="sxs-lookup"><span data-stu-id="4b62d-204">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed on application shutdown.</span></span> <span data-ttu-id="4b62d-205">Vzhledem k tomu, že paměť není uvolněna, dokud nebude aplikace vypnuta, je nutné vzít v úvahu použití paměti s singleton.</span><span class="sxs-lookup"><span data-stu-id="4b62d-205">Because memory is not released until the app is shut down, memory use with a singleton must be considered.</span></span>

> [!WARNING]
> <span data-ttu-id="4b62d-206">Neprovádějte ***Překlad oboru*** služby z typu singleton.</span><span class="sxs-lookup"><span data-stu-id="4b62d-206">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="4b62d-207">Může dojít k tomu, že služba má při zpracování dalších požadavků špatný stav.</span><span class="sxs-lookup"><span data-stu-id="4b62d-207">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="4b62d-208">Je dobré vyřešit službu typu Singleton z oboru nebo přechodné služby.</span><span class="sxs-lookup"><span data-stu-id="4b62d-208">It's fine to resolve a singleton service from a scoped or transient service.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="4b62d-209">Metody registrace služby</span><span class="sxs-lookup"><span data-stu-id="4b62d-209">Service registration methods</span></span>

<span data-ttu-id="4b62d-210">Metody rozšíření registrace služby nabízejí přetížení, která jsou užitečná pro konkrétní scénáře.</span><span class="sxs-lookup"><span data-stu-id="4b62d-210">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>
<!-- Review: Auto disposal at end of app lifetime is not what you think of auto disposal  -->

| <span data-ttu-id="4b62d-211">Metoda</span><span class="sxs-lookup"><span data-stu-id="4b62d-211">Method</span></span> | <span data-ttu-id="4b62d-212">Automaticky</span><span class="sxs-lookup"><span data-stu-id="4b62d-212">Automatic</span></span><br><span data-ttu-id="4b62d-213">object</span><span class="sxs-lookup"><span data-stu-id="4b62d-213">object</span></span><br><span data-ttu-id="4b62d-214">odvod</span><span class="sxs-lookup"><span data-stu-id="4b62d-214">disposal</span></span> | <span data-ttu-id="4b62d-215">Několik</span><span class="sxs-lookup"><span data-stu-id="4b62d-215">Multiple</span></span><br><span data-ttu-id="4b62d-216">implementace</span><span class="sxs-lookup"><span data-stu-id="4b62d-216">implementations</span></span> | <span data-ttu-id="4b62d-217">Pass – argumenty</span><span class="sxs-lookup"><span data-stu-id="4b62d-217">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="4b62d-218">Příklad:</span><span class="sxs-lookup"><span data-stu-id="4b62d-218">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="4b62d-219">Ano</span><span class="sxs-lookup"><span data-stu-id="4b62d-219">Yes</span></span> | <span data-ttu-id="4b62d-220">Ano</span><span class="sxs-lookup"><span data-stu-id="4b62d-220">Yes</span></span> | <span data-ttu-id="4b62d-221">No</span><span class="sxs-lookup"><span data-stu-id="4b62d-221">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="4b62d-222">Příklady:</span><span class="sxs-lookup"><span data-stu-id="4b62d-222">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep(99));` | <span data-ttu-id="4b62d-223">Ano</span><span class="sxs-lookup"><span data-stu-id="4b62d-223">Yes</span></span> | <span data-ttu-id="4b62d-224">Ano</span><span class="sxs-lookup"><span data-stu-id="4b62d-224">Yes</span></span> | <span data-ttu-id="4b62d-225">Ano</span><span class="sxs-lookup"><span data-stu-id="4b62d-225">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="4b62d-226">Příklad:</span><span class="sxs-lookup"><span data-stu-id="4b62d-226">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="4b62d-227">Ano</span><span class="sxs-lookup"><span data-stu-id="4b62d-227">Yes</span></span> | <span data-ttu-id="4b62d-228">No</span><span class="sxs-lookup"><span data-stu-id="4b62d-228">No</span></span> | <span data-ttu-id="4b62d-229">No</span><span class="sxs-lookup"><span data-stu-id="4b62d-229">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="4b62d-230">Příklady:</span><span class="sxs-lookup"><span data-stu-id="4b62d-230">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep(99));` | <span data-ttu-id="4b62d-231">No</span><span class="sxs-lookup"><span data-stu-id="4b62d-231">No</span></span> | <span data-ttu-id="4b62d-232">Ano</span><span class="sxs-lookup"><span data-stu-id="4b62d-232">Yes</span></span> | <span data-ttu-id="4b62d-233">Ano</span><span class="sxs-lookup"><span data-stu-id="4b62d-233">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="4b62d-234">Příklady:</span><span class="sxs-lookup"><span data-stu-id="4b62d-234">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep(99));` | <span data-ttu-id="4b62d-235">No</span><span class="sxs-lookup"><span data-stu-id="4b62d-235">No</span></span> | <span data-ttu-id="4b62d-236">No</span><span class="sxs-lookup"><span data-stu-id="4b62d-236">No</span></span> | <span data-ttu-id="4b62d-237">Ano</span><span class="sxs-lookup"><span data-stu-id="4b62d-237">Yes</span></span> |

<span data-ttu-id="4b62d-238">Další informace o vyřazení typů najdete v části věnované [vyřazení služeb](#disposal-of-services) .</span><span class="sxs-lookup"><span data-stu-id="4b62d-238">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="4b62d-239">Běžným scénářem pro více implementací je vytvoření [typů pro testování](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="4b62d-239">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="4b62d-240">`TryAdd{LIFETIME}` metody registrují službu, pokud ještě není zaregistrovaná implementace.</span><span class="sxs-lookup"><span data-stu-id="4b62d-240">`TryAdd{LIFETIME}` methods register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="4b62d-241">V následujícím příkladu se první řádek registruje `MyDependency` pro `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="4b62d-241">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="4b62d-242">Druhý řádek nemá žádný účinek, protože `IMyDependency` už má registrovanou implementaci:</span><span class="sxs-lookup"><span data-stu-id="4b62d-242">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="4b62d-243">Další informace:</span><span class="sxs-lookup"><span data-stu-id="4b62d-243">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="4b62d-244">Metody [TryAddEnumerable (ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) registrují službu, pokud již neexistuje implementace *stejného typu*.</span><span class="sxs-lookup"><span data-stu-id="4b62d-244">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="4b62d-245">Několik služeb je vyřešeno prostřednictvím `IEnumerable<{SERVICE}>` .</span><span class="sxs-lookup"><span data-stu-id="4b62d-245">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="4b62d-246">Při registraci služeb by měl vývojář přidat instanci, pokud už jeden ze stejného typu není přidaný.</span><span class="sxs-lookup"><span data-stu-id="4b62d-246">When registering services, the developer should add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="4b62d-247">Obecně autoři knihovny používají `TryAddEnumerable` k tomu, aby nedocházelo k registraci více kopií implementace v kontejneru.</span><span class="sxs-lookup"><span data-stu-id="4b62d-247">Generally, library authors use `TryAddEnumerable` to avoid registering multiple copies of an implementation in the container.</span></span>

<span data-ttu-id="4b62d-248">V následujícím příkladu se první řádek registruje `MyDep` pro `IMyDep1` .</span><span class="sxs-lookup"><span data-stu-id="4b62d-248">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="4b62d-249">Druhý řádek se registruje `MyDep` pro `IMyDep2` .</span><span class="sxs-lookup"><span data-stu-id="4b62d-249">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="4b62d-250">Třetí řádek nemá žádný vliv, protože `IMyDep1` už má registrovanou implementaci `MyDep` :</span><span class="sxs-lookup"><span data-stu-id="4b62d-250">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

<span data-ttu-id="4b62d-251">Registrace služby je obecně závislá na nezávislém pořadí s výjimkou registrace více implementací stejného typu.</span><span class="sxs-lookup"><span data-stu-id="4b62d-251">Service registration is generally order independent except when registering multiple implementations of the same type.</span></span>

<span data-ttu-id="4b62d-252">`IServiceCollection` je kolekce <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> .</span><span class="sxs-lookup"><span data-stu-id="4b62d-252">`IServiceCollection` is a collection of <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>.</span></span> <span data-ttu-id="4b62d-253">Následující kód ukazuje, jak přidat službu s konstruktorem:</span><span class="sxs-lookup"><span data-stu-id="4b62d-253">The following code shows how to add a service with a constructor:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup5.cs?name=snippet)]

<span data-ttu-id="4b62d-254">`Add{LIFETIME}`Metody používají stejný přístup.</span><span class="sxs-lookup"><span data-stu-id="4b62d-254">The `Add{LIFETIME}` methods use the same approach.</span></span> <span data-ttu-id="4b62d-255">Podívejte se například na [zdrojový kód do AddScoped](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span><span class="sxs-lookup"><span data-stu-id="4b62d-255">For example, see the [source code to AddScoped](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span></span>

### <a name="constructor-injection-behavior"></a><span data-ttu-id="4b62d-256">Chování injektáže konstruktoru</span><span class="sxs-lookup"><span data-stu-id="4b62d-256">Constructor injection behavior</span></span>

<span data-ttu-id="4b62d-257">Služby je možné vyřešit pomocí dvou mechanismů:</span><span class="sxs-lookup"><span data-stu-id="4b62d-257">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="4b62d-258"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span><span class="sxs-lookup"><span data-stu-id="4b62d-258"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span></span>
  * <span data-ttu-id="4b62d-259">Vytvoří objekty bez registrace služby v kontejneru pro vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="4b62d-259">Creates objects without service registration in the dependency injection container.</span></span>
  * <span data-ttu-id="4b62d-260">Používá se s funkcemi architektury, jako jsou například [pomocníka značek](xref:mvc/views/tag-helpers/intro), řadiče MVC a [pořadače modelů](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="4b62d-260">Used with framework features, such as [Tag Helpers](xref:mvc/views/tag-helpers/intro), MVC controllers, and [model binders](xref:mvc/models/model-binding).</span></span>

<span data-ttu-id="4b62d-261">Konstruktory mohou přijímat argumenty, které nejsou poskytovány vkládáním závislostí, ale argumenty musí přiřadit výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="4b62d-261">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="4b62d-262">Když jsou služby vyřešeny `IServiceProvider` nebo `ActivatorUtilities` , [Injektáže konstruktoru](xref:mvc/controllers/dependency-injection#constructor-injection) vyžaduje *veřejný* konstruktor.</span><span class="sxs-lookup"><span data-stu-id="4b62d-262">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="4b62d-263">Když jsou služby vyřešeny nástrojem `ActivatorUtilities` , [Injektáže konstruktoru](xref:mvc/controllers/dependency-injection#constructor-injection) vyžaduje, aby existoval pouze jeden příslušný konstruktor.</span><span class="sxs-lookup"><span data-stu-id="4b62d-263">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="4b62d-264">Přetížení konstruktoru jsou podporovaná, ale může existovat jenom jedno přetížení, jehož argumenty můžou být splněné vkládáním závislostí.</span><span class="sxs-lookup"><span data-stu-id="4b62d-264">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="4b62d-265">Entity Framework kontexty</span><span class="sxs-lookup"><span data-stu-id="4b62d-265">Entity Framework contexts</span></span>

<span data-ttu-id="4b62d-266">Entity Framework kontexty se obvykle přidávají do kontejneru služby pomocí [rozsahu životnosti](#service-lifetimes) , protože operace databáze webové aplikace jsou obvykle vymezeny na požadavky klienta.</span><span class="sxs-lookup"><span data-stu-id="4b62d-266">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="4b62d-267">Výchozí doba života je vymezena v případě, že při registraci kontextu databáze není zadána doba života při [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) přetížení.</span><span class="sxs-lookup"><span data-stu-id="4b62d-267">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="4b62d-268">Služby pro danou dobu života by neměly používat kontext databáze s kratší dobou životnosti než služba.</span><span class="sxs-lookup"><span data-stu-id="4b62d-268">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="4b62d-269">Možnosti života a registrace</span><span class="sxs-lookup"><span data-stu-id="4b62d-269">Lifetime and registration options</span></span>

<span data-ttu-id="4b62d-270">Chcete-li předvést rozdíl mezi možnostmi životního cyklu a registrací, zvažte následující rozhraní, která představují úlohy jako operaci s identifikátorem `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="4b62d-270">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with an identifier, `OperationId`.</span></span> <span data-ttu-id="4b62d-271">V závislosti na tom, jak je životnost služby operace nakonfigurovaná pro následující rozhraní, kontejner poskytuje buď stejnou, nebo jinou instanci služby, když ho požaduje třída:</span><span class="sxs-lookup"><span data-stu-id="4b62d-271">Depending on how the lifetime of an operation's service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="4b62d-272">Rozhraní jsou implementována ve `Operation` třídě.</span><span class="sxs-lookup"><span data-stu-id="4b62d-272">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="4b62d-273">`Operation`Konstruktor generuje poslední 4 znaky identifikátoru GUID, pokud není zadaný:</span><span class="sxs-lookup"><span data-stu-id="4b62d-273">The `Operation` constructor generates the last 4 characters of a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<!--
An `OperationService` is registered that depends on each of the other `Operation` types. When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.

* When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`. `OperationService` receives a new instance of the `IOperationTransient` class. The new instance yields a different `OperationId`.
* When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request. Across client requests, both services share a different `OperationId` value.
* When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

-->

<span data-ttu-id="4b62d-274">V `Startup.ConfigureServices` je každý typ přidán do kontejneru podle jeho pojmenované životnosti:</span><span class="sxs-lookup"><span data-stu-id="4b62d-274">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="4b62d-275">Ukázková aplikace ukazuje dobu života objektu v rámci a mezi požadavky.</span><span class="sxs-lookup"><span data-stu-id="4b62d-275">The sample app demonstrates object lifetimes within and between requests.</span></span> <span data-ttu-id="4b62d-276">Ukázková aplikace `IndexModel` a middleware si vyžádá každý druh `IOperation` typu a zaznamená `OperationId` :</span><span class="sxs-lookup"><span data-stu-id="4b62d-276">The sample app's `IndexModel` and middleware requests each kind of `IOperation` type and logs the `OperationId`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="4b62d-277">Middleware je podobný jako `IndexModel` a řeší stejné služby:</span><span class="sxs-lookup"><span data-stu-id="4b62d-277">The middleware is similar to the `IndexModel` and resolves the same services:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet)]

<span data-ttu-id="4b62d-278">Oborové služby musí být vyřešené v `InvokeAsync` metodě:</span><span class="sxs-lookup"><span data-stu-id="4b62d-278">Scoped services must be resolved in the `InvokeAsync` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet2&highlight=2)]

<span data-ttu-id="4b62d-279">Výstup protokolovacího nástroje ukazuje:</span><span class="sxs-lookup"><span data-stu-id="4b62d-279">The logger output shows:</span></span>

* <span data-ttu-id="4b62d-280">*Přechodné* objekty jsou vždy odlišné.</span><span class="sxs-lookup"><span data-stu-id="4b62d-280">*Transient* objects are always different.</span></span> <span data-ttu-id="4b62d-281">Přechodná `OperationId` hodnota se liší v `IndexModel` a middlewaru.</span><span class="sxs-lookup"><span data-stu-id="4b62d-281">The transient `OperationId` value is different in the `IndexModel` and the middleware.</span></span>
* <span data-ttu-id="4b62d-282">*Vymezené* objekty jsou v každé žádosti stejné, ale v každé žádosti se liší.</span><span class="sxs-lookup"><span data-stu-id="4b62d-282">*Scoped* objects are the same in each request but different across each request.</span></span>
* <span data-ttu-id="4b62d-283">Objekty *singleton* jsou pro každý požadavek stejné.</span><span class="sxs-lookup"><span data-stu-id="4b62d-283">*Singleton* objects are the same for every request.</span></span>

<span data-ttu-id="4b62d-284">Chcete-li snížit výstup protokolování, nastavte v *appsettings.Development.jsv* souboru "Logging: LogLevel: Microsoft: Error":</span><span class="sxs-lookup"><span data-stu-id="4b62d-284">To reduce the logging output, set "Logging:LogLevel:Microsoft:Error" in the *appsettings.Development.json* file:</span></span>

[!code-json[](dependency-injection/samples/3.x/DependencyInjectionSample/appsettings.Development.json?highlight=7)]

## <a name="call-services-from-main"></a><span data-ttu-id="4b62d-285">Volat služby z Main</span><span class="sxs-lookup"><span data-stu-id="4b62d-285">Call services from main</span></span>

<span data-ttu-id="4b62d-286">Vytvořte <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> pomocí [IServiceScopeFactory. CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) pro řešení oboru služby v rámci rozsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="4b62d-286">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="4b62d-287">Tento přístup je užitečný pro přístup k oboruované službě při spuštění za účelem spuštění úloh inicializace:</span><span class="sxs-lookup"><span data-stu-id="4b62d-287">This approach is useful to access a scoped service at startup to run initialization tasks:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        var host = CreateHostBuilder(args).Build();

        using (var scope = host.Services.CreateScope())
        {
            var services = scope.ServiceProvider;

            try
            {
                SeedData.Initialize(services);
            }
            catch (Exception ex)
            {
                var logger = services.GetRequiredService<ILogger<Program>>();
                logger.LogError(ex, "An error occurred seeding the DB.");
            }
        }

        host.Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

<span data-ttu-id="4b62d-288">Předchozí kód je z návodu [Přidat inicializátor počáteční](xref:tutorials/razor-pages/sql?#add-the-seed-initializer) hodnoty v Razor kurzu stránky.</span><span class="sxs-lookup"><span data-stu-id="4b62d-288">The preceding code is from [Add the seed initializer](xref:tutorials/razor-pages/sql?#add-the-seed-initializer) in the Razor Pages tutorial.</span></span>

<span data-ttu-id="4b62d-289">Následující příklad ukazuje, jak získat kontext pro `IMyDependency` `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="4b62d-289">The following example shows how to obtain a context for the `IMyDependency` in `Program.Main`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Program.cs?name=snippet)]

<a name="sv"></a>

## <a name="scope-validation"></a><span data-ttu-id="4b62d-290">Ověřování oboru</span><span class="sxs-lookup"><span data-stu-id="4b62d-290">Scope validation</span></span>

<span data-ttu-id="4b62d-291">Když je aplikace spuštěná ve [vývojovém prostředí](xref:fundamentals/environments) a volá [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) , aby se vytvořila hostitel, použije výchozí poskytovatel služeb kontroly, aby ověřil, že:</span><span class="sxs-lookup"><span data-stu-id="4b62d-291">When the app is running in the [Development environment](xref:fundamentals/environments) and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="4b62d-292">Oborové služby nejsou přímo nebo nepřímo vyřešeny od poskytovatele kořenové služby.</span><span class="sxs-lookup"><span data-stu-id="4b62d-292">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="4b62d-293">Oborové služby nejsou přímo nebo nepřímo vložené do singleton.</span><span class="sxs-lookup"><span data-stu-id="4b62d-293">Scoped services aren't directly or indirectly injected into singletons.</span></span>
* <span data-ttu-id="4b62d-294">Přechodné služby nejsou přímo ani nepřímo vloženy do ojedinělých nebo vymezených služeb.</span><span class="sxs-lookup"><span data-stu-id="4b62d-294">Transient services aren't directly or indirectly injected into singletons or scoped services.</span></span>

<span data-ttu-id="4b62d-295">Poskytovatel kořenové služby se vytvoří, když <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> se zavolá.</span><span class="sxs-lookup"><span data-stu-id="4b62d-295">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="4b62d-296">Doba života poskytovatele kořenové služby odpovídá době životnosti aplikace, když poskytovatel spustí aplikaci a je uvolněna při ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="4b62d-296">The root service provider's lifetime corresponds to the app's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="4b62d-297">Oborové služby jsou uvolněny kontejnerem, který je vytvořil.</span><span class="sxs-lookup"><span data-stu-id="4b62d-297">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="4b62d-298">Pokud je v kořenovém kontejneru vytvořena vymezená služba, doba života služby je efektivně povýšena na typ singleton, protože je uvolněna pouze kořenovým kontejnerem při vypnutí aplikace.</span><span class="sxs-lookup"><span data-stu-id="4b62d-298">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app is shut down.</span></span> <span data-ttu-id="4b62d-299">Při ověřování oborů služeb se tyto situace zachytí `BuildServiceProvider` .</span><span class="sxs-lookup"><span data-stu-id="4b62d-299">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="4b62d-300">Další informace najdete v tématu [ověřování oboru](xref:fundamentals/host/web-host#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="4b62d-300">For more information, see [Scope validation](xref:fundamentals/host/web-host#scope-validation).</span></span>

## <a name="request-services"></a><span data-ttu-id="4b62d-301">Žádosti o služby</span><span class="sxs-lookup"><span data-stu-id="4b62d-301">Request Services</span></span>

<span data-ttu-id="4b62d-302">Služby, které jsou k dispozici v rámci žádosti o ASP.NET Core, `HttpContext` jsou zpřístupněny prostřednictvím kolekce [HttpContext. RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) .</span><span class="sxs-lookup"><span data-stu-id="4b62d-302">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="4b62d-303">Žádosti o služby reprezentují služby nakonfigurované a požadované v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="4b62d-303">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="4b62d-304">Když objekty určují závislosti, jsou splněny typy nalezenými v `RequestServices` , ne `ApplicationServices` .</span><span class="sxs-lookup"><span data-stu-id="4b62d-304">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="4b62d-305">Obecně platí, že aplikace by tyto vlastnosti neměla používat přímo.</span><span class="sxs-lookup"><span data-stu-id="4b62d-305">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="4b62d-306">Místo toho požádejte o typy, které třídy vyžadují prostřednictvím konstruktorů třídy, a umožněte rozhraní vkládat závislosti.</span><span class="sxs-lookup"><span data-stu-id="4b62d-306">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span> <span data-ttu-id="4b62d-307">To poskytuje třídy, které jsou snáze testovány.</span><span class="sxs-lookup"><span data-stu-id="4b62d-307">This yields classes that are easier to test.</span></span>

<span data-ttu-id="4b62d-308">ASP.NET Core vytvoří obor pro každý požadavek a `RequestServices` vystaví poskytovatele služby s vymezeným oborem.</span><span class="sxs-lookup"><span data-stu-id="4b62d-308">ASP.NET Core creates a scope per request and `RequestServices` exposes the scoped service provider.</span></span> <span data-ttu-id="4b62d-309">Všechny oborové služby platí, pokud je žádost aktivní.</span><span class="sxs-lookup"><span data-stu-id="4b62d-309">All scoped services are valid for as long as the request is active.</span></span>

> [!NOTE]
> <span data-ttu-id="4b62d-310">Preferovat požadavky na závislosti jako parametry konstruktoru pro přístup ke `RequestServices` kolekci.</span><span class="sxs-lookup"><span data-stu-id="4b62d-310">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="4b62d-311">Navrhnout služby pro vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="4b62d-311">Design services for dependency injection</span></span>

<span data-ttu-id="4b62d-312">Osvědčené postupy:</span><span class="sxs-lookup"><span data-stu-id="4b62d-312">Best practices are to:</span></span>

* <span data-ttu-id="4b62d-313">Navrhněte služby pro použití injektáže závislosti k získání jejich závislostí.</span><span class="sxs-lookup"><span data-stu-id="4b62d-313">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="4b62d-314">Vyhněte se stavovým, statickým třídám a členům.</span><span class="sxs-lookup"><span data-stu-id="4b62d-314">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="4b62d-315">Navrhněte aplikace, aby místo toho používaly služby s jedním prvkem, což se vyhne vytváření globálního stavu.</span><span class="sxs-lookup"><span data-stu-id="4b62d-315">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="4b62d-316">Vyhněte se přímému vytváření instancí závislých tříd v rámci služeb.</span><span class="sxs-lookup"><span data-stu-id="4b62d-316">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="4b62d-317">Přímá instance Couples kód na konkrétní implementaci.</span><span class="sxs-lookup"><span data-stu-id="4b62d-317">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="4b62d-318">Vytvářejte třídy aplikací malými, dobře faktoringně a snadno testované.</span><span class="sxs-lookup"><span data-stu-id="4b62d-318">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="4b62d-319">Pokud se zdá, že třída má příliš mnoho vložených závislostí, obvykle se jedná o znaménko, že třída má příliš mnoho zodpovědnosti a je v rozporu s [principem jediné zodpovědnosti (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="4b62d-319">If a class seems to have too many injected dependencies, that's generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="4b62d-320">Pokuste se Refaktorovat třídu přesunutím některých odpovědností do nové třídy.</span><span class="sxs-lookup"><span data-stu-id="4b62d-320">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="4b62d-321">Mějte na paměti, že Razor stránky tříd modelu stránky a třídy KONTROLERU MVC by se měly zaměřit na uživatelské rozhraní.</span><span class="sxs-lookup"><span data-stu-id="4b62d-321">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="4b62d-322">Vyřazení služeb</span><span class="sxs-lookup"><span data-stu-id="4b62d-322">Disposal of services</span></span>

<span data-ttu-id="4b62d-323">Kontejner volá <xref:System.IDisposable.Dispose*> typy, které <xref:System.IDisposable> vytvoří.</span><span class="sxs-lookup"><span data-stu-id="4b62d-323">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="4b62d-324">Služby by nikdy neměly být zneškodněny jakýmkoli kódem, který službu vyřešil z kontejneru.</span><span class="sxs-lookup"><span data-stu-id="4b62d-324">Services should never be disposed by any code that resolved the service from a container.</span></span> <span data-ttu-id="4b62d-325">Pokud je typ nebo objekt pro vytváření zaregistrován jako singleton, kontejner odstraní singleton.</span><span class="sxs-lookup"><span data-stu-id="4b62d-325">If a type or factory is registered as a singleton, the container disposes the singleton.</span></span>

<span data-ttu-id="4b62d-326">V následujícím příkladu jsou služby vytvořeny pomocí kontejneru služby a automaticky odstraněny:</span><span class="sxs-lookup"><span data-stu-id="4b62d-326">In the following example, the services are created by the service container and disposed automatically:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Services/Service1.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="4b62d-327">Konzola ladění po každé aktualizaci stránky indexu zobrazí následující výstup:</span><span class="sxs-lookup"><span data-stu-id="4b62d-327">The debug console shows the following output after each refresh of the Index page:</span></span>

```console
Service1: IndexModel.OnGet
Service2: IndexModel.OnGet
Service3: IndexModel.OnGet
Service1.Dispose
```

### <a name="services-not-created-by-the-service-container"></a><span data-ttu-id="4b62d-328">Služby, které nevytvořil kontejner služby</span><span class="sxs-lookup"><span data-stu-id="4b62d-328">Services not created by the service container</span></span>
<!--Review: Who cares that service instances aren't disposed, singletons aren't disposed until the app shuts down anyway.
  -->
<span data-ttu-id="4b62d-329">Uvažujte následující kód:</span><span class="sxs-lookup"><span data-stu-id="4b62d-329">Consider the following code:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup2.cs?name=snippet)]

<span data-ttu-id="4b62d-330">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="4b62d-330">In the preceding code:</span></span>

* <span data-ttu-id="4b62d-331">Instance služby nejsou vytvořeny kontejnerem služby.</span><span class="sxs-lookup"><span data-stu-id="4b62d-331">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="4b62d-332">Zamýšlené životnosti služeb nejsou rozhraním známy.</span><span class="sxs-lookup"><span data-stu-id="4b62d-332">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="4b62d-333">Rozhraní neodstraní služby automaticky.</span><span class="sxs-lookup"><span data-stu-id="4b62d-333">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="4b62d-334">Pokud se služby explicitně neodstraní v kódu pro vývojáře, zůstanou zachovány, dokud se aplikace neukončí.</span><span class="sxs-lookup"><span data-stu-id="4b62d-334">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="4b62d-335">Doprovodné materiály k rozhraní IDisposable pro přechodné a sdílené instance</span><span class="sxs-lookup"><span data-stu-id="4b62d-335">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="4b62d-336">Přechodná, omezená doba života</span><span class="sxs-lookup"><span data-stu-id="4b62d-336">Transient, limited lifetime</span></span>

<span data-ttu-id="4b62d-337">**Scénář**</span><span class="sxs-lookup"><span data-stu-id="4b62d-337">**Scenario**</span></span>

<span data-ttu-id="4b62d-338">Aplikace vyžaduje <xref:System.IDisposable> instanci s přechodným životností pro některý z následujících scénářů:</span><span class="sxs-lookup"><span data-stu-id="4b62d-338">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="4b62d-339">Instance je vyřešena v kořenovém oboru (kořenovém kontejneru).</span><span class="sxs-lookup"><span data-stu-id="4b62d-339">The instance is resolved in the root scope (root container).</span></span>
* <span data-ttu-id="4b62d-340">Instance by měla být uvolněna před ukončením oboru.</span><span class="sxs-lookup"><span data-stu-id="4b62d-340">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="4b62d-341">**Řešení**</span><span class="sxs-lookup"><span data-stu-id="4b62d-341">**Solution**</span></span>

<span data-ttu-id="4b62d-342">Pomocí modelu továrny vytvořte instanci mimo nadřazený obor.</span><span class="sxs-lookup"><span data-stu-id="4b62d-342">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="4b62d-343">V takové situaci by aplikace měla obvykle `Create` metodu, která volá přímo konstruktor finálního typu.</span><span class="sxs-lookup"><span data-stu-id="4b62d-343">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="4b62d-344">Pokud má konečný typ jiné závislosti, továrna může:</span><span class="sxs-lookup"><span data-stu-id="4b62d-344">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="4b62d-345">Přijmout <xref:System.IServiceProvider> ve svém konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="4b62d-345">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="4b62d-346">Slouží <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> k vytvoření instance instance mimo kontejner, při použití kontejneru pro jeho závislosti.</span><span class="sxs-lookup"><span data-stu-id="4b62d-346">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="4b62d-347">Sdílená instance, omezená doba života</span><span class="sxs-lookup"><span data-stu-id="4b62d-347">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="4b62d-348">**Scénář**</span><span class="sxs-lookup"><span data-stu-id="4b62d-348">**Scenario**</span></span>

<span data-ttu-id="4b62d-349">Aplikace vyžaduje sdílenou <xref:System.IDisposable> instanci napříč více službami, ale <xref:System.IDisposable> měla by mít omezené trvání.</span><span class="sxs-lookup"><span data-stu-id="4b62d-349">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="4b62d-350">**Řešení**</span><span class="sxs-lookup"><span data-stu-id="4b62d-350">**Solution**</span></span>

<span data-ttu-id="4b62d-351">Zaregistrujte instanci s vymezenou životností.</span><span class="sxs-lookup"><span data-stu-id="4b62d-351">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="4b62d-352">Použijte <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> k zahájení a vytvoření nového <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> .</span><span class="sxs-lookup"><span data-stu-id="4b62d-352">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="4b62d-353"><xref:System.IServiceProvider>Požadované služby získáte pomocí tohoto oboru.</span><span class="sxs-lookup"><span data-stu-id="4b62d-353">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="4b62d-354">Vyřadit rozsah, pokud by životnost měla končit.</span><span class="sxs-lookup"><span data-stu-id="4b62d-354">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-idisposable-guidelines"></a><span data-ttu-id="4b62d-355">Obecné pokyny pro IDisposable</span><span class="sxs-lookup"><span data-stu-id="4b62d-355">General IDisposable guidelines</span></span>

* <span data-ttu-id="4b62d-356">Neregistrujte <xref:System.IDisposable> instance s přechodným oborem.</span><span class="sxs-lookup"><span data-stu-id="4b62d-356">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="4b62d-357">Místo toho použijte výrobní model.</span><span class="sxs-lookup"><span data-stu-id="4b62d-357">Use the factory pattern instead.</span></span>
* <span data-ttu-id="4b62d-358">V kořenovém oboru neřešte přechodné nebo vymezené <xref:System.IDisposable> instance.</span><span class="sxs-lookup"><span data-stu-id="4b62d-358">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="4b62d-359">Jedinou obecnou výjimkou je, když aplikace vytvoří nebo znovu vytvoří a odstraní <xref:System.IServiceProvider> , což není ideální vzor.</span><span class="sxs-lookup"><span data-stu-id="4b62d-359">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="4b62d-360">Příjem <xref:System.IDisposable> závislosti přes di nevyžaduje, aby se příjemce sám implementoval <xref:System.IDisposable> .</span><span class="sxs-lookup"><span data-stu-id="4b62d-360">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="4b62d-361">Příjemce <xref:System.IDisposable> závislosti by neměl volat <xref:System.IDisposable.Dispose%2A> tuto závislost.</span><span class="sxs-lookup"><span data-stu-id="4b62d-361">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="4b62d-362">Obory by měly sloužit k řízení životnosti služeb.</span><span class="sxs-lookup"><span data-stu-id="4b62d-362">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="4b62d-363">Obory nejsou hierarchické a mezi obory není žádné zvláštní připojení.</span><span class="sxs-lookup"><span data-stu-id="4b62d-363">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="4b62d-364">Výchozí nahrazení kontejneru služby</span><span class="sxs-lookup"><span data-stu-id="4b62d-364">Default service container replacement</span></span>

<span data-ttu-id="4b62d-365">Integrovaný kontejner služeb je navržený tak, aby sloužil potřebám architektury a většině spotřebitelských aplikací.</span><span class="sxs-lookup"><span data-stu-id="4b62d-365">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="4b62d-366">Pokud nepotřebujete určitou funkci, kterou integrovaný kontejner nepodporuje, doporučujeme použít vestavěný kontejner, například:</span><span class="sxs-lookup"><span data-stu-id="4b62d-366">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="4b62d-367">Vkládání vlastností</span><span class="sxs-lookup"><span data-stu-id="4b62d-367">Property injection</span></span>
* <span data-ttu-id="4b62d-368">Vkládání na základě názvu</span><span class="sxs-lookup"><span data-stu-id="4b62d-368">Injection based on name</span></span>
* <span data-ttu-id="4b62d-369">Podřízené kontejnery</span><span class="sxs-lookup"><span data-stu-id="4b62d-369">Child containers</span></span>
* <span data-ttu-id="4b62d-370">Vlastní Správa životního cyklu</span><span class="sxs-lookup"><span data-stu-id="4b62d-370">Custom lifetime management</span></span>
* <span data-ttu-id="4b62d-371">`Func<T>` Podpora opožděné inicializace</span><span class="sxs-lookup"><span data-stu-id="4b62d-371">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="4b62d-372">Registrace založená na konvencích</span><span class="sxs-lookup"><span data-stu-id="4b62d-372">Convention-based registration</span></span>

<span data-ttu-id="4b62d-373">U ASP.NET Corech aplikací lze použít následující kontejnery třetích stran:</span><span class="sxs-lookup"><span data-stu-id="4b62d-373">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="4b62d-374">Autofac</span><span class="sxs-lookup"><span data-stu-id="4b62d-374">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="4b62d-375">DryIoc</span><span class="sxs-lookup"><span data-stu-id="4b62d-375">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="4b62d-376">Integrit</span><span class="sxs-lookup"><span data-stu-id="4b62d-376">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="4b62d-377">LightInject</span><span class="sxs-lookup"><span data-stu-id="4b62d-377">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="4b62d-378">Lamar</span><span class="sxs-lookup"><span data-stu-id="4b62d-378">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="4b62d-379">Stashbox</span><span class="sxs-lookup"><span data-stu-id="4b62d-379">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="4b62d-380">Unity</span><span class="sxs-lookup"><span data-stu-id="4b62d-380">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="4b62d-381">Bezpečnost vlákna</span><span class="sxs-lookup"><span data-stu-id="4b62d-381">Thread safety</span></span>

<span data-ttu-id="4b62d-382">Vytvořte služby, které jsou v bezpečí pro přístup z více vláken.</span><span class="sxs-lookup"><span data-stu-id="4b62d-382">Create thread-safe singleton services.</span></span> <span data-ttu-id="4b62d-383">Pokud má služba typu Singleton závislost na přechodné službě, přechodná služba může také vyžadovat zabezpečení vlákna v závislosti na tom, jak je používá typ singleton.</span><span class="sxs-lookup"><span data-stu-id="4b62d-383">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="4b62d-384">Výrobní metoda samostatné služby, jako je například druhý argument pro [AddSingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), nemusí být bezpečná pro přístup z více vláken.</span><span class="sxs-lookup"><span data-stu-id="4b62d-384">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="4b62d-385">Podobně jako konstruktor typu ( `static` ), je zaručeno, že bude volán jednou vláknem.</span><span class="sxs-lookup"><span data-stu-id="4b62d-385">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="4b62d-386">Doporučení</span><span class="sxs-lookup"><span data-stu-id="4b62d-386">Recommendations</span></span>

* <span data-ttu-id="4b62d-387">`async/await``Task`řešení služeb a se nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="4b62d-387">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="4b62d-388">Jazyk C# nepodporuje asynchronní konstruktory.</span><span class="sxs-lookup"><span data-stu-id="4b62d-388">C# does not support asynchronous constructors.</span></span> <span data-ttu-id="4b62d-389">Doporučeným vzorem je použití asynchronních metod po synchronním překladu služby.</span><span class="sxs-lookup"><span data-stu-id="4b62d-389">The recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>
* <span data-ttu-id="4b62d-390">Neukládejte data a konfiguraci přímo do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="4b62d-390">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="4b62d-391">Například nákupní košík uživatele by neměl být obvykle přidán do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="4b62d-391">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="4b62d-392">Konfigurace by měla používat [vzor možností](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="4b62d-392">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="4b62d-393">Podobně nepoužívejte objekty "držitel dat", které existují pouze k povolení přístupu k jinému objektu.</span><span class="sxs-lookup"><span data-stu-id="4b62d-393">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="4b62d-394">Je lepší žádat o skutečnou položku přes DI.</span><span class="sxs-lookup"><span data-stu-id="4b62d-394">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="4b62d-395">Vyhněte se statickému přístupu ke službám.</span><span class="sxs-lookup"><span data-stu-id="4b62d-395">Avoid static access to services.</span></span> <span data-ttu-id="4b62d-396">Vyhněte se například statickému psaní [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) pro použití jinde).</span><span class="sxs-lookup"><span data-stu-id="4b62d-396">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>
* <span data-ttu-id="4b62d-397">Snažte se DI Factory rychle a synchronně.</span><span class="sxs-lookup"><span data-stu-id="4b62d-397">Keep DI factories fast and synchronous.</span></span>
* <span data-ttu-id="4b62d-398">Vyhněte se použití *vzoru lokátoru služby*.</span><span class="sxs-lookup"><span data-stu-id="4b62d-398">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="4b62d-399">Například Nevolejte <xref:System.IServiceProvider.GetService*> pro získání instance služby, když můžete místo toho použít di:</span><span class="sxs-lookup"><span data-stu-id="4b62d-399">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="4b62d-400">**Nesprávně:**</span><span class="sxs-lookup"><span data-stu-id="4b62d-400">**Incorrect:**</span></span>

    ![Nesprávný kód](dependency-injection/_static/bad.png)

  <span data-ttu-id="4b62d-402">**Správné**:</span><span class="sxs-lookup"><span data-stu-id="4b62d-402">**Correct**:</span></span>

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
* <span data-ttu-id="4b62d-403">Další změnou lokátoru služby, aby se zabránilo, je vložení továrny, která vyřeší závislosti za běhu.</span><span class="sxs-lookup"><span data-stu-id="4b62d-403">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="4b62d-404">Oba tyto postupy kombinují [inverze strategií řízení](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .</span><span class="sxs-lookup"><span data-stu-id="4b62d-404">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
* <span data-ttu-id="4b62d-405">Vyhněte se statickému přístupu k `HttpContext` (například [IHttpContextAccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="4b62d-405">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<a name="ASP0000"></a>
* <span data-ttu-id="4b62d-406">Vyhněte se volání do <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> v `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="4b62d-406">Avoid calls to <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> in `ConfigureServices`.</span></span> <span data-ttu-id="4b62d-407">`BuildServiceProvider`K volání obvykle dochází, když vývojář chce službu vyřešit `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="4b62d-407">Calling `BuildServiceProvider` typically happens when the developer wants to resolve a service in `ConfigureServices`.</span></span> <span data-ttu-id="4b62d-408">Zvažte například případ, kdy potřebujete získat `LoginPath` z konfigurace.</span><span class="sxs-lookup"><span data-stu-id="4b62d-408">For example, consider the case where you need go get the `LoginPath` from configuration.</span></span> <span data-ttu-id="4b62d-409">Vyhněte se následujícímu kódu:</span><span class="sxs-lookup"><span data-stu-id="4b62d-409">Avoid the following code:</span></span>

  ![Chybný kód volající BuildServiceProvider](~/fundamentals/dependency-injection/_static/badcodeX.png)

  <span data-ttu-id="4b62d-411">Na předchozím obrázku vyberte zelenou vlnovku pod nadpisem `services.BuildServiceProvider` zobrazit následující upozornění ASP0000:</span><span class="sxs-lookup"><span data-stu-id="4b62d-411">In the preceding image, selecting the green wavy line under `services.BuildServiceProvider` shows the following ASP0000 warning:</span></span>
    * <span data-ttu-id="4b62d-412">ASP0000 volání ' BuildServiceProvider ' z kódu aplikace vede k vytvoření další kopie ojedinělých služeb, které jsou vytvářeny.</span><span class="sxs-lookup"><span data-stu-id="4b62d-412">ASP0000 Calling 'BuildServiceProvider' from application code results in an additional copy of singleton services being created.</span></span> <span data-ttu-id="4b62d-413">Zvažte alternativy, jako jsou třeba závislosti, jako jsou například služby, jako jsou parametry konfigurace.</span><span class="sxs-lookup"><span data-stu-id="4b62d-413">Consider alternatives such as dependency injecting services as parameters to 'Configure'.</span></span>

   <span data-ttu-id="4b62d-414">Volání `BuildServiceProvider` vytvoří druhý kontejner, který může vytvořit roztrhané jednoznačné objekty a způsobit odkazy na grafy objektů napříč více kontejnery.</span><span class="sxs-lookup"><span data-stu-id="4b62d-414">Calling `BuildServiceProvider` creates a second container, which can create torn singletons and cause references to object graphs across multiple containers.</span></span> <span data-ttu-id="4b62d-415">Správný způsob, jak získat `LoginPath` , je použít vzor možnosti s di:</span><span class="sxs-lookup"><span data-stu-id="4b62d-415">A correct way to get `LoginPath` is using the option pattern with DI:</span></span>

  [!code-csharp[](dependency-injection/samples/3.x/AntiPattern3/Startup.cs?name=snippet)]

* <span data-ttu-id="4b62d-416">Přechodné služby za použití jsou zachyceny kontejnerem k vyřazení.</span><span class="sxs-lookup"><span data-stu-id="4b62d-416">Disposable transient services are captured by the container for disposal.</span></span> <span data-ttu-id="4b62d-417">To může způsobit nevracení paměti, pokud je vyřešeno z kontejneru nejvyšší úrovně.</span><span class="sxs-lookup"><span data-stu-id="4b62d-417">This can turn into a memory leak if resolved from the top level container.</span></span>
* <span data-ttu-id="4b62d-418">Povolte ověřování oboru a ujistěte se, že aplikace nemá oborové služby zachytávání typu singleton.</span><span class="sxs-lookup"><span data-stu-id="4b62d-418">Enable scope validation to make sure the app doesn't have scoped services capturing singletons.</span></span> <span data-ttu-id="4b62d-419">Další informace najdete v tématu [ověřování oboru](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="4b62d-419">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="4b62d-420">Podobně jako u všech sad doporučení se může stát, že se vyžaduje ignorování doporučení.</span><span class="sxs-lookup"><span data-stu-id="4b62d-420">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="4b62d-421">Výjimky jsou zřídka, většinou zvláštní případy v rámci samotného rozhraní.</span><span class="sxs-lookup"><span data-stu-id="4b62d-421">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="4b62d-422">DI je *alternativou* ke vzorům statických nebo globálních přístupů k objektům.</span><span class="sxs-lookup"><span data-stu-id="4b62d-422">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="4b62d-423">Je možné, že nebudete moci využít výhody DI, pokud je kombinujete se statickým přístupem k objektům.</span><span class="sxs-lookup"><span data-stu-id="4b62d-423">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="4b62d-424">Doporučené vzory pro víceklientské architektury v DI</span><span class="sxs-lookup"><span data-stu-id="4b62d-424">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="4b62d-425">[Sadu jader](https://github.com/OrchardCMS/OrchardCore) nabízí víceklientské architektury.</span><span class="sxs-lookup"><span data-stu-id="4b62d-425">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) provides multi-tenancy.</span></span> <span data-ttu-id="4b62d-426">Další informace najdete v dokumentaci k sadě [sad](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="4b62d-426">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="4b62d-427">V ukázkových aplikacích v tématu najdete https://github.com/OrchardCMS/OrchardCore.Samples příklady vytváření modulárních a víceklientské aplikací s využitím jenom sady Core Core Framework bez jakýchkoli funkcí specifických pro CMS.</span><span class="sxs-lookup"><span data-stu-id="4b62d-427">See the samples apps at https://github.com/OrchardCMS/OrchardCore.Samples for examples of how to build modular and multi-tenant apps using just Orchard Core Framework without any of the CMS specific features.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="4b62d-428">Služby poskytované rozhraním</span><span class="sxs-lookup"><span data-stu-id="4b62d-428">Framework-provided services</span></span>

<span data-ttu-id="4b62d-429">`Startup.ConfigureServices`Metoda zodpovídá za definování služeb, které aplikace používá, včetně funkcí platformy, jako je například Entity Framework Core a ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="4b62d-429">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="4b62d-430">Zpočátku `IServiceCollection` `ConfigureServices` služba má služby definované rozhraním v závislosti na [tom, jak byl hostitel nakonfigurovaný](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="4b62d-430">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="4b62d-431">Aplikace založené na šablonách ASP.NET Core mají více než 250 služeb zaregistrovaných v rámci rozhraní.</span><span class="sxs-lookup"><span data-stu-id="4b62d-431">Apps based on an ASP.NET Core templates have more than 250 services registered by the framework.</span></span> <span data-ttu-id="4b62d-432">V následující tabulce je uvedena malá ukázka služeb registrovaných v rozhraní.</span><span class="sxs-lookup"><span data-stu-id="4b62d-432">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="4b62d-433">Typ služby</span><span class="sxs-lookup"><span data-stu-id="4b62d-433">Service Type</span></span> | <span data-ttu-id="4b62d-434">Doba platnosti</span><span class="sxs-lookup"><span data-stu-id="4b62d-434">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="4b62d-435">Dočasný</span><span class="sxs-lookup"><span data-stu-id="4b62d-435">Transient</span></span> |
| <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> | <span data-ttu-id="4b62d-436">Singleton</span><span class="sxs-lookup"><span data-stu-id="4b62d-436">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> | <span data-ttu-id="4b62d-437">Singleton</span><span class="sxs-lookup"><span data-stu-id="4b62d-437">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="4b62d-438">Singleton</span><span class="sxs-lookup"><span data-stu-id="4b62d-438">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="4b62d-439">Dočasný</span><span class="sxs-lookup"><span data-stu-id="4b62d-439">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="4b62d-440">Singleton</span><span class="sxs-lookup"><span data-stu-id="4b62d-440">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="4b62d-441">Dočasný</span><span class="sxs-lookup"><span data-stu-id="4b62d-441">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="4b62d-442">Singleton</span><span class="sxs-lookup"><span data-stu-id="4b62d-442">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="4b62d-443">Singleton</span><span class="sxs-lookup"><span data-stu-id="4b62d-443">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="4b62d-444">Singleton</span><span class="sxs-lookup"><span data-stu-id="4b62d-444">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="4b62d-445">Dočasný</span><span class="sxs-lookup"><span data-stu-id="4b62d-445">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="4b62d-446">Singleton</span><span class="sxs-lookup"><span data-stu-id="4b62d-446">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="4b62d-447">Singleton</span><span class="sxs-lookup"><span data-stu-id="4b62d-447">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="4b62d-448">Singleton</span><span class="sxs-lookup"><span data-stu-id="4b62d-448">Singleton</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="4b62d-449">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="4b62d-449">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* [<span data-ttu-id="4b62d-450">NORWEGIAN Developers Conference se vzory konferencí pro vývoj aplikací pro DI</span><span class="sxs-lookup"><span data-stu-id="4b62d-450">NDC Conference Patterns for DI app development</span></span>](https://www.youtube.com/watch?v=x-C-CNBVTaY)
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="4b62d-451">Čtyři způsoby, jak vyřadit IDisposable v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4b62d-451">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="4b62d-452">Zápis čistého kódu v ASP.NET Core pomocí injektáže závislosti (MSDN)</span><span class="sxs-lookup"><span data-stu-id="4b62d-452">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="4b62d-453">Explicitní závislosti – princip</span><span class="sxs-lookup"><span data-stu-id="4b62d-453">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="4b62d-454">Inverze řídicích kontejnerů a vzor injektáže závislosti (Martin Fowlera)</span><span class="sxs-lookup"><span data-stu-id="4b62d-454">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="4b62d-455">Jak zaregistrovat službu s více rozhraními v ASP.NET Core DI</span><span class="sxs-lookup"><span data-stu-id="4b62d-455">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4b62d-456">[Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com)a [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="4b62d-456">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="4b62d-457">ASP.NET Core podporuje vzor návrhu softwaru pro vkládání závislostí (DI), což je technika pro dosažení [inverze ovládacího prvku (IOC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) mezi třídami a jejich závislostmi.</span><span class="sxs-lookup"><span data-stu-id="4b62d-457">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="4b62d-458">Další informace, které jsou specifické pro vkládání závislostí v rámci řadičů MVC, najdete v tématu <xref:mvc/controllers/dependency-injection> .</span><span class="sxs-lookup"><span data-stu-id="4b62d-458">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="4b62d-459">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4b62d-459">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="4b62d-460">Přehled injektáže závislosti</span><span class="sxs-lookup"><span data-stu-id="4b62d-460">Overview of dependency injection</span></span>

<span data-ttu-id="4b62d-461">*Závislost* je libovolný objekt, který vyžaduje jiný objekt.</span><span class="sxs-lookup"><span data-stu-id="4b62d-461">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="4b62d-462">Projděte si následující `MyDependency` třídu s `WriteMessage` metodou, na které jsou závislé jiné třídy v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="4b62d-462">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="4b62d-463">Instance `MyDependency` třídy může být vytvořena, aby byla `WriteMessage` Metoda k dispozici pro třídu.</span><span class="sxs-lookup"><span data-stu-id="4b62d-463">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="4b62d-464">`MyDependency`Třída je závislostí `IndexModel` třídy:</span><span class="sxs-lookup"><span data-stu-id="4b62d-464">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="4b62d-465">Třída vytvoří a přímo závisí na `MyDependency` instanci.</span><span class="sxs-lookup"><span data-stu-id="4b62d-465">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="4b62d-466">Závislosti kódu (například předchozí příklad) jsou problematické a je třeba se jim vyhnout z následujících důvodů:</span><span class="sxs-lookup"><span data-stu-id="4b62d-466">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="4b62d-467">Chcete-li nahradit `MyDependency` jinou implementací, třída musí být upravena.</span><span class="sxs-lookup"><span data-stu-id="4b62d-467">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="4b62d-468">Pokud `MyDependency` má závislosti, musí být nakonfigurovány třídou.</span><span class="sxs-lookup"><span data-stu-id="4b62d-468">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="4b62d-469">Ve velkém projektu s více třídami, v závislosti na tom `MyDependency` , je kód konfigurace rozptýlen napříč aplikací.</span><span class="sxs-lookup"><span data-stu-id="4b62d-469">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="4b62d-470">Tato implementace je obtížná pro testování částí.</span><span class="sxs-lookup"><span data-stu-id="4b62d-470">This implementation is difficult to unit test.</span></span> <span data-ttu-id="4b62d-471">Aplikace by měla používat třídu typu "nebo zástupné procedury" `MyDependency` , což není u tohoto přístupu možné.</span><span class="sxs-lookup"><span data-stu-id="4b62d-471">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="4b62d-472">Vkládání závislostí řeší tyto problémy prostřednictvím:</span><span class="sxs-lookup"><span data-stu-id="4b62d-472">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="4b62d-473">Použití rozhraní nebo základní třídy k abstrakci implementace závislosti.</span><span class="sxs-lookup"><span data-stu-id="4b62d-473">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="4b62d-474">Registrace závislosti v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="4b62d-474">Registration of the dependency in a service container.</span></span> <span data-ttu-id="4b62d-475">ASP.NET Core poskytuje integrovaný kontejner služeb <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="4b62d-475">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="4b62d-476">Služby jsou registrovány v metodě aplikace `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="4b62d-476">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="4b62d-477">*Vložení* služby do konstruktoru třídy, kde se používá.</span><span class="sxs-lookup"><span data-stu-id="4b62d-477">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="4b62d-478">Rozhraní přebírá zodpovědnost za vytvoření instance závislosti a její odstranění, když už ji nepotřebujete.</span><span class="sxs-lookup"><span data-stu-id="4b62d-478">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="4b62d-479">V [ukázkové aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) `IMyDependency` definuje rozhraní metodu, kterou služba poskytuje aplikaci:</span><span class="sxs-lookup"><span data-stu-id="4b62d-479">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="4b62d-480">Toto rozhraní je implementováno konkrétním typem `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="4b62d-480">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="4b62d-481">`MyDependency` vyžádá <xref:Microsoft.Extensions.Logging.ILogger`1> v jeho konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="4b62d-481">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="4b62d-482">Není neobvyklé používat vkládání závislostí v zřetězeném způsobem.</span><span class="sxs-lookup"><span data-stu-id="4b62d-482">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="4b62d-483">Každá požadovaná závislost zase vyžádá své vlastní závislosti.</span><span class="sxs-lookup"><span data-stu-id="4b62d-483">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="4b62d-484">Kontejner vyřeší závislosti v grafu a vrátí plně přeloženou službu.</span><span class="sxs-lookup"><span data-stu-id="4b62d-484">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="4b62d-485">Kolektivní sada závislostí, které je třeba vyřešit, se obvykle označuje jako *strom závislosti*, *graf závislosti*nebo *graf objektů*.</span><span class="sxs-lookup"><span data-stu-id="4b62d-485">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="4b62d-486">`IMyDependency` a `ILogger<TCategoryName>` musí být registrována v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="4b62d-486">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="4b62d-487">`IMyDependency` je zaregistrován v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="4b62d-487">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="4b62d-488">`ILogger<TCategoryName>` je zaregistrované v infrastruktuře abstrakce protokolování, takže se jedná o [službu](#framework-provided-services) , která je zaregistrovaná ve výchozím rozhraní.</span><span class="sxs-lookup"><span data-stu-id="4b62d-488">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="4b62d-489">Kontejner se překládá `ILogger<TCategoryName>` tím, že využije [(Obecné) otevřené typy](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)a eliminuje nutnost registrace každého [(obecného) konstruovaného typu](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="4b62d-489">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="4b62d-490">V ukázkové aplikaci `IMyDependency` je služba zaregistrovaná u konkrétního typu `MyDependency` .</span><span class="sxs-lookup"><span data-stu-id="4b62d-490">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="4b62d-491">Registruje rozsah platnosti služby po dobu životnosti jediného požadavku.</span><span class="sxs-lookup"><span data-stu-id="4b62d-491">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="4b62d-492">[Doby platnosti služeb](#service-lifetimes) jsou popsány dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="4b62d-492">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="4b62d-493">Jednotlivé `services.Add{SERVICE_NAME}` metody rozšíření přidávají (a případně nakonfigurují) služby.</span><span class="sxs-lookup"><span data-stu-id="4b62d-493">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="4b62d-494">Například `services.AddMvc()` přidá Razor stránky služeb a MVC vyžaduje.</span><span class="sxs-lookup"><span data-stu-id="4b62d-494">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="4b62d-495">Doporučujeme, aby aplikace dodržovaly tuto konvenci.</span><span class="sxs-lookup"><span data-stu-id="4b62d-495">We recommended that apps follow this convention.</span></span> <span data-ttu-id="4b62d-496">Do oboru názvů [Microsoft. Extensions. DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) umístěte metody rozšíření pro zapouzdření skupin registrací služby.</span><span class="sxs-lookup"><span data-stu-id="4b62d-496">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="4b62d-497">Pokud konstruktor služby vyžaduje [vestavěný typ](/dotnet/csharp/language-reference/keywords/built-in-types-table), například `string` ,, může být typ vložen pomocí [Konfigurace](xref:fundamentals/configuration/index) nebo [vzoru možností](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="4b62d-497">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="4b62d-498">Instance služby je požadována prostřednictvím konstruktoru třídy, kde je služba používána a přiřazena k soukromému poli.</span><span class="sxs-lookup"><span data-stu-id="4b62d-498">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="4b62d-499">Pole se používá pro přístup ke službě podle potřeby v celé třídě.</span><span class="sxs-lookup"><span data-stu-id="4b62d-499">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="4b62d-500">V ukázkové aplikaci `IMyDependency` je instance vyžádána a používána pro volání `WriteMessage` metody služby:</span><span class="sxs-lookup"><span data-stu-id="4b62d-500">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="4b62d-501">Služby vložené do spuštění</span><span class="sxs-lookup"><span data-stu-id="4b62d-501">Services injected into Startup</span></span>

<span data-ttu-id="4b62d-502">`Startup`Při použití obecného hostitele () mohou být do konstruktoru vloženy pouze následující typy služeb <xref:Microsoft.Extensions.Hosting.IHostBuilder> :</span><span class="sxs-lookup"><span data-stu-id="4b62d-502">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="4b62d-503">Služby je možné vložit do `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="4b62d-503">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="4b62d-504">Další informace naleznete v tématu <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="4b62d-504">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="4b62d-505">Služby poskytované rozhraním</span><span class="sxs-lookup"><span data-stu-id="4b62d-505">Framework-provided services</span></span>

<span data-ttu-id="4b62d-506">`Startup.ConfigureServices`Metoda zodpovídá za definování služeb, které aplikace používá, včetně funkcí platformy, jako je například Entity Framework Core a ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="4b62d-506">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="4b62d-507">Zpočátku `IServiceCollection` `ConfigureServices` služba má služby definované rozhraním v závislosti na [tom, jak byl hostitel nakonfigurovaný](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="4b62d-507">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="4b62d-508">Pro aplikaci, která je založená na šabloně ASP.NET Core, není běžné, že mají stovky služeb zaregistrovaných v rámci rozhraní.</span><span class="sxs-lookup"><span data-stu-id="4b62d-508">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="4b62d-509">V následující tabulce je uvedena malá ukázka služeb registrovaných v rozhraní.</span><span class="sxs-lookup"><span data-stu-id="4b62d-509">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="4b62d-510">Typ služby</span><span class="sxs-lookup"><span data-stu-id="4b62d-510">Service Type</span></span> | <span data-ttu-id="4b62d-511">Doba platnosti</span><span class="sxs-lookup"><span data-stu-id="4b62d-511">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="4b62d-512">Dočasný</span><span class="sxs-lookup"><span data-stu-id="4b62d-512">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="4b62d-513">Singleton</span><span class="sxs-lookup"><span data-stu-id="4b62d-513">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="4b62d-514">Singleton</span><span class="sxs-lookup"><span data-stu-id="4b62d-514">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="4b62d-515">Singleton</span><span class="sxs-lookup"><span data-stu-id="4b62d-515">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="4b62d-516">Dočasný</span><span class="sxs-lookup"><span data-stu-id="4b62d-516">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="4b62d-517">Singleton</span><span class="sxs-lookup"><span data-stu-id="4b62d-517">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="4b62d-518">Dočasný</span><span class="sxs-lookup"><span data-stu-id="4b62d-518">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="4b62d-519">Singleton</span><span class="sxs-lookup"><span data-stu-id="4b62d-519">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="4b62d-520">Singleton</span><span class="sxs-lookup"><span data-stu-id="4b62d-520">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="4b62d-521">Singleton</span><span class="sxs-lookup"><span data-stu-id="4b62d-521">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="4b62d-522">Dočasný</span><span class="sxs-lookup"><span data-stu-id="4b62d-522">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="4b62d-523">Singleton</span><span class="sxs-lookup"><span data-stu-id="4b62d-523">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="4b62d-524">Singleton</span><span class="sxs-lookup"><span data-stu-id="4b62d-524">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="4b62d-525">Singleton</span><span class="sxs-lookup"><span data-stu-id="4b62d-525">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="4b62d-526">Registrace dalších služeb pomocí rozšiřujících metod</span><span class="sxs-lookup"><span data-stu-id="4b62d-526">Register additional services with extension methods</span></span>

<span data-ttu-id="4b62d-527">Pokud je k dispozici metoda rozšíření kolekce služeb pro registraci služby (a jejích závislých služeb, v případě potřeby), konvence používá jedinou `Add{SERVICE_NAME}` metodu rozšíření k registraci všech služeb vyžadovaných touto službou.</span><span class="sxs-lookup"><span data-stu-id="4b62d-527">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="4b62d-528">Následující kód je příkladem přidání dalších služeb do kontejneru pomocí metod rozšíření [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) a <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> :</span><span class="sxs-lookup"><span data-stu-id="4b62d-528">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="4b62d-529">Další informace najdete <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> ve třídě v dokumentaci k rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="4b62d-529">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="4b62d-530">Životnost služeb</span><span class="sxs-lookup"><span data-stu-id="4b62d-530">Service lifetimes</span></span>

<span data-ttu-id="4b62d-531">Vyberte odpovídající dobu života pro každou registrovanou službu.</span><span class="sxs-lookup"><span data-stu-id="4b62d-531">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="4b62d-532">Služby ASP.NET Core Services je možné nakonfigurovat s následujícími životnostmi:</span><span class="sxs-lookup"><span data-stu-id="4b62d-532">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="4b62d-533">Dočasný</span><span class="sxs-lookup"><span data-stu-id="4b62d-533">Transient</span></span>

<span data-ttu-id="4b62d-534">Služba přechodných životností ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*> ) se vytvoří pokaždé, když se požadují z kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="4b62d-534">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="4b62d-535">Tato životnost funguje nejlépe pro odlehčené bezstavové služby.</span><span class="sxs-lookup"><span data-stu-id="4b62d-535">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="4b62d-536">V aplikacích, které zpracovávají požadavky, jsou přechodné služby na konci žádosti zrušené.</span><span class="sxs-lookup"><span data-stu-id="4b62d-536">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="4b62d-537">Obor</span><span class="sxs-lookup"><span data-stu-id="4b62d-537">Scoped</span></span>

<span data-ttu-id="4b62d-538">Oborové služby () s rozsahem platnosti ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*> ) se vytvoří jednou za požadavek klienta (připojení).</span><span class="sxs-lookup"><span data-stu-id="4b62d-538">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="4b62d-539">V aplikacích, které zpracovávají požadavky, se vymezené služby na konci žádosti odstraní.</span><span class="sxs-lookup"><span data-stu-id="4b62d-539">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="4b62d-540">Při použití oboru služby v middlewaru založit službu do `Invoke` `InvokeAsync` metody nebo.</span><span class="sxs-lookup"><span data-stu-id="4b62d-540">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="4b62d-541">Nepoužívejte [vkládání pomocí injektáže konstruktoru](xref:mvc/controllers/dependency-injection#constructor-injection) , protože vynutí, aby se služba chovala jako typ singleton.</span><span class="sxs-lookup"><span data-stu-id="4b62d-541">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="4b62d-542">Další informace naleznete v tématu <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="4b62d-542">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="4b62d-543">Singleton</span><span class="sxs-lookup"><span data-stu-id="4b62d-543">Singleton</span></span>

<span data-ttu-id="4b62d-544">Služba singleton životnosti ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*> ) je vytvořena při prvním vyžádání (nebo při `Startup.ConfigureServices` spuštění a instance je určena pro registraci služby).</span><span class="sxs-lookup"><span data-stu-id="4b62d-544">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="4b62d-545">Každý další požadavek používá stejnou instanci.</span><span class="sxs-lookup"><span data-stu-id="4b62d-545">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="4b62d-546">Pokud aplikace vyžaduje chování singleton, doporučuje se povolit kontejneru služby spravovat dobu života služby.</span><span class="sxs-lookup"><span data-stu-id="4b62d-546">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="4b62d-547">Neimplementujte vzor návrhu singleton a poskytněte uživatelský kód pro správu životního cyklu objektu ve třídě.</span><span class="sxs-lookup"><span data-stu-id="4b62d-547">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="4b62d-548">V aplikacích, které zpracovávají požadavky, jsou nejednoznačné služby uvolněny, když <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> je uvolněna při vypnutí aplikace.</span><span class="sxs-lookup"><span data-stu-id="4b62d-548">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="4b62d-549">Není bezpečné přeložit oborovou službu z typu singleton.</span><span class="sxs-lookup"><span data-stu-id="4b62d-549">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="4b62d-550">Může dojít k tomu, že služba má při zpracování dalších požadavků špatný stav.</span><span class="sxs-lookup"><span data-stu-id="4b62d-550">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="4b62d-551">Metody registrace služby</span><span class="sxs-lookup"><span data-stu-id="4b62d-551">Service registration methods</span></span>

<span data-ttu-id="4b62d-552">Metody rozšíření registrace služby nabízejí přetížení, která jsou užitečná pro konkrétní scénáře.</span><span class="sxs-lookup"><span data-stu-id="4b62d-552">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="4b62d-553">Metoda</span><span class="sxs-lookup"><span data-stu-id="4b62d-553">Method</span></span> | <span data-ttu-id="4b62d-554">Automaticky</span><span class="sxs-lookup"><span data-stu-id="4b62d-554">Automatic</span></span><br><span data-ttu-id="4b62d-555">object</span><span class="sxs-lookup"><span data-stu-id="4b62d-555">object</span></span><br><span data-ttu-id="4b62d-556">odvod</span><span class="sxs-lookup"><span data-stu-id="4b62d-556">disposal</span></span> | <span data-ttu-id="4b62d-557">Několik</span><span class="sxs-lookup"><span data-stu-id="4b62d-557">Multiple</span></span><br><span data-ttu-id="4b62d-558">implementace</span><span class="sxs-lookup"><span data-stu-id="4b62d-558">implementations</span></span> | <span data-ttu-id="4b62d-559">Pass – argumenty</span><span class="sxs-lookup"><span data-stu-id="4b62d-559">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="4b62d-560">Příklad:</span><span class="sxs-lookup"><span data-stu-id="4b62d-560">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="4b62d-561">Ano</span><span class="sxs-lookup"><span data-stu-id="4b62d-561">Yes</span></span> | <span data-ttu-id="4b62d-562">Ano</span><span class="sxs-lookup"><span data-stu-id="4b62d-562">Yes</span></span> | <span data-ttu-id="4b62d-563">No</span><span class="sxs-lookup"><span data-stu-id="4b62d-563">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="4b62d-564">Příklady:</span><span class="sxs-lookup"><span data-stu-id="4b62d-564">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="4b62d-565">Ano</span><span class="sxs-lookup"><span data-stu-id="4b62d-565">Yes</span></span> | <span data-ttu-id="4b62d-566">Ano</span><span class="sxs-lookup"><span data-stu-id="4b62d-566">Yes</span></span> | <span data-ttu-id="4b62d-567">Ano</span><span class="sxs-lookup"><span data-stu-id="4b62d-567">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="4b62d-568">Příklad:</span><span class="sxs-lookup"><span data-stu-id="4b62d-568">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="4b62d-569">Ano</span><span class="sxs-lookup"><span data-stu-id="4b62d-569">Yes</span></span> | <span data-ttu-id="4b62d-570">No</span><span class="sxs-lookup"><span data-stu-id="4b62d-570">No</span></span> | <span data-ttu-id="4b62d-571">No</span><span class="sxs-lookup"><span data-stu-id="4b62d-571">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="4b62d-572">Příklady:</span><span class="sxs-lookup"><span data-stu-id="4b62d-572">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="4b62d-573">No</span><span class="sxs-lookup"><span data-stu-id="4b62d-573">No</span></span> | <span data-ttu-id="4b62d-574">Ano</span><span class="sxs-lookup"><span data-stu-id="4b62d-574">Yes</span></span> | <span data-ttu-id="4b62d-575">Ano</span><span class="sxs-lookup"><span data-stu-id="4b62d-575">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="4b62d-576">Příklady:</span><span class="sxs-lookup"><span data-stu-id="4b62d-576">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="4b62d-577">No</span><span class="sxs-lookup"><span data-stu-id="4b62d-577">No</span></span> | <span data-ttu-id="4b62d-578">No</span><span class="sxs-lookup"><span data-stu-id="4b62d-578">No</span></span> | <span data-ttu-id="4b62d-579">Ano</span><span class="sxs-lookup"><span data-stu-id="4b62d-579">Yes</span></span> |

<span data-ttu-id="4b62d-580">Další informace o vyřazení typů najdete v části věnované [vyřazení služeb](#disposal-of-services) .</span><span class="sxs-lookup"><span data-stu-id="4b62d-580">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="4b62d-581">Běžným scénářem pro více implementací je vytvoření [typů pro testování](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="4b62d-581">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="4b62d-582">`TryAdd{LIFETIME}` metody registrují službu jenom v případě, že už není zaregistrovaná implementace.</span><span class="sxs-lookup"><span data-stu-id="4b62d-582">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="4b62d-583">V následujícím příkladu se první řádek registruje `MyDependency` pro `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="4b62d-583">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="4b62d-584">Druhý řádek nemá žádný účinek, protože `IMyDependency` už má registrovanou implementaci:</span><span class="sxs-lookup"><span data-stu-id="4b62d-584">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="4b62d-585">Další informace:</span><span class="sxs-lookup"><span data-stu-id="4b62d-585">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="4b62d-586">Metody [TryAddEnumerable (ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) registrují službu pouze v případě, že již není implementace *stejného typu*.</span><span class="sxs-lookup"><span data-stu-id="4b62d-586">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="4b62d-587">Několik služeb je vyřešeno prostřednictvím `IEnumerable<{SERVICE}>` .</span><span class="sxs-lookup"><span data-stu-id="4b62d-587">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="4b62d-588">Při registraci služeb chce vývojář přidat instanci jenom v případě, že už jeden ze stejného typu není přidaný.</span><span class="sxs-lookup"><span data-stu-id="4b62d-588">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="4b62d-589">Obecně je tato metoda používána autory knihovny k tomu, aby nedocházelo k registraci dvou kopií instance v kontejneru.</span><span class="sxs-lookup"><span data-stu-id="4b62d-589">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="4b62d-590">V následujícím příkladu se první řádek registruje `MyDep` pro `IMyDep1` .</span><span class="sxs-lookup"><span data-stu-id="4b62d-590">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="4b62d-591">Druhý řádek se registruje `MyDep` pro `IMyDep2` .</span><span class="sxs-lookup"><span data-stu-id="4b62d-591">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="4b62d-592">Třetí řádek nemá žádný vliv, protože `IMyDep1` už má registrovanou implementaci `MyDep` :</span><span class="sxs-lookup"><span data-stu-id="4b62d-592">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="4b62d-593">Chování injektáže konstruktoru</span><span class="sxs-lookup"><span data-stu-id="4b62d-593">Constructor injection behavior</span></span>

<span data-ttu-id="4b62d-594">Služby je možné vyřešit pomocí dvou mechanismů:</span><span class="sxs-lookup"><span data-stu-id="4b62d-594">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="4b62d-595"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Povoluje vytvoření objektu bez registrace služby v kontejneru vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="4b62d-595"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="4b62d-596">`ActivatorUtilities` se používá s uživateli s přístupem k uživatelům, jako jsou například rutiny značek, řadiče MVC a pořadače modelů.</span><span class="sxs-lookup"><span data-stu-id="4b62d-596">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="4b62d-597">Konstruktory mohou přijímat argumenty, které nejsou poskytovány vkládáním závislostí, ale argumenty musí přiřadit výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="4b62d-597">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="4b62d-598">Když jsou služby vyřešeny `IServiceProvider` nebo `ActivatorUtilities` , [Injektáže konstruktoru](xref:mvc/controllers/dependency-injection#constructor-injection) vyžaduje *veřejný* konstruktor.</span><span class="sxs-lookup"><span data-stu-id="4b62d-598">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="4b62d-599">Když jsou služby vyřešeny nástrojem `ActivatorUtilities` , [Injektáže konstruktoru](xref:mvc/controllers/dependency-injection#constructor-injection) vyžaduje, aby existoval pouze jeden příslušný konstruktor.</span><span class="sxs-lookup"><span data-stu-id="4b62d-599">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="4b62d-600">Přetížení konstruktoru jsou podporovaná, ale může existovat jenom jedno přetížení, jehož argumenty můžou být splněné vkládáním závislostí.</span><span class="sxs-lookup"><span data-stu-id="4b62d-600">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="4b62d-601">Entity Framework kontexty</span><span class="sxs-lookup"><span data-stu-id="4b62d-601">Entity Framework contexts</span></span>

<span data-ttu-id="4b62d-602">Entity Framework kontexty se obvykle přidávají do kontejneru služby pomocí [rozsahu životnosti](#service-lifetimes) , protože operace databáze webové aplikace jsou obvykle vymezeny na požadavky klienta.</span><span class="sxs-lookup"><span data-stu-id="4b62d-602">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="4b62d-603">Výchozí doba života je vymezena v případě, že při registraci kontextu databáze není zadána doba života při [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) přetížení.</span><span class="sxs-lookup"><span data-stu-id="4b62d-603">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="4b62d-604">Služby pro danou dobu života by neměly používat kontext databáze s kratší dobou životnosti než služba.</span><span class="sxs-lookup"><span data-stu-id="4b62d-604">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="4b62d-605">Možnosti života a registrace</span><span class="sxs-lookup"><span data-stu-id="4b62d-605">Lifetime and registration options</span></span>

<span data-ttu-id="4b62d-606">Chcete-li předvést rozdíl mezi možnostmi životního cyklu a registrací, zvažte následující rozhraní, která představují úlohy jako operaci s jedinečným identifikátorem `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="4b62d-606">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="4b62d-607">V závislosti na tom, jak je životnost provozní služby nakonfigurována pro následující rozhraní, kontejner poskytuje buď stejnou nebo jinou instanci služby, když je vyžádána třídou:</span><span class="sxs-lookup"><span data-stu-id="4b62d-607">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="4b62d-608">Rozhraní jsou implementována ve `Operation` třídě.</span><span class="sxs-lookup"><span data-stu-id="4b62d-608">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="4b62d-609">`Operation`Konstruktor generuje identifikátor GUID, pokud není dodán:</span><span class="sxs-lookup"><span data-stu-id="4b62d-609">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="4b62d-610">`OperationService`Je zaregistrován, který závisí na každém z ostatních `Operation` typů.</span><span class="sxs-lookup"><span data-stu-id="4b62d-610">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="4b62d-611">Když `OperationService` je požadavek proveden prostřednictvím injektáže závislosti, obdrží buď novou instanci každé služby, nebo existující instanci na základě životnosti závislé služby.</span><span class="sxs-lookup"><span data-stu-id="4b62d-611">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="4b62d-612">Když se v případě vyžádání z kontejneru vytvoří přechodné služby, `OperationId` liší se od `IOperationTransient` služby `OperationId` `OperationService` .</span><span class="sxs-lookup"><span data-stu-id="4b62d-612">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="4b62d-613">`OperationService` přijme novou instanci `IOperationTransient` třídy.</span><span class="sxs-lookup"><span data-stu-id="4b62d-613">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="4b62d-614">Nová instance vrací jinou instanci `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="4b62d-614">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="4b62d-615">Když se vytvoří vymezené služby pro jednotlivé požadavky klienta, `OperationId` `IOperationScoped` služba je stejná jako `OperationService` v rámci žádosti klienta.</span><span class="sxs-lookup"><span data-stu-id="4b62d-615">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="4b62d-616">V rámci požadavků klientů obě služby sdílejí jinou `OperationId` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="4b62d-616">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="4b62d-617">Pokud se služby instance singleton a singleton vytvoří jednou a použije se pro všechny požadavky klientů a všechny služby, `OperationId` je v rámci všech žádostí o službu konstantní.</span><span class="sxs-lookup"><span data-stu-id="4b62d-617">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="4b62d-618">V `Startup.ConfigureServices` je každý typ přidán do kontejneru podle jeho pojmenované životnosti:</span><span class="sxs-lookup"><span data-stu-id="4b62d-618">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="4b62d-619">`IOperationSingletonInstance`Služba používá konkrétní instanci se známým ID `Guid.Empty` .</span><span class="sxs-lookup"><span data-stu-id="4b62d-619">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="4b62d-620">Je jasné, že pokud se tento typ používá (jeho identifikátor GUID je všechny nuly).</span><span class="sxs-lookup"><span data-stu-id="4b62d-620">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="4b62d-621">Ukázková aplikace ukazuje dobu života objektu v rámci a mezi jednotlivými požadavky.</span><span class="sxs-lookup"><span data-stu-id="4b62d-621">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="4b62d-622">Ukázková aplikace `IndexModel` požaduje každý druh `IOperation` typu a `OperationService` .</span><span class="sxs-lookup"><span data-stu-id="4b62d-622">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="4b62d-623">Stránka potom zobrazí všechny hodnoty třídy modelu stránky a `OperationId` hodnoty služby prostřednictvím přiřazení vlastností:</span><span class="sxs-lookup"><span data-stu-id="4b62d-623">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="4b62d-624">Výsledkem dvou následujících výstupů jsou výsledky dvou požadavků:</span><span class="sxs-lookup"><span data-stu-id="4b62d-624">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="4b62d-625">**První požadavek:**</span><span class="sxs-lookup"><span data-stu-id="4b62d-625">**First request:**</span></span>

<span data-ttu-id="4b62d-626">Operace kontroleru:</span><span class="sxs-lookup"><span data-stu-id="4b62d-626">Controller operations:</span></span>

<span data-ttu-id="4b62d-627">Přechodný: d233e165-f417-469b-A866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="4b62d-627">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="4b62d-628">Vymezené: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="4b62d-628">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="4b62d-629">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="4b62d-629">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="4b62d-630">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="4b62d-630">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="4b62d-631">`OperationService` Operations</span><span class="sxs-lookup"><span data-stu-id="4b62d-631">`OperationService` operations:</span></span>

<span data-ttu-id="4b62d-632">Přechodný: c6b049eb-1318-4E31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="4b62d-632">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="4b62d-633">Vymezené: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="4b62d-633">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="4b62d-634">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="4b62d-634">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="4b62d-635">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="4b62d-635">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="4b62d-636">**Druhá žádost:**</span><span class="sxs-lookup"><span data-stu-id="4b62d-636">**Second request:**</span></span>

<span data-ttu-id="4b62d-637">Operace kontroleru:</span><span class="sxs-lookup"><span data-stu-id="4b62d-637">Controller operations:</span></span>

<span data-ttu-id="4b62d-638">Přechodný: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="4b62d-638">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="4b62d-639">Vymezené: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="4b62d-639">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="4b62d-640">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="4b62d-640">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="4b62d-641">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="4b62d-641">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="4b62d-642">`OperationService` Operations</span><span class="sxs-lookup"><span data-stu-id="4b62d-642">`OperationService` operations:</span></span>

<span data-ttu-id="4b62d-643">Přechodný: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="4b62d-643">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="4b62d-644">Vymezené: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="4b62d-644">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="4b62d-645">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="4b62d-645">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="4b62d-646">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="4b62d-646">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="4b62d-647">Pozor, které `OperationId` hodnoty se liší v rámci požadavku a mezi požadavky:</span><span class="sxs-lookup"><span data-stu-id="4b62d-647">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="4b62d-648">*Přechodné* objekty jsou vždy odlišné.</span><span class="sxs-lookup"><span data-stu-id="4b62d-648">*Transient* objects are always different.</span></span> <span data-ttu-id="4b62d-649">Přechodná `OperationId` hodnota pro první i druhý požadavek klienta se liší pro obě `OperationService` operace i mezi požadavky klientů.</span><span class="sxs-lookup"><span data-stu-id="4b62d-649">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="4b62d-650">Každé žádosti o službu a žádosti klienta se poskytne nová instance.</span><span class="sxs-lookup"><span data-stu-id="4b62d-650">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="4b62d-651">*Vymezené* objekty jsou v požadavku klienta stejné, ale v rámci požadavků klientů se liší.</span><span class="sxs-lookup"><span data-stu-id="4b62d-651">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="4b62d-652">Objekty *singleton* jsou stejné pro každý objekt a každý požadavek bez ohledu na to, zda `Operation` je instance uvedena v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="4b62d-652">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="4b62d-653">Volat služby z Main</span><span class="sxs-lookup"><span data-stu-id="4b62d-653">Call services from main</span></span>

<span data-ttu-id="4b62d-654">Vytvořte <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> pomocí [IServiceScopeFactory. CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) pro řešení oboru služby v rámci rozsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="4b62d-654">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="4b62d-655">Tento přístup je užitečný pro přístup k oboruované službě při spuštění za účelem spouštění úloh inicializace.</span><span class="sxs-lookup"><span data-stu-id="4b62d-655">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="4b62d-656">Následující příklad ukazuje, jak získat kontext pro `MyScopedService` `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="4b62d-656">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="4b62d-657">Ověřování oboru</span><span class="sxs-lookup"><span data-stu-id="4b62d-657">Scope validation</span></span>

<span data-ttu-id="4b62d-658">Když je aplikace spuštěná ve vývojovém prostředí, provede výchozí poskytovatel služeb kontroly, aby ověřil, že:</span><span class="sxs-lookup"><span data-stu-id="4b62d-658">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="4b62d-659">Oborové služby nejsou přímo nebo nepřímo vyřešeny od poskytovatele kořenové služby.</span><span class="sxs-lookup"><span data-stu-id="4b62d-659">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="4b62d-660">Oborové služby nejsou přímo nebo nepřímo vložené do singleton.</span><span class="sxs-lookup"><span data-stu-id="4b62d-660">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="4b62d-661">Poskytovatel kořenové služby se vytvoří, když <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> se zavolá.</span><span class="sxs-lookup"><span data-stu-id="4b62d-661">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="4b62d-662">Doba života poskytovatele kořenové služby odpovídá době životnosti aplikace nebo serveru, když se poskytovatel spustí s aplikací a je uvolněný při ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="4b62d-662">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="4b62d-663">Oborové služby jsou uvolněny kontejnerem, který je vytvořil.</span><span class="sxs-lookup"><span data-stu-id="4b62d-663">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="4b62d-664">Pokud je v kořenovém kontejneru vytvořena vymezená služba, doba života služby je efektivně povýšena na typ singleton, protože je uvolněna pouze kořenovým kontejnerem při vypnutí aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="4b62d-664">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="4b62d-665">Při ověřování oborů služeb se tyto situace zachytí `BuildServiceProvider` .</span><span class="sxs-lookup"><span data-stu-id="4b62d-665">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="4b62d-666">Další informace naleznete v tématu <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="4b62d-666">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>   

## <a name="request-services"></a><span data-ttu-id="4b62d-667">Žádosti o služby</span><span class="sxs-lookup"><span data-stu-id="4b62d-667">Request Services</span></span>

<span data-ttu-id="4b62d-668">Služby, které jsou k dispozici v rámci žádosti o ASP.NET Core, `HttpContext` jsou zpřístupněny prostřednictvím kolekce [HttpContext. RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) .</span><span class="sxs-lookup"><span data-stu-id="4b62d-668">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="4b62d-669">Žádosti o služby reprezentují služby nakonfigurované a požadované v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="4b62d-669">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="4b62d-670">Když objekty určují závislosti, jsou splněny typy nalezenými v `RequestServices` , ne `ApplicationServices` .</span><span class="sxs-lookup"><span data-stu-id="4b62d-670">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="4b62d-671">Obecně platí, že aplikace by tyto vlastnosti neměla používat přímo.</span><span class="sxs-lookup"><span data-stu-id="4b62d-671">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="4b62d-672">Místo toho požádejte o typy, které třídy vyžadují prostřednictvím konstruktorů třídy, a umožněte rozhraní vložit závislosti.</span><span class="sxs-lookup"><span data-stu-id="4b62d-672">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="4b62d-673">To poskytuje třídy, které jsou snáze testovány.</span><span class="sxs-lookup"><span data-stu-id="4b62d-673">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="4b62d-674">Preferovat požadavky na závislosti jako parametry konstruktoru pro přístup ke `RequestServices` kolekci.</span><span class="sxs-lookup"><span data-stu-id="4b62d-674">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="4b62d-675">Navrhnout služby pro vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="4b62d-675">Design services for dependency injection</span></span>

<span data-ttu-id="4b62d-676">Osvědčené postupy:</span><span class="sxs-lookup"><span data-stu-id="4b62d-676">Best practices are to:</span></span>

* <span data-ttu-id="4b62d-677">Navrhněte služby pro použití injektáže závislosti k získání jejich závislostí.</span><span class="sxs-lookup"><span data-stu-id="4b62d-677">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="4b62d-678">Vyhněte se stavovým, statickým třídám a členům.</span><span class="sxs-lookup"><span data-stu-id="4b62d-678">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="4b62d-679">Navrhněte aplikace, aby místo toho používaly služby s jedním prvkem, což se vyhne vytváření globálního stavu.</span><span class="sxs-lookup"><span data-stu-id="4b62d-679">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="4b62d-680">Vyhněte se přímému vytváření instancí závislých tříd v rámci služeb.</span><span class="sxs-lookup"><span data-stu-id="4b62d-680">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="4b62d-681">Přímá instance Couples kód na konkrétní implementaci.</span><span class="sxs-lookup"><span data-stu-id="4b62d-681">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="4b62d-682">Vytvářejte třídy aplikací malými, dobře faktoringně a snadno testované.</span><span class="sxs-lookup"><span data-stu-id="4b62d-682">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="4b62d-683">Pokud se zdá, že třída má příliš mnoho vložených závislostí, obvykle se jedná o znaménko, že třída má příliš mnoho zodpovědnosti a je v rozporu s [principem jediné zodpovědnosti (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="4b62d-683">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="4b62d-684">Pokuste se Refaktorovat třídu přesunutím některých odpovědností do nové třídy.</span><span class="sxs-lookup"><span data-stu-id="4b62d-684">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="4b62d-685">Mějte na paměti, že Razor stránky tříd modelu stránky a třídy KONTROLERU MVC by se měly zaměřit na uživatelské rozhraní.</span><span class="sxs-lookup"><span data-stu-id="4b62d-685">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="4b62d-686">Obchodní pravidla a podrobnosti implementace přístupu k datům by měly být uchovávány ve třídách, které jsou vhodné pro tyto [samostatné obavy](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="4b62d-686">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="4b62d-687">Vyřazení služeb</span><span class="sxs-lookup"><span data-stu-id="4b62d-687">Disposal of services</span></span>

<span data-ttu-id="4b62d-688">Kontejner volá <xref:System.IDisposable.Dispose*> typy, které <xref:System.IDisposable> vytvoří.</span><span class="sxs-lookup"><span data-stu-id="4b62d-688">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="4b62d-689">Pokud je instance přidána do kontejneru pomocí uživatelského kódu, nebude uvolněna automaticky.</span><span class="sxs-lookup"><span data-stu-id="4b62d-689">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="4b62d-690">V následujícím příkladu jsou služby vytvořeny pomocí kontejneru služby a automaticky odstraněny:</span><span class="sxs-lookup"><span data-stu-id="4b62d-690">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="4b62d-691">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="4b62d-691">In the following example:</span></span>

* <span data-ttu-id="4b62d-692">Instance služby nejsou vytvořeny kontejnerem služby.</span><span class="sxs-lookup"><span data-stu-id="4b62d-692">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="4b62d-693">Zamýšlené životnosti služeb nejsou rozhraním známy.</span><span class="sxs-lookup"><span data-stu-id="4b62d-693">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="4b62d-694">Rozhraní neodstraní služby automaticky.</span><span class="sxs-lookup"><span data-stu-id="4b62d-694">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="4b62d-695">Pokud se služby explicitně neodstraní v kódu pro vývojáře, zůstanou zachovány, dokud se aplikace neukončí.</span><span class="sxs-lookup"><span data-stu-id="4b62d-695">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="4b62d-696">Doprovodné materiály k rozhraní IDisposable pro přechodné a sdílené instance</span><span class="sxs-lookup"><span data-stu-id="4b62d-696">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="4b62d-697">Přechodná, omezená doba života</span><span class="sxs-lookup"><span data-stu-id="4b62d-697">Transient, limited lifetime</span></span>

<span data-ttu-id="4b62d-698">**Scénář**</span><span class="sxs-lookup"><span data-stu-id="4b62d-698">**Scenario**</span></span>

<span data-ttu-id="4b62d-699">Aplikace vyžaduje <xref:System.IDisposable> instanci s přechodným životností pro některý z následujících scénářů:</span><span class="sxs-lookup"><span data-stu-id="4b62d-699">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="4b62d-700">Instance je vyřešena v kořenovém oboru.</span><span class="sxs-lookup"><span data-stu-id="4b62d-700">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="4b62d-701">Instance by měla být uvolněna před ukončením oboru.</span><span class="sxs-lookup"><span data-stu-id="4b62d-701">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="4b62d-702">**Řešení**</span><span class="sxs-lookup"><span data-stu-id="4b62d-702">**Solution**</span></span>

<span data-ttu-id="4b62d-703">Pomocí modelu továrny vytvořte instanci mimo nadřazený obor.</span><span class="sxs-lookup"><span data-stu-id="4b62d-703">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="4b62d-704">V takové situaci by aplikace měla obvykle `Create` metodu, která volá přímo konstruktor finálního typu.</span><span class="sxs-lookup"><span data-stu-id="4b62d-704">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="4b62d-705">Pokud má konečný typ jiné závislosti, továrna může:</span><span class="sxs-lookup"><span data-stu-id="4b62d-705">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="4b62d-706">Přijmout <xref:System.IServiceProvider> ve svém konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="4b62d-706">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="4b62d-707">Slouží <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> k vytvoření instance instance mimo kontejner, při použití kontejneru pro jeho závislosti.</span><span class="sxs-lookup"><span data-stu-id="4b62d-707">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="4b62d-708">Sdílená instance, omezená doba života</span><span class="sxs-lookup"><span data-stu-id="4b62d-708">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="4b62d-709">**Scénář**</span><span class="sxs-lookup"><span data-stu-id="4b62d-709">**Scenario**</span></span>

<span data-ttu-id="4b62d-710">Aplikace vyžaduje sdílenou <xref:System.IDisposable> instanci napříč více službami, ale <xref:System.IDisposable> měla by mít omezené trvání.</span><span class="sxs-lookup"><span data-stu-id="4b62d-710">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="4b62d-711">**Řešení**</span><span class="sxs-lookup"><span data-stu-id="4b62d-711">**Solution**</span></span>

<span data-ttu-id="4b62d-712">Zaregistrujte instanci s vymezenou životností.</span><span class="sxs-lookup"><span data-stu-id="4b62d-712">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="4b62d-713">Použijte <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> k zahájení a vytvoření nového <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> .</span><span class="sxs-lookup"><span data-stu-id="4b62d-713">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="4b62d-714"><xref:System.IServiceProvider>Požadované služby získáte pomocí tohoto oboru.</span><span class="sxs-lookup"><span data-stu-id="4b62d-714">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="4b62d-715">Vyřadit rozsah, pokud by životnost měla končit.</span><span class="sxs-lookup"><span data-stu-id="4b62d-715">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="4b62d-716">Obecné pokyny</span><span class="sxs-lookup"><span data-stu-id="4b62d-716">General Guidelines</span></span>

* <span data-ttu-id="4b62d-717">Neregistrujte <xref:System.IDisposable> instance s přechodným oborem.</span><span class="sxs-lookup"><span data-stu-id="4b62d-717">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="4b62d-718">Místo toho použijte výrobní model.</span><span class="sxs-lookup"><span data-stu-id="4b62d-718">Use the factory pattern instead.</span></span>
* <span data-ttu-id="4b62d-719">V kořenovém oboru neřešte přechodné nebo vymezené <xref:System.IDisposable> instance.</span><span class="sxs-lookup"><span data-stu-id="4b62d-719">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="4b62d-720">Jedinou obecnou výjimkou je, když aplikace vytvoří nebo znovu vytvoří a odstraní <xref:System.IServiceProvider> , což není ideální vzor.</span><span class="sxs-lookup"><span data-stu-id="4b62d-720">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="4b62d-721">Příjem <xref:System.IDisposable> závislosti přes di nevyžaduje, aby se příjemce sám implementoval <xref:System.IDisposable> .</span><span class="sxs-lookup"><span data-stu-id="4b62d-721">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="4b62d-722">Příjemce <xref:System.IDisposable> závislosti by neměl volat <xref:System.IDisposable.Dispose%2A> tuto závislost.</span><span class="sxs-lookup"><span data-stu-id="4b62d-722">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="4b62d-723">Obory by měly sloužit k řízení životnosti služeb.</span><span class="sxs-lookup"><span data-stu-id="4b62d-723">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="4b62d-724">Obory nejsou hierarchické a mezi obory není žádné zvláštní připojení.</span><span class="sxs-lookup"><span data-stu-id="4b62d-724">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="4b62d-725">Výchozí nahrazení kontejneru služby</span><span class="sxs-lookup"><span data-stu-id="4b62d-725">Default service container replacement</span></span>

<span data-ttu-id="4b62d-726">Integrovaný kontejner služeb je navržený tak, aby sloužil potřebám architektury a většině spotřebitelských aplikací.</span><span class="sxs-lookup"><span data-stu-id="4b62d-726">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="4b62d-727">Pokud nepotřebujete určitou funkci, kterou integrovaný kontejner nepodporuje, doporučujeme použít vestavěný kontejner, například:</span><span class="sxs-lookup"><span data-stu-id="4b62d-727">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="4b62d-728">Vkládání vlastností</span><span class="sxs-lookup"><span data-stu-id="4b62d-728">Property injection</span></span>
* <span data-ttu-id="4b62d-729">Vkládání na základě názvu</span><span class="sxs-lookup"><span data-stu-id="4b62d-729">Injection based on name</span></span>
* <span data-ttu-id="4b62d-730">Podřízené kontejnery</span><span class="sxs-lookup"><span data-stu-id="4b62d-730">Child containers</span></span>
* <span data-ttu-id="4b62d-731">Vlastní Správa životního cyklu</span><span class="sxs-lookup"><span data-stu-id="4b62d-731">Custom lifetime management</span></span>
* <span data-ttu-id="4b62d-732">`Func<T>` Podpora opožděné inicializace</span><span class="sxs-lookup"><span data-stu-id="4b62d-732">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="4b62d-733">Registrace založená na konvencích</span><span class="sxs-lookup"><span data-stu-id="4b62d-733">Convention-based registration</span></span>

<span data-ttu-id="4b62d-734">U ASP.NET Corech aplikací lze použít následující kontejnery třetích stran:</span><span class="sxs-lookup"><span data-stu-id="4b62d-734">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="4b62d-735">Autofac</span><span class="sxs-lookup"><span data-stu-id="4b62d-735">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="4b62d-736">DryIoc</span><span class="sxs-lookup"><span data-stu-id="4b62d-736">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="4b62d-737">Integrit</span><span class="sxs-lookup"><span data-stu-id="4b62d-737">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="4b62d-738">LightInject</span><span class="sxs-lookup"><span data-stu-id="4b62d-738">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="4b62d-739">Lamar</span><span class="sxs-lookup"><span data-stu-id="4b62d-739">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="4b62d-740">Stashbox</span><span class="sxs-lookup"><span data-stu-id="4b62d-740">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="4b62d-741">Unity</span><span class="sxs-lookup"><span data-stu-id="4b62d-741">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="4b62d-742">Bezpečnost vlákna</span><span class="sxs-lookup"><span data-stu-id="4b62d-742">Thread safety</span></span>

<span data-ttu-id="4b62d-743">Vytvořte služby, které jsou v bezpečí pro přístup z více vláken.</span><span class="sxs-lookup"><span data-stu-id="4b62d-743">Create thread-safe singleton services.</span></span> <span data-ttu-id="4b62d-744">Pokud má služba typu Singleton závislost na přechodné službě, přechodná služba může také vyžadovat zabezpečení vlákna v závislosti na tom, jak je používá typ singleton.</span><span class="sxs-lookup"><span data-stu-id="4b62d-744">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="4b62d-745">Výrobní metoda samostatné služby, jako je například druhý argument pro [AddSingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), nemusí být bezpečná pro přístup z více vláken.</span><span class="sxs-lookup"><span data-stu-id="4b62d-745">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="4b62d-746">Podobně jako konstruktor typu ( `static` ), je zaručeno, že bude volán jednou vláknem.</span><span class="sxs-lookup"><span data-stu-id="4b62d-746">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="4b62d-747">Doporučení</span><span class="sxs-lookup"><span data-stu-id="4b62d-747">Recommendations</span></span>

* <span data-ttu-id="4b62d-748">`async/await``Task`řešení služeb a se nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="4b62d-748">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="4b62d-749">Jazyk C# nepodporuje asynchronní konstruktory; Proto je doporučeným vzorem použití asynchronních metod po synchronním překladu služby.</span><span class="sxs-lookup"><span data-stu-id="4b62d-749">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="4b62d-750">Neukládejte data a konfiguraci přímo do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="4b62d-750">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="4b62d-751">Například nákupní košík uživatele by neměl být obvykle přidán do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="4b62d-751">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="4b62d-752">Konfigurace by měla používat [vzor možností](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="4b62d-752">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="4b62d-753">Podobně nepoužívejte objekty "držitel dat", které existují pouze k povolení přístupu k jinému objektu.</span><span class="sxs-lookup"><span data-stu-id="4b62d-753">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="4b62d-754">Je lepší žádat o skutečnou položku přes DI.</span><span class="sxs-lookup"><span data-stu-id="4b62d-754">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="4b62d-755">Vyhněte se statickému přístupu ke službám.</span><span class="sxs-lookup"><span data-stu-id="4b62d-755">Avoid static access to services.</span></span> <span data-ttu-id="4b62d-756">Vyhněte se například statickému psaní [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) pro použití jinde.</span><span class="sxs-lookup"><span data-stu-id="4b62d-756">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere.</span></span>

* <span data-ttu-id="4b62d-757">Vyhněte se použití *vzoru lokátoru služby*, který kombinuje [inverzi strategií řízení](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .</span><span class="sxs-lookup"><span data-stu-id="4b62d-757">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
  * <span data-ttu-id="4b62d-758">Nevolejte <xref:System.IServiceProvider.GetService*> pro získání instance služby, když můžete místo toho použít di:</span><span class="sxs-lookup"><span data-stu-id="4b62d-758">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="4b62d-759">**Nesprávně:**</span><span class="sxs-lookup"><span data-stu-id="4b62d-759">**Incorrect:**</span></span>

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
   
    <span data-ttu-id="4b62d-760">**Správné**:</span><span class="sxs-lookup"><span data-stu-id="4b62d-760">**Correct**:</span></span>

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

* <span data-ttu-id="4b62d-761">Vyhněte se vkládání továrny, která řeší závislosti za běhu pomocí <xref:System.IServiceProvider.GetService*> .</span><span class="sxs-lookup"><span data-stu-id="4b62d-761">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>
* <span data-ttu-id="4b62d-762">Vyhněte se statickému přístupu k `HttpContext` (například [IHttpContextAccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="4b62d-762">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="4b62d-763">Podobně jako u všech sad doporučení se může stát, že se vyžaduje ignorování doporučení.</span><span class="sxs-lookup"><span data-stu-id="4b62d-763">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="4b62d-764">Výjimky jsou zřídka, většinou zvláštní případy v rámci samotného rozhraní.</span><span class="sxs-lookup"><span data-stu-id="4b62d-764">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="4b62d-765">DI je *alternativou* ke vzorům statických nebo globálních přístupů k objektům.</span><span class="sxs-lookup"><span data-stu-id="4b62d-765">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="4b62d-766">Je možné, že nebudete moci využít výhody DI, pokud je kombinujete se statickým přístupem k objektům.</span><span class="sxs-lookup"><span data-stu-id="4b62d-766">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4b62d-767">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="4b62d-767">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="4b62d-768">Čtyři způsoby, jak vyřadit IDisposable v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4b62d-768">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="4b62d-769">Zápis čistého kódu v ASP.NET Core pomocí injektáže závislosti (MSDN)</span><span class="sxs-lookup"><span data-stu-id="4b62d-769">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="4b62d-770">Explicitní závislosti – princip</span><span class="sxs-lookup"><span data-stu-id="4b62d-770">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="4b62d-771">Inverze řídicích kontejnerů a vzor injektáže závislosti (Martin Fowlera)</span><span class="sxs-lookup"><span data-stu-id="4b62d-771">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="4b62d-772">Jak zaregistrovat službu s více rozhraními v ASP.NET Core DI</span><span class="sxs-lookup"><span data-stu-id="4b62d-772">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

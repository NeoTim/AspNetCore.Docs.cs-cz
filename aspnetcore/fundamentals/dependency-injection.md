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
ms.openlocfilehash: 99e0109ea4c2526e9f91a8a4df23c4557e9be83a
ms.sourcegitcommit: d7991068bc6b04063f4bd836fc5b9591d614d448
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91762305"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="c7203-103">Vkládání závislostí v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c7203-103">Dependency injection in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c7203-104">Od [Kirka Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com)a [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="c7203-104">By [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="c7203-105">ASP.NET Core podporuje vzor návrhu softwaru pro vkládání závislostí (DI), což je technika pro dosažení [inverze ovládacího prvku (IOC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) mezi třídami a jejich závislostmi.</span><span class="sxs-lookup"><span data-stu-id="c7203-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="c7203-106">Další informace, které jsou specifické pro vkládání závislostí v rámci řadičů MVC, najdete v tématu <xref:mvc/controllers/dependency-injection> .</span><span class="sxs-lookup"><span data-stu-id="c7203-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="c7203-107">Další informace o vkládání závislostí z možností naleznete v tématu <xref:fundamentals/configuration/options> .</span><span class="sxs-lookup"><span data-stu-id="c7203-107">For more information on dependency injection of options, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="c7203-108">Toto téma poskytuje informace o vkládání závislostí v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c7203-108">This topic provides information on dependency injection in ASP.NET Core.</span></span> <span data-ttu-id="c7203-109">Informace o použití injektáže závislosti v konzolových aplikacích naleznete v tématu [Injektáže závislosti v rozhraní .NET](/dotnet/core/extensions/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="c7203-109">For information on using dependency injection  in console apps, see [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection).</span></span>

<span data-ttu-id="c7203-110">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c7203-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="c7203-111">Přehled injektáže závislosti</span><span class="sxs-lookup"><span data-stu-id="c7203-111">Overview of dependency injection</span></span>

<span data-ttu-id="c7203-112">*Závislost* je objekt, na kterém je závislý jiný objekt.</span><span class="sxs-lookup"><span data-stu-id="c7203-112">A *dependency* is an object that another object depends on.</span></span> <span data-ttu-id="c7203-113">Projděte si následující `MyDependency` třídu s `WriteMessage` metodou, na které jsou závislé jiné třídy:</span><span class="sxs-lookup"><span data-stu-id="c7203-113">Examine the following `MyDependency` class with a `WriteMessage` method that other classes depend on:</span></span>

```csharp
public class MyDependency
{
    public void WriteMessage(string message)
    {
        Console.WriteLine($"MyDependency.WriteMessage called. Message: {message}");
    }
}
```

<span data-ttu-id="c7203-114">Třída může vytvořit instanci `MyDependency` třídy, aby bylo možné využít její `WriteMessage` metodu.</span><span class="sxs-lookup"><span data-stu-id="c7203-114">A class can create an instance of the `MyDependency` class to make use of its `WriteMessage` method.</span></span> <span data-ttu-id="c7203-115">V následujícím příkladu `MyDependency` je třída závislostí `IndexModel` třídy:</span><span class="sxs-lookup"><span data-stu-id="c7203-115">In the following example, the `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="c7203-116">Třída vytvoří a přímo závisí na `MyDependency` třídě.</span><span class="sxs-lookup"><span data-stu-id="c7203-116">The class creates and directly depends on the `MyDependency` class.</span></span> <span data-ttu-id="c7203-117">Závislosti kódu, jako v předchozím příkladu, jsou problematické a je třeba se jim vyhnout z následujících důvodů:</span><span class="sxs-lookup"><span data-stu-id="c7203-117">Code dependencies, such as in the previous example, are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="c7203-118">Chcete-li nahradit `MyDependency` jinou implementací, `IndexModel` Třída musí být upravena.</span><span class="sxs-lookup"><span data-stu-id="c7203-118">To replace `MyDependency` with a different implementation, the `IndexModel` class must be modified.</span></span>
* <span data-ttu-id="c7203-119">Pokud `MyDependency` má závislosti, musí být také nakonfigurovány `IndexModel` třídou.</span><span class="sxs-lookup"><span data-stu-id="c7203-119">If `MyDependency` has dependencies, they must also be configured by the `IndexModel` class.</span></span> <span data-ttu-id="c7203-120">Ve velkém projektu s více třídami, v závislosti na tom `MyDependency` , je kód konfigurace rozptýlen napříč aplikací.</span><span class="sxs-lookup"><span data-stu-id="c7203-120">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="c7203-121">Tato implementace je obtížná pro testování částí.</span><span class="sxs-lookup"><span data-stu-id="c7203-121">This implementation is difficult to unit test.</span></span> <span data-ttu-id="c7203-122">Aplikace by měla používat třídu typu "nebo zástupné procedury" `MyDependency` , což není u tohoto přístupu možné.</span><span class="sxs-lookup"><span data-stu-id="c7203-122">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="c7203-123">Vkládání závislostí řeší tyto problémy prostřednictvím:</span><span class="sxs-lookup"><span data-stu-id="c7203-123">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="c7203-124">Použití rozhraní nebo základní třídy k abstrakci implementace závislosti.</span><span class="sxs-lookup"><span data-stu-id="c7203-124">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="c7203-125">Registrace závislosti v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="c7203-125">Registration of the dependency in a service container.</span></span> <span data-ttu-id="c7203-126">ASP.NET Core poskytuje integrovaný kontejner služeb <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="c7203-126">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="c7203-127">Služby jsou obvykle registrovány v metodě aplikace `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="c7203-127">Services are typically registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="c7203-128">*Vložení* služby do konstruktoru třídy, kde se používá.</span><span class="sxs-lookup"><span data-stu-id="c7203-128">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="c7203-129">Rozhraní přebírá zodpovědnost za vytvoření instance závislosti a její odstranění, když už ji nepotřebujete.</span><span class="sxs-lookup"><span data-stu-id="c7203-129">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="c7203-130">V [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) `IMyDependency` definuje rozhraní `WriteMessage` metodu:</span><span class="sxs-lookup"><span data-stu-id="c7203-130">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="c7203-131">Toto rozhraní je implementováno konkrétním typem `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="c7203-131">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="c7203-132">Ukázková aplikace registruje `IMyDependency` službu pomocí konkrétního typu `MyDependency` .</span><span class="sxs-lookup"><span data-stu-id="c7203-132">The sample app registers the `IMyDependency` service with the concrete type `MyDependency`.</span></span> <span data-ttu-id="c7203-133"><xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A>Metoda registruje službu s rozsahem životnosti, životností jediného požadavku.</span><span class="sxs-lookup"><span data-stu-id="c7203-133">The <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> method registers the service with a scoped lifetime, the lifetime of a single request.</span></span> <span data-ttu-id="c7203-134">[Doby platnosti služeb](#service-lifetimes) jsou popsány dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="c7203-134">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency.cs?name=snippet1)]

<span data-ttu-id="c7203-135">V ukázkové aplikaci `IMyDependency` se vyžádá služba a použije se k volání `WriteMessage` metody:</span><span class="sxs-lookup"><span data-stu-id="c7203-135">In the sample app, the `IMyDependency` service is requested and used to call the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index2.cshtml.cs?name=snippet1)]

<span data-ttu-id="c7203-136">Pomocí vzoru DI se kontroler:</span><span class="sxs-lookup"><span data-stu-id="c7203-136">By using the DI pattern, the controller:</span></span>

* <span data-ttu-id="c7203-137">Nepoužívá konkrétní typ `MyDependency` , pouze rozhraní, které `IMyDependency` implementuje.</span><span class="sxs-lookup"><span data-stu-id="c7203-137">Doesn't use the concrete type `MyDependency`, only the `IMyDependency` interface it implements.</span></span> <span data-ttu-id="c7203-138">Díky tomu je možné snadno změnit implementaci, kterou kontroler používá bez změny kontroleru.</span><span class="sxs-lookup"><span data-stu-id="c7203-138">That makes it easy to change the implementation that the controller uses without modifying the controller.</span></span>
* <span data-ttu-id="c7203-139">Nevytvoří instanci, která je `MyDependency` vytvořena kontejnerem di.</span><span class="sxs-lookup"><span data-stu-id="c7203-139">Doesn't create an instance of `MyDependency`, it's created by the DI container.</span></span>

<span data-ttu-id="c7203-140">Implementaci `IMyDependency` rozhraní lze zlepšit pomocí integrovaného protokolovacího rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="c7203-140">The implementation of the `IMyDependency` interface can be improved by using the built-in logging API:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet2)]

<span data-ttu-id="c7203-141">Aktualizovaná `ConfigureServices` Metoda registruje novou `IMyDependency` implementaci:</span><span class="sxs-lookup"><span data-stu-id="c7203-141">The updated `ConfigureServices` method registers the new `IMyDependency` implementation:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency2.cs?name=snippet1)]

<span data-ttu-id="c7203-142">`MyDependency2` závisí na <xref:Microsoft.Extensions.Logging.ILogger%601> tom, které požadavky v konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="c7203-142">`MyDependency2` depends on <xref:Microsoft.Extensions.Logging.ILogger%601>, which it requests in the constructor.</span></span> <span data-ttu-id="c7203-143">`ILogger<TCategoryName>` je [služba poskytovaná rozhraním](#framework-provided-services).</span><span class="sxs-lookup"><span data-stu-id="c7203-143">`ILogger<TCategoryName>` is a [framework-provided service](#framework-provided-services).</span></span>

<span data-ttu-id="c7203-144">Není neobvyklé používat vkládání závislostí v zřetězeném způsobem.</span><span class="sxs-lookup"><span data-stu-id="c7203-144">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="c7203-145">Každá požadovaná závislost zase vyžádá své vlastní závislosti.</span><span class="sxs-lookup"><span data-stu-id="c7203-145">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="c7203-146">Kontejner vyřeší závislosti v grafu a vrátí plně přeloženou službu.</span><span class="sxs-lookup"><span data-stu-id="c7203-146">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="c7203-147">Kolektivní sada závislostí, které je třeba vyřešit, se obvykle označuje jako *strom závislosti*, *graf závislosti*nebo *graf objektů*.</span><span class="sxs-lookup"><span data-stu-id="c7203-147">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="c7203-148">Kontejner se překládá `ILogger<TCategoryName>` tím, že využije [(Obecné) otevřené typy](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)a eliminuje nutnost registrovat každý [(obecný) konstruovaný typ](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span><span class="sxs-lookup"><span data-stu-id="c7203-148">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span></span>

<span data-ttu-id="c7203-149">V terminologii injektáže závislostí služba:</span><span class="sxs-lookup"><span data-stu-id="c7203-149">In dependency injection terminology, a service:</span></span>

* <span data-ttu-id="c7203-150">Je obvykle objekt, který poskytuje službu jiným objektům, jako je například `IMyDependency` služba.</span><span class="sxs-lookup"><span data-stu-id="c7203-150">Is typically an object that provides a service to other objects, such as the `IMyDependency` service.</span></span>
* <span data-ttu-id="c7203-151">Nesouvisí s webovou službou, i když služba může používat webovou službu.</span><span class="sxs-lookup"><span data-stu-id="c7203-151">Is not related to a web service, although the service may use a web service.</span></span>

<span data-ttu-id="c7203-152">Rozhraní poskytuje robustní systém [protokolování](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="c7203-152">The framework provides a robust [logging](xref:fundamentals/logging/index) system.</span></span> <span data-ttu-id="c7203-153">`IMyDependency`Implementace uvedené v předchozích příkladech byly zapsány k demonstraci základního di, nikoli k implementaci protokolování.</span><span class="sxs-lookup"><span data-stu-id="c7203-153">The `IMyDependency` implementations shown in the preceding examples were written to demonstrate basic DI, not to implement logging.</span></span> <span data-ttu-id="c7203-154">Většina aplikací by neměla potřebovat zapisovat protokolovací nástroje.</span><span class="sxs-lookup"><span data-stu-id="c7203-154">Most apps shouldn't need to write loggers.</span></span> <span data-ttu-id="c7203-155">Následující kód ukazuje použití výchozího protokolování, které nevyžaduje registraci žádné služby v nástroji `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="c7203-155">The following code demonstrates using the default logging, which doesn't require any services to be registered in `ConfigureServices`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="c7203-156">Pomocí předchozího kódu není nutné aktualizovat `ConfigureServices` , protože [protokolování](xref:fundamentals/logging/index) je poskytováno rozhraním.</span><span class="sxs-lookup"><span data-stu-id="c7203-156">Using the preceding code, there is no need to update `ConfigureServices`, because [logging](xref:fundamentals/logging/index) is provided by the framework.</span></span>

## <a name="services-injected-into-startup"></a><span data-ttu-id="c7203-157">Služby vložené do spuštění</span><span class="sxs-lookup"><span data-stu-id="c7203-157">Services injected into Startup</span></span>

<span data-ttu-id="c7203-158">Služby lze vložit do `Startup` konstruktoru a do `Startup.Configure` metody.</span><span class="sxs-lookup"><span data-stu-id="c7203-158">Services can be injected into the `Startup` constructor and the `Startup.Configure` method.</span></span>

<span data-ttu-id="c7203-159">`Startup`Při použití obecného hostitele () mohou být do konstruktoru vloženy pouze následující služby <xref:Microsoft.Extensions.Hosting.IHostBuilder> :</span><span class="sxs-lookup"><span data-stu-id="c7203-159">Only the following services can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="c7203-160">Kterákoli služba zaregistrovaná v kontejneru DI může být vložená do `Startup.Configure` metody:</span><span class="sxs-lookup"><span data-stu-id="c7203-160">Any service registered with the DI container can be injected into the `Startup.Configure` method:</span></span>

```csharp
public void Configure(IApplicationBuilder app, ILogger<Startup> logger)
{
    ...
}
```

<span data-ttu-id="c7203-161">Další informace najdete v tématu <xref:fundamentals/startup> a [Konfigurace přístupu při spuštění](xref:fundamentals/configuration/index#access-configuration-in-startup).</span><span class="sxs-lookup"><span data-stu-id="c7203-161">For more information, see <xref:fundamentals/startup> and [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup).</span></span>

## <a name="register-groups-of-services-with-extension-methods"></a><span data-ttu-id="c7203-162">Registrace skupin služeb s metodami rozšíření</span><span class="sxs-lookup"><span data-stu-id="c7203-162">Register groups of services with extension methods</span></span>

<span data-ttu-id="c7203-163">Rozhraní ASP.NET Core Framework používá konvenci pro registraci skupiny souvisejících služeb.</span><span class="sxs-lookup"><span data-stu-id="c7203-163">The ASP.NET Core framework uses a convention for registering a group of related services.</span></span> <span data-ttu-id="c7203-164">Konvence je určena k `Add{GROUP_NAME}` registraci všech služeb vyžadovaných funkcí rozhraní pomocí jediné metody rozšíření.</span><span class="sxs-lookup"><span data-stu-id="c7203-164">The convention is to use a single `Add{GROUP_NAME}` extension method to register all of the services required by a framework feature.</span></span> <span data-ttu-id="c7203-165">Například <Microsoft. Extensions. DependencyInjection. MvcServiceCollectionExtensions. AddControllers> rozšiřující metoda registruje služby požadované pro řadiče MVC.</span><span class="sxs-lookup"><span data-stu-id="c7203-165">For example, the <Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers> extension method registers the services required for MVC controllers.</span></span>

<span data-ttu-id="c7203-166">Následující kód je vygenerován Razor šablonou stránky pomocí individuálních uživatelských účtů a ukazuje, jak přidat další služby do kontejneru pomocí metod rozšíření <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> a <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A> :</span><span class="sxs-lookup"><span data-stu-id="c7203-166">The following code is generated by the Razor Pages template using individual user accounts and shows how to add additional services to the container using the extension methods <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A>:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupEF.cs?name=snippet)]

[!INCLUDE[](~/includes/combine-di.md)]

## <a name="service-lifetimes"></a><span data-ttu-id="c7203-167">Životnost služeb</span><span class="sxs-lookup"><span data-stu-id="c7203-167">Service lifetimes</span></span>

<span data-ttu-id="c7203-168">Služby je možné zaregistrovat s jednou z následujících dob života:</span><span class="sxs-lookup"><span data-stu-id="c7203-168">Services can be registered with one of the following lifetimes:</span></span>

* <span data-ttu-id="c7203-169">Dočasný</span><span class="sxs-lookup"><span data-stu-id="c7203-169">Transient</span></span>
* <span data-ttu-id="c7203-170">Obor</span><span class="sxs-lookup"><span data-stu-id="c7203-170">Scoped</span></span>
* <span data-ttu-id="c7203-171">Singleton</span><span class="sxs-lookup"><span data-stu-id="c7203-171">Singleton</span></span>

<span data-ttu-id="c7203-172">V následujících částech jsou popsány všechny předchozí životnosti.</span><span class="sxs-lookup"><span data-stu-id="c7203-172">The following sections describe each of the preceding lifetimes.</span></span> <span data-ttu-id="c7203-173">Vyberte odpovídající dobu života pro každou registrovanou službu.</span><span class="sxs-lookup"><span data-stu-id="c7203-173">Choose an appropriate lifetime for each registered service.</span></span> 

### <a name="transient"></a><span data-ttu-id="c7203-174">Dočasný</span><span class="sxs-lookup"><span data-stu-id="c7203-174">Transient</span></span>

<span data-ttu-id="c7203-175">Dočasné služby životnosti se vytváří pokaždé, když jsou požadovány z kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="c7203-175">Transient lifetime services are created each time they're requested from the service container.</span></span> <span data-ttu-id="c7203-176">Tato životnost funguje nejlépe pro odlehčené bezstavové služby.</span><span class="sxs-lookup"><span data-stu-id="c7203-176">This lifetime works best for lightweight, stateless services.</span></span> <span data-ttu-id="c7203-177">Zaregistrujte přechodné služby pomocí <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient%2A> .</span><span class="sxs-lookup"><span data-stu-id="c7203-177">Register transient services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient%2A>.</span></span>

<span data-ttu-id="c7203-178">V aplikacích, které zpracovávají požadavky, jsou přechodné služby na konci žádosti zrušené.</span><span class="sxs-lookup"><span data-stu-id="c7203-178">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="c7203-179">Obor</span><span class="sxs-lookup"><span data-stu-id="c7203-179">Scoped</span></span>

<span data-ttu-id="c7203-180">Služby životnosti v oboru jsou vytvářeny jednou za požadavek klienta (připojení).</span><span class="sxs-lookup"><span data-stu-id="c7203-180">Scoped lifetime services are created once per client request (connection).</span></span> <span data-ttu-id="c7203-181">Zaregistrujte obor služeb pomocí <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> .</span><span class="sxs-lookup"><span data-stu-id="c7203-181">Register scoped services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A>.</span></span>

<span data-ttu-id="c7203-182">V aplikacích, které zpracovávají požadavky, se vymezené služby na konci žádosti odstraní.</span><span class="sxs-lookup"><span data-stu-id="c7203-182">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

<span data-ttu-id="c7203-183">Při použití Entity Framework Core <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> metoda rozšíření registruje `DbContext` typy s vymezenou životností ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="c7203-183">When using Entity Framework Core, the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension method registers `DbContext` types with a scoped lifetime by default.</span></span>

<span data-ttu-id="c7203-184">Neprovádějte ***Překlad oboru*** služby z typu singleton.</span><span class="sxs-lookup"><span data-stu-id="c7203-184">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="c7203-185">Může dojít k tomu, že služba má při zpracování dalších požadavků špatný stav.</span><span class="sxs-lookup"><span data-stu-id="c7203-185">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="c7203-186">Je to v pořádku:</span><span class="sxs-lookup"><span data-stu-id="c7203-186">It's fine to:</span></span>

* <span data-ttu-id="c7203-187">Vyřešte službu typu Singleton z oboru nebo přechodné služby.</span><span class="sxs-lookup"><span data-stu-id="c7203-187">Resolve a singleton service from a scoped or transient service.</span></span>
* <span data-ttu-id="c7203-188">Vyřešte oborovou službu z jiné vymezené nebo přechodné služby.</span><span class="sxs-lookup"><span data-stu-id="c7203-188">Resolve a scoped service from another scoped or transient service.</span></span>

<span data-ttu-id="c7203-189">Ve výchozím nastavení ve vývojovém prostředí vyřeší služba z jiné služby s delší životností výjimku.</span><span class="sxs-lookup"><span data-stu-id="c7203-189">By default, in the development environment, resolving a service from another service with a longer lifetime throws an exception.</span></span> <span data-ttu-id="c7203-190">Další informace najdete v tématu [ověřování oboru](#sv).</span><span class="sxs-lookup"><span data-stu-id="c7203-190">For more information, see [Scope validation](#sv).</span></span>

<span data-ttu-id="c7203-191">Pokud chcete používat vymezené služby v middlewaru, použijte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="c7203-191">To use scoped services in middleware, use one of the following approaches:</span></span>

* <span data-ttu-id="c7203-192">Službu zahájíte do služby nebo do metody middlewaru `Invoke` `InvokeAsync` .</span><span class="sxs-lookup"><span data-stu-id="c7203-192">Inject the service into the middleware's `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="c7203-193">Použití [Injektáže konstruktoru](xref:mvc/controllers/dependency-injection#constructor-injection) vyvolá výjimku za běhu, protože vynutí, aby se služba s oborem chovala jako typ singleton.</span><span class="sxs-lookup"><span data-stu-id="c7203-193">Using [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) throws a runtime exception because it forces the scoped service to behave like a singleton.</span></span> <span data-ttu-id="c7203-194">Ukázka v části [Možnosti životního cyklu a registrace](#lifetime-and-registration-options) demonstruje `InvokeAsync` přístup.</span><span class="sxs-lookup"><span data-stu-id="c7203-194">The sample in the [Lifetime and registration options](#lifetime-and-registration-options) section demonstrates the `InvokeAsync` approach.</span></span>
* <span data-ttu-id="c7203-195">Použijte [middleware založený na továrně](xref:fundamentals/middleware/extensibility).</span><span class="sxs-lookup"><span data-stu-id="c7203-195">Use [Factory-based middleware](xref:fundamentals/middleware/extensibility).</span></span> <span data-ttu-id="c7203-196">Middleware registrované pomocí tohoto přístupu se aktivují na žádost klienta (připojení), která umožňuje vkládání oboru služeb do metody middlewaru `InvokeAsync` .</span><span class="sxs-lookup"><span data-stu-id="c7203-196">Middleware registered using this approach is activated per client request (connection), which allows scoped services to be injected into the middleware's `InvokeAsync` method.</span></span>

<span data-ttu-id="c7203-197">Další informace naleznete v tématu <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="c7203-197">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="c7203-198">Singleton</span><span class="sxs-lookup"><span data-stu-id="c7203-198">Singleton</span></span>

<span data-ttu-id="c7203-199">Služba životnosti singleton je vytvořena buď:</span><span class="sxs-lookup"><span data-stu-id="c7203-199">Singleton lifetime services are created either:</span></span>

* <span data-ttu-id="c7203-200">Při prvním vyžádání.</span><span class="sxs-lookup"><span data-stu-id="c7203-200">The first time they're requested.</span></span>
* <span data-ttu-id="c7203-201">Vývojář při poskytování instance implementace přímo do kontejneru.</span><span class="sxs-lookup"><span data-stu-id="c7203-201">By the developer, when providing an implementation instance directly to the container.</span></span> <span data-ttu-id="c7203-202">Tento přístup je potřeba jenom zřídka.</span><span class="sxs-lookup"><span data-stu-id="c7203-202">This approach is rarely needed.</span></span>

<span data-ttu-id="c7203-203">Každý další požadavek používá stejnou instanci.</span><span class="sxs-lookup"><span data-stu-id="c7203-203">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="c7203-204">Pokud aplikace vyžaduje chování singleton, umožněte kontejneru služby spravovat dobu života služby.</span><span class="sxs-lookup"><span data-stu-id="c7203-204">If the app requires singleton behavior, allow the service container to manage the service's lifetime.</span></span> <span data-ttu-id="c7203-205">Neimplementujte vzor návrhu singleton a poskytněte kód pro odstranění typu singleton.</span><span class="sxs-lookup"><span data-stu-id="c7203-205">Don't implement the singleton design pattern and provide code to dispose of the singleton.</span></span> <span data-ttu-id="c7203-206">Služby by nikdy neměly být odstraněny kódem, který službu vyřešil z kontejneru.</span><span class="sxs-lookup"><span data-stu-id="c7203-206">Services should never be disposed by code that resolved the service from the container.</span></span> <span data-ttu-id="c7203-207">Pokud je typ nebo objekt pro vytváření zaregistrován jako singleton, kontejner odstraní singleton automaticky.</span><span class="sxs-lookup"><span data-stu-id="c7203-207">If a type or factory is registered as a singleton, the container disposes the singleton automatically.</span></span>

<span data-ttu-id="c7203-208">Registrovat služby s jedním prvkem pomocí <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A> .</span><span class="sxs-lookup"><span data-stu-id="c7203-208">Register singleton services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A>.</span></span> <span data-ttu-id="c7203-209">Služby s jedním prvkem musí být bezpečné pro přístup z více vláken a často se používají ve bezstavových službách.</span><span class="sxs-lookup"><span data-stu-id="c7203-209">Singleton services must be thread safe and are often used in stateless services.</span></span>

<span data-ttu-id="c7203-210">V aplikacích, které zpracovávají požadavky, jsou nejednoznačné služby uvolněny při <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> vyřazení aplikace z vypnutí.</span><span class="sxs-lookup"><span data-stu-id="c7203-210">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed on application shutdown.</span></span> <span data-ttu-id="c7203-211">Vzhledem k tomu, že paměť není uvolněna, dokud nebude aplikace vypnutá, zvažte použití paměti u služby s jedním prvkem.</span><span class="sxs-lookup"><span data-stu-id="c7203-211">Because memory is not released until the app is shut down, consider memory use with a singleton service.</span></span>

> [!WARNING]
> <span data-ttu-id="c7203-212">Neprovádějte ***Překlad oboru*** služby z typu singleton.</span><span class="sxs-lookup"><span data-stu-id="c7203-212">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="c7203-213">Může dojít k tomu, že služba má při zpracování dalších požadavků špatný stav.</span><span class="sxs-lookup"><span data-stu-id="c7203-213">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="c7203-214">Je dobré vyřešit službu typu Singleton z oboru nebo přechodné služby.</span><span class="sxs-lookup"><span data-stu-id="c7203-214">It's fine to resolve a singleton service from a scoped or transient service.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="c7203-215">Metody registrace služby</span><span class="sxs-lookup"><span data-stu-id="c7203-215">Service registration methods</span></span>

<span data-ttu-id="c7203-216">Rozhraní poskytuje metody rozšíření pro registraci služby, které jsou užitečné v konkrétních scénářích:</span><span class="sxs-lookup"><span data-stu-id="c7203-216">The framework provides service registration extension methods that are useful in specific scenarios:</span></span>

<!-- Review: Auto disposal at end of app lifetime is not what you think of auto disposal  -->

| <span data-ttu-id="c7203-217">Metoda</span><span class="sxs-lookup"><span data-stu-id="c7203-217">Method</span></span>                                                                                                                                                                              | <span data-ttu-id="c7203-218">Automaticky</span><span class="sxs-lookup"><span data-stu-id="c7203-218">Automatic</span></span><br><span data-ttu-id="c7203-219">object</span><span class="sxs-lookup"><span data-stu-id="c7203-219">object</span></span><br><span data-ttu-id="c7203-220">odvod</span><span class="sxs-lookup"><span data-stu-id="c7203-220">disposal</span></span> | <span data-ttu-id="c7203-221">Několik</span><span class="sxs-lookup"><span data-stu-id="c7203-221">Multiple</span></span><br><span data-ttu-id="c7203-222">implementace</span><span class="sxs-lookup"><span data-stu-id="c7203-222">implementations</span></span> | <span data-ttu-id="c7203-223">Pass – argumenty</span><span class="sxs-lookup"><span data-stu-id="c7203-223">Pass args</span></span> |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------:|:---------------------------:|:---------:|
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="c7203-224">Příklad:</span><span class="sxs-lookup"><span data-stu-id="c7203-224">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();`                                                                             | <span data-ttu-id="c7203-225">Ano</span><span class="sxs-lookup"><span data-stu-id="c7203-225">Yes</span></span>                             | <span data-ttu-id="c7203-226">Ano</span><span class="sxs-lookup"><span data-stu-id="c7203-226">Yes</span></span>                         | <span data-ttu-id="c7203-227">No</span><span class="sxs-lookup"><span data-stu-id="c7203-227">No</span></span>        |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="c7203-228">Příklady:</span><span class="sxs-lookup"><span data-stu-id="c7203-228">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep(99));` | <span data-ttu-id="c7203-229">Ano</span><span class="sxs-lookup"><span data-stu-id="c7203-229">Yes</span></span>                             | <span data-ttu-id="c7203-230">Ano</span><span class="sxs-lookup"><span data-stu-id="c7203-230">Yes</span></span>                         | <span data-ttu-id="c7203-231">Ano</span><span class="sxs-lookup"><span data-stu-id="c7203-231">Yes</span></span>       |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="c7203-232">Příklad:</span><span class="sxs-lookup"><span data-stu-id="c7203-232">Example:</span></span><br>`services.AddSingleton<MyDep>();`                                                                                                | <span data-ttu-id="c7203-233">Ano</span><span class="sxs-lookup"><span data-stu-id="c7203-233">Yes</span></span>                             | <span data-ttu-id="c7203-234">No</span><span class="sxs-lookup"><span data-stu-id="c7203-234">No</span></span>                          | <span data-ttu-id="c7203-235">No</span><span class="sxs-lookup"><span data-stu-id="c7203-235">No</span></span>        |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="c7203-236">Příklady:</span><span class="sxs-lookup"><span data-stu-id="c7203-236">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep(99));`                    | <span data-ttu-id="c7203-237">No</span><span class="sxs-lookup"><span data-stu-id="c7203-237">No</span></span>                              | <span data-ttu-id="c7203-238">Ano</span><span class="sxs-lookup"><span data-stu-id="c7203-238">Yes</span></span>                         | <span data-ttu-id="c7203-239">Ano</span><span class="sxs-lookup"><span data-stu-id="c7203-239">Yes</span></span>       |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="c7203-240">Příklady:</span><span class="sxs-lookup"><span data-stu-id="c7203-240">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep(99));`                                               | <span data-ttu-id="c7203-241">No</span><span class="sxs-lookup"><span data-stu-id="c7203-241">No</span></span>                              | <span data-ttu-id="c7203-242">No</span><span class="sxs-lookup"><span data-stu-id="c7203-242">No</span></span>                          | <span data-ttu-id="c7203-243">Ano</span><span class="sxs-lookup"><span data-stu-id="c7203-243">Yes</span></span>       |

<span data-ttu-id="c7203-244">Další informace o vyřazení typů najdete v části věnované [vyřazení služeb](#disposal-of-services) .</span><span class="sxs-lookup"><span data-stu-id="c7203-244">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="c7203-245">Při napodobování [typů pro testování](xref:test/integration-tests#inject-mock-services)je běžné použít více implementací.</span><span class="sxs-lookup"><span data-stu-id="c7203-245">It's common to use multiple implementations when [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="c7203-246">Rozhraní poskytuje také `TryAdd{LIFETIME}` metody rozšíření, které registrují službu pouze v případě, že již není zaregistrována implementace.</span><span class="sxs-lookup"><span data-stu-id="c7203-246">The framework also provides `TryAdd{LIFETIME}` extension methods, which register the service only if there isn't already an implementation registered.</span></span>

<span data-ttu-id="c7203-247">V následujícím příkladu je volání `AddSingleton` registrováno `MyDependency` jako implementace pro `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="c7203-247">In the following example, the call to `AddSingleton` registers `MyDependency` as an implementation for `IMyDependency`.</span></span> <span data-ttu-id="c7203-248">Volání `TryAddSingleton` nemá žádný účinek, protože `IMyDependency` již má registrovanou implementaci:</span><span class="sxs-lookup"><span data-stu-id="c7203-248">The call to `TryAddSingleton` has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="c7203-249">Další informace naleznete v tématech:</span><span class="sxs-lookup"><span data-stu-id="c7203-249">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton%2A>

<span data-ttu-id="c7203-250">Metody [TryAddEnumerable (ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable%2A) registrují službu pouze v případě, že již neexistují implementace *stejného typu*.</span><span class="sxs-lookup"><span data-stu-id="c7203-250">The [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable%2A) methods register the service only if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="c7203-251">Několik služeb je vyřešeno prostřednictvím `IEnumerable<{SERVICE}>` .</span><span class="sxs-lookup"><span data-stu-id="c7203-251">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="c7203-252">Při registraci služeb by měl vývojář přidat instanci, pokud už jeden ze stejného typu není přidaný.</span><span class="sxs-lookup"><span data-stu-id="c7203-252">When registering services, the developer should add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="c7203-253">Obecně autoři knihovny používají `TryAddEnumerable` k tomu, aby nedocházelo k registraci více kopií implementace v kontejneru.</span><span class="sxs-lookup"><span data-stu-id="c7203-253">Generally, library authors use `TryAddEnumerable` to avoid registering multiple copies of an implementation in the container.</span></span>

<span data-ttu-id="c7203-254">V následujícím příkladu se první volání `TryAddEnumerable` registruje `MyDependency` jako implementace pro `IMyDependency1` .</span><span class="sxs-lookup"><span data-stu-id="c7203-254">In the following example, the first call to `TryAddEnumerable` registers `MyDependency` as an implementation for `IMyDependency1`.</span></span> <span data-ttu-id="c7203-255">Druhý registr volání `MyDependency` pro `IMyDependency2` .</span><span class="sxs-lookup"><span data-stu-id="c7203-255">The second call registers `MyDependency` for `IMyDependency2`.</span></span> <span data-ttu-id="c7203-256">Třetí volání nemá žádný účinek, protože `IMyDependency1` už má registrovanou implementaci `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="c7203-256">The third call has no effect because `IMyDependency1` already has a registered implementation of `MyDependency`:</span></span>

```csharp
public interface IMyDependency1 { }
public interface IMyDependency2 { }

public class MyDependency : IMyDependency1, IMyDependency2 { }

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency1, MyDependency>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency2, MyDependency>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency1, MyDependency>());
```

<span data-ttu-id="c7203-257">Registrace služby je obecně závislá na nezávislém pořadí s výjimkou registrace více implementací stejného typu.</span><span class="sxs-lookup"><span data-stu-id="c7203-257">Service registration is generally order independent except when registering multiple implementations of the same type.</span></span>

<span data-ttu-id="c7203-258">`IServiceCollection` je kolekce <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> objektů.</span><span class="sxs-lookup"><span data-stu-id="c7203-258">`IServiceCollection` is a collection of <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> objects.</span></span> <span data-ttu-id="c7203-259">Následující příklad ukazuje, jak zaregistrovat službu vytvořením a přidáním `ServiceDescriptor` :</span><span class="sxs-lookup"><span data-stu-id="c7203-259">The following example shows how to register a service by creating and adding a `ServiceDescriptor`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup5.cs?name=snippet)]

<span data-ttu-id="c7203-260">Předdefinované `Add{LIFETIME}` metody používají stejný přístup.</span><span class="sxs-lookup"><span data-stu-id="c7203-260">The built-in `Add{LIFETIME}` methods use the same approach.</span></span> <span data-ttu-id="c7203-261">Podívejte se například na [zdrojový kód AddScoped](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span><span class="sxs-lookup"><span data-stu-id="c7203-261">For example, see the [AddScoped source code](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span></span>

### <a name="constructor-injection-behavior"></a><span data-ttu-id="c7203-262">Chování injektáže konstruktoru</span><span class="sxs-lookup"><span data-stu-id="c7203-262">Constructor injection behavior</span></span>

<span data-ttu-id="c7203-263">Služby je možné vyřešit pomocí:</span><span class="sxs-lookup"><span data-stu-id="c7203-263">Services can be resolved by using:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="c7203-264"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span><span class="sxs-lookup"><span data-stu-id="c7203-264"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span></span>
  * <span data-ttu-id="c7203-265">Vytvoří objekty, které nejsou registrovány v kontejneru.</span><span class="sxs-lookup"><span data-stu-id="c7203-265">Creates objects that aren't registered in the container.</span></span>
  * <span data-ttu-id="c7203-266">Používá se s funkcemi architektury, jako jsou například [pomocníka značek](xref:mvc/views/tag-helpers/intro), řadiče MVC a [pořadače modelů](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="c7203-266">Used with framework features, such as [Tag Helpers](xref:mvc/views/tag-helpers/intro), MVC controllers, and [model binders](xref:mvc/models/model-binding).</span></span>

<span data-ttu-id="c7203-267">Konstruktory mohou přijímat argumenty, které nejsou poskytovány vkládáním závislostí, ale argumenty musí přiřadit výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="c7203-267">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="c7203-268">Když jsou služby vyřešeny `IServiceProvider` nebo `ActivatorUtilities` , [Injektáže konstruktoru](xref:mvc/controllers/dependency-injection#constructor-injection) vyžaduje *veřejný* konstruktor.</span><span class="sxs-lookup"><span data-stu-id="c7203-268">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="c7203-269">Když jsou služby vyřešeny nástrojem `ActivatorUtilities` , [Injektáže konstruktoru](xref:mvc/controllers/dependency-injection#constructor-injection) vyžaduje, aby existoval pouze jeden příslušný konstruktor.</span><span class="sxs-lookup"><span data-stu-id="c7203-269">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="c7203-270">Přetížení konstruktoru jsou podporovaná, ale může existovat jenom jedno přetížení, jehož argumenty můžou být splněné vkládáním závislostí.</span><span class="sxs-lookup"><span data-stu-id="c7203-270">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="c7203-271">Entity Framework kontexty</span><span class="sxs-lookup"><span data-stu-id="c7203-271">Entity Framework contexts</span></span>

<span data-ttu-id="c7203-272">Ve výchozím nastavení se Entity Framework kontexty přidávají do kontejneru služby pomocí [rozsahu životnosti](#service-lifetimes) , protože operace databáze webové aplikace jsou normálně vymezeny na žádost klienta.</span><span class="sxs-lookup"><span data-stu-id="c7203-272">By default, Entity Framework contexts are added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="c7203-273">Chcete-li použít jinou dobu života, určete dobu života pomocí <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> přetížení.</span><span class="sxs-lookup"><span data-stu-id="c7203-273">To use a different lifetime, specify the lifetime by using an <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> overload.</span></span> <span data-ttu-id="c7203-274">Služby pro danou dobu života by neměly používat kontext databáze s dobou životnosti, která je kratší než doba života služby.</span><span class="sxs-lookup"><span data-stu-id="c7203-274">Services of a given lifetime shouldn't use a database context with a lifetime that's shorter than the service's lifetime.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="c7203-275">Možnosti života a registrace</span><span class="sxs-lookup"><span data-stu-id="c7203-275">Lifetime and registration options</span></span>

<span data-ttu-id="c7203-276">Chcete-li předvést rozdíl mezi životností služeb a jejich možnostmi registrace, zvažte následující rozhraní, která představují úlohu jako operaci s identifikátorem `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="c7203-276">To demonstrate the difference between service lifetimes and their registration options, consider the following interfaces that represent a task as an operation with an identifier, `OperationId`.</span></span> <span data-ttu-id="c7203-277">V závislosti na tom, jak je doba platnosti služby operace nakonfigurovaná pro následující rozhraní, kontejner poskytuje buď stejné nebo jiné instance služby, když ho požaduje třída:</span><span class="sxs-lookup"><span data-stu-id="c7203-277">Depending on how the lifetime of an operation's service is configured for the following interfaces, the container provides either the same or different instances of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="c7203-278">Následující `Operation` Třída implementuje všechna předchozí rozhraní.</span><span class="sxs-lookup"><span data-stu-id="c7203-278">The following `Operation` class implements all of the preceding interfaces.</span></span> <span data-ttu-id="c7203-279">`Operation`Konstruktor generuje identifikátor GUID a ukládá poslední 4 znaky ve `OperationId` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="c7203-279">The `Operation` constructor generates a GUID and stores the last 4 characters in the `OperationId` property:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<!--
An `OperationService` is registered that depends on each of the other `Operation` types. When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.

* When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`. `OperationService` receives a new instance of the `IOperationTransient` class. The new instance yields a different `OperationId`.
* When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request. Across client requests, both services share a different `OperationId` value.
* When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]
-->

<span data-ttu-id="c7203-280">`Startup.ConfigureServices`Metoda vytvoří více registrací `Operation` třídy podle pojmenovaných životností:</span><span class="sxs-lookup"><span data-stu-id="c7203-280">The `Startup.ConfigureServices` method creates multiple registrations of the `Operation` class according to the named lifetimes:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="c7203-281">Ukázková aplikace ukazuje dobu života objektu v rámci i mezi požadavky.</span><span class="sxs-lookup"><span data-stu-id="c7203-281">The sample app demonstrates object lifetimes both within and between requests.</span></span> <span data-ttu-id="c7203-282">`IndexModel`A middleware vyžádá každý druh `IOperation` typu a protokoluje `OperationId` pro každý typ:</span><span class="sxs-lookup"><span data-stu-id="c7203-282">The `IndexModel` and the middleware request each kind of `IOperation` type and log the `OperationId` for each:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="c7203-283">Podobně jako rozhraní `IndexModel` , middleware řeší stejné služby:</span><span class="sxs-lookup"><span data-stu-id="c7203-283">Similar to the `IndexModel`, the middleware resolves the same services:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet)]

<span data-ttu-id="c7203-284">Oborové služby musí být vyřešené v `InvokeAsync` metodě:</span><span class="sxs-lookup"><span data-stu-id="c7203-284">Scoped services must be resolved in the `InvokeAsync` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet2&highlight=2)]

<span data-ttu-id="c7203-285">Výstup protokolovacího nástroje ukazuje:</span><span class="sxs-lookup"><span data-stu-id="c7203-285">The logger output shows:</span></span>

* <span data-ttu-id="c7203-286">*Přechodné* objekty jsou vždy odlišné.</span><span class="sxs-lookup"><span data-stu-id="c7203-286">*Transient* objects are always different.</span></span> <span data-ttu-id="c7203-287">Přechodná `OperationId` hodnota se liší v `IndexModel` a v middlewaru.</span><span class="sxs-lookup"><span data-stu-id="c7203-287">The transient `OperationId` value is different in the `IndexModel` and in the middleware.</span></span>
* <span data-ttu-id="c7203-288">*Vymezené* objekty jsou pro každý požadavek stejné, ale v každé žádosti se liší.</span><span class="sxs-lookup"><span data-stu-id="c7203-288">*Scoped* objects are the same for each request but different across each request.</span></span>
* <span data-ttu-id="c7203-289">Objekty *singleton* jsou pro každý požadavek stejné.</span><span class="sxs-lookup"><span data-stu-id="c7203-289">*Singleton* objects are the same for every request.</span></span>

<span data-ttu-id="c7203-290">Chcete-li snížit výstup protokolování, nastavte v *appsettings.Development.jsv* souboru "Logging: LogLevel: Microsoft: Error":</span><span class="sxs-lookup"><span data-stu-id="c7203-290">To reduce the logging output, set "Logging:LogLevel:Microsoft:Error" in the *appsettings.Development.json* file:</span></span>

[!code-json[](dependency-injection/samples/3.x/DependencyInjectionSample/appsettings.Development.json?highlight=7)]

## <a name="call-services-from-main"></a><span data-ttu-id="c7203-291">Volat služby z Main</span><span class="sxs-lookup"><span data-stu-id="c7203-291">Call services from main</span></span>

<span data-ttu-id="c7203-292">Vytvořte <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> pomocí [IServiceScopeFactory. CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) pro řešení oboru služby v rámci rozsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="c7203-292">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="c7203-293">Tento přístup je užitečný pro přístup k oboruované službě při spuštění za účelem spouštění úloh inicializace.</span><span class="sxs-lookup"><span data-stu-id="c7203-293">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span>

<span data-ttu-id="c7203-294">Následující příklad ukazuje, jak přistupovat k oboru `IMyDependency` služby a volat jeho `WriteMessage` metodu v `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="c7203-294">The following example shows how to access the scoped `IMyDependency` service and call its `WriteMessage` method in `Program.Main`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Program.cs?name=snippet)]

<a name="sv"></a>

## <a name="scope-validation"></a><span data-ttu-id="c7203-295">Ověřování oboru</span><span class="sxs-lookup"><span data-stu-id="c7203-295">Scope validation</span></span>

<span data-ttu-id="c7203-296">Když je aplikace spuštěná ve [vývojovém prostředí](xref:fundamentals/environments) a volá [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) , aby se vytvořila hostitel, použije výchozí poskytovatel služeb kontroly, aby ověřil, že:</span><span class="sxs-lookup"><span data-stu-id="c7203-296">When the app runs in the [Development environment](xref:fundamentals/environments) and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="c7203-297">Oborové služby se nevyřešily od poskytovatele kořenové služby.</span><span class="sxs-lookup"><span data-stu-id="c7203-297">Scoped services aren't resolved from the root service provider.</span></span>
* <span data-ttu-id="c7203-298">Oborové služby se nevkládají do singleton.</span><span class="sxs-lookup"><span data-stu-id="c7203-298">Scoped services aren't injected into singletons.</span></span>

<span data-ttu-id="c7203-299">Poskytovatel kořenové služby se vytvoří, když <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> se zavolá.</span><span class="sxs-lookup"><span data-stu-id="c7203-299">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> is called.</span></span> <span data-ttu-id="c7203-300">Doba života poskytovatele kořenové služby odpovídá době životnosti aplikace, když poskytovatel spustí aplikaci a je uvolněna při ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="c7203-300">The root service provider's lifetime corresponds to the app's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="c7203-301">Oborové služby jsou uvolněny kontejnerem, který je vytvořil.</span><span class="sxs-lookup"><span data-stu-id="c7203-301">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="c7203-302">Pokud je v kořenovém kontejneru vytvořena vymezená služba, doba života služby je efektivně povýšena na typ singleton, protože je uvolněna pouze kořenovým kontejnerem, když aplikace vypíná.</span><span class="sxs-lookup"><span data-stu-id="c7203-302">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when the app shuts down.</span></span> <span data-ttu-id="c7203-303">Při ověřování oborů služeb se tyto situace zachytí `BuildServiceProvider` .</span><span class="sxs-lookup"><span data-stu-id="c7203-303">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="c7203-304">Další informace najdete v tématu [ověřování oboru](xref:fundamentals/host/web-host#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="c7203-304">For more information, see [Scope validation](xref:fundamentals/host/web-host#scope-validation).</span></span>

## <a name="request-services"></a><span data-ttu-id="c7203-305">Žádosti o služby</span><span class="sxs-lookup"><span data-stu-id="c7203-305">Request Services</span></span>

<span data-ttu-id="c7203-306">Služby, které jsou k dispozici v rámci žádosti o ASP.NET Core, jsou zpřístupněny prostřednictvím kolekce [HttpContext. RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) .</span><span class="sxs-lookup"><span data-stu-id="c7203-306">The services available within an ASP.NET Core request are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span> <span data-ttu-id="c7203-307">Pokud jsou služby požadovány zevnitř žádosti, služby a jejich závislosti jsou řešeny z `RequestServices` kolekce.</span><span class="sxs-lookup"><span data-stu-id="c7203-307">When services are requested from inside of a request, the services and their dependencies are resolved from the `RequestServices` collection.</span></span>

<span data-ttu-id="c7203-308">Architektura vytvoří obor na žádost a `RequestServices` zpřístupní poskytovatele vymezeného rozsahu.</span><span class="sxs-lookup"><span data-stu-id="c7203-308">The framework creates a scope per request and `RequestServices` exposes the scoped service provider.</span></span> <span data-ttu-id="c7203-309">Všechny oborové služby platí, pokud je žádost aktivní.</span><span class="sxs-lookup"><span data-stu-id="c7203-309">All scoped services are valid for as long as the request is active.</span></span>

> [!NOTE]
> <span data-ttu-id="c7203-310">Preferovat požadavky na závislosti jako parametry konstruktoru pro překlad služeb z `RequestServices` kolekce.</span><span class="sxs-lookup"><span data-stu-id="c7203-310">Prefer requesting dependencies as constructor parameters to resolving services from the `RequestServices` collection.</span></span> <span data-ttu-id="c7203-311">Výsledkem jsou třídy, které jsou snáze testovány.</span><span class="sxs-lookup"><span data-stu-id="c7203-311">This results in classes that are easier to test.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="c7203-312">Navrhnout služby pro vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="c7203-312">Design services for dependency injection</span></span>

<span data-ttu-id="c7203-313">Při navrhování služeb pro vkládání závislostí:</span><span class="sxs-lookup"><span data-stu-id="c7203-313">When designing services for dependency injection:</span></span>

* <span data-ttu-id="c7203-314">Vyhněte se stavovým, statickým třídám a členům.</span><span class="sxs-lookup"><span data-stu-id="c7203-314">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="c7203-315">Vyhněte se vytváření globálního stavu tím, že aplikace navrhujete, aby místo toho používaly služby singleton.</span><span class="sxs-lookup"><span data-stu-id="c7203-315">Avoid creating global state by designing apps to use singleton services instead.</span></span>
* <span data-ttu-id="c7203-316">Vyhněte se přímému vytváření instancí závislých tříd v rámci služeb.</span><span class="sxs-lookup"><span data-stu-id="c7203-316">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="c7203-317">Přímá instance Couples kód na konkrétní implementaci.</span><span class="sxs-lookup"><span data-stu-id="c7203-317">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="c7203-318">Vytvářejte služby s malým, dobře faktoringem a snadnou otestováním.</span><span class="sxs-lookup"><span data-stu-id="c7203-318">Make services small, well-factored, and easily tested.</span></span>

<span data-ttu-id="c7203-319">Pokud má třída mnoho vložených závislostí, může se jednat o znaménko, že třída má příliš mnoho zodpovědnosti a je v rozporu s [principem jediné zodpovědnosti (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="c7203-319">If a class has a lot of injected dependencies, it might be a sign that the class has too many responsibilities and violates the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="c7203-320">Pokuste se Refaktorovat třídu přesunutím některých jeho odpovědností do nových tříd.</span><span class="sxs-lookup"><span data-stu-id="c7203-320">Attempt to refactor the class by moving some of its responsibilities into new classes.</span></span> <span data-ttu-id="c7203-321">Mějte na paměti, že Razor stránky tříd modelu stránky a třídy KONTROLERU MVC by se měly zaměřit na uživatelské rozhraní.</span><span class="sxs-lookup"><span data-stu-id="c7203-321">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="c7203-322">Vyřazení služeb</span><span class="sxs-lookup"><span data-stu-id="c7203-322">Disposal of services</span></span>

<span data-ttu-id="c7203-323">Kontejner volá <xref:System.IDisposable.Dispose%2A> typy, které <xref:System.IDisposable> vytvoří.</span><span class="sxs-lookup"><span data-stu-id="c7203-323">The container calls <xref:System.IDisposable.Dispose%2A> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="c7203-324">Služby vyřešené z kontejneru by nikdy neměly být odstraněny vývojářem.</span><span class="sxs-lookup"><span data-stu-id="c7203-324">Services resolved from the container should never be disposed by the developer.</span></span> <span data-ttu-id="c7203-325">Pokud je typ nebo objekt pro vytváření zaregistrován jako singleton, kontejner odstraní singleton automaticky.</span><span class="sxs-lookup"><span data-stu-id="c7203-325">If a type or factory is registered as a singleton, the container disposes the singleton automatically.</span></span>

<span data-ttu-id="c7203-326">V následujícím příkladu jsou služby vytvořeny pomocí kontejneru služby a automaticky odstraněny:</span><span class="sxs-lookup"><span data-stu-id="c7203-326">In the following example, the services are created by the service container and disposed automatically:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Services/Service1.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="c7203-327">Konzola ladění po každé aktualizaci stránky indexu zobrazí následující výstup:</span><span class="sxs-lookup"><span data-stu-id="c7203-327">The debug console shows the following output after each refresh of the Index page:</span></span>

```console
Service1: IndexModel.OnGet
Service2: IndexModel.OnGet
Service3: IndexModel.OnGet
Service1.Dispose
```

### <a name="services-not-created-by-the-service-container"></a><span data-ttu-id="c7203-328">Služby, které nevytvořil kontejner služby</span><span class="sxs-lookup"><span data-stu-id="c7203-328">Services not created by the service container</span></span>

<span data-ttu-id="c7203-329">Uvažujte následující kód:</span><span class="sxs-lookup"><span data-stu-id="c7203-329">Consider the following code:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup2.cs?name=snippet)]

<span data-ttu-id="c7203-330">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="c7203-330">In the preceding code:</span></span>

* <span data-ttu-id="c7203-331">Instance služby nejsou vytvořeny kontejnerem služby.</span><span class="sxs-lookup"><span data-stu-id="c7203-331">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="c7203-332">Rozhraní neodstraní služby automaticky.</span><span class="sxs-lookup"><span data-stu-id="c7203-332">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="c7203-333">Vývojář zodpovídá za likvidaci služeb.</span><span class="sxs-lookup"><span data-stu-id="c7203-333">The developer is responsible for disposing the services.</span></span>

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="c7203-334">Doprovodné materiály k rozhraní IDisposable pro přechodné a sdílené instance</span><span class="sxs-lookup"><span data-stu-id="c7203-334">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="c7203-335">Přechodná, omezená doba života</span><span class="sxs-lookup"><span data-stu-id="c7203-335">Transient, limited lifetime</span></span>

<span data-ttu-id="c7203-336">**Scénář**</span><span class="sxs-lookup"><span data-stu-id="c7203-336">**Scenario**</span></span>

<span data-ttu-id="c7203-337">Aplikace vyžaduje <xref:System.IDisposable> instanci s přechodným životností pro některý z následujících scénářů:</span><span class="sxs-lookup"><span data-stu-id="c7203-337">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="c7203-338">Instance je vyřešena v kořenovém oboru (kořenovém kontejneru).</span><span class="sxs-lookup"><span data-stu-id="c7203-338">The instance is resolved in the root scope (root container).</span></span>
* <span data-ttu-id="c7203-339">Instance by měla být uvolněna před ukončením oboru.</span><span class="sxs-lookup"><span data-stu-id="c7203-339">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="c7203-340">**Řešení**</span><span class="sxs-lookup"><span data-stu-id="c7203-340">**Solution**</span></span>

<span data-ttu-id="c7203-341">Pomocí modelu továrny vytvořte instanci mimo nadřazený obor.</span><span class="sxs-lookup"><span data-stu-id="c7203-341">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="c7203-342">V takové situaci by aplikace měla obvykle `Create` metodu, která volá přímo konstruktor finálního typu.</span><span class="sxs-lookup"><span data-stu-id="c7203-342">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="c7203-343">Pokud má konečný typ jiné závislosti, továrna může:</span><span class="sxs-lookup"><span data-stu-id="c7203-343">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="c7203-344">Přijmout <xref:System.IServiceProvider> ve svém konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="c7203-344">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="c7203-345">Slouží <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> k vytvoření instance instance mimo kontejner, při použití kontejneru pro jeho závislosti.</span><span class="sxs-lookup"><span data-stu-id="c7203-345">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside of the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="c7203-346">Sdílená instance, omezená doba života</span><span class="sxs-lookup"><span data-stu-id="c7203-346">Shared instance, limited lifetime</span></span>

<span data-ttu-id="c7203-347">**Scénář**</span><span class="sxs-lookup"><span data-stu-id="c7203-347">**Scenario**</span></span>

<span data-ttu-id="c7203-348">Aplikace vyžaduje sdílenou <xref:System.IDisposable> instanci napříč více službami, ale <xref:System.IDisposable> instance by měla mít omezené trvání.</span><span class="sxs-lookup"><span data-stu-id="c7203-348">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> instance should have a limited lifetime.</span></span>

<span data-ttu-id="c7203-349">**Řešení**</span><span class="sxs-lookup"><span data-stu-id="c7203-349">**Solution**</span></span>

<span data-ttu-id="c7203-350">Zaregistrujte instanci s vymezenou životností.</span><span class="sxs-lookup"><span data-stu-id="c7203-350">Register the instance with a scoped lifetime.</span></span> <span data-ttu-id="c7203-351">Použijte <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> k vytvoření nového <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> .</span><span class="sxs-lookup"><span data-stu-id="c7203-351">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="c7203-352"><xref:System.IServiceProvider>Požadované služby získáte pomocí tohoto oboru.</span><span class="sxs-lookup"><span data-stu-id="c7203-352">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="c7203-353">Pokud už tento obor nepotřebujete, vyřadit ho.</span><span class="sxs-lookup"><span data-stu-id="c7203-353">Dispose the scope when it's no longer needed.</span></span>

#### <a name="general-idisposable-guidelines"></a><span data-ttu-id="c7203-354">Obecné pokyny pro IDisposable</span><span class="sxs-lookup"><span data-stu-id="c7203-354">General IDisposable guidelines</span></span>

* <span data-ttu-id="c7203-355">Neregistrujte <xref:System.IDisposable> instance s přechodným životností.</span><span class="sxs-lookup"><span data-stu-id="c7203-355">Don't register <xref:System.IDisposable> instances with a transient lifetime.</span></span> <span data-ttu-id="c7203-356">Místo toho použijte výrobní model.</span><span class="sxs-lookup"><span data-stu-id="c7203-356">Use the factory pattern instead.</span></span>
* <span data-ttu-id="c7203-357"><xref:System.IDisposable>V kořenovém oboru neodstraňujte instance s přechodnou nebo vymezenou životností.</span><span class="sxs-lookup"><span data-stu-id="c7203-357">Don't resolve <xref:System.IDisposable> instances with a transient or scoped lifetime in the root scope.</span></span> <span data-ttu-id="c7203-358">Jedinou výjimkou je, že pokud aplikace vytvoří nebo znovu vytvoří a odstraní <xref:System.IServiceProvider> , ale to není ideální vzor.</span><span class="sxs-lookup"><span data-stu-id="c7203-358">The only exception to this is if the app creates/recreates and disposes <xref:System.IServiceProvider>, but this isn't an ideal pattern.</span></span>
* <span data-ttu-id="c7203-359">Příjem <xref:System.IDisposable> závislosti přes di nevyžaduje, aby se příjemce sám implementoval <xref:System.IDisposable> .</span><span class="sxs-lookup"><span data-stu-id="c7203-359">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="c7203-360">Příjemce <xref:System.IDisposable> závislosti by neměl volat <xref:System.IDisposable.Dispose%2A> tuto závislost.</span><span class="sxs-lookup"><span data-stu-id="c7203-360">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="c7203-361">Použijte obory k řízení životnosti služeb.</span><span class="sxs-lookup"><span data-stu-id="c7203-361">Use scopes to control the lifetimes of services.</span></span> <span data-ttu-id="c7203-362">Obory nejsou hierarchické a mezi obory není žádné zvláštní připojení.</span><span class="sxs-lookup"><span data-stu-id="c7203-362">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="c7203-363">Výchozí nahrazení kontejneru služby</span><span class="sxs-lookup"><span data-stu-id="c7203-363">Default service container replacement</span></span>

<span data-ttu-id="c7203-364">Integrovaný kontejner služeb je navržený tak, aby sloužil potřebám architektury a většině spotřebitelských aplikací.</span><span class="sxs-lookup"><span data-stu-id="c7203-364">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="c7203-365">Pokud nepotřebujete konkrétní funkci, kterou nepodporuje, doporučujeme použít vestavěný kontejner, například:</span><span class="sxs-lookup"><span data-stu-id="c7203-365">We recommend using the built-in container unless you need a specific feature that it doesn't support, such as:</span></span>

* <span data-ttu-id="c7203-366">Vkládání vlastností</span><span class="sxs-lookup"><span data-stu-id="c7203-366">Property injection</span></span>
* <span data-ttu-id="c7203-367">Vkládání na základě názvu</span><span class="sxs-lookup"><span data-stu-id="c7203-367">Injection based on name</span></span>
* <span data-ttu-id="c7203-368">Podřízené kontejnery</span><span class="sxs-lookup"><span data-stu-id="c7203-368">Child containers</span></span>
* <span data-ttu-id="c7203-369">Vlastní Správa životního cyklu</span><span class="sxs-lookup"><span data-stu-id="c7203-369">Custom lifetime management</span></span>
* <span data-ttu-id="c7203-370">`Func<T>` Podpora opožděné inicializace</span><span class="sxs-lookup"><span data-stu-id="c7203-370">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="c7203-371">Registrace založená na konvencích</span><span class="sxs-lookup"><span data-stu-id="c7203-371">Convention-based registration</span></span>

<span data-ttu-id="c7203-372">U ASP.NET Corech aplikací lze použít následující kontejnery třetích stran:</span><span class="sxs-lookup"><span data-stu-id="c7203-372">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="c7203-373">Autofac</span><span class="sxs-lookup"><span data-stu-id="c7203-373">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="c7203-374">DryIoc</span><span class="sxs-lookup"><span data-stu-id="c7203-374">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="c7203-375">Integrit</span><span class="sxs-lookup"><span data-stu-id="c7203-375">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="c7203-376">LightInject</span><span class="sxs-lookup"><span data-stu-id="c7203-376">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="c7203-377">Lamar</span><span class="sxs-lookup"><span data-stu-id="c7203-377">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="c7203-378">Stashbox</span><span class="sxs-lookup"><span data-stu-id="c7203-378">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="c7203-379">Unity</span><span class="sxs-lookup"><span data-stu-id="c7203-379">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

## <a name="thread-safety"></a><span data-ttu-id="c7203-380">Bezpečnost vlákna</span><span class="sxs-lookup"><span data-stu-id="c7203-380">Thread safety</span></span>

<span data-ttu-id="c7203-381">Vytvořte služby, které jsou v bezpečí pro přístup z více vláken.</span><span class="sxs-lookup"><span data-stu-id="c7203-381">Create thread-safe singleton services.</span></span> <span data-ttu-id="c7203-382">Pokud má služba typu Singleton závislost na přechodné službě, přechodná služba může také vyžadovat zabezpečení vlákna v závislosti na tom, jak je používá typ singleton.</span><span class="sxs-lookup"><span data-stu-id="c7203-382">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending on how it's used by the singleton.</span></span>

<span data-ttu-id="c7203-383">Výrobní metoda samostatné služby, jako je například druhý argument pro [AddSingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A), nemusí být bezpečná pro přístup z více vláken.</span><span class="sxs-lookup"><span data-stu-id="c7203-383">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A), doesn't need to be thread-safe.</span></span> <span data-ttu-id="c7203-384">Podobně jako konstruktor typu ( `static` ), je zaručeno volání pouze jednou vláknem.</span><span class="sxs-lookup"><span data-stu-id="c7203-384">Like a type (`static`) constructor, it's guaranteed to be called only once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="c7203-385">Doporučení</span><span class="sxs-lookup"><span data-stu-id="c7203-385">Recommendations</span></span>

* <span data-ttu-id="c7203-386">`async/await``Task`řešení služeb založené na základech se nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="c7203-386">`async/await` and `Task` based service resolution isn't supported.</span></span> <span data-ttu-id="c7203-387">Vzhledem k tomu, že jazyk C# nepodporuje asynchronní konstruktory, použijte asynchronní metody po synchronním překladu služby.</span><span class="sxs-lookup"><span data-stu-id="c7203-387">Because C# doesn't support asynchronous constructors, use asynchronous methods after synchronously resolving the service.</span></span>
* <span data-ttu-id="c7203-388">Neukládejte data a konfiguraci přímo do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="c7203-388">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="c7203-389">Například nákupní košík uživatele by neměl být obvykle přidán do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="c7203-389">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="c7203-390">Konfigurace by měla používat [vzor možností](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="c7203-390">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="c7203-391">Podobně nepoužívejte objekty "držitel dat", které existují pouze k povolení přístupu k jinému objektu.</span><span class="sxs-lookup"><span data-stu-id="c7203-391">Similarly, avoid "data holder" objects that only exist to allow access to another object.</span></span> <span data-ttu-id="c7203-392">Je lepší žádat o skutečnou položku přes DI.</span><span class="sxs-lookup"><span data-stu-id="c7203-392">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="c7203-393">Vyhněte se statickému přístupu ke službám.</span><span class="sxs-lookup"><span data-stu-id="c7203-393">Avoid static access to services.</span></span> <span data-ttu-id="c7203-394">Vyhněte se například zachycení [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) jako statické pole nebo vlastnost pro použití jinde.</span><span class="sxs-lookup"><span data-stu-id="c7203-394">For example, avoid capturing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) as a static field or property for use elsewhere.</span></span>
* <span data-ttu-id="c7203-395">Snažte se DI Factory rychle a synchronně.</span><span class="sxs-lookup"><span data-stu-id="c7203-395">Keep DI factories fast and synchronous.</span></span>
* <span data-ttu-id="c7203-396">Vyhněte se použití *vzoru lokátoru služby*.</span><span class="sxs-lookup"><span data-stu-id="c7203-396">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="c7203-397">Například Nevolejte <xref:System.IServiceProvider.GetService%2A> pro získání instance služby, když můžete místo toho použít di:</span><span class="sxs-lookup"><span data-stu-id="c7203-397">For example, don't invoke <xref:System.IServiceProvider.GetService%2A> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="c7203-398">**Nesprávně:**</span><span class="sxs-lookup"><span data-stu-id="c7203-398">**Incorrect:**</span></span>

    ![Nesprávný kód](dependency-injection/_static/bad.png)

  <span data-ttu-id="c7203-400">**Správné**:</span><span class="sxs-lookup"><span data-stu-id="c7203-400">**Correct**:</span></span>

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
* <span data-ttu-id="c7203-401">Další změnou lokátoru služby, aby se zabránilo, je vložení továrny, která vyřeší závislosti za běhu.</span><span class="sxs-lookup"><span data-stu-id="c7203-401">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="c7203-402">Oba tyto postupy kombinují [inverze strategií řízení](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .</span><span class="sxs-lookup"><span data-stu-id="c7203-402">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
* <span data-ttu-id="c7203-403">Vyhněte se statickému přístupu k `HttpContext` (například [IHttpContextAccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="c7203-403">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<a name="ASP0000"></a>
* <span data-ttu-id="c7203-404">Vyhněte se volání do <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> v `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="c7203-404">Avoid calls to <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> in `ConfigureServices`.</span></span> <span data-ttu-id="c7203-405">`BuildServiceProvider`K volání obvykle dochází, když vývojář chce službu vyřešit `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="c7203-405">Calling `BuildServiceProvider` typically happens when the developer wants to resolve a service in `ConfigureServices`.</span></span> <span data-ttu-id="c7203-406">Zvažte například případ, kdy `LoginPath` je načten z konfigurace.</span><span class="sxs-lookup"><span data-stu-id="c7203-406">For example, consider the case where the `LoginPath` is loaded from configuration.</span></span> <span data-ttu-id="c7203-407">Vyhněte se následujícímu přístupu:</span><span class="sxs-lookup"><span data-stu-id="c7203-407">Avoid the following approach:</span></span>

  ![Chybný kód volající BuildServiceProvider](~/fundamentals/dependency-injection/_static/badcodeX.png)

  <span data-ttu-id="c7203-409">Na předchozím obrázku vyberte zelenou vlnovku pod nadpisem `services.BuildServiceProvider` zobrazit následující upozornění ASP0000:</span><span class="sxs-lookup"><span data-stu-id="c7203-409">In the preceding image, selecting the green wavy line under `services.BuildServiceProvider` shows the following ASP0000 warning:</span></span>

  > <span data-ttu-id="c7203-410">ASP0000 volání ' BuildServiceProvider ' z kódu aplikace vede k vytvoření další kopie ojedinělých služeb, které jsou vytvářeny.</span><span class="sxs-lookup"><span data-stu-id="c7203-410">ASP0000 Calling 'BuildServiceProvider' from application code results in an additional copy of singleton services being created.</span></span> <span data-ttu-id="c7203-411">Zvažte alternativy, jako jsou třeba závislosti, jako jsou například služby, jako jsou parametry konfigurace.</span><span class="sxs-lookup"><span data-stu-id="c7203-411">Consider alternatives such as dependency injecting services as parameters to 'Configure'.</span></span>

  <span data-ttu-id="c7203-412">Volání `BuildServiceProvider` vytvoří druhý kontejner, který může vytvořit roztrhané jednoznačné objekty a způsobit odkazy na grafy objektů napříč více kontejnery.</span><span class="sxs-lookup"><span data-stu-id="c7203-412">Calling `BuildServiceProvider` creates a second container, which can create torn singletons and cause references to object graphs across multiple containers.</span></span>

  <span data-ttu-id="c7203-413">Správný způsob, jak získat `LoginPath` , je použít integrovanou podporu vzorce možností pro di:</span><span class="sxs-lookup"><span data-stu-id="c7203-413">A correct way to get `LoginPath` is to use the options pattern's built-in support for DI:</span></span>

  [!code-csharp[](dependency-injection/samples/3.x/AntiPattern3/Startup.cs?name=snippet)]

* <span data-ttu-id="c7203-414">Přechodné služby za použití jsou zachyceny kontejnerem k vyřazení.</span><span class="sxs-lookup"><span data-stu-id="c7203-414">Disposable transient services are captured by the container for disposal.</span></span> <span data-ttu-id="c7203-415">To může způsobit nevracení paměti, pokud je vyřešeno z kontejneru nejvyšší úrovně.</span><span class="sxs-lookup"><span data-stu-id="c7203-415">This can turn into a memory leak if resolved from the top level container.</span></span>
* <span data-ttu-id="c7203-416">Povolte ověřování oboru, abyste se ujistili, že aplikace nemá k dispozici jednoznačné služby, které zachytí obor služeb.</span><span class="sxs-lookup"><span data-stu-id="c7203-416">Enable scope validation to make sure the app doesn't have singletons that capture scoped services.</span></span> <span data-ttu-id="c7203-417">Další informace najdete v tématu [ověřování oboru](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="c7203-417">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="c7203-418">Podobně jako u všech sad doporučení se může stát, že se vyžaduje ignorování doporučení.</span><span class="sxs-lookup"><span data-stu-id="c7203-418">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="c7203-419">Výjimky jsou zřídka, většinou zvláštní případy v rámci samotného rozhraní.</span><span class="sxs-lookup"><span data-stu-id="c7203-419">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="c7203-420">DI je *alternativou* ke vzorům statických nebo globálních přístupů k objektům.</span><span class="sxs-lookup"><span data-stu-id="c7203-420">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="c7203-421">Je možné, že nebudete moci využít výhody DI, pokud je kombinujete se statickým přístupem k objektům.</span><span class="sxs-lookup"><span data-stu-id="c7203-421">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="c7203-422">Doporučené vzory pro víceklientské architektury v DI</span><span class="sxs-lookup"><span data-stu-id="c7203-422">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="c7203-423">[Sadu jader](https://github.com/OrchardCMS/OrchardCore) je aplikační architektura pro vytváření modulárních aplikací s více klienty na ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c7203-423">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) is an application framework for building modular, multi-tenant applications on ASP.NET Core.</span></span> <span data-ttu-id="c7203-424">Další informace najdete v dokumentaci k sadě [sad](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="c7203-424">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="c7203-425">V [ukázkách Core Core](https://github.com/OrchardCMS/OrchardCore.Samples) najdete příklady vytváření modulárních a víceklientské aplikací s využitím jenom sady Core Core Framework bez jakýchkoli funkcí specifických pro CMS.</span><span class="sxs-lookup"><span data-stu-id="c7203-425">See the [Orchard Core samples](https://github.com/OrchardCMS/OrchardCore.Samples) for examples of how to build modular and multi-tenant apps using just the Orchard Core Framework without any of its CMS-specific features.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="c7203-426">Služby poskytované rozhraním</span><span class="sxs-lookup"><span data-stu-id="c7203-426">Framework-provided services</span></span>

<span data-ttu-id="c7203-427">`Startup.ConfigureServices`Metoda zaregistruje služby, které aplikace používá, včetně funkcí platformy, jako je například Entity Framework Core a ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="c7203-427">The `Startup.ConfigureServices` method registers services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="c7203-428">Zpočátku `IServiceCollection` `ConfigureServices` služba má služby definované rozhraním v závislosti na [tom, jak byl hostitel nakonfigurovaný](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="c7203-428">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="c7203-429">Pro aplikace založené na šablonách ASP.NET Core registruje rozhraní více než 250 služeb.</span><span class="sxs-lookup"><span data-stu-id="c7203-429">For apps based on the ASP.NET Core templates, the framework registers more than 250 services.</span></span> 

<span data-ttu-id="c7203-430">V následující tabulce je uveden malý vzorek těchto služeb registrovaných v rozhraní:</span><span class="sxs-lookup"><span data-stu-id="c7203-430">The following table lists a small sample of these framework-registered services:</span></span>

| <span data-ttu-id="c7203-431">Typ služby</span><span class="sxs-lookup"><span data-stu-id="c7203-431">Service Type</span></span>                                                                                    | <span data-ttu-id="c7203-432">Doba platnosti</span><span class="sxs-lookup"><span data-stu-id="c7203-432">Lifetime</span></span>  |
|-------------------------------------------------------------------------------------------------|-----------|
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="c7203-433">Dočasný</span><span class="sxs-lookup"><span data-stu-id="c7203-433">Transient</span></span> |
| <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>                                    | <span data-ttu-id="c7203-434">Singleton</span><span class="sxs-lookup"><span data-stu-id="c7203-434">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>                                         | <span data-ttu-id="c7203-435">Singleton</span><span class="sxs-lookup"><span data-stu-id="c7203-435">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName>                           | <span data-ttu-id="c7203-436">Singleton</span><span class="sxs-lookup"><span data-stu-id="c7203-436">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName>                     | <span data-ttu-id="c7203-437">Dočasný</span><span class="sxs-lookup"><span data-stu-id="c7203-437">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName>                     | <span data-ttu-id="c7203-438">Singleton</span><span class="sxs-lookup"><span data-stu-id="c7203-438">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName>                   | <span data-ttu-id="c7203-439">Dočasný</span><span class="sxs-lookup"><span data-stu-id="c7203-439">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger%601?displayProperty=fullName>                        | <span data-ttu-id="c7203-440">Singleton</span><span class="sxs-lookup"><span data-stu-id="c7203-440">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName>                     | <span data-ttu-id="c7203-441">Singleton</span><span class="sxs-lookup"><span data-stu-id="c7203-441">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName>              | <span data-ttu-id="c7203-442">Singleton</span><span class="sxs-lookup"><span data-stu-id="c7203-442">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions%601?displayProperty=fullName>              | <span data-ttu-id="c7203-443">Dočasný</span><span class="sxs-lookup"><span data-stu-id="c7203-443">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions%601?displayProperty=fullName>                       | <span data-ttu-id="c7203-444">Singleton</span><span class="sxs-lookup"><span data-stu-id="c7203-444">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName>                             | <span data-ttu-id="c7203-445">Singleton</span><span class="sxs-lookup"><span data-stu-id="c7203-445">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName>                           | <span data-ttu-id="c7203-446">Singleton</span><span class="sxs-lookup"><span data-stu-id="c7203-446">Singleton</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="c7203-447">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="c7203-447">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* [<span data-ttu-id="c7203-448">NORWEGIAN Developers Conference se vzory konferencí pro vývoj aplikací pro DI</span><span class="sxs-lookup"><span data-stu-id="c7203-448">NDC Conference Patterns for DI app development</span></span>](https://www.youtube.com/watch?v=x-C-CNBVTaY)
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="c7203-449">Čtyři způsoby, jak vyřadit IDisposable v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c7203-449">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="c7203-450">Zápis čistého kódu v ASP.NET Core pomocí injektáže závislosti (MSDN)</span><span class="sxs-lookup"><span data-stu-id="c7203-450">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="c7203-451">Explicitní závislosti – princip</span><span class="sxs-lookup"><span data-stu-id="c7203-451">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="c7203-452">Inverze řídicích kontejnerů a vzor injektáže závislosti (Martin Fowlera)</span><span class="sxs-lookup"><span data-stu-id="c7203-452">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="c7203-453">Jak zaregistrovat službu s více rozhraními v ASP.NET Core DI</span><span class="sxs-lookup"><span data-stu-id="c7203-453">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c7203-454">[Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com)a [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="c7203-454">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="c7203-455">ASP.NET Core podporuje vzor návrhu softwaru pro vkládání závislostí (DI), což je technika pro dosažení [inverze ovládacího prvku (IOC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) mezi třídami a jejich závislostmi.</span><span class="sxs-lookup"><span data-stu-id="c7203-455">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="c7203-456">Další informace, které jsou specifické pro vkládání závislostí v rámci řadičů MVC, najdete v tématu <xref:mvc/controllers/dependency-injection> .</span><span class="sxs-lookup"><span data-stu-id="c7203-456">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="c7203-457">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c7203-457">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="c7203-458">Přehled injektáže závislosti</span><span class="sxs-lookup"><span data-stu-id="c7203-458">Overview of dependency injection</span></span>

<span data-ttu-id="c7203-459">*Závislost* je libovolný objekt, který vyžaduje jiný objekt.</span><span class="sxs-lookup"><span data-stu-id="c7203-459">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="c7203-460">Projděte si následující `MyDependency` třídu s `WriteMessage` metodou, na které jsou závislé jiné třídy v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="c7203-460">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="c7203-461">Instance `MyDependency` třídy může být vytvořena, aby byla `WriteMessage` Metoda k dispozici pro třídu.</span><span class="sxs-lookup"><span data-stu-id="c7203-461">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="c7203-462">`MyDependency`Třída je závislostí `IndexModel` třídy:</span><span class="sxs-lookup"><span data-stu-id="c7203-462">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="c7203-463">Třída vytvoří a přímo závisí na `MyDependency` instanci.</span><span class="sxs-lookup"><span data-stu-id="c7203-463">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="c7203-464">Závislosti kódu (například předchozí příklad) jsou problematické a je třeba se jim vyhnout z následujících důvodů:</span><span class="sxs-lookup"><span data-stu-id="c7203-464">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="c7203-465">Chcete-li nahradit `MyDependency` jinou implementací, třída musí být upravena.</span><span class="sxs-lookup"><span data-stu-id="c7203-465">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="c7203-466">Pokud `MyDependency` má závislosti, musí být nakonfigurovány třídou.</span><span class="sxs-lookup"><span data-stu-id="c7203-466">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="c7203-467">Ve velkém projektu s více třídami, v závislosti na tom `MyDependency` , je kód konfigurace rozptýlen napříč aplikací.</span><span class="sxs-lookup"><span data-stu-id="c7203-467">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="c7203-468">Tato implementace je obtížná pro testování částí.</span><span class="sxs-lookup"><span data-stu-id="c7203-468">This implementation is difficult to unit test.</span></span> <span data-ttu-id="c7203-469">Aplikace by měla používat třídu typu "nebo zástupné procedury" `MyDependency` , což není u tohoto přístupu možné.</span><span class="sxs-lookup"><span data-stu-id="c7203-469">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="c7203-470">Vkládání závislostí řeší tyto problémy prostřednictvím:</span><span class="sxs-lookup"><span data-stu-id="c7203-470">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="c7203-471">Použití rozhraní nebo základní třídy k abstrakci implementace závislosti.</span><span class="sxs-lookup"><span data-stu-id="c7203-471">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="c7203-472">Registrace závislosti v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="c7203-472">Registration of the dependency in a service container.</span></span> <span data-ttu-id="c7203-473">ASP.NET Core poskytuje integrovaný kontejner služeb <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="c7203-473">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="c7203-474">Služby jsou registrovány v metodě aplikace `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="c7203-474">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="c7203-475">*Vložení* služby do konstruktoru třídy, kde se používá.</span><span class="sxs-lookup"><span data-stu-id="c7203-475">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="c7203-476">Rozhraní přebírá zodpovědnost za vytvoření instance závislosti a její odstranění, když už ji nepotřebujete.</span><span class="sxs-lookup"><span data-stu-id="c7203-476">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="c7203-477">V [ukázkové aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) `IMyDependency` definuje rozhraní metodu, kterou služba poskytuje aplikaci:</span><span class="sxs-lookup"><span data-stu-id="c7203-477">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="c7203-478">Toto rozhraní je implementováno konkrétním typem `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="c7203-478">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="c7203-479">`MyDependency` vyžádá <xref:Microsoft.Extensions.Logging.ILogger`1> v jeho konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="c7203-479">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="c7203-480">Není neobvyklé používat vkládání závislostí v zřetězeném způsobem.</span><span class="sxs-lookup"><span data-stu-id="c7203-480">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="c7203-481">Každá požadovaná závislost zase vyžádá své vlastní závislosti.</span><span class="sxs-lookup"><span data-stu-id="c7203-481">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="c7203-482">Kontejner vyřeší závislosti v grafu a vrátí plně přeloženou službu.</span><span class="sxs-lookup"><span data-stu-id="c7203-482">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="c7203-483">Kolektivní sada závislostí, které je třeba vyřešit, se obvykle označuje jako *strom závislosti*, *graf závislosti*nebo *graf objektů*.</span><span class="sxs-lookup"><span data-stu-id="c7203-483">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="c7203-484">`IMyDependency` a `ILogger<TCategoryName>` musí být registrována v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="c7203-484">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="c7203-485">`IMyDependency` je zaregistrován v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="c7203-485">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c7203-486">`ILogger<TCategoryName>` je zaregistrované v infrastruktuře abstrakce protokolování, takže se jedná o [službu](#framework-provided-services) , která je zaregistrovaná ve výchozím rozhraní.</span><span class="sxs-lookup"><span data-stu-id="c7203-486">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="c7203-487">Kontejner se překládá `ILogger<TCategoryName>` tím, že využije [(Obecné) otevřené typy](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)a eliminuje nutnost registrace každého [(obecného) konstruovaného typu](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="c7203-487">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="c7203-488">V ukázkové aplikaci `IMyDependency` je služba zaregistrovaná u konkrétního typu `MyDependency` .</span><span class="sxs-lookup"><span data-stu-id="c7203-488">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="c7203-489">Registruje rozsah platnosti služby po dobu životnosti jediného požadavku.</span><span class="sxs-lookup"><span data-stu-id="c7203-489">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="c7203-490">[Doby platnosti služeb](#service-lifetimes) jsou popsány dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="c7203-490">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="c7203-491">Jednotlivé `services.Add{SERVICE_NAME}` metody rozšíření přidávají (a případně nakonfigurují) služby.</span><span class="sxs-lookup"><span data-stu-id="c7203-491">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="c7203-492">Například `services.AddMvc()` přidá Razor stránky služeb a MVC vyžaduje.</span><span class="sxs-lookup"><span data-stu-id="c7203-492">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="c7203-493">Doporučujeme, aby aplikace dodržovaly tuto konvenci.</span><span class="sxs-lookup"><span data-stu-id="c7203-493">We recommended that apps follow this convention.</span></span> <span data-ttu-id="c7203-494">Do oboru názvů [Microsoft. Extensions. DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) umístěte metody rozšíření pro zapouzdření skupin registrací služby.</span><span class="sxs-lookup"><span data-stu-id="c7203-494">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="c7203-495">Pokud konstruktor služby vyžaduje [vestavěný typ](/dotnet/csharp/language-reference/keywords/built-in-types-table), například `string` ,, může být typ vložen pomocí [Konfigurace](xref:fundamentals/configuration/index) nebo [vzoru možností](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="c7203-495">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="c7203-496">Instance služby je požadována prostřednictvím konstruktoru třídy, kde je služba používána a přiřazena k soukromému poli.</span><span class="sxs-lookup"><span data-stu-id="c7203-496">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="c7203-497">Pole se používá pro přístup ke službě podle potřeby v celé třídě.</span><span class="sxs-lookup"><span data-stu-id="c7203-497">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="c7203-498">V ukázkové aplikaci `IMyDependency` je instance vyžádána a používána pro volání `WriteMessage` metody služby:</span><span class="sxs-lookup"><span data-stu-id="c7203-498">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="c7203-499">Služby vložené do spuštění</span><span class="sxs-lookup"><span data-stu-id="c7203-499">Services injected into Startup</span></span>

<span data-ttu-id="c7203-500">`Startup`Při použití obecného hostitele () mohou být do konstruktoru vloženy pouze následující typy služeb <xref:Microsoft.Extensions.Hosting.IHostBuilder> :</span><span class="sxs-lookup"><span data-stu-id="c7203-500">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="c7203-501">Služby je možné vložit do `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="c7203-501">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="c7203-502">Další informace naleznete v tématu <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="c7203-502">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="c7203-503">Služby poskytované rozhraním</span><span class="sxs-lookup"><span data-stu-id="c7203-503">Framework-provided services</span></span>

<span data-ttu-id="c7203-504">`Startup.ConfigureServices`Metoda zodpovídá za definování služeb, které aplikace používá, včetně funkcí platformy, jako je například Entity Framework Core a ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="c7203-504">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="c7203-505">Zpočátku `IServiceCollection` `ConfigureServices` služba má služby definované rozhraním v závislosti na [tom, jak byl hostitel nakonfigurovaný](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="c7203-505">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="c7203-506">Pro aplikaci, která je založená na šabloně ASP.NET Core, není běžné, že mají stovky služeb zaregistrovaných v rámci rozhraní.</span><span class="sxs-lookup"><span data-stu-id="c7203-506">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="c7203-507">V následující tabulce je uvedena malá ukázka služeb registrovaných v rozhraní.</span><span class="sxs-lookup"><span data-stu-id="c7203-507">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="c7203-508">Typ služby</span><span class="sxs-lookup"><span data-stu-id="c7203-508">Service Type</span></span> | <span data-ttu-id="c7203-509">Doba platnosti</span><span class="sxs-lookup"><span data-stu-id="c7203-509">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="c7203-510">Dočasný</span><span class="sxs-lookup"><span data-stu-id="c7203-510">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="c7203-511">Singleton</span><span class="sxs-lookup"><span data-stu-id="c7203-511">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="c7203-512">Singleton</span><span class="sxs-lookup"><span data-stu-id="c7203-512">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="c7203-513">Singleton</span><span class="sxs-lookup"><span data-stu-id="c7203-513">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="c7203-514">Dočasný</span><span class="sxs-lookup"><span data-stu-id="c7203-514">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="c7203-515">Singleton</span><span class="sxs-lookup"><span data-stu-id="c7203-515">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="c7203-516">Dočasný</span><span class="sxs-lookup"><span data-stu-id="c7203-516">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="c7203-517">Singleton</span><span class="sxs-lookup"><span data-stu-id="c7203-517">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="c7203-518">Singleton</span><span class="sxs-lookup"><span data-stu-id="c7203-518">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="c7203-519">Singleton</span><span class="sxs-lookup"><span data-stu-id="c7203-519">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="c7203-520">Dočasný</span><span class="sxs-lookup"><span data-stu-id="c7203-520">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="c7203-521">Singleton</span><span class="sxs-lookup"><span data-stu-id="c7203-521">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="c7203-522">Singleton</span><span class="sxs-lookup"><span data-stu-id="c7203-522">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="c7203-523">Singleton</span><span class="sxs-lookup"><span data-stu-id="c7203-523">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="c7203-524">Registrace dalších služeb pomocí rozšiřujících metod</span><span class="sxs-lookup"><span data-stu-id="c7203-524">Register additional services with extension methods</span></span>

<span data-ttu-id="c7203-525">Pokud je k dispozici metoda rozšíření kolekce služeb pro registraci služby (a jejích závislých služeb, v případě potřeby), konvence používá jedinou `Add{SERVICE_NAME}` metodu rozšíření k registraci všech služeb vyžadovaných touto službou.</span><span class="sxs-lookup"><span data-stu-id="c7203-525">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="c7203-526">Následující kód je příkladem přidání dalších služeb do kontejneru pomocí metod rozšíření [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) a <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> :</span><span class="sxs-lookup"><span data-stu-id="c7203-526">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="c7203-527">Další informace najdete <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> ve třídě v dokumentaci k rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="c7203-527">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="c7203-528">Životnost služeb</span><span class="sxs-lookup"><span data-stu-id="c7203-528">Service lifetimes</span></span>

<span data-ttu-id="c7203-529">Vyberte odpovídající dobu života pro každou registrovanou službu.</span><span class="sxs-lookup"><span data-stu-id="c7203-529">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="c7203-530">Služby ASP.NET Core Services je možné nakonfigurovat s následujícími životnostmi:</span><span class="sxs-lookup"><span data-stu-id="c7203-530">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="c7203-531">Dočasný</span><span class="sxs-lookup"><span data-stu-id="c7203-531">Transient</span></span>

<span data-ttu-id="c7203-532">Služba přechodných životností ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*> ) se vytvoří pokaždé, když se požadují z kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="c7203-532">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="c7203-533">Tato životnost funguje nejlépe pro odlehčené bezstavové služby.</span><span class="sxs-lookup"><span data-stu-id="c7203-533">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="c7203-534">V aplikacích, které zpracovávají požadavky, jsou přechodné služby na konci žádosti zrušené.</span><span class="sxs-lookup"><span data-stu-id="c7203-534">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="c7203-535">Obor</span><span class="sxs-lookup"><span data-stu-id="c7203-535">Scoped</span></span>

<span data-ttu-id="c7203-536">Oborové služby () s rozsahem platnosti ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*> ) se vytvoří jednou za požadavek klienta (připojení).</span><span class="sxs-lookup"><span data-stu-id="c7203-536">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="c7203-537">V aplikacích, které zpracovávají požadavky, se vymezené služby na konci žádosti odstraní.</span><span class="sxs-lookup"><span data-stu-id="c7203-537">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="c7203-538">Při použití oboru služby v middlewaru založit službu do `Invoke` `InvokeAsync` metody nebo.</span><span class="sxs-lookup"><span data-stu-id="c7203-538">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="c7203-539">Nepoužívejte [vkládání pomocí injektáže konstruktoru](xref:mvc/controllers/dependency-injection#constructor-injection) , protože vynutí, aby se služba chovala jako typ singleton.</span><span class="sxs-lookup"><span data-stu-id="c7203-539">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="c7203-540">Další informace naleznete v tématu <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="c7203-540">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="c7203-541">Singleton</span><span class="sxs-lookup"><span data-stu-id="c7203-541">Singleton</span></span>

<span data-ttu-id="c7203-542">Služba singleton životnosti ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*> ) je vytvořena při prvním vyžádání (nebo při `Startup.ConfigureServices` spuštění a instance je určena pro registraci služby).</span><span class="sxs-lookup"><span data-stu-id="c7203-542">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="c7203-543">Každý další požadavek používá stejnou instanci.</span><span class="sxs-lookup"><span data-stu-id="c7203-543">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="c7203-544">Pokud aplikace vyžaduje chování singleton, doporučuje se povolit kontejneru služby spravovat dobu života služby.</span><span class="sxs-lookup"><span data-stu-id="c7203-544">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="c7203-545">Neimplementujte vzor návrhu singleton a poskytněte uživatelský kód pro správu životního cyklu objektu ve třídě.</span><span class="sxs-lookup"><span data-stu-id="c7203-545">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="c7203-546">V aplikacích, které zpracovávají požadavky, jsou nejednoznačné služby uvolněny, když <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> je uvolněna při vypnutí aplikace.</span><span class="sxs-lookup"><span data-stu-id="c7203-546">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="c7203-547">Není bezpečné přeložit oborovou službu z typu singleton.</span><span class="sxs-lookup"><span data-stu-id="c7203-547">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="c7203-548">Může dojít k tomu, že služba má při zpracování dalších požadavků špatný stav.</span><span class="sxs-lookup"><span data-stu-id="c7203-548">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="c7203-549">Metody registrace služby</span><span class="sxs-lookup"><span data-stu-id="c7203-549">Service registration methods</span></span>

<span data-ttu-id="c7203-550">Metody rozšíření registrace služby nabízejí přetížení, která jsou užitečná pro konkrétní scénáře.</span><span class="sxs-lookup"><span data-stu-id="c7203-550">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="c7203-551">Metoda</span><span class="sxs-lookup"><span data-stu-id="c7203-551">Method</span></span> | <span data-ttu-id="c7203-552">Automaticky</span><span class="sxs-lookup"><span data-stu-id="c7203-552">Automatic</span></span><br><span data-ttu-id="c7203-553">object</span><span class="sxs-lookup"><span data-stu-id="c7203-553">object</span></span><br><span data-ttu-id="c7203-554">odvod</span><span class="sxs-lookup"><span data-stu-id="c7203-554">disposal</span></span> | <span data-ttu-id="c7203-555">Několik</span><span class="sxs-lookup"><span data-stu-id="c7203-555">Multiple</span></span><br><span data-ttu-id="c7203-556">implementace</span><span class="sxs-lookup"><span data-stu-id="c7203-556">implementations</span></span> | <span data-ttu-id="c7203-557">Pass – argumenty</span><span class="sxs-lookup"><span data-stu-id="c7203-557">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="c7203-558">Příklad:</span><span class="sxs-lookup"><span data-stu-id="c7203-558">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="c7203-559">Ano</span><span class="sxs-lookup"><span data-stu-id="c7203-559">Yes</span></span> | <span data-ttu-id="c7203-560">Ano</span><span class="sxs-lookup"><span data-stu-id="c7203-560">Yes</span></span> | <span data-ttu-id="c7203-561">No</span><span class="sxs-lookup"><span data-stu-id="c7203-561">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="c7203-562">Příklady:</span><span class="sxs-lookup"><span data-stu-id="c7203-562">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="c7203-563">Ano</span><span class="sxs-lookup"><span data-stu-id="c7203-563">Yes</span></span> | <span data-ttu-id="c7203-564">Ano</span><span class="sxs-lookup"><span data-stu-id="c7203-564">Yes</span></span> | <span data-ttu-id="c7203-565">Ano</span><span class="sxs-lookup"><span data-stu-id="c7203-565">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="c7203-566">Příklad:</span><span class="sxs-lookup"><span data-stu-id="c7203-566">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="c7203-567">Ano</span><span class="sxs-lookup"><span data-stu-id="c7203-567">Yes</span></span> | <span data-ttu-id="c7203-568">No</span><span class="sxs-lookup"><span data-stu-id="c7203-568">No</span></span> | <span data-ttu-id="c7203-569">No</span><span class="sxs-lookup"><span data-stu-id="c7203-569">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="c7203-570">Příklady:</span><span class="sxs-lookup"><span data-stu-id="c7203-570">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="c7203-571">No</span><span class="sxs-lookup"><span data-stu-id="c7203-571">No</span></span> | <span data-ttu-id="c7203-572">Ano</span><span class="sxs-lookup"><span data-stu-id="c7203-572">Yes</span></span> | <span data-ttu-id="c7203-573">Ano</span><span class="sxs-lookup"><span data-stu-id="c7203-573">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="c7203-574">Příklady:</span><span class="sxs-lookup"><span data-stu-id="c7203-574">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="c7203-575">No</span><span class="sxs-lookup"><span data-stu-id="c7203-575">No</span></span> | <span data-ttu-id="c7203-576">No</span><span class="sxs-lookup"><span data-stu-id="c7203-576">No</span></span> | <span data-ttu-id="c7203-577">Ano</span><span class="sxs-lookup"><span data-stu-id="c7203-577">Yes</span></span> |

<span data-ttu-id="c7203-578">Další informace o vyřazení typů najdete v části věnované [vyřazení služeb](#disposal-of-services) .</span><span class="sxs-lookup"><span data-stu-id="c7203-578">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="c7203-579">Běžným scénářem pro více implementací je vytvoření [typů pro testování](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="c7203-579">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="c7203-580">`TryAdd{LIFETIME}` metody registrují službu jenom v případě, že už není zaregistrovaná implementace.</span><span class="sxs-lookup"><span data-stu-id="c7203-580">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="c7203-581">V následujícím příkladu se první řádek registruje `MyDependency` pro `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="c7203-581">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="c7203-582">Druhý řádek nemá žádný účinek, protože `IMyDependency` už má registrovanou implementaci:</span><span class="sxs-lookup"><span data-stu-id="c7203-582">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="c7203-583">Další informace naleznete v tématech:</span><span class="sxs-lookup"><span data-stu-id="c7203-583">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="c7203-584">Metody [TryAddEnumerable (ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) registrují službu pouze v případě, že již není implementace *stejného typu*.</span><span class="sxs-lookup"><span data-stu-id="c7203-584">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="c7203-585">Několik služeb je vyřešeno prostřednictvím `IEnumerable<{SERVICE}>` .</span><span class="sxs-lookup"><span data-stu-id="c7203-585">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="c7203-586">Při registraci služeb chce vývojář přidat instanci jenom v případě, že už jeden ze stejného typu není přidaný.</span><span class="sxs-lookup"><span data-stu-id="c7203-586">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="c7203-587">Obecně je tato metoda používána autory knihovny k tomu, aby nedocházelo k registraci dvou kopií instance v kontejneru.</span><span class="sxs-lookup"><span data-stu-id="c7203-587">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="c7203-588">V následujícím příkladu se první řádek registruje `MyDep` pro `IMyDep1` .</span><span class="sxs-lookup"><span data-stu-id="c7203-588">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="c7203-589">Druhý řádek se registruje `MyDep` pro `IMyDep2` .</span><span class="sxs-lookup"><span data-stu-id="c7203-589">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="c7203-590">Třetí řádek nemá žádný vliv, protože `IMyDep1` už má registrovanou implementaci `MyDep` :</span><span class="sxs-lookup"><span data-stu-id="c7203-590">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="c7203-591">Chování injektáže konstruktoru</span><span class="sxs-lookup"><span data-stu-id="c7203-591">Constructor injection behavior</span></span>

<span data-ttu-id="c7203-592">Služby je možné vyřešit pomocí dvou mechanismů:</span><span class="sxs-lookup"><span data-stu-id="c7203-592">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="c7203-593"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Povoluje vytvoření objektu bez registrace služby v kontejneru vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="c7203-593"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="c7203-594">`ActivatorUtilities` se používá s uživateli s přístupem k uživatelům, jako jsou například rutiny značek, řadiče MVC a pořadače modelů.</span><span class="sxs-lookup"><span data-stu-id="c7203-594">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="c7203-595">Konstruktory mohou přijímat argumenty, které nejsou poskytovány vkládáním závislostí, ale argumenty musí přiřadit výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="c7203-595">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="c7203-596">Když jsou služby vyřešeny `IServiceProvider` nebo `ActivatorUtilities` , [Injektáže konstruktoru](xref:mvc/controllers/dependency-injection#constructor-injection) vyžaduje *veřejný* konstruktor.</span><span class="sxs-lookup"><span data-stu-id="c7203-596">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="c7203-597">Když jsou služby vyřešeny nástrojem `ActivatorUtilities` , [Injektáže konstruktoru](xref:mvc/controllers/dependency-injection#constructor-injection) vyžaduje, aby existoval pouze jeden příslušný konstruktor.</span><span class="sxs-lookup"><span data-stu-id="c7203-597">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="c7203-598">Přetížení konstruktoru jsou podporovaná, ale může existovat jenom jedno přetížení, jehož argumenty můžou být splněné vkládáním závislostí.</span><span class="sxs-lookup"><span data-stu-id="c7203-598">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="c7203-599">Entity Framework kontexty</span><span class="sxs-lookup"><span data-stu-id="c7203-599">Entity Framework contexts</span></span>

<span data-ttu-id="c7203-600">Entity Framework kontexty se obvykle přidávají do kontejneru služby pomocí [rozsahu životnosti](#service-lifetimes) , protože operace databáze webové aplikace jsou obvykle vymezeny na požadavky klienta.</span><span class="sxs-lookup"><span data-stu-id="c7203-600">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="c7203-601">Výchozí doba života je vymezena v případě, že při registraci kontextu databáze není zadána doba života při [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) přetížení.</span><span class="sxs-lookup"><span data-stu-id="c7203-601">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="c7203-602">Služby pro danou dobu života by neměly používat kontext databáze s kratší dobou životnosti než služba.</span><span class="sxs-lookup"><span data-stu-id="c7203-602">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="c7203-603">Možnosti života a registrace</span><span class="sxs-lookup"><span data-stu-id="c7203-603">Lifetime and registration options</span></span>

<span data-ttu-id="c7203-604">Chcete-li předvést rozdíl mezi možnostmi životního cyklu a registrací, zvažte následující rozhraní, která představují úlohy jako operaci s jedinečným identifikátorem `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="c7203-604">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="c7203-605">V závislosti na tom, jak je životnost provozní služby nakonfigurována pro následující rozhraní, kontejner poskytuje buď stejnou nebo jinou instanci služby, když je vyžádána třídou:</span><span class="sxs-lookup"><span data-stu-id="c7203-605">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="c7203-606">Rozhraní jsou implementována ve `Operation` třídě.</span><span class="sxs-lookup"><span data-stu-id="c7203-606">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="c7203-607">`Operation`Konstruktor generuje identifikátor GUID, pokud není dodán:</span><span class="sxs-lookup"><span data-stu-id="c7203-607">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="c7203-608">`OperationService`Je zaregistrován, který závisí na každém z ostatních `Operation` typů.</span><span class="sxs-lookup"><span data-stu-id="c7203-608">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="c7203-609">Když `OperationService` je požadavek proveden prostřednictvím injektáže závislosti, obdrží buď novou instanci každé služby, nebo existující instanci na základě životnosti závislé služby.</span><span class="sxs-lookup"><span data-stu-id="c7203-609">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="c7203-610">Když se v případě vyžádání z kontejneru vytvoří přechodné služby, `OperationId` liší se od `IOperationTransient` služby `OperationId` `OperationService` .</span><span class="sxs-lookup"><span data-stu-id="c7203-610">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="c7203-611">`OperationService` přijme novou instanci `IOperationTransient` třídy.</span><span class="sxs-lookup"><span data-stu-id="c7203-611">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="c7203-612">Nová instance vrací jinou instanci `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="c7203-612">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="c7203-613">Když se vytvoří vymezené služby pro jednotlivé požadavky klienta, `OperationId` `IOperationScoped` služba je stejná jako `OperationService` v rámci žádosti klienta.</span><span class="sxs-lookup"><span data-stu-id="c7203-613">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="c7203-614">V rámci požadavků klientů obě služby sdílejí jinou `OperationId` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="c7203-614">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="c7203-615">Pokud se služby instance singleton a singleton vytvoří jednou a použije se pro všechny požadavky klientů a všechny služby, `OperationId` je v rámci všech žádostí o službu konstantní.</span><span class="sxs-lookup"><span data-stu-id="c7203-615">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="c7203-616">V `Startup.ConfigureServices` je každý typ přidán do kontejneru podle jeho pojmenované životnosti:</span><span class="sxs-lookup"><span data-stu-id="c7203-616">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="c7203-617">`IOperationSingletonInstance`Služba používá konkrétní instanci se známým ID `Guid.Empty` .</span><span class="sxs-lookup"><span data-stu-id="c7203-617">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="c7203-618">Je jasné, že pokud se tento typ používá (jeho identifikátor GUID je všechny nuly).</span><span class="sxs-lookup"><span data-stu-id="c7203-618">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="c7203-619">Ukázková aplikace ukazuje dobu života objektu v rámci a mezi jednotlivými požadavky.</span><span class="sxs-lookup"><span data-stu-id="c7203-619">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="c7203-620">Ukázková aplikace `IndexModel` požaduje každý druh `IOperation` typu a `OperationService` .</span><span class="sxs-lookup"><span data-stu-id="c7203-620">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="c7203-621">Stránka potom zobrazí všechny hodnoty třídy modelu stránky a `OperationId` hodnoty služby prostřednictvím přiřazení vlastností:</span><span class="sxs-lookup"><span data-stu-id="c7203-621">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="c7203-622">Výsledkem dvou následujících výstupů jsou výsledky dvou požadavků:</span><span class="sxs-lookup"><span data-stu-id="c7203-622">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="c7203-623">**První požadavek:**</span><span class="sxs-lookup"><span data-stu-id="c7203-623">**First request:**</span></span>

<span data-ttu-id="c7203-624">Operace kontroleru:</span><span class="sxs-lookup"><span data-stu-id="c7203-624">Controller operations:</span></span>

<span data-ttu-id="c7203-625">Přechodný: d233e165-f417-469b-A866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="c7203-625">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="c7203-626">Vymezené: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="c7203-626">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="c7203-627">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="c7203-627">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="c7203-628">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="c7203-628">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="c7203-629">`OperationService` Operations</span><span class="sxs-lookup"><span data-stu-id="c7203-629">`OperationService` operations:</span></span>

<span data-ttu-id="c7203-630">Přechodný: c6b049eb-1318-4E31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="c7203-630">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="c7203-631">Vymezené: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="c7203-631">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="c7203-632">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="c7203-632">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="c7203-633">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="c7203-633">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="c7203-634">**Druhá žádost:**</span><span class="sxs-lookup"><span data-stu-id="c7203-634">**Second request:**</span></span>

<span data-ttu-id="c7203-635">Operace kontroleru:</span><span class="sxs-lookup"><span data-stu-id="c7203-635">Controller operations:</span></span>

<span data-ttu-id="c7203-636">Přechodný: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="c7203-636">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="c7203-637">Vymezené: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="c7203-637">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="c7203-638">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="c7203-638">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="c7203-639">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="c7203-639">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="c7203-640">`OperationService` Operations</span><span class="sxs-lookup"><span data-stu-id="c7203-640">`OperationService` operations:</span></span>

<span data-ttu-id="c7203-641">Přechodný: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="c7203-641">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="c7203-642">Vymezené: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="c7203-642">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="c7203-643">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="c7203-643">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="c7203-644">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="c7203-644">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="c7203-645">Pozor, které `OperationId` hodnoty se liší v rámci požadavku a mezi požadavky:</span><span class="sxs-lookup"><span data-stu-id="c7203-645">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="c7203-646">*Přechodné* objekty jsou vždy odlišné.</span><span class="sxs-lookup"><span data-stu-id="c7203-646">*Transient* objects are always different.</span></span> <span data-ttu-id="c7203-647">Přechodná `OperationId` hodnota pro první i druhý požadavek klienta se liší pro obě `OperationService` operace i mezi požadavky klientů.</span><span class="sxs-lookup"><span data-stu-id="c7203-647">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="c7203-648">Každé žádosti o službu a žádosti klienta se poskytne nová instance.</span><span class="sxs-lookup"><span data-stu-id="c7203-648">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="c7203-649">*Vymezené* objekty jsou v požadavku klienta stejné, ale v rámci požadavků klientů se liší.</span><span class="sxs-lookup"><span data-stu-id="c7203-649">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="c7203-650">Objekty *singleton* jsou stejné pro každý objekt a každý požadavek bez ohledu na to, zda `Operation` je instance uvedena v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="c7203-650">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="c7203-651">Volat služby z Main</span><span class="sxs-lookup"><span data-stu-id="c7203-651">Call services from main</span></span>

<span data-ttu-id="c7203-652">Vytvořte <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> pomocí [IServiceScopeFactory. CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) pro řešení oboru služby v rámci rozsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="c7203-652">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="c7203-653">Tento přístup je užitečný pro přístup k oboruované službě při spuštění za účelem spouštění úloh inicializace.</span><span class="sxs-lookup"><span data-stu-id="c7203-653">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="c7203-654">Následující příklad ukazuje, jak získat kontext pro `MyScopedService` `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="c7203-654">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="c7203-655">Ověřování oboru</span><span class="sxs-lookup"><span data-stu-id="c7203-655">Scope validation</span></span>

<span data-ttu-id="c7203-656">Když je aplikace spuštěná ve vývojovém prostředí, provede výchozí poskytovatel služeb kontroly, aby ověřil, že:</span><span class="sxs-lookup"><span data-stu-id="c7203-656">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="c7203-657">Oborové služby nejsou přímo nebo nepřímo vyřešeny od poskytovatele kořenové služby.</span><span class="sxs-lookup"><span data-stu-id="c7203-657">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="c7203-658">Oborové služby nejsou přímo nebo nepřímo vložené do singleton.</span><span class="sxs-lookup"><span data-stu-id="c7203-658">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="c7203-659">Poskytovatel kořenové služby se vytvoří, když <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> se zavolá.</span><span class="sxs-lookup"><span data-stu-id="c7203-659">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="c7203-660">Doba života poskytovatele kořenové služby odpovídá době životnosti aplikace nebo serveru, když se poskytovatel spustí s aplikací a je uvolněný při ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="c7203-660">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="c7203-661">Oborové služby jsou uvolněny kontejnerem, který je vytvořil.</span><span class="sxs-lookup"><span data-stu-id="c7203-661">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="c7203-662">Pokud je v kořenovém kontejneru vytvořena vymezená služba, doba života služby je efektivně povýšena na typ singleton, protože je uvolněna pouze kořenovým kontejnerem při vypnutí aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="c7203-662">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="c7203-663">Při ověřování oborů služeb se tyto situace zachytí `BuildServiceProvider` .</span><span class="sxs-lookup"><span data-stu-id="c7203-663">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="c7203-664">Další informace naleznete v tématu <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="c7203-664">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>   

## <a name="request-services"></a><span data-ttu-id="c7203-665">Žádosti o služby</span><span class="sxs-lookup"><span data-stu-id="c7203-665">Request Services</span></span>

<span data-ttu-id="c7203-666">Služby, které jsou k dispozici v rámci žádosti o ASP.NET Core, `HttpContext` jsou zpřístupněny prostřednictvím kolekce [HttpContext. RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) .</span><span class="sxs-lookup"><span data-stu-id="c7203-666">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="c7203-667">Žádosti o služby reprezentují služby nakonfigurované a požadované v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="c7203-667">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="c7203-668">Když objekty určují závislosti, jsou splněny typy nalezenými v `RequestServices` , ne `ApplicationServices` .</span><span class="sxs-lookup"><span data-stu-id="c7203-668">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="c7203-669">Obecně platí, že aplikace by tyto vlastnosti neměla používat přímo.</span><span class="sxs-lookup"><span data-stu-id="c7203-669">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="c7203-670">Místo toho požádejte o typy, které třídy vyžadují prostřednictvím konstruktorů třídy, a umožněte rozhraní vložit závislosti.</span><span class="sxs-lookup"><span data-stu-id="c7203-670">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="c7203-671">To poskytuje třídy, které jsou snáze testovány.</span><span class="sxs-lookup"><span data-stu-id="c7203-671">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="c7203-672">Preferovat požadavky na závislosti jako parametry konstruktoru pro přístup ke `RequestServices` kolekci.</span><span class="sxs-lookup"><span data-stu-id="c7203-672">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="c7203-673">Navrhnout služby pro vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="c7203-673">Design services for dependency injection</span></span>

<span data-ttu-id="c7203-674">Osvědčené postupy:</span><span class="sxs-lookup"><span data-stu-id="c7203-674">Best practices are to:</span></span>

* <span data-ttu-id="c7203-675">Navrhněte služby pro použití injektáže závislosti k získání jejich závislostí.</span><span class="sxs-lookup"><span data-stu-id="c7203-675">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="c7203-676">Vyhněte se stavovým, statickým třídám a členům.</span><span class="sxs-lookup"><span data-stu-id="c7203-676">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="c7203-677">Navrhněte aplikace, aby místo toho používaly služby s jedním prvkem, což se vyhne vytváření globálního stavu.</span><span class="sxs-lookup"><span data-stu-id="c7203-677">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="c7203-678">Vyhněte se přímému vytváření instancí závislých tříd v rámci služeb.</span><span class="sxs-lookup"><span data-stu-id="c7203-678">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="c7203-679">Přímá instance Couples kód na konkrétní implementaci.</span><span class="sxs-lookup"><span data-stu-id="c7203-679">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="c7203-680">Vytvářejte třídy aplikací malými, dobře faktoringně a snadno testované.</span><span class="sxs-lookup"><span data-stu-id="c7203-680">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="c7203-681">Pokud se zdá, že třída má příliš mnoho vložených závislostí, obvykle se jedná o znaménko, že třída má příliš mnoho zodpovědnosti a je v rozporu s [principem jediné zodpovědnosti (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="c7203-681">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="c7203-682">Pokuste se Refaktorovat třídu přesunutím některých odpovědností do nové třídy.</span><span class="sxs-lookup"><span data-stu-id="c7203-682">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="c7203-683">Mějte na paměti, že Razor stránky tříd modelu stránky a třídy KONTROLERU MVC by se měly zaměřit na uživatelské rozhraní.</span><span class="sxs-lookup"><span data-stu-id="c7203-683">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="c7203-684">Obchodní pravidla a podrobnosti implementace přístupu k datům by měly být uchovávány ve třídách, které jsou vhodné pro tyto [samostatné obavy](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="c7203-684">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="c7203-685">Vyřazení služeb</span><span class="sxs-lookup"><span data-stu-id="c7203-685">Disposal of services</span></span>

<span data-ttu-id="c7203-686">Kontejner volá <xref:System.IDisposable.Dispose*> typy, které <xref:System.IDisposable> vytvoří.</span><span class="sxs-lookup"><span data-stu-id="c7203-686">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="c7203-687">Pokud je instance přidána do kontejneru pomocí uživatelského kódu, nebude uvolněna automaticky.</span><span class="sxs-lookup"><span data-stu-id="c7203-687">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="c7203-688">V následujícím příkladu jsou služby vytvořeny pomocí kontejneru služby a automaticky odstraněny:</span><span class="sxs-lookup"><span data-stu-id="c7203-688">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="c7203-689">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="c7203-689">In the following example:</span></span>

* <span data-ttu-id="c7203-690">Instance služby nejsou vytvořeny kontejnerem služby.</span><span class="sxs-lookup"><span data-stu-id="c7203-690">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="c7203-691">Zamýšlené životnosti služeb nejsou rozhraním známy.</span><span class="sxs-lookup"><span data-stu-id="c7203-691">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="c7203-692">Rozhraní neodstraní služby automaticky.</span><span class="sxs-lookup"><span data-stu-id="c7203-692">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="c7203-693">Pokud se služby explicitně neodstraní v kódu pro vývojáře, zůstanou zachovány, dokud se aplikace neukončí.</span><span class="sxs-lookup"><span data-stu-id="c7203-693">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="c7203-694">Doprovodné materiály k rozhraní IDisposable pro přechodné a sdílené instance</span><span class="sxs-lookup"><span data-stu-id="c7203-694">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="c7203-695">Přechodná, omezená doba života</span><span class="sxs-lookup"><span data-stu-id="c7203-695">Transient, limited lifetime</span></span>

<span data-ttu-id="c7203-696">**Scénář**</span><span class="sxs-lookup"><span data-stu-id="c7203-696">**Scenario**</span></span>

<span data-ttu-id="c7203-697">Aplikace vyžaduje <xref:System.IDisposable> instanci s přechodným životností pro některý z následujících scénářů:</span><span class="sxs-lookup"><span data-stu-id="c7203-697">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="c7203-698">Instance je vyřešena v kořenovém oboru.</span><span class="sxs-lookup"><span data-stu-id="c7203-698">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="c7203-699">Instance by měla být uvolněna před ukončením oboru.</span><span class="sxs-lookup"><span data-stu-id="c7203-699">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="c7203-700">**Řešení**</span><span class="sxs-lookup"><span data-stu-id="c7203-700">**Solution**</span></span>

<span data-ttu-id="c7203-701">Pomocí modelu továrny vytvořte instanci mimo nadřazený obor.</span><span class="sxs-lookup"><span data-stu-id="c7203-701">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="c7203-702">V takové situaci by aplikace měla obvykle `Create` metodu, která volá přímo konstruktor finálního typu.</span><span class="sxs-lookup"><span data-stu-id="c7203-702">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="c7203-703">Pokud má konečný typ jiné závislosti, továrna může:</span><span class="sxs-lookup"><span data-stu-id="c7203-703">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="c7203-704">Přijmout <xref:System.IServiceProvider> ve svém konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="c7203-704">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="c7203-705">Slouží <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> k vytvoření instance instance mimo kontejner, při použití kontejneru pro jeho závislosti.</span><span class="sxs-lookup"><span data-stu-id="c7203-705">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="c7203-706">Sdílená instance, omezená doba života</span><span class="sxs-lookup"><span data-stu-id="c7203-706">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="c7203-707">**Scénář**</span><span class="sxs-lookup"><span data-stu-id="c7203-707">**Scenario**</span></span>

<span data-ttu-id="c7203-708">Aplikace vyžaduje sdílenou <xref:System.IDisposable> instanci napříč více službami, ale <xref:System.IDisposable> měla by mít omezené trvání.</span><span class="sxs-lookup"><span data-stu-id="c7203-708">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="c7203-709">**Řešení**</span><span class="sxs-lookup"><span data-stu-id="c7203-709">**Solution**</span></span>

<span data-ttu-id="c7203-710">Zaregistrujte instanci s vymezenou životností.</span><span class="sxs-lookup"><span data-stu-id="c7203-710">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="c7203-711">Použijte <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> k zahájení a vytvoření nového <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> .</span><span class="sxs-lookup"><span data-stu-id="c7203-711">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="c7203-712"><xref:System.IServiceProvider>Požadované služby získáte pomocí tohoto oboru.</span><span class="sxs-lookup"><span data-stu-id="c7203-712">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="c7203-713">Vyřadit rozsah, pokud by životnost měla končit.</span><span class="sxs-lookup"><span data-stu-id="c7203-713">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="c7203-714">Obecné pokyny</span><span class="sxs-lookup"><span data-stu-id="c7203-714">General Guidelines</span></span>

* <span data-ttu-id="c7203-715">Neregistrujte <xref:System.IDisposable> instance s přechodným oborem.</span><span class="sxs-lookup"><span data-stu-id="c7203-715">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="c7203-716">Místo toho použijte výrobní model.</span><span class="sxs-lookup"><span data-stu-id="c7203-716">Use the factory pattern instead.</span></span>
* <span data-ttu-id="c7203-717">V kořenovém oboru neřešte přechodné nebo vymezené <xref:System.IDisposable> instance.</span><span class="sxs-lookup"><span data-stu-id="c7203-717">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="c7203-718">Jedinou obecnou výjimkou je, když aplikace vytvoří nebo znovu vytvoří a odstraní <xref:System.IServiceProvider> , což není ideální vzor.</span><span class="sxs-lookup"><span data-stu-id="c7203-718">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="c7203-719">Příjem <xref:System.IDisposable> závislosti přes di nevyžaduje, aby se příjemce sám implementoval <xref:System.IDisposable> .</span><span class="sxs-lookup"><span data-stu-id="c7203-719">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="c7203-720">Příjemce <xref:System.IDisposable> závislosti by neměl volat <xref:System.IDisposable.Dispose%2A> tuto závislost.</span><span class="sxs-lookup"><span data-stu-id="c7203-720">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="c7203-721">Obory by měly sloužit k řízení životnosti služeb.</span><span class="sxs-lookup"><span data-stu-id="c7203-721">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="c7203-722">Obory nejsou hierarchické a mezi obory není žádné zvláštní připojení.</span><span class="sxs-lookup"><span data-stu-id="c7203-722">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="c7203-723">Výchozí nahrazení kontejneru služby</span><span class="sxs-lookup"><span data-stu-id="c7203-723">Default service container replacement</span></span>

<span data-ttu-id="c7203-724">Integrovaný kontejner služeb je navržený tak, aby sloužil potřebám architektury a většině spotřebitelských aplikací.</span><span class="sxs-lookup"><span data-stu-id="c7203-724">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="c7203-725">Pokud nepotřebujete určitou funkci, kterou integrovaný kontejner nepodporuje, doporučujeme použít vestavěný kontejner, například:</span><span class="sxs-lookup"><span data-stu-id="c7203-725">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="c7203-726">Vkládání vlastností</span><span class="sxs-lookup"><span data-stu-id="c7203-726">Property injection</span></span>
* <span data-ttu-id="c7203-727">Vkládání na základě názvu</span><span class="sxs-lookup"><span data-stu-id="c7203-727">Injection based on name</span></span>
* <span data-ttu-id="c7203-728">Podřízené kontejnery</span><span class="sxs-lookup"><span data-stu-id="c7203-728">Child containers</span></span>
* <span data-ttu-id="c7203-729">Vlastní Správa životního cyklu</span><span class="sxs-lookup"><span data-stu-id="c7203-729">Custom lifetime management</span></span>
* <span data-ttu-id="c7203-730">`Func<T>` Podpora opožděné inicializace</span><span class="sxs-lookup"><span data-stu-id="c7203-730">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="c7203-731">Registrace založená na konvencích</span><span class="sxs-lookup"><span data-stu-id="c7203-731">Convention-based registration</span></span>

<span data-ttu-id="c7203-732">U ASP.NET Corech aplikací lze použít následující kontejnery třetích stran:</span><span class="sxs-lookup"><span data-stu-id="c7203-732">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="c7203-733">Autofac</span><span class="sxs-lookup"><span data-stu-id="c7203-733">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="c7203-734">DryIoc</span><span class="sxs-lookup"><span data-stu-id="c7203-734">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="c7203-735">Integrit</span><span class="sxs-lookup"><span data-stu-id="c7203-735">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="c7203-736">LightInject</span><span class="sxs-lookup"><span data-stu-id="c7203-736">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="c7203-737">Lamar</span><span class="sxs-lookup"><span data-stu-id="c7203-737">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="c7203-738">Stashbox</span><span class="sxs-lookup"><span data-stu-id="c7203-738">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="c7203-739">Unity</span><span class="sxs-lookup"><span data-stu-id="c7203-739">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="c7203-740">Bezpečnost vlákna</span><span class="sxs-lookup"><span data-stu-id="c7203-740">Thread safety</span></span>

<span data-ttu-id="c7203-741">Vytvořte služby, které jsou v bezpečí pro přístup z více vláken.</span><span class="sxs-lookup"><span data-stu-id="c7203-741">Create thread-safe singleton services.</span></span> <span data-ttu-id="c7203-742">Pokud má služba typu Singleton závislost na přechodné službě, přechodná služba může také vyžadovat zabezpečení vlákna v závislosti na tom, jak je používá typ singleton.</span><span class="sxs-lookup"><span data-stu-id="c7203-742">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="c7203-743">Výrobní metoda samostatné služby, jako je například druhý argument pro [AddSingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), nemusí být bezpečná pro přístup z více vláken.</span><span class="sxs-lookup"><span data-stu-id="c7203-743">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="c7203-744">Podobně jako konstruktor typu ( `static` ), je zaručeno, že bude volán jednou vláknem.</span><span class="sxs-lookup"><span data-stu-id="c7203-744">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="c7203-745">Doporučení</span><span class="sxs-lookup"><span data-stu-id="c7203-745">Recommendations</span></span>

* <span data-ttu-id="c7203-746">`async/await``Task`řešení služeb a se nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="c7203-746">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="c7203-747">Jazyk C# nepodporuje asynchronní konstruktory; Proto je doporučeným vzorem použití asynchronních metod po synchronním překladu služby.</span><span class="sxs-lookup"><span data-stu-id="c7203-747">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="c7203-748">Neukládejte data a konfiguraci přímo do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="c7203-748">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="c7203-749">Například nákupní košík uživatele by neměl být obvykle přidán do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="c7203-749">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="c7203-750">Konfigurace by měla používat [vzor možností](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="c7203-750">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="c7203-751">Podobně nepoužívejte objekty "držitel dat", které existují pouze k povolení přístupu k jinému objektu.</span><span class="sxs-lookup"><span data-stu-id="c7203-751">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="c7203-752">Je lepší žádat o skutečnou položku přes DI.</span><span class="sxs-lookup"><span data-stu-id="c7203-752">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="c7203-753">Vyhněte se statickému přístupu ke službám.</span><span class="sxs-lookup"><span data-stu-id="c7203-753">Avoid static access to services.</span></span> <span data-ttu-id="c7203-754">Vyhněte se například statickému psaní [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) pro použití jinde.</span><span class="sxs-lookup"><span data-stu-id="c7203-754">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere.</span></span>

* <span data-ttu-id="c7203-755">Vyhněte se použití *vzoru lokátoru služby*, který kombinuje [inverzi strategií řízení](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .</span><span class="sxs-lookup"><span data-stu-id="c7203-755">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
  * <span data-ttu-id="c7203-756">Nevolejte <xref:System.IServiceProvider.GetService*> pro získání instance služby, když můžete místo toho použít di:</span><span class="sxs-lookup"><span data-stu-id="c7203-756">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="c7203-757">**Nesprávně:**</span><span class="sxs-lookup"><span data-stu-id="c7203-757">**Incorrect:**</span></span>

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
   
    <span data-ttu-id="c7203-758">**Správné**:</span><span class="sxs-lookup"><span data-stu-id="c7203-758">**Correct**:</span></span>

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

* <span data-ttu-id="c7203-759">Vyhněte se vkládání továrny, která řeší závislosti za běhu pomocí <xref:System.IServiceProvider.GetService*> .</span><span class="sxs-lookup"><span data-stu-id="c7203-759">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>
* <span data-ttu-id="c7203-760">Vyhněte se statickému přístupu k `HttpContext` (například [IHttpContextAccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="c7203-760">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="c7203-761">Podobně jako u všech sad doporučení se může stát, že se vyžaduje ignorování doporučení.</span><span class="sxs-lookup"><span data-stu-id="c7203-761">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="c7203-762">Výjimky jsou zřídka, většinou zvláštní případy v rámci samotného rozhraní.</span><span class="sxs-lookup"><span data-stu-id="c7203-762">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="c7203-763">DI je *alternativou* ke vzorům statických nebo globálních přístupů k objektům.</span><span class="sxs-lookup"><span data-stu-id="c7203-763">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="c7203-764">Je možné, že nebudete moci využít výhody DI, pokud je kombinujete se statickým přístupem k objektům.</span><span class="sxs-lookup"><span data-stu-id="c7203-764">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c7203-765">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="c7203-765">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="c7203-766">Čtyři způsoby, jak vyřadit IDisposable v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c7203-766">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="c7203-767">Zápis čistého kódu v ASP.NET Core pomocí injektáže závislosti (MSDN)</span><span class="sxs-lookup"><span data-stu-id="c7203-767">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="c7203-768">Explicitní závislosti – princip</span><span class="sxs-lookup"><span data-stu-id="c7203-768">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="c7203-769">Inverze řídicích kontejnerů a vzor injektáže závislosti (Martin Fowlera)</span><span class="sxs-lookup"><span data-stu-id="c7203-769">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="c7203-770">Jak zaregistrovat službu s více rozhraními v ASP.NET Core DI</span><span class="sxs-lookup"><span data-stu-id="c7203-770">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

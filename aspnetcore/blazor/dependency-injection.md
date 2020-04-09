---
title: ASP.NET Blazor základní vkládání závislostí
author: guardrex
description: Podívejte Blazor se, jak můžou aplikace vstřikovat služby do komponent.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/20/2020
no-loc:
- Blazor
- SignalR
uid: blazor/dependency-injection
ms.openlocfilehash: 4cdde9ee8c9fd9adf00894a067d32965b180e5ec
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658072"
---
# <a name="aspnet-core-blazor-dependency-injection"></a><span data-ttu-id="d646b-103">ASP.NET Vkládání závislostí Core Blazor</span><span class="sxs-lookup"><span data-stu-id="d646b-103">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="d646b-104">Podle [Rainer Stropek](https://www.timecockpit.com) a [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="d646b-104">By [Rainer Stropek](https://www.timecockpit.com) and [Mike Rousos](https://github.com/mjrousos)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="d646b-105">Blazor podporuje [vkládání závislostí (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="d646b-105">Blazor supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="d646b-106">Aplikace můžou používat integrované služby jejich vložením do součástí.</span><span class="sxs-lookup"><span data-stu-id="d646b-106">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="d646b-107">Aplikace mohou také definovat a zaregistrovat vlastní služby a zpřístupnit je v celé aplikaci prostřednictvím DI.</span><span class="sxs-lookup"><span data-stu-id="d646b-107">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

<span data-ttu-id="d646b-108">DI je technika pro přístup ke službám nakonfigurovaným v centrálním umístění.</span><span class="sxs-lookup"><span data-stu-id="d646b-108">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="d646b-109">To může být užitečné v aplikacích Blazor:</span><span class="sxs-lookup"><span data-stu-id="d646b-109">This can be useful in Blazor apps to:</span></span>

* <span data-ttu-id="d646b-110">Sdílet jednu instanci třídy služby napříč mnoha součástmi, označované jako služba *singleton.*</span><span class="sxs-lookup"><span data-stu-id="d646b-110">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="d646b-111">Odpojte součásti od konkrétních tříd služeb pomocí referenčních abstrakcí.</span><span class="sxs-lookup"><span data-stu-id="d646b-111">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="d646b-112">Zvažte například `IDataAccess` rozhraní pro přístup k datům v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d646b-112">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="d646b-113">Rozhraní je implementováno `DataAccess` konkrétní třídou a registrováno jako služba v kontejneru služeb aplikace.</span><span class="sxs-lookup"><span data-stu-id="d646b-113">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="d646b-114">Když komponenta používá DI `IDataAccess` pro příjem implementace, komponenta není spojena s konkrétní typ.</span><span class="sxs-lookup"><span data-stu-id="d646b-114">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="d646b-115">Implementace může být vyměněna, možná pro falešnou implementaci v testování částí.</span><span class="sxs-lookup"><span data-stu-id="d646b-115">The implementation can be swapped, perhaps for a mock implementation in unit tests.</span></span>

## <a name="default-services"></a><span data-ttu-id="d646b-116">Výchozí služby</span><span class="sxs-lookup"><span data-stu-id="d646b-116">Default services</span></span>

<span data-ttu-id="d646b-117">Výchozí služby se automaticky přidají do kolekce služeb aplikace.</span><span class="sxs-lookup"><span data-stu-id="d646b-117">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="d646b-118">Služba</span><span class="sxs-lookup"><span data-stu-id="d646b-118">Service</span></span> | <span data-ttu-id="d646b-119">Doba platnosti</span><span class="sxs-lookup"><span data-stu-id="d646b-119">Lifetime</span></span> | <span data-ttu-id="d646b-120">Popis</span><span class="sxs-lookup"><span data-stu-id="d646b-120">Description</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="d646b-121">Singleton</span><span class="sxs-lookup"><span data-stu-id="d646b-121">Singleton</span></span> | <span data-ttu-id="d646b-122">Poskytuje metody pro odesílání požadavků HTTP a příjem odpovědí HTTP z prostředku identifikovaného identifikátorem URI.</span><span class="sxs-lookup"><span data-stu-id="d646b-122">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span><br><br><span data-ttu-id="d646b-123">Instance `HttpClient` aplikace Blazor WebAssembly používá prohlížeč pro zpracování http provozu na pozadí.</span><span class="sxs-lookup"><span data-stu-id="d646b-123">The instance of `HttpClient` in a Blazor WebAssembly app uses the browser for handling the HTTP traffic in the background.</span></span><br><br><span data-ttu-id="d646b-124">Aplikace Blazor Server ve `HttpClient` výchozím nastavení neobsahují nakonfigurovanou službu.</span><span class="sxs-lookup"><span data-stu-id="d646b-124">Blazor Server apps don't include an `HttpClient` configured as a service by default.</span></span> <span data-ttu-id="d646b-125">`HttpClient` Poskytněte aplikaci Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="d646b-125">Provide an `HttpClient` to a Blazor Server app.</span></span><br><br><span data-ttu-id="d646b-126">Další informace naleznete v tématu <xref:blazor/call-web-api>.</span><span class="sxs-lookup"><span data-stu-id="d646b-126">For more information, see <xref:blazor/call-web-api>.</span></span> |
| `IJSRuntime` | <span data-ttu-id="d646b-127">Singleton (Blazor WebAssembly)</span><span class="sxs-lookup"><span data-stu-id="d646b-127">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="d646b-128">S rozsahem (Blazor Server)</span><span class="sxs-lookup"><span data-stu-id="d646b-128">Scoped (Blazor Server)</span></span> | <span data-ttu-id="d646b-129">Představuje instanci runtime JavaScript, kam jsou odesílána volání JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="d646b-129">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="d646b-130">Další informace naleznete v tématu <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="d646b-130">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span> |
| `NavigationManager` | <span data-ttu-id="d646b-131">Singleton (Blazor WebAssembly)</span><span class="sxs-lookup"><span data-stu-id="d646b-131">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="d646b-132">S rozsahem (Blazor Server)</span><span class="sxs-lookup"><span data-stu-id="d646b-132">Scoped (Blazor Server)</span></span> | <span data-ttu-id="d646b-133">Obsahuje pomocné servery pro práci s identifikátory URI a stav navigace.</span><span class="sxs-lookup"><span data-stu-id="d646b-133">Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="d646b-134">Další informace naleznete v tématu [URI a pomocníci stavu navigace](xref:blazor/routing#uri-and-navigation-state-helpers).</span><span class="sxs-lookup"><span data-stu-id="d646b-134">For more information, see [URI and navigation state helpers](xref:blazor/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="d646b-135">Vlastní poskytovatel služeb automaticky neposkytuje výchozí služby uvedené v tabulce.</span><span class="sxs-lookup"><span data-stu-id="d646b-135">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="d646b-136">Pokud používáte vlastního poskytovatele služeb a požadujete některou ze služeb uvedených v tabulce, přidejte požadované služby k novému poskytovateli služeb.</span><span class="sxs-lookup"><span data-stu-id="d646b-136">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="d646b-137">Přidání služeb do aplikace</span><span class="sxs-lookup"><span data-stu-id="d646b-137">Add services to an app</span></span>

### <a name="blazor-webassembly"></a><span data-ttu-id="d646b-138">Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="d646b-138">Blazor WebAssembly</span></span>

<span data-ttu-id="d646b-139">Konfigurace služeb pro kolekci služeb `Main` aplikace v metodě *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="d646b-139">Configure services for the app's service collection in the `Main` method of *Program.cs*.</span></span> <span data-ttu-id="d646b-140">V následujícím příkladu `MyDependency` je implementace `IMyDependency`registrována pro :</span><span class="sxs-lookup"><span data-stu-id="d646b-140">In the following example, the `MyDependency` implementation is registered for `IMyDependency`:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<IMyDependency, MyDependency>();
        builder.RootComponents.Add<App>("app");

        await builder.Build().RunAsync();
    }
}
```

<span data-ttu-id="d646b-141">Po sestavování hostitele lze přistupovat ke službám z kořenového oboru DI před vykreslením všech součástí.</span><span class="sxs-lookup"><span data-stu-id="d646b-141">Once the host is built, services can be accessed from the root DI scope before any components are rendered.</span></span> <span data-ttu-id="d646b-142">To může být užitečné pro spuštění logiky inicializace před vykreslením obsahu:</span><span class="sxs-lookup"><span data-stu-id="d646b-142">This can be useful for running initialization logic before rendering content:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<WeatherService>();
        builder.RootComponents.Add<App>("app");

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync();

        await host.RunAsync();
    }
}
```

<span data-ttu-id="d646b-143">Hostitel také poskytuje instanci centrální konfigurace pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d646b-143">The host also provides a central configuration instance for the app.</span></span> <span data-ttu-id="d646b-144">V návaznosti na předchozí příklad je adresa URL meteorologické služby předána z výchozího zdroje `InitializeWeatherAsync`konfigurace (například *appsettings.json*) do :</span><span class="sxs-lookup"><span data-stu-id="d646b-144">Building on the preceding example, the weather service's URL is passed from a default configuration source (for example, *appsettings.json*) to `InitializeWeatherAsync`:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<WeatherService>();
        builder.RootComponents.Add<App>("app");

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync(
            host.Configuration["WeatherServiceUrl"]);

        await host.RunAsync();
    }
}
```

### <a name="blazor-server"></a><span data-ttu-id="d646b-145">Blazor Server</span><span class="sxs-lookup"><span data-stu-id="d646b-145">Blazor Server</span></span>

<span data-ttu-id="d646b-146">Po vytvoření nové aplikace, `Startup.ConfigureServices` zkontrolujte metodu:</span><span class="sxs-lookup"><span data-stu-id="d646b-146">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

<span data-ttu-id="d646b-147">Metoda `ConfigureServices` je předána <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, což je seznam objektů<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>popisovače služby ( ).</span><span class="sxs-lookup"><span data-stu-id="d646b-147">The `ConfigureServices` method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="d646b-148">Služby jsou přidány poskytováním popisovačů služby do kolekce služeb.</span><span class="sxs-lookup"><span data-stu-id="d646b-148">Services are added by providing service descriptors to the service collection.</span></span> <span data-ttu-id="d646b-149">Následující příklad ukazuje koncept s `IDataAccess` rozhraním a `DataAccess`jeho konkrétní implementaci :</span><span class="sxs-lookup"><span data-stu-id="d646b-149">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a><span data-ttu-id="d646b-150">Životnost</span><span class="sxs-lookup"><span data-stu-id="d646b-150">Service lifetime</span></span>

<span data-ttu-id="d646b-151">Služby lze konfigurovat s životností uvedenou v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="d646b-151">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="d646b-152">Doba platnosti</span><span class="sxs-lookup"><span data-stu-id="d646b-152">Lifetime</span></span> | <span data-ttu-id="d646b-153">Popis</span><span class="sxs-lookup"><span data-stu-id="d646b-153">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped*> | Blazor<span data-ttu-id="d646b-154">Aplikace WebAssembly aktuálně nemají koncept oborů DI.</span><span class="sxs-lookup"><span data-stu-id="d646b-154"> WebAssembly apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="d646b-155">`Scoped`-registrované služby `Singleton` se chovají jako služby.</span><span class="sxs-lookup"><span data-stu-id="d646b-155">`Scoped`-registered services behave like `Singleton` services.</span></span> <span data-ttu-id="d646b-156">Model hostování Blazor serveru však `Scoped` podporuje životnost.</span><span class="sxs-lookup"><span data-stu-id="d646b-156">However, the Blazor Server hosting model supports the `Scoped` lifetime.</span></span> <span data-ttu-id="d646b-157">V Blazor aplikacích Server je registrace služby s vymezeným oborem pro *připojení*.</span><span class="sxs-lookup"><span data-stu-id="d646b-157">In Blazor Server apps, a scoped service registration is scoped to the *connection*.</span></span> <span data-ttu-id="d646b-158">Z tohoto důvodu použití služeb s vymezeným oborem je upřednostňováno pro služby, které by měly být vymezeny na aktuálního uživatele, i v případě, že aktuální záměr je spustit na straně klienta v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="d646b-158">For this reason, using scoped services is preferred for services that should be scoped to the current user, even if the current intent is to run client-side in the browser.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton*> | <span data-ttu-id="d646b-159">DI vytvoří *jednu instanci* služby.</span><span class="sxs-lookup"><span data-stu-id="d646b-159">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="d646b-160">Všechny součásti, `Singleton` které vyžadují službu, obdrží instanci stejné služby.</span><span class="sxs-lookup"><span data-stu-id="d646b-160">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient*> | <span data-ttu-id="d646b-161">Vždy, když komponenta získá `Transient` instanci služby z kontejneru služby, obdrží *novou instanci* služby.</span><span class="sxs-lookup"><span data-stu-id="d646b-161">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="d646b-162">DI systém je založen na di systému v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d646b-162">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="d646b-163">Další informace naleznete v tématu <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="d646b-163">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="d646b-164">Vyžádání služby v součásti</span><span class="sxs-lookup"><span data-stu-id="d646b-164">Request a service in a component</span></span>

<span data-ttu-id="d646b-165">Po služby jsou přidány do kolekce služeb, vstřikovat služby do komponent pomocí [ \@směrnice inject](xref:mvc/views/razor#inject) Razor.</span><span class="sxs-lookup"><span data-stu-id="d646b-165">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) Razor directive.</span></span> <span data-ttu-id="d646b-166">`@inject`má dva parametry:</span><span class="sxs-lookup"><span data-stu-id="d646b-166">`@inject` has two parameters:</span></span>

* <span data-ttu-id="d646b-167">Zadejte &ndash; Typ služby, kterou chcete vložit.</span><span class="sxs-lookup"><span data-stu-id="d646b-167">Type &ndash; The type of the service to inject.</span></span>
* <span data-ttu-id="d646b-168">Vlastnost &ndash; Název vlastnosti přijímající vstřikované služby aplikace.</span><span class="sxs-lookup"><span data-stu-id="d646b-168">Property &ndash; The name of the property receiving the injected app service.</span></span> <span data-ttu-id="d646b-169">Vlastnost nevyžaduje ruční vytvoření.</span><span class="sxs-lookup"><span data-stu-id="d646b-169">The property doesn't require manual creation.</span></span> <span data-ttu-id="d646b-170">Kompilátor vytvoří vlastnost.</span><span class="sxs-lookup"><span data-stu-id="d646b-170">The compiler creates the property.</span></span>

<span data-ttu-id="d646b-171">Další informace naleznete v tématu <xref:mvc/views/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="d646b-171">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="d646b-172">Použijte `@inject` více příkazů k vložení různých služeb.</span><span class="sxs-lookup"><span data-stu-id="d646b-172">Use multiple `@inject` statements to inject different services.</span></span>

<span data-ttu-id="d646b-173">Následující příklad ukazuje, `@inject`jak používat .</span><span class="sxs-lookup"><span data-stu-id="d646b-173">The following example shows how to use `@inject`.</span></span> <span data-ttu-id="d646b-174">Implementace služby `Services.IDataAccess` je vložena do vlastnosti `DataRepository`komponenty .</span><span class="sxs-lookup"><span data-stu-id="d646b-174">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="d646b-175">Všimněte si, jak `IDataAccess` kód používá pouze abstrakce:</span><span class="sxs-lookup"><span data-stu-id="d646b-175">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,23)]

<span data-ttu-id="d646b-176">Interně generované vlastnosti`DataRepository`( ) `InjectAttribute` používá atribut.</span><span class="sxs-lookup"><span data-stu-id="d646b-176">Internally, the generated property (`DataRepository`) uses the `InjectAttribute` attribute.</span></span> <span data-ttu-id="d646b-177">Obvykle tento atribut není použit přímo.</span><span class="sxs-lookup"><span data-stu-id="d646b-177">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="d646b-178">Pokud je pro komponenty požadována základní třída a pro základní třídu `InjectAttribute`jsou vyžadovány také vstřikované vlastnosti, ručně přidejte :</span><span class="sxs-lookup"><span data-stu-id="d646b-178">If a base class is required for components and injected properties are also required for the base class, manually add the `InjectAttribute`:</span></span>

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

<span data-ttu-id="d646b-179">V komponentách odvozených ze základní `@inject` třídy není směrnice vyžadována.</span><span class="sxs-lookup"><span data-stu-id="d646b-179">In components derived from the base class, the `@inject` directive isn't required.</span></span> <span data-ttu-id="d646b-180">Základní `InjectAttribute` třída je dostatečná:</span><span class="sxs-lookup"><span data-stu-id="d646b-180">The `InjectAttribute` of the base class is sufficient:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="d646b-181">Použití DI ve službách</span><span class="sxs-lookup"><span data-stu-id="d646b-181">Use DI in services</span></span>

<span data-ttu-id="d646b-182">Komplexní služby mohou vyžadovat další služby.</span><span class="sxs-lookup"><span data-stu-id="d646b-182">Complex services might require additional services.</span></span> <span data-ttu-id="d646b-183">V předchozím příkladu `DataAccess` může `HttpClient` vyžadovat výchozí službu.</span><span class="sxs-lookup"><span data-stu-id="d646b-183">In the prior example, `DataAccess` might require the `HttpClient` default service.</span></span> <span data-ttu-id="d646b-184">`@inject`(nebo `InjectAttribute`) není k dispozici pro použití ve službách.</span><span class="sxs-lookup"><span data-stu-id="d646b-184">`@inject` (or the `InjectAttribute`) isn't available for use in services.</span></span> <span data-ttu-id="d646b-185">Místo toho musí být *použitvstřižek konstruktoru.*</span><span class="sxs-lookup"><span data-stu-id="d646b-185">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="d646b-186">Požadované služby jsou přidány přidáním parametrů do konstruktoru služby.</span><span class="sxs-lookup"><span data-stu-id="d646b-186">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="d646b-187">Když DI vytvoří službu, rozpozná služby, které vyžaduje v konstruktoru a poskytuje je odpovídajícím způsobem.</span><span class="sxs-lookup"><span data-stu-id="d646b-187">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span>

```csharp
public class DataAccess : IDataAccess
{
    // The constructor receives an HttpClient via dependency
    // injection. HttpClient is a default service.
    public DataAccess(HttpClient client)
    {
        ...
    }
}
```

<span data-ttu-id="d646b-188">Předpoklady pro vstřikování konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="d646b-188">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="d646b-189">Jeden konstruktor musí existovat, jehož argumenty mohou být splněny DI.</span><span class="sxs-lookup"><span data-stu-id="d646b-189">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="d646b-190">Další parametry, na které se nevztahuje DI, jsou povoleny, pokud určují výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="d646b-190">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="d646b-191">Příslušný konstruktor musí být *veřejný*.</span><span class="sxs-lookup"><span data-stu-id="d646b-191">The applicable constructor must be *public*.</span></span>
* <span data-ttu-id="d646b-192">Jeden příslušný konstruktor musí existovat.</span><span class="sxs-lookup"><span data-stu-id="d646b-192">One applicable constructor must exist.</span></span> <span data-ttu-id="d646b-193">V případě nejednoznačnosti DI vyvolá výjimku.</span><span class="sxs-lookup"><span data-stu-id="d646b-193">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="d646b-194">Třídy základních součástí nástroje pro správu oboru DI</span><span class="sxs-lookup"><span data-stu-id="d646b-194">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="d646b-195">V ASP.NET základních aplikací jsou služby s vymezeným oborem obvykle vymezeny podle aktuálního požadavku.</span><span class="sxs-lookup"><span data-stu-id="d646b-195">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="d646b-196">Po dokončení požadavku jsou všechny oborové nebo přechodné služby vyřazeny systémem DI.</span><span class="sxs-lookup"><span data-stu-id="d646b-196">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="d646b-197">V Blazor serverových aplikacích trvá obor požadavku po dobu trvání připojení klienta, což může mít za následek přechodné služby a služby s rozsahem, které žijí mnohem déle, než bylo očekáváno.</span><span class="sxs-lookup"><span data-stu-id="d646b-197">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span> <span data-ttu-id="d646b-198">V Blazor aplikacích WebAssembly jsou služby registrované s rozsahem životnosti považovány za singletons, takže žijí déle než oborové služby v typických ASP.NET základních aplikacích.</span><span class="sxs-lookup"><span data-stu-id="d646b-198">In Blazor WebAssembly apps, services registered with a scoped lifetime are treated as singletons, so they live longer than scoped services in typical ASP.NET Core apps.</span></span>

<span data-ttu-id="d646b-199">Přístup, který omezuje životnost Blazor služby v `OwningComponentBase` aplikacích je použití typu.</span><span class="sxs-lookup"><span data-stu-id="d646b-199">An approach that limits a service lifetime in Blazor apps is use of the `OwningComponentBase` type.</span></span> <span data-ttu-id="d646b-200">`OwningComponentBase`je abstraktní typ odvozený od `ComponentBase` který vytváří obor DI odpovídající životnosti komponenty.</span><span class="sxs-lookup"><span data-stu-id="d646b-200">`OwningComponentBase` is an abstract type derived from `ComponentBase` that creates a DI scope corresponding to the lifetime of the component.</span></span> <span data-ttu-id="d646b-201">Pomocí tohoto oboru je možné použít služby DI s rozsahem životnosta a mít je žít tak dlouho, jak součást.</span><span class="sxs-lookup"><span data-stu-id="d646b-201">Using this scope, it's possible to use DI services with a scoped lifetime and have them live as long as the component.</span></span> <span data-ttu-id="d646b-202">Při zničení součásti jsou uvolněny také služby od poskytovatele služeb s vymezenou s rozsahem komponenty.</span><span class="sxs-lookup"><span data-stu-id="d646b-202">When the component is destroyed, services from the component's scoped service provider are disposed as well.</span></span> <span data-ttu-id="d646b-203">To může být užitečné pro služby, které:</span><span class="sxs-lookup"><span data-stu-id="d646b-203">This can be useful for services that:</span></span>

* <span data-ttu-id="d646b-204">By měl být znovu použit v rámci součásti, protože přechodná životnost je nevhodná.</span><span class="sxs-lookup"><span data-stu-id="d646b-204">Should be reused within a component, as the transient lifetime is inappropriate.</span></span>
* <span data-ttu-id="d646b-205">By neměly být sdíleny mezi součástmi, protože životnost singleton je nevhodná.</span><span class="sxs-lookup"><span data-stu-id="d646b-205">Shouldn't be shared across components, as the singleton lifetime is inappropriate.</span></span>

<span data-ttu-id="d646b-206">K dispozici jsou `OwningComponentBase` dvě verze typu:</span><span class="sxs-lookup"><span data-stu-id="d646b-206">Two versions of the `OwningComponentBase` type are available:</span></span>

* <span data-ttu-id="d646b-207">`OwningComponentBase`je abstraktní, jednorázové podřízená vlastnost typu `ComponentBase` s chráněnou `ScopedServices` vlastností typu `IServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="d646b-207">`OwningComponentBase` is an abstract, disposable child of the `ComponentBase` type with a protected `ScopedServices` property of type `IServiceProvider`.</span></span> <span data-ttu-id="d646b-208">Tohoto zprostředkovatele lze vyřešit služby, které jsou vymezeny na životnost součásti.</span><span class="sxs-lookup"><span data-stu-id="d646b-208">This provider can be used to resolve services that are scoped to the lifetime of the component.</span></span>

  <span data-ttu-id="d646b-209">Služby DI vložené `@inject` do `InjectAttribute` komponenty pomocí nebo (`[Inject]`) nejsou vytvořeny v oboru komponenty.</span><span class="sxs-lookup"><span data-stu-id="d646b-209">DI services injected into the component using `@inject` or the `InjectAttribute` (`[Inject]`) aren't created in the component's scope.</span></span> <span data-ttu-id="d646b-210">Chcete-li použít obor komponenty, musí `ScopedServices.GetRequiredService` `ScopedServices.GetService`být služby vyřešeny pomocí nebo .</span><span class="sxs-lookup"><span data-stu-id="d646b-210">To use the component's scope, services must be resolved using `ScopedServices.GetRequiredService` or `ScopedServices.GetService`.</span></span> <span data-ttu-id="d646b-211">Všechny služby `ScopedServices` vyřešené pomocí zprostředkovatele mají své závislosti poskytované ze stejného oboru.</span><span class="sxs-lookup"><span data-stu-id="d646b-211">Any services resolved using the `ScopedServices` provider have their dependencies provided from that same scope.</span></span>

  ```razor
  @page "/preferences"
  @using Microsoft.Extensions.DependencyInjection
  @inherits OwningComponentBase

  <h1>User (@UserService.Name)</h1>

  <ul>
      @foreach (var setting in SettingService.GetSettings())
      {
          <li>@setting.SettingName: @setting.SettingValue</li>
      }
  </ul>

  @code {
      private IUserService UserService { get; set; }
      private ISettingService SettingService { get; set; }

      protected override void OnInitialized()
      {
          UserService = ScopedServices.GetRequiredService<IUserService>();
          SettingService = ScopedServices.GetRequiredService<ISettingService>();
      }
  }
  ```

* <span data-ttu-id="d646b-212">`OwningComponentBase<T>`odvozuje `OwningComponentBase` a přidává `Service` vlastnost, která `T` vrací instanci z oboru Zprostředkovatel DI.</span><span class="sxs-lookup"><span data-stu-id="d646b-212">`OwningComponentBase<T>` derives from `OwningComponentBase` and adds a property `Service` that returns an instance of `T` from the scoped DI provider.</span></span> <span data-ttu-id="d646b-213">Tento typ je pohodlný způsob, jak získat přístup `IServiceProvider` k službám s oborem bez použití instance, kdy existuje jedna primární služba, kterou aplikace vyžaduje z kontejneru DI pomocí oboru komponenty.</span><span class="sxs-lookup"><span data-stu-id="d646b-213">This type is a convenient way to access scoped services without using an instance of `IServiceProvider` when there's one primary service the app requires from the DI container using the component's scope.</span></span> <span data-ttu-id="d646b-214">Vlastnost `ScopedServices` je k dispozici, takže aplikace může získat služby jiných typů, v případě potřeby.</span><span class="sxs-lookup"><span data-stu-id="d646b-214">The `ScopedServices` property is available, so the app can get services of other types, if necessary.</span></span>

  ```razor
  @page "/users"
  @attribute [Authorize]
  @inherits OwningComponentBase<AppDbContext>

  <h1>Users (@Service.Users.Count())</h1>

  <ul>
      @foreach (var user in Service.Users)
      {
          <li>@user.UserName</li>
      }
  </ul>
  ```

## <a name="use-of-entity-framework-dbcontext-from-di"></a><span data-ttu-id="d646b-215">Použití entity Framework DbContext z DI</span><span class="sxs-lookup"><span data-stu-id="d646b-215">Use of Entity Framework DbContext from DI</span></span>

<span data-ttu-id="d646b-216">Jeden běžný typ služby načíst z DI ve `DbContext` webových aplikacích je entity Framework (EF) objekty.</span><span class="sxs-lookup"><span data-stu-id="d646b-216">One common service type to retrieve from DI in web apps is Entity Framework (EF) `DbContext` objects.</span></span> <span data-ttu-id="d646b-217">Registrace služby `IServiceCollection.AddDbContext` EF `DbContext` pomocí přidá jako službu s oborem ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="d646b-217">Registering EF services using `IServiceCollection.AddDbContext` adds the `DbContext` as a scoped service by default.</span></span> <span data-ttu-id="d646b-218">Registrace jako služba s vymezeným oborem může vést k problémům v Blazor aplikacích, protože způsobuje, `DbContext` že instance budou dlouhodobé a sdílené v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="d646b-218">Registering as a scoped service can lead to problems in Blazor apps because it causes `DbContext` instances to be long-lived and shared across the app.</span></span> <span data-ttu-id="d646b-219">`DbContext`není bezpečný pro přístup z více vláken a nesmí být používán současně.</span><span class="sxs-lookup"><span data-stu-id="d646b-219">`DbContext` isn't thread-safe and must not be used concurrently.</span></span>

<span data-ttu-id="d646b-220">V závislosti na `OwningComponentBase` aplikaci může problém `DbContext` vyřešit *may* použití k omezení rozsahu a na jednu součást.</span><span class="sxs-lookup"><span data-stu-id="d646b-220">Depending on the app, using `OwningComponentBase` to limit the scope of a `DbContext` to a single component *may* solve the issue.</span></span> <span data-ttu-id="d646b-221">Pokud `DbContext` součást nepoužívá paralelně, odvození komponenty z `OwningComponentBase` a načítání `DbContext` z `ScopedServices` je dostačující, protože zajišťuje, že:</span><span class="sxs-lookup"><span data-stu-id="d646b-221">If a component doesn't use a `DbContext` in parallel, deriving the component from `OwningComponentBase` and retrieving the `DbContext` from `ScopedServices` is sufficient because it ensures that:</span></span>

* <span data-ttu-id="d646b-222">Samostatné součásti nesdílejí `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="d646b-222">Separate components don't share a `DbContext`.</span></span>
* <span data-ttu-id="d646b-223">Žije `DbContext` jen tak dlouho, jak součást závisí na tom.</span><span class="sxs-lookup"><span data-stu-id="d646b-223">The `DbContext` lives only as long as the component depending on it.</span></span>

<span data-ttu-id="d646b-224">Pokud jedna součást může `DbContext` používat souběžně (například pokaždé, když uživatel vybere `OwningComponentBase` tlačítko), i pomocí nezabrání problémům s souběžnými operacemi EF.</span><span class="sxs-lookup"><span data-stu-id="d646b-224">If a single component might use a `DbContext` concurrently (for example, every time a user selects a button), even using `OwningComponentBase` doesn't avoid issues with concurrent EF operations.</span></span> <span data-ttu-id="d646b-225">V takovém případě použijte `DbContext` pro každou logickou operaci EF jiný.</span><span class="sxs-lookup"><span data-stu-id="d646b-225">In that case, use a different `DbContext` for each logical EF operation.</span></span> <span data-ttu-id="d646b-226">Použijte některý z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="d646b-226">Use either of the following approaches:</span></span>

* <span data-ttu-id="d646b-227">Vytvořte `DbContext` přímo `DbContextOptions<TContext>` pomocí jako argument, který lze načíst z DI a je bezpečné pro přístup z více vláken.</span><span class="sxs-lookup"><span data-stu-id="d646b-227">Create the `DbContext` directly using `DbContextOptions<TContext>` as an argument, which can be retrieved from DI and is thread safe.</span></span>

    ```razor
    @page "/example"
    @inject DbContextOptions<AppDbContext> DbContextOptions

    <ul>
        @foreach (var item in _data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> _data = new List<string>();

        private async Task LoadData()
        {
            _data = await GetAsync();
            StateHasChanged();
        }

        public async Task<List<string>> GetAsync()
        {
            using (var context = new AppDbContext(DbContextOptions))
            {
                return await context.Products.Select(p => p.Name).ToListAsync();
            }
        }
    }
    ```

* <span data-ttu-id="d646b-228">Zaregistrujte `DbContext` v kontejneru služby s přechodnou životností:</span><span class="sxs-lookup"><span data-stu-id="d646b-228">Register the `DbContext` in the service container with a transient lifetime:</span></span>
  * <span data-ttu-id="d646b-229">Při registraci kontextu `ServiceLifetime.Transient`použijte .</span><span class="sxs-lookup"><span data-stu-id="d646b-229">When registering the context, use `ServiceLifetime.Transient`.</span></span> <span data-ttu-id="d646b-230">Metoda `AddDbContext` rozšíření má dva volitelné `ServiceLifetime`parametry typu .</span><span class="sxs-lookup"><span data-stu-id="d646b-230">The `AddDbContext` extension method takes two optional parameters of type `ServiceLifetime`.</span></span> <span data-ttu-id="d646b-231">Chcete-li použít tento `contextLifetime` přístup, `ServiceLifetime.Transient`pouze parametr musí být .</span><span class="sxs-lookup"><span data-stu-id="d646b-231">To use this approach, only the `contextLifetime` parameter needs to be `ServiceLifetime.Transient`.</span></span> <span data-ttu-id="d646b-232">`optionsLifetime`může zachovat výchozí `ServiceLifetime.Scoped`hodnotu aplikace .</span><span class="sxs-lookup"><span data-stu-id="d646b-232">`optionsLifetime` can keep its default value of `ServiceLifetime.Scoped`.</span></span>

    ```csharp
    services.AddDbContext<AppDbContext>(options =>
         options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")),
         ServiceLifetime.Transient);
    ```  

  * <span data-ttu-id="d646b-233">Přechodný `DbContext` může být vložen jako normální `@inject`(pomocí ) do komponent, které nebudou provádět více operací EF paralelně.</span><span class="sxs-lookup"><span data-stu-id="d646b-233">The transient `DbContext` can be injected as normal (using `@inject`) into components that will not execute multiple EF operations in parallel.</span></span> <span data-ttu-id="d646b-234">Ty, které mohou provádět více operací `DbContext` EF současně můžete `IServiceProvider.GetRequiredService`požadovat samostatné objekty pro každou paralelní operaci pomocí .</span><span class="sxs-lookup"><span data-stu-id="d646b-234">Those that may perform multiple EF operations simultaneously can request separate `DbContext` objects for each parallel operation using `IServiceProvider.GetRequiredService`.</span></span>

    ```razor
    @page "/example"
    @using Microsoft.Extensions.DependencyInjection
    @inject IServiceProvider ServiceProvider

    <ul>
        @foreach (var item in _data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> _data = new List<string>();

        private async Task LoadData()
        {
            _data = await GetAsync();
            StateHasChanged();
        }

        public async Task<List<string>> GetAsync()
        {
            using (var context = ServiceProvider.GetRequiredService<AppDbContext>())
            {
                return await context.Products.Select(p => p.Name).ToListAsync();
            }
        }
    }
    ```

## <a name="additional-resources"></a><span data-ttu-id="d646b-235">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="d646b-235">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>

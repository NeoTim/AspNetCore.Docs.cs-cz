---
title: Vkládání závislostí ASP.NET Core Blazor
author: guardrex
description: Podívejte se, jak Blazor aplikace můžou vkládat služby do součástí.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/20/2020
no-loc:
- Blazor
- SignalR
uid: blazor/dependency-injection
ms.openlocfilehash: 4cdde9ee8c9fd9adf00894a067d32965b180e5ec
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78658072"
---
# <a name="aspnet-core-blazor-dependency-injection"></a><span data-ttu-id="88be7-103">Vkládání závislostí ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="88be7-103">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="88be7-104">Od [Rainer Stropek](https://www.timecockpit.com) a [Jan Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="88be7-104">By [Rainer Stropek](https://www.timecockpit.com) and [Mike Rousos](https://github.com/mjrousos)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="88be7-105">Blazor podporuje [vkládání závislostí (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="88be7-105">Blazor supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="88be7-106">Aplikace mohou používat vestavěné služby jejich vložením do komponent.</span><span class="sxs-lookup"><span data-stu-id="88be7-106">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="88be7-107">Aplikace můžou také definovat a registrovat vlastní služby a zpřístupnit je v celé aplikaci přes DI.</span><span class="sxs-lookup"><span data-stu-id="88be7-107">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

<span data-ttu-id="88be7-108">DI je technika přístupu ke službám nakonfigurovaným v centrálním umístění.</span><span class="sxs-lookup"><span data-stu-id="88be7-108">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="88be7-109">To může být užitečné v aplikacích Blazor k těmto akcím:</span><span class="sxs-lookup"><span data-stu-id="88be7-109">This can be useful in Blazor apps to:</span></span>

* <span data-ttu-id="88be7-110">Sdílejte jednu instanci třídy služby napříč mnoha komponentami, která se označuje jako služba typu *singleton* .</span><span class="sxs-lookup"><span data-stu-id="88be7-110">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="88be7-111">Oddělit komponenty od konkrétních tříd služeb pomocí abstrakcí odkazů.</span><span class="sxs-lookup"><span data-stu-id="88be7-111">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="88be7-112">Představte si třeba rozhraní `IDataAccess` pro přístup k datům v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="88be7-112">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="88be7-113">Rozhraní je implementováno konkrétní `DataAccess`ou třídou a registrováno jako služba v kontejneru služby aplikace.</span><span class="sxs-lookup"><span data-stu-id="88be7-113">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="88be7-114">Pokud komponenta používá DI pro příjem `IDataAccess` implementace, komponenta není spojena se konkrétním typem.</span><span class="sxs-lookup"><span data-stu-id="88be7-114">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="88be7-115">Implementaci je možné prohodit, třeba pro podrobnější implementaci v testování částí.</span><span class="sxs-lookup"><span data-stu-id="88be7-115">The implementation can be swapped, perhaps for a mock implementation in unit tests.</span></span>

## <a name="default-services"></a><span data-ttu-id="88be7-116">Výchozí služby</span><span class="sxs-lookup"><span data-stu-id="88be7-116">Default services</span></span>

<span data-ttu-id="88be7-117">Výchozí služby se automaticky přidají do kolekce služeb aplikace.</span><span class="sxs-lookup"><span data-stu-id="88be7-117">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="88be7-118">Služba</span><span class="sxs-lookup"><span data-stu-id="88be7-118">Service</span></span> | <span data-ttu-id="88be7-119">Životnost</span><span class="sxs-lookup"><span data-stu-id="88be7-119">Lifetime</span></span> | <span data-ttu-id="88be7-120">Popis</span><span class="sxs-lookup"><span data-stu-id="88be7-120">Description</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="88be7-121">Singleton</span><span class="sxs-lookup"><span data-stu-id="88be7-121">Singleton</span></span> | <span data-ttu-id="88be7-122">Poskytuje metody pro posílání požadavků HTTP a příjem odpovědí HTTP z prostředku identifikovaného identifikátorem URI.</span><span class="sxs-lookup"><span data-stu-id="88be7-122">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span><br><br><span data-ttu-id="88be7-123">Instance `HttpClient` v aplikaci WebAssembly v Blazor používá prohlížeč pro zpracování provozu HTTP na pozadí.</span><span class="sxs-lookup"><span data-stu-id="88be7-123">The instance of `HttpClient` in a Blazor WebAssembly app uses the browser for handling the HTTP traffic in the background.</span></span><br><br><span data-ttu-id="88be7-124">Aplikace Blazor Server ve výchozím nastavení nezahrnují `HttpClient` nakonfigurovanou jako službu.</span><span class="sxs-lookup"><span data-stu-id="88be7-124">Blazor Server apps don't include an `HttpClient` configured as a service by default.</span></span> <span data-ttu-id="88be7-125">Poskytněte `HttpClient` aplikaci Blazor serveru.</span><span class="sxs-lookup"><span data-stu-id="88be7-125">Provide an `HttpClient` to a Blazor Server app.</span></span><br><br><span data-ttu-id="88be7-126">Další informace naleznete v tématu <xref:blazor/call-web-api>.</span><span class="sxs-lookup"><span data-stu-id="88be7-126">For more information, see <xref:blazor/call-web-api>.</span></span> |
| `IJSRuntime` | <span data-ttu-id="88be7-127">Singleton (Blazor WebAssembly)</span><span class="sxs-lookup"><span data-stu-id="88be7-127">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="88be7-128">Vymezený obor (Blazor Server)</span><span class="sxs-lookup"><span data-stu-id="88be7-128">Scoped (Blazor Server)</span></span> | <span data-ttu-id="88be7-129">Představuje instanci modulu runtime jazyka JavaScript, kde jsou odesílána volání jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="88be7-129">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="88be7-130">Další informace naleznete v tématu <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="88be7-130">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span> |
| `NavigationManager` | <span data-ttu-id="88be7-131">Singleton (Blazor WebAssembly)</span><span class="sxs-lookup"><span data-stu-id="88be7-131">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="88be7-132">Vymezený obor (Blazor Server)</span><span class="sxs-lookup"><span data-stu-id="88be7-132">Scoped (Blazor Server)</span></span> | <span data-ttu-id="88be7-133">Obsahuje nápovědu pro práci s identifikátory URI a stavem navigace.</span><span class="sxs-lookup"><span data-stu-id="88be7-133">Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="88be7-134">Další informace najdete v tématu věnovaném [identifikátorům URI a nápovědě k informacím o stavu navigace](xref:blazor/routing#uri-and-navigation-state-helpers).</span><span class="sxs-lookup"><span data-stu-id="88be7-134">For more information, see [URI and navigation state helpers](xref:blazor/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="88be7-135">Vlastní zprostředkovatel služeb automaticky neposkytuje výchozí služby uvedené v tabulce.</span><span class="sxs-lookup"><span data-stu-id="88be7-135">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="88be7-136">Pokud používáte vlastního poskytovatele služeb a potřebujete některou ze služeb zobrazených v tabulce, přidejte požadované služby k novému poskytovateli služeb.</span><span class="sxs-lookup"><span data-stu-id="88be7-136">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="88be7-137">Přidání služeb do aplikace</span><span class="sxs-lookup"><span data-stu-id="88be7-137">Add services to an app</span></span>

### <a name="blazor-webassembly"></a><span data-ttu-id="88be7-138">Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="88be7-138">Blazor WebAssembly</span></span>

<span data-ttu-id="88be7-139">Nakonfigurujte služby pro kolekci služeb aplikace v metodě `Main` *program.cs*.</span><span class="sxs-lookup"><span data-stu-id="88be7-139">Configure services for the app's service collection in the `Main` method of *Program.cs*.</span></span> <span data-ttu-id="88be7-140">V následujícím příkladu je `MyDependency` implementace registrována pro `IMyDependency`:</span><span class="sxs-lookup"><span data-stu-id="88be7-140">In the following example, the `MyDependency` implementation is registered for `IMyDependency`:</span></span>

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

<span data-ttu-id="88be7-141">Po sestavení hostitele je možné služby získávat z kořenového oboru DI před vykreslením všech komponent.</span><span class="sxs-lookup"><span data-stu-id="88be7-141">Once the host is built, services can be accessed from the root DI scope before any components are rendered.</span></span> <span data-ttu-id="88be7-142">To může být užitečné pro spuštění logiky inicializace před vykreslením obsahu:</span><span class="sxs-lookup"><span data-stu-id="88be7-142">This can be useful for running initialization logic before rendering content:</span></span>

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

<span data-ttu-id="88be7-143">Hostitel taky poskytuje centrální instanci konfigurace pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="88be7-143">The host also provides a central configuration instance for the app.</span></span> <span data-ttu-id="88be7-144">V předchozím příkladu je adresa URL služby počasí předána z výchozího zdroje konfigurace (například *appSettings. JSON*) do `InitializeWeatherAsync`:</span><span class="sxs-lookup"><span data-stu-id="88be7-144">Building on the preceding example, the weather service's URL is passed from a default configuration source (for example, *appsettings.json*) to `InitializeWeatherAsync`:</span></span>

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

### <a name="blazor-server"></a><span data-ttu-id="88be7-145">Blazor Server</span><span class="sxs-lookup"><span data-stu-id="88be7-145">Blazor Server</span></span>

<span data-ttu-id="88be7-146">Po vytvoření nové aplikace Projděte metodu `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="88be7-146">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

<span data-ttu-id="88be7-147">Metodě `ConfigureServices` se předává <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, což je seznam objektů deskriptoru služby (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span><span class="sxs-lookup"><span data-stu-id="88be7-147">The `ConfigureServices` method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="88be7-148">Služby se přidávají tím, že se do kolekce služeb poskytují popisovače služby.</span><span class="sxs-lookup"><span data-stu-id="88be7-148">Services are added by providing service descriptors to the service collection.</span></span> <span data-ttu-id="88be7-149">Následující příklad ukazuje koncept s rozhraním `IDataAccess` a jeho konkrétní implementací `DataAccess`:</span><span class="sxs-lookup"><span data-stu-id="88be7-149">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a><span data-ttu-id="88be7-150">Doba života služby</span><span class="sxs-lookup"><span data-stu-id="88be7-150">Service lifetime</span></span>

<span data-ttu-id="88be7-151">Služby je možné konfigurovat s životností, která jsou uvedená v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="88be7-151">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="88be7-152">Životnost</span><span class="sxs-lookup"><span data-stu-id="88be7-152">Lifetime</span></span> | <span data-ttu-id="88be7-153">Popis</span><span class="sxs-lookup"><span data-stu-id="88be7-153">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped*> | <span data-ttu-id="88be7-154">aplikace Blazor WebAssembly aktuálně nemají koncept typu DI scopes.</span><span class="sxs-lookup"><span data-stu-id="88be7-154">Blazor WebAssembly apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="88be7-155">služby registrované `Scoped`se chovají jako služby `Singleton`.</span><span class="sxs-lookup"><span data-stu-id="88be7-155">`Scoped`-registered services behave like `Singleton` services.</span></span> <span data-ttu-id="88be7-156">Model hostování Blazor serveru však podporuje `Scoped` životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="88be7-156">However, the Blazor Server hosting model supports the `Scoped` lifetime.</span></span> <span data-ttu-id="88be7-157">V Blazorch serverových aplikacích je vymezená registrace služby vymezená na *připojení*.</span><span class="sxs-lookup"><span data-stu-id="88be7-157">In Blazor Server apps, a scoped service registration is scoped to the *connection*.</span></span> <span data-ttu-id="88be7-158">Z tohoto důvodu je vhodnější použití oboru služeb pro služby, které by měly být vymezeny na aktuálního uživatele, a to i v případě, že aktuální záměr je spustit na straně klienta v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="88be7-158">For this reason, using scoped services is preferred for services that should be scoped to the current user, even if the current intent is to run client-side in the browser.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton*> | <span data-ttu-id="88be7-159">DI vytvoří *jednu instanci* služby.</span><span class="sxs-lookup"><span data-stu-id="88be7-159">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="88be7-160">Všechny součásti, které vyžadují službu `Singleton`, obdrží instanci stejné služby.</span><span class="sxs-lookup"><span data-stu-id="88be7-160">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient*> | <span data-ttu-id="88be7-161">Pokaždé, když komponenta získá instanci `Transient` služby z kontejneru služby, obdrží *novou instanci* služby.</span><span class="sxs-lookup"><span data-stu-id="88be7-161">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="88be7-162">Systém DI je založený na systému DI v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="88be7-162">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="88be7-163">Další informace naleznete v tématu <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="88be7-163">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="88be7-164">Vyžádání služby v součásti</span><span class="sxs-lookup"><span data-stu-id="88be7-164">Request a service in a component</span></span>

<span data-ttu-id="88be7-165">Po přidání služeb do kolekce služeb tyto služby vloží do součástí pomocí direktivy [\@vkládání](xref:mvc/views/razor#inject) Razor.</span><span class="sxs-lookup"><span data-stu-id="88be7-165">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) Razor directive.</span></span> <span data-ttu-id="88be7-166">`@inject` má dva parametry:</span><span class="sxs-lookup"><span data-stu-id="88be7-166">`@inject` has two parameters:</span></span>

* <span data-ttu-id="88be7-167">Zadejte &ndash; typ služby, kterou chcete vložit.</span><span class="sxs-lookup"><span data-stu-id="88be7-167">Type &ndash; The type of the service to inject.</span></span>
* <span data-ttu-id="88be7-168">Vlastnost &ndash; název vlastnosti, která přijímá vloženou službu App Service.</span><span class="sxs-lookup"><span data-stu-id="88be7-168">Property &ndash; The name of the property receiving the injected app service.</span></span> <span data-ttu-id="88be7-169">Vlastnost nevyžaduje ruční vytvoření.</span><span class="sxs-lookup"><span data-stu-id="88be7-169">The property doesn't require manual creation.</span></span> <span data-ttu-id="88be7-170">Kompilátor vytvoří vlastnost.</span><span class="sxs-lookup"><span data-stu-id="88be7-170">The compiler creates the property.</span></span>

<span data-ttu-id="88be7-171">Další informace naleznete v tématu <xref:mvc/views/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="88be7-171">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="88be7-172">Pro vložení různých služeb použijte více příkazů `@inject`.</span><span class="sxs-lookup"><span data-stu-id="88be7-172">Use multiple `@inject` statements to inject different services.</span></span>

<span data-ttu-id="88be7-173">Následující příklad ukazuje, jak použít `@inject`.</span><span class="sxs-lookup"><span data-stu-id="88be7-173">The following example shows how to use `@inject`.</span></span> <span data-ttu-id="88be7-174">Služba implementující `Services.IDataAccess` je vložená do `DataRepository`vlastností komponenty.</span><span class="sxs-lookup"><span data-stu-id="88be7-174">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="88be7-175">Všimněte si, jak kód používá `IDataAccess` abstrakce:</span><span class="sxs-lookup"><span data-stu-id="88be7-175">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,23)]

<span data-ttu-id="88be7-176">Interně, generovaná vlastnost (`DataRepository`) používá atribut `InjectAttribute`.</span><span class="sxs-lookup"><span data-stu-id="88be7-176">Internally, the generated property (`DataRepository`) uses the `InjectAttribute` attribute.</span></span> <span data-ttu-id="88be7-177">Obvykle se tento atribut nepoužívá přímo.</span><span class="sxs-lookup"><span data-stu-id="88be7-177">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="88be7-178">Pokud je vyžadována základní třída pro součásti a vložené vlastnosti jsou také požadovány pro základní třídu, přidejte `InjectAttribute`ručně:</span><span class="sxs-lookup"><span data-stu-id="88be7-178">If a base class is required for components and injected properties are also required for the base class, manually add the `InjectAttribute`:</span></span>

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

<span data-ttu-id="88be7-179">V součástech odvozených ze základní třídy není `@inject` direktiva vyžadována.</span><span class="sxs-lookup"><span data-stu-id="88be7-179">In components derived from the base class, the `@inject` directive isn't required.</span></span> <span data-ttu-id="88be7-180">`InjectAttribute` základní třídy jsou dostatečné:</span><span class="sxs-lookup"><span data-stu-id="88be7-180">The `InjectAttribute` of the base class is sufficient:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="88be7-181">Použití DI v službách</span><span class="sxs-lookup"><span data-stu-id="88be7-181">Use DI in services</span></span>

<span data-ttu-id="88be7-182">Komplexní služby můžou vyžadovat další služby.</span><span class="sxs-lookup"><span data-stu-id="88be7-182">Complex services might require additional services.</span></span> <span data-ttu-id="88be7-183">V předchozím příkladu `DataAccess` může vyžadovat `HttpClient` výchozí službu.</span><span class="sxs-lookup"><span data-stu-id="88be7-183">In the prior example, `DataAccess` might require the `HttpClient` default service.</span></span> <span data-ttu-id="88be7-184">`@inject` (nebo `InjectAttribute`) nejsou k dispozici pro použití v rámci služeb.</span><span class="sxs-lookup"><span data-stu-id="88be7-184">`@inject` (or the `InjectAttribute`) isn't available for use in services.</span></span> <span data-ttu-id="88be7-185">Místo toho se musí použít *Injektáže konstruktoru* .</span><span class="sxs-lookup"><span data-stu-id="88be7-185">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="88be7-186">Požadované služby jsou přidány přidáním parametrů do konstruktoru služby.</span><span class="sxs-lookup"><span data-stu-id="88be7-186">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="88be7-187">Když DI vytvoří službu, rozpoznává služby, které vyžaduje v konstruktoru, a odpovídajícím způsobem je poskytne.</span><span class="sxs-lookup"><span data-stu-id="88be7-187">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span>

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

<span data-ttu-id="88be7-188">Předpoklady pro vložení konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="88be7-188">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="88be7-189">Je nutné, aby jeden konstruktor existoval, jehož argumenty mohou být splněny pomocí DI.</span><span class="sxs-lookup"><span data-stu-id="88be7-189">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="88be7-190">Další parametry, které nejsou pokryty parametrem DI, jsou povoleny, pokud určují výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="88be7-190">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="88be7-191">Příslušný konstruktor musí být *veřejný*.</span><span class="sxs-lookup"><span data-stu-id="88be7-191">The applicable constructor must be *public*.</span></span>
* <span data-ttu-id="88be7-192">Musí existovat jeden použitelný konstruktor.</span><span class="sxs-lookup"><span data-stu-id="88be7-192">One applicable constructor must exist.</span></span> <span data-ttu-id="88be7-193">V případě nejednoznačnosti Vyvolá příkaz DI výjimku.</span><span class="sxs-lookup"><span data-stu-id="88be7-193">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="88be7-194">Základní třídy komponenty nástroje pro správu oboru DI</span><span class="sxs-lookup"><span data-stu-id="88be7-194">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="88be7-195">V aplikacích ASP.NET Core jsou oborové služby obvykle vymezeny na aktuální požadavek.</span><span class="sxs-lookup"><span data-stu-id="88be7-195">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="88be7-196">Po dokončení žádosti se v systému DI odstraní všechny obory nebo přechodné služby.</span><span class="sxs-lookup"><span data-stu-id="88be7-196">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="88be7-197">V Blazorch serverových aplikací je rozsah požadavků po dobu trvání připojení klienta, což může vést k přechodným a oborovým službám, které jsou mnohem delší, než se očekávalo.</span><span class="sxs-lookup"><span data-stu-id="88be7-197">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span> <span data-ttu-id="88be7-198">V Blazorch aplikacích WebAssembly se služby zaregistrované s vymezeným životním cyklem považují za singleton, takže v typických ASP.NET Corech aplikacích budou živě fungovat déle než vymezené služby.</span><span class="sxs-lookup"><span data-stu-id="88be7-198">In Blazor WebAssembly apps, services registered with a scoped lifetime are treated as singletons, so they live longer than scoped services in typical ASP.NET Core apps.</span></span>

<span data-ttu-id="88be7-199">Přístup, který omezuje dobu života služby v aplikacích Blazor, používá typ `OwningComponentBase`.</span><span class="sxs-lookup"><span data-stu-id="88be7-199">An approach that limits a service lifetime in Blazor apps is use of the `OwningComponentBase` type.</span></span> <span data-ttu-id="88be7-200">`OwningComponentBase` je abstraktní typ odvozený z `ComponentBase`, který vytváří obor DI odpovídající životnosti komponenty.</span><span class="sxs-lookup"><span data-stu-id="88be7-200">`OwningComponentBase` is an abstract type derived from `ComponentBase` that creates a DI scope corresponding to the lifetime of the component.</span></span> <span data-ttu-id="88be7-201">Pomocí tohoto oboru je možné používat DI Services s vymezeným životním cyklem a mít je živý, dokud bude komponenta.</span><span class="sxs-lookup"><span data-stu-id="88be7-201">Using this scope, it's possible to use DI services with a scoped lifetime and have them live as long as the component.</span></span> <span data-ttu-id="88be7-202">Po zničení součásti budou všechny služby z oboru poskytovatele služeb komponent odstraněny také.</span><span class="sxs-lookup"><span data-stu-id="88be7-202">When the component is destroyed, services from the component's scoped service provider are disposed as well.</span></span> <span data-ttu-id="88be7-203">To může být užitečné pro služby, které:</span><span class="sxs-lookup"><span data-stu-id="88be7-203">This can be useful for services that:</span></span>

* <span data-ttu-id="88be7-204">By měl být znovu použit v rámci součásti, protože přechodná životnost je nevhodná.</span><span class="sxs-lookup"><span data-stu-id="88be7-204">Should be reused within a component, as the transient lifetime is inappropriate.</span></span>
* <span data-ttu-id="88be7-205">By neměl být sdílen napříč komponentami, protože životnost singleton je nevhodná.</span><span class="sxs-lookup"><span data-stu-id="88be7-205">Shouldn't be shared across components, as the singleton lifetime is inappropriate.</span></span>

<span data-ttu-id="88be7-206">K dispozici jsou dvě verze `OwningComponentBase` typu:</span><span class="sxs-lookup"><span data-stu-id="88be7-206">Two versions of the `OwningComponentBase` type are available:</span></span>

* <span data-ttu-id="88be7-207">`OwningComponentBase` je abstraktní podřízený typ `ComponentBase` typu s chráněnou `ScopedServices`ou vlastností typu `IServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="88be7-207">`OwningComponentBase` is an abstract, disposable child of the `ComponentBase` type with a protected `ScopedServices` property of type `IServiceProvider`.</span></span> <span data-ttu-id="88be7-208">Tento zprostředkovatel lze použít k překladu služeb, které jsou vymezeny na dobu života součásti.</span><span class="sxs-lookup"><span data-stu-id="88be7-208">This provider can be used to resolve services that are scoped to the lifetime of the component.</span></span>

  <span data-ttu-id="88be7-209">DI Services vložené do komponenty pomocí `@inject` nebo `InjectAttribute` (`[Inject]`) nejsou vytvořeny v oboru součásti.</span><span class="sxs-lookup"><span data-stu-id="88be7-209">DI services injected into the component using `@inject` or the `InjectAttribute` (`[Inject]`) aren't created in the component's scope.</span></span> <span data-ttu-id="88be7-210">Chcete-li použít rozsah komponenty, je nutné služby přeložit pomocí `ScopedServices.GetRequiredService` nebo `ScopedServices.GetService`.</span><span class="sxs-lookup"><span data-stu-id="88be7-210">To use the component's scope, services must be resolved using `ScopedServices.GetRequiredService` or `ScopedServices.GetService`.</span></span> <span data-ttu-id="88be7-211">Všechny služby vyřešené pomocí poskytovatele `ScopedServices` mají své závislosti ze stejného oboru.</span><span class="sxs-lookup"><span data-stu-id="88be7-211">Any services resolved using the `ScopedServices` provider have their dependencies provided from that same scope.</span></span>

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

* <span data-ttu-id="88be7-212">`OwningComponentBase<T>` je odvozen z `OwningComponentBase` a přidá vlastnost `Service`, která vrací instanci `T` z oboru DI Provider.</span><span class="sxs-lookup"><span data-stu-id="88be7-212">`OwningComponentBase<T>` derives from `OwningComponentBase` and adds a property `Service` that returns an instance of `T` from the scoped DI provider.</span></span> <span data-ttu-id="88be7-213">Tento typ je pohodlným způsobem, jak přistupovat k vymezeným službám bez použití instance `IServiceProvider`, pokud existuje jedna primární služba, kterou aplikace vyžaduje z kontejneru DI pomocí oboru komponenty.</span><span class="sxs-lookup"><span data-stu-id="88be7-213">This type is a convenient way to access scoped services without using an instance of `IServiceProvider` when there's one primary service the app requires from the DI container using the component's scope.</span></span> <span data-ttu-id="88be7-214">Vlastnost `ScopedServices` je k dispozici, takže aplikace může v případě potřeby získat služby jiných typů.</span><span class="sxs-lookup"><span data-stu-id="88be7-214">The `ScopedServices` property is available, so the app can get services of other types, if necessary.</span></span>

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

## <a name="use-of-entity-framework-dbcontext-from-di"></a><span data-ttu-id="88be7-215">Použití Entity Framework DbContext z DI</span><span class="sxs-lookup"><span data-stu-id="88be7-215">Use of Entity Framework DbContext from DI</span></span>

<span data-ttu-id="88be7-216">Jeden běžný typ služby k načtení z DI ve Web Apps je Entity Framework (EF) `DbContext` objektů.</span><span class="sxs-lookup"><span data-stu-id="88be7-216">One common service type to retrieve from DI in web apps is Entity Framework (EF) `DbContext` objects.</span></span> <span data-ttu-id="88be7-217">Registrace služeb EF pomocí `IServiceCollection.AddDbContext` ve výchozím nastavení přidá `DbContext` jako vymezenou službu.</span><span class="sxs-lookup"><span data-stu-id="88be7-217">Registering EF services using `IServiceCollection.AddDbContext` adds the `DbContext` as a scoped service by default.</span></span> <span data-ttu-id="88be7-218">Registrace jako vymezená služba může vést k problémům v Blazorch aplikacích, protože způsobí, že `DbContext` instance budou dlouhodobé a sdílené v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="88be7-218">Registering as a scoped service can lead to problems in Blazor apps because it causes `DbContext` instances to be long-lived and shared across the app.</span></span> <span data-ttu-id="88be7-219">`DbContext` není bezpečná pro přístup z více vláken a nesmí se používat současně.</span><span class="sxs-lookup"><span data-stu-id="88be7-219">`DbContext` isn't thread-safe and must not be used concurrently.</span></span>

<span data-ttu-id="88be7-220">V závislosti na aplikaci *může* tento problém vyřešit použití `OwningComponentBase` k omezení rozsahu `DbContext` na jednu komponentu.</span><span class="sxs-lookup"><span data-stu-id="88be7-220">Depending on the app, using `OwningComponentBase` to limit the scope of a `DbContext` to a single component *may* solve the issue.</span></span> <span data-ttu-id="88be7-221">Pokud komponenta nepoužívá `DbContext` paralelně, je odvozena komponenta z `OwningComponentBase` a načítání `DbContext` z `ScopedServices` je dostačující, protože zajišťuje:</span><span class="sxs-lookup"><span data-stu-id="88be7-221">If a component doesn't use a `DbContext` in parallel, deriving the component from `OwningComponentBase` and retrieving the `DbContext` from `ScopedServices` is sufficient because it ensures that:</span></span>

* <span data-ttu-id="88be7-222">Samostatné součásti nesdílejí `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="88be7-222">Separate components don't share a `DbContext`.</span></span>
* <span data-ttu-id="88be7-223">`DbContext` žije pouze tak dlouho, jak je komponenta závislá.</span><span class="sxs-lookup"><span data-stu-id="88be7-223">The `DbContext` lives only as long as the component depending on it.</span></span>

<span data-ttu-id="88be7-224">Pokud jedna součást může použít `DbContext` souběžně (například pokaždé, když uživatel vybere tlačítko), i když pomocí `OwningComponentBase` se vyhnete problémům s souběžnými operacemi EF.</span><span class="sxs-lookup"><span data-stu-id="88be7-224">If a single component might use a `DbContext` concurrently (for example, every time a user selects a button), even using `OwningComponentBase` doesn't avoid issues with concurrent EF operations.</span></span> <span data-ttu-id="88be7-225">V takovém případě použijte pro každou logickou operaci EF jiný `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="88be7-225">In that case, use a different `DbContext` for each logical EF operation.</span></span> <span data-ttu-id="88be7-226">Použijte některý z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="88be7-226">Use either of the following approaches:</span></span>

* <span data-ttu-id="88be7-227">Vytvořte `DbContext` přímo pomocí `DbContextOptions<TContext>` jako argument, který lze načíst z typu DI a je bezpečný pro přístup z více vláken.</span><span class="sxs-lookup"><span data-stu-id="88be7-227">Create the `DbContext` directly using `DbContextOptions<TContext>` as an argument, which can be retrieved from DI and is thread safe.</span></span>

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

* <span data-ttu-id="88be7-228">Zaregistrujte `DbContext` v kontejneru služby s přechodným trváním:</span><span class="sxs-lookup"><span data-stu-id="88be7-228">Register the `DbContext` in the service container with a transient lifetime:</span></span>
  * <span data-ttu-id="88be7-229">Při registraci kontextu použijte `ServiceLifetime.Transient`.</span><span class="sxs-lookup"><span data-stu-id="88be7-229">When registering the context, use `ServiceLifetime.Transient`.</span></span> <span data-ttu-id="88be7-230">Metoda rozšíření `AddDbContext` přebírá dva volitelné parametry typu `ServiceLifetime`.</span><span class="sxs-lookup"><span data-stu-id="88be7-230">The `AddDbContext` extension method takes two optional parameters of type `ServiceLifetime`.</span></span> <span data-ttu-id="88be7-231">Chcete-li použít tento přístup, je nutné `ServiceLifetime.Transient`pouze parametr `contextLifetime`.</span><span class="sxs-lookup"><span data-stu-id="88be7-231">To use this approach, only the `contextLifetime` parameter needs to be `ServiceLifetime.Transient`.</span></span> <span data-ttu-id="88be7-232">`optionsLifetime` může zachovat výchozí hodnotu `ServiceLifetime.Scoped`.</span><span class="sxs-lookup"><span data-stu-id="88be7-232">`optionsLifetime` can keep its default value of `ServiceLifetime.Scoped`.</span></span>

    ```csharp
    services.AddDbContext<AppDbContext>(options =>
         options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")),
         ServiceLifetime.Transient);
    ```  

  * <span data-ttu-id="88be7-233">Přechodný `DbContext` lze vložit jako normální (pomocí `@inject`) do komponent, které nespustí paralelní provádění více operací EF.</span><span class="sxs-lookup"><span data-stu-id="88be7-233">The transient `DbContext` can be injected as normal (using `@inject`) into components that will not execute multiple EF operations in parallel.</span></span> <span data-ttu-id="88be7-234">Ty, které mohou provádět více operací EF současně, mohou požadovat samostatné `DbContext` objekty pro každou paralelní operaci pomocí `IServiceProvider.GetRequiredService`.</span><span class="sxs-lookup"><span data-stu-id="88be7-234">Those that may perform multiple EF operations simultaneously can request separate `DbContext` objects for each parallel operation using `IServiceProvider.GetRequiredService`.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="88be7-235">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="88be7-235">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>

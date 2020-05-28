---
<span data-ttu-id="b233c-101">title: ' ASP.NET Core Blazor Injektáže závislosti ' Autor: Description: ' jak Blazor aplikace můžou vkládat služby do součástí. '</span><span class="sxs-lookup"><span data-stu-id="b233c-101">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="b233c-102">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="b233c-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b233c-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b233c-103">'Blazor'</span></span>
- <span data-ttu-id="b233c-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b233c-104">'Identity'</span></span>
- <span data-ttu-id="b233c-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b233c-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="b233c-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b233c-106">'Razor'</span></span>
- <span data-ttu-id="b233c-107">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="b233c-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-dependency-injection"></a><span data-ttu-id="b233c-108">BlazorVkládání závislostí ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b233c-108">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="b233c-109">Od [Rainer Stropek](https://www.timecockpit.com) a [Jan Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="b233c-109">By [Rainer Stropek](https://www.timecockpit.com) and [Mike Rousos](https://github.com/mjrousos)</span></span>

Blazor<span data-ttu-id="b233c-110">podporuje [vkládání závislostí (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="b233c-110"> supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="b233c-111">Aplikace mohou používat vestavěné služby jejich vložením do komponent.</span><span class="sxs-lookup"><span data-stu-id="b233c-111">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="b233c-112">Aplikace můžou také definovat a registrovat vlastní služby a zpřístupnit je v celé aplikaci přes DI.</span><span class="sxs-lookup"><span data-stu-id="b233c-112">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

<span data-ttu-id="b233c-113">DI je technika přístupu ke službám nakonfigurovaným v centrálním umístění.</span><span class="sxs-lookup"><span data-stu-id="b233c-113">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="b233c-114">To může být užitečné v Blazor aplikacích:</span><span class="sxs-lookup"><span data-stu-id="b233c-114">This can be useful in Blazor apps to:</span></span>

* <span data-ttu-id="b233c-115">Sdílejte jednu instanci třídy služby napříč mnoha komponentami, která se označuje jako služba typu *singleton* .</span><span class="sxs-lookup"><span data-stu-id="b233c-115">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="b233c-116">Oddělit komponenty od konkrétních tříd služeb pomocí abstrakcí odkazů.</span><span class="sxs-lookup"><span data-stu-id="b233c-116">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="b233c-117">Představte si třeba rozhraní `IDataAccess` pro přístup k datům v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b233c-117">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="b233c-118">Rozhraní je implementováno konkrétní `DataAccess` třídou a registrováno jako služba v kontejneru služby aplikace.</span><span class="sxs-lookup"><span data-stu-id="b233c-118">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="b233c-119">Pokud komponenta používá DI pro příjem `IDataAccess` implementace, komponenta není spojena se konkrétním typem.</span><span class="sxs-lookup"><span data-stu-id="b233c-119">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="b233c-120">Implementaci je možné prohodit, třeba pro podrobnější implementaci v testování částí.</span><span class="sxs-lookup"><span data-stu-id="b233c-120">The implementation can be swapped, perhaps for a mock implementation in unit tests.</span></span>

## <a name="default-services"></a><span data-ttu-id="b233c-121">Výchozí služby</span><span class="sxs-lookup"><span data-stu-id="b233c-121">Default services</span></span>

<span data-ttu-id="b233c-122">Výchozí služby se automaticky přidají do kolekce služeb aplikace.</span><span class="sxs-lookup"><span data-stu-id="b233c-122">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="b233c-123">Služba</span><span class="sxs-lookup"><span data-stu-id="b233c-123">Service</span></span> | <span data-ttu-id="b233c-124">Doba platnosti</span><span class="sxs-lookup"><span data-stu-id="b233c-124">Lifetime</span></span> | <span data-ttu-id="b233c-125">Description</span><span class="sxs-lookup"><span data-stu-id="b233c-125">Description</span></span> |
| ---
<span data-ttu-id="b233c-126">title: ' ASP.NET Core Blazor Injektáže závislosti ' Autor: Description: ' jak Blazor aplikace můžou vkládat služby do součástí. '</span><span class="sxs-lookup"><span data-stu-id="b233c-126">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="b233c-127">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="b233c-127">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b233c-128">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b233c-128">'Blazor'</span></span>
- <span data-ttu-id="b233c-129">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b233c-129">'Identity'</span></span>
- <span data-ttu-id="b233c-130">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b233c-130">'Let's Encrypt'</span></span>
- <span data-ttu-id="b233c-131">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b233c-131">'Razor'</span></span>
- <span data-ttu-id="b233c-132">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="b233c-132">'SignalR' uid:</span></span> 

<span data-ttu-id="b233c-133">---- | ---Název: "ASP.NET Core Blazor Injektáže" autor: Description: ' Podívejte se Blazor , jak aplikace můžou vkládat služby do součástí. '</span><span class="sxs-lookup"><span data-stu-id="b233c-133">---- | --- title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="b233c-134">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="b233c-134">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b233c-135">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b233c-135">'Blazor'</span></span>
- <span data-ttu-id="b233c-136">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b233c-136">'Identity'</span></span>
- <span data-ttu-id="b233c-137">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b233c-137">'Let's Encrypt'</span></span>
- <span data-ttu-id="b233c-138">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b233c-138">'Razor'</span></span>
- <span data-ttu-id="b233c-139">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="b233c-139">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b233c-140">title: ' ASP.NET Core Blazor Injektáže závislosti ' Autor: Description: ' jak Blazor aplikace můžou vkládat služby do součástí. '</span><span class="sxs-lookup"><span data-stu-id="b233c-140">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="b233c-141">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="b233c-141">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b233c-142">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b233c-142">'Blazor'</span></span>
- <span data-ttu-id="b233c-143">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b233c-143">'Identity'</span></span>
- <span data-ttu-id="b233c-144">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b233c-144">'Let's Encrypt'</span></span>
- <span data-ttu-id="b233c-145">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b233c-145">'Razor'</span></span>
- <span data-ttu-id="b233c-146">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="b233c-146">'SignalR' uid:</span></span> 

<span data-ttu-id="b233c-147">---- | ---Název: "ASP.NET Core Blazor Injektáže" autor: Description: ' Podívejte se Blazor , jak aplikace můžou vkládat služby do součástí. '</span><span class="sxs-lookup"><span data-stu-id="b233c-147">---- | --- title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="b233c-148">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="b233c-148">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b233c-149">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b233c-149">'Blazor'</span></span>
- <span data-ttu-id="b233c-150">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b233c-150">'Identity'</span></span>
- <span data-ttu-id="b233c-151">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b233c-151">'Let's Encrypt'</span></span>
- <span data-ttu-id="b233c-152">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b233c-152">'Razor'</span></span>
- <span data-ttu-id="b233c-153">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="b233c-153">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b233c-154">title: ' ASP.NET Core Blazor Injektáže závislosti ' Autor: Description: ' jak Blazor aplikace můžou vkládat služby do součástí. '</span><span class="sxs-lookup"><span data-stu-id="b233c-154">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="b233c-155">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="b233c-155">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b233c-156">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b233c-156">'Blazor'</span></span>
- <span data-ttu-id="b233c-157">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b233c-157">'Identity'</span></span>
- <span data-ttu-id="b233c-158">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b233c-158">'Let's Encrypt'</span></span>
- <span data-ttu-id="b233c-159">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b233c-159">'Razor'</span></span>
- <span data-ttu-id="b233c-160">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="b233c-160">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b233c-161">title: ' ASP.NET Core Blazor Injektáže závislosti ' Autor: Description: ' jak Blazor aplikace můžou vkládat služby do součástí. '</span><span class="sxs-lookup"><span data-stu-id="b233c-161">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="b233c-162">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="b233c-162">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b233c-163">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b233c-163">'Blazor'</span></span>
- <span data-ttu-id="b233c-164">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b233c-164">'Identity'</span></span>
- <span data-ttu-id="b233c-165">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b233c-165">'Let's Encrypt'</span></span>
- <span data-ttu-id="b233c-166">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b233c-166">'Razor'</span></span>
- <span data-ttu-id="b233c-167">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="b233c-167">'SignalR' uid:</span></span> 

<span data-ttu-id="b233c-168">------ | | <xref:System.Net.Http.HttpClient> | Přechodný | Poskytuje metody pro posílání požadavků HTTP a příjem odpovědí HTTP z prostředku identifikovaného identifikátorem URI.</span><span class="sxs-lookup"><span data-stu-id="b233c-168">------ | | <xref:System.Net.Http.HttpClient> | Transient | Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span><br><br><span data-ttu-id="b233c-169">Instance <xref:System.Net.Http.HttpClient> v Blazor aplikaci WebAssembly používá prohlížeč pro zpracování provozu http na pozadí.</span><span class="sxs-lookup"><span data-stu-id="b233c-169">The instance of <xref:System.Net.Http.HttpClient> in a Blazor WebAssembly app uses the browser for handling the HTTP traffic in the background.</span></span><br><br>Blazor<span data-ttu-id="b233c-170">Serverové aplikace <xref:System.Net.Http.HttpClient> ve výchozím nastavení neobsahují nakonfigurovaný jako službu.</span><span class="sxs-lookup"><span data-stu-id="b233c-170"> Server apps don't include an <xref:System.Net.Http.HttpClient> configured as a service by default.</span></span> <span data-ttu-id="b233c-171">Poskytněte <xref:System.Net.Http.HttpClient> Blazor serverovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b233c-171">Provide an <xref:System.Net.Http.HttpClient> to a Blazor Server app.</span></span><br><br><span data-ttu-id="b233c-172">Další informace naleznete v tématu <xref:blazor/call-web-api>.</span><span class="sxs-lookup"><span data-stu-id="b233c-172">For more information, see <xref:blazor/call-web-api>.</span></span> <span data-ttu-id="b233c-173">| | <xref:Microsoft.JSInterop.IJSRuntime> | Singleton ( Blazor WebAssembly)</span><span class="sxs-lookup"><span data-stu-id="b233c-173">| | <xref:Microsoft.JSInterop.IJSRuntime> | Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="b233c-174">Obor ( Blazor Server) | Představuje instanci modulu runtime jazyka JavaScript, kde jsou odesílána volání jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b233c-174">Scoped (Blazor Server) | Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="b233c-175">Další informace naleznete v tématu <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="b233c-175">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span> <span data-ttu-id="b233c-176">| | <xref:Microsoft.AspNetCore.Components.NavigationManager> | Singleton ( Blazor WebAssembly)</span><span class="sxs-lookup"><span data-stu-id="b233c-176">| | <xref:Microsoft.AspNetCore.Components.NavigationManager> | Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="b233c-177">Obor ( Blazor Server) | Obsahuje nápovědu pro práci s identifikátory URI a stavem navigace.</span><span class="sxs-lookup"><span data-stu-id="b233c-177">Scoped (Blazor Server) | Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="b233c-178">Další informace najdete v tématu věnovaném [identifikátorům URI a nápovědě k informacím o stavu navigace](xref:blazor/routing#uri-and-navigation-state-helpers).</span><span class="sxs-lookup"><span data-stu-id="b233c-178">For more information, see [URI and navigation state helpers](xref:blazor/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="b233c-179">Vlastní zprostředkovatel služeb automaticky neposkytuje výchozí služby uvedené v tabulce.</span><span class="sxs-lookup"><span data-stu-id="b233c-179">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="b233c-180">Pokud používáte vlastního poskytovatele služeb a potřebujete některou ze služeb zobrazených v tabulce, přidejte požadované služby k novému poskytovateli služeb.</span><span class="sxs-lookup"><span data-stu-id="b233c-180">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="b233c-181">Přidání služeb do aplikace</span><span class="sxs-lookup"><span data-stu-id="b233c-181">Add services to an app</span></span>

### <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="b233c-182">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="b233c-182"> WebAssembly</span></span>

<span data-ttu-id="b233c-183">Nakonfigurujte služby pro kolekci služeb aplikace v `Main` metodě *program.cs*.</span><span class="sxs-lookup"><span data-stu-id="b233c-183">Configure services for the app's service collection in the `Main` method of *Program.cs*.</span></span> <span data-ttu-id="b233c-184">V následujícím příkladu `MyDependency` je implementace zaregistrována pro `IMyDependency` :</span><span class="sxs-lookup"><span data-stu-id="b233c-184">In the following example, the `MyDependency` implementation is registered for `IMyDependency`:</span></span>

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

<span data-ttu-id="b233c-185">Po sestavení hostitele je možné služby získávat z kořenového oboru DI před vykreslením všech komponent.</span><span class="sxs-lookup"><span data-stu-id="b233c-185">Once the host is built, services can be accessed from the root DI scope before any components are rendered.</span></span> <span data-ttu-id="b233c-186">To může být užitečné pro spuštění logiky inicializace před vykreslením obsahu:</span><span class="sxs-lookup"><span data-stu-id="b233c-186">This can be useful for running initialization logic before rendering content:</span></span>

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

<span data-ttu-id="b233c-187">Hostitel taky poskytuje centrální instanci konfigurace pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b233c-187">The host also provides a central configuration instance for the app.</span></span> <span data-ttu-id="b233c-188">V předchozím příkladu je adresa URL služby počasí předána z výchozího zdroje konfigurace (například *appSettings. JSON*) `InitializeWeatherAsync` :</span><span class="sxs-lookup"><span data-stu-id="b233c-188">Building on the preceding example, the weather service's URL is passed from a default configuration source (for example, *appsettings.json*) to `InitializeWeatherAsync`:</span></span>

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

### <a name="blazor-server"></a>Blazor<span data-ttu-id="b233c-189">WebServer</span><span class="sxs-lookup"><span data-stu-id="b233c-189"> Server</span></span>

<span data-ttu-id="b233c-190">Po vytvoření nové aplikace si Projděte tuto `Startup.ConfigureServices` metodu:</span><span class="sxs-lookup"><span data-stu-id="b233c-190">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

<span data-ttu-id="b233c-191"><xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A>Metoda je předána <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> , což je seznam objektů deskriptoru služby ( <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> ).</span><span class="sxs-lookup"><span data-stu-id="b233c-191">The <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="b233c-192">Služby se přidávají tím, že se do kolekce služeb poskytují popisovače služby.</span><span class="sxs-lookup"><span data-stu-id="b233c-192">Services are added by providing service descriptors to the service collection.</span></span> <span data-ttu-id="b233c-193">Následující příklad ukazuje koncept s `IDataAccess` rozhraním a jeho konkrétní implementací `DataAccess` :</span><span class="sxs-lookup"><span data-stu-id="b233c-193">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a><span data-ttu-id="b233c-194">Doba života služby</span><span class="sxs-lookup"><span data-stu-id="b233c-194">Service lifetime</span></span>

<span data-ttu-id="b233c-195">Služby je možné konfigurovat s životností, která jsou uvedená v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="b233c-195">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="b233c-196">Doba platnosti</span><span class="sxs-lookup"><span data-stu-id="b233c-196">Lifetime</span></span> | <span data-ttu-id="b233c-197">Description</span><span class="sxs-lookup"><span data-stu-id="b233c-197">Description</span></span> |
| ---
<span data-ttu-id="b233c-198">title: ' ASP.NET Core Blazor Injektáže závislosti ' Autor: Description: ' jak Blazor aplikace můžou vkládat služby do součástí. '</span><span class="sxs-lookup"><span data-stu-id="b233c-198">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="b233c-199">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="b233c-199">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b233c-200">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b233c-200">'Blazor'</span></span>
- <span data-ttu-id="b233c-201">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b233c-201">'Identity'</span></span>
- <span data-ttu-id="b233c-202">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b233c-202">'Let's Encrypt'</span></span>
- <span data-ttu-id="b233c-203">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b233c-203">'Razor'</span></span>
- <span data-ttu-id="b233c-204">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="b233c-204">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b233c-205">title: ' ASP.NET Core Blazor Injektáže závislosti ' Autor: Description: ' jak Blazor aplikace můžou vkládat služby do součástí. '</span><span class="sxs-lookup"><span data-stu-id="b233c-205">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="b233c-206">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="b233c-206">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b233c-207">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b233c-207">'Blazor'</span></span>
- <span data-ttu-id="b233c-208">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b233c-208">'Identity'</span></span>
- <span data-ttu-id="b233c-209">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b233c-209">'Let's Encrypt'</span></span>
- <span data-ttu-id="b233c-210">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b233c-210">'Razor'</span></span>
- <span data-ttu-id="b233c-211">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="b233c-211">'SignalR' uid:</span></span> 

<span data-ttu-id="b233c-212">---- | ---Název: "ASP.NET Core Blazor Injektáže" autor: Description: ' Podívejte se Blazor , jak aplikace můžou vkládat služby do součástí. '</span><span class="sxs-lookup"><span data-stu-id="b233c-212">---- | --- title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="b233c-213">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="b233c-213">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b233c-214">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b233c-214">'Blazor'</span></span>
- <span data-ttu-id="b233c-215">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b233c-215">'Identity'</span></span>
- <span data-ttu-id="b233c-216">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b233c-216">'Let's Encrypt'</span></span>
- <span data-ttu-id="b233c-217">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b233c-217">'Razor'</span></span>
- <span data-ttu-id="b233c-218">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="b233c-218">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b233c-219">title: ' ASP.NET Core Blazor Injektáže závislosti ' Autor: Description: ' jak Blazor aplikace můžou vkládat služby do součástí. '</span><span class="sxs-lookup"><span data-stu-id="b233c-219">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="b233c-220">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="b233c-220">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b233c-221">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b233c-221">'Blazor'</span></span>
- <span data-ttu-id="b233c-222">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b233c-222">'Identity'</span></span>
- <span data-ttu-id="b233c-223">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b233c-223">'Let's Encrypt'</span></span>
- <span data-ttu-id="b233c-224">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b233c-224">'Razor'</span></span>
- <span data-ttu-id="b233c-225">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="b233c-225">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b233c-226">title: ' ASP.NET Core Blazor Injektáže závislosti ' Autor: Description: ' jak Blazor aplikace můžou vkládat služby do součástí. '</span><span class="sxs-lookup"><span data-stu-id="b233c-226">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="b233c-227">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="b233c-227">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b233c-228">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b233c-228">'Blazor'</span></span>
- <span data-ttu-id="b233c-229">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b233c-229">'Identity'</span></span>
- <span data-ttu-id="b233c-230">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b233c-230">'Let's Encrypt'</span></span>
- <span data-ttu-id="b233c-231">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b233c-231">'Razor'</span></span>
- <span data-ttu-id="b233c-232">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="b233c-232">'SignalR' uid:</span></span> 

<span data-ttu-id="b233c-233">------ | | <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | Blazor Aplikace pro WebAssembly aktuálně nemají koncept typu DI obory.</span><span class="sxs-lookup"><span data-stu-id="b233c-233">------ | | <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | Blazor WebAssembly apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="b233c-234">`Scoped`– registrované služby se chovají jako `Singleton` služby.</span><span class="sxs-lookup"><span data-stu-id="b233c-234">`Scoped`-registered services behave like `Singleton` services.</span></span> <span data-ttu-id="b233c-235">BlazorModel hostování serveru však podporuje `Scoped` dobu života.</span><span class="sxs-lookup"><span data-stu-id="b233c-235">However, the Blazor Server hosting model supports the `Scoped` lifetime.</span></span> <span data-ttu-id="b233c-236">V rámci Blazor serverových aplikací je vymezená registrace služby vymezená na *připojení*.</span><span class="sxs-lookup"><span data-stu-id="b233c-236">In Blazor Server apps, a scoped service registration is scoped to the *connection*.</span></span> <span data-ttu-id="b233c-237">Z tohoto důvodu je vhodnější použití oboru služeb pro služby, které by měly být vymezeny na aktuálního uživatele, a to i v případě, že aktuální záměr je spustit na straně klienta v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="b233c-237">For this reason, using scoped services is preferred for services that should be scoped to the current user, even if the current intent is to run client-side in the browser.</span></span> <span data-ttu-id="b233c-238">| | <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | DI vytvoří *jednu instanci* služby.</span><span class="sxs-lookup"><span data-stu-id="b233c-238">| | <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | DI creates a *single instance* of the service.</span></span> <span data-ttu-id="b233c-239">Všechny součásti, které vyžadují `Singleton` službu, obdrží instanci stejné služby.</span><span class="sxs-lookup"><span data-stu-id="b233c-239">All components requiring a `Singleton` service receive an instance of the same service.</span></span> <span data-ttu-id="b233c-240">| | <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | Pokaždé, když komponenta získá instanci `Transient` služby z kontejneru služby, obdrží *novou instanci* služby.</span><span class="sxs-lookup"><span data-stu-id="b233c-240">| | <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="b233c-241">Systém DI je založený na systému DI v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b233c-241">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="b233c-242">Další informace naleznete v tématu <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="b233c-242">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="b233c-243">Vyžádání služby v součásti</span><span class="sxs-lookup"><span data-stu-id="b233c-243">Request a service in a component</span></span>

<span data-ttu-id="b233c-244">Po přidání služeb do kolekce služeb tyto služby vloží do součástí pomocí direktivy [ \@ vložení](xref:mvc/views/razor#inject) Razor .</span><span class="sxs-lookup"><span data-stu-id="b233c-244">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) Razor directive.</span></span> <span data-ttu-id="b233c-245">[`@inject`](xref:mvc/views/razor#inject)má dva parametry:</span><span class="sxs-lookup"><span data-stu-id="b233c-245">[`@inject`](xref:mvc/views/razor#inject) has two parameters:</span></span>

* <span data-ttu-id="b233c-246">Typ: typ služby, která se má vložit.</span><span class="sxs-lookup"><span data-stu-id="b233c-246">Type: The type of the service to inject.</span></span>
* <span data-ttu-id="b233c-247">Vlastnost: název vlastnosti přijímající vloženou službu App Service.</span><span class="sxs-lookup"><span data-stu-id="b233c-247">Property: The name of the property receiving the injected app service.</span></span> <span data-ttu-id="b233c-248">Vlastnost nevyžaduje ruční vytvoření.</span><span class="sxs-lookup"><span data-stu-id="b233c-248">The property doesn't require manual creation.</span></span> <span data-ttu-id="b233c-249">Kompilátor vytvoří vlastnost.</span><span class="sxs-lookup"><span data-stu-id="b233c-249">The compiler creates the property.</span></span>

<span data-ttu-id="b233c-250">Další informace naleznete v tématu <xref:mvc/views/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="b233c-250">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="b233c-251">[`@inject`](xref:mvc/views/razor#inject)Pro vložení různých služeb použijte více příkazů.</span><span class="sxs-lookup"><span data-stu-id="b233c-251">Use multiple [`@inject`](xref:mvc/views/razor#inject) statements to inject different services.</span></span>

<span data-ttu-id="b233c-252">Následující příklad ukazuje, jak použít [`@inject`](xref:mvc/views/razor#inject) .</span><span class="sxs-lookup"><span data-stu-id="b233c-252">The following example shows how to use [`@inject`](xref:mvc/views/razor#inject).</span></span> <span data-ttu-id="b233c-253">Implementace služby `Services.IDataAccess` je vložena do vlastnosti komponenty `DataRepository` .</span><span class="sxs-lookup"><span data-stu-id="b233c-253">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="b233c-254">Všimněte si, jak kód používá `IDataAccess` abstrakci:</span><span class="sxs-lookup"><span data-stu-id="b233c-254">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,20)]

<span data-ttu-id="b233c-255">Interně vygenerovaná vlastnost ( `DataRepository` ) používá [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) atribut.</span><span class="sxs-lookup"><span data-stu-id="b233c-255">Internally, the generated property (`DataRepository`) uses the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute.</span></span> <span data-ttu-id="b233c-256">Obvykle se tento atribut nepoužívá přímo.</span><span class="sxs-lookup"><span data-stu-id="b233c-256">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="b233c-257">Pokud je vyžadována základní třída pro součásti a vložené vlastnosti jsou také požadovány pro základní třídu, přidejte ručně [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) atribut:</span><span class="sxs-lookup"><span data-stu-id="b233c-257">If a base class is required for components and injected properties are also required for the base class, manually add the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute:</span></span>

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

<span data-ttu-id="b233c-258">V součástech odvozených ze základní třídy [`@inject`](xref:mvc/views/razor#inject) není direktiva vyžadována.</span><span class="sxs-lookup"><span data-stu-id="b233c-258">In components derived from the base class, the [`@inject`](xref:mvc/views/razor#inject) directive isn't required.</span></span> <span data-ttu-id="b233c-259"><xref:Microsoft.AspNetCore.Components.InjectAttribute>Základní třída je dostačující:</span><span class="sxs-lookup"><span data-stu-id="b233c-259">The <xref:Microsoft.AspNetCore.Components.InjectAttribute> of the base class is sufficient:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="b233c-260">Použití DI v službách</span><span class="sxs-lookup"><span data-stu-id="b233c-260">Use DI in services</span></span>

<span data-ttu-id="b233c-261">Komplexní služby můžou vyžadovat další služby.</span><span class="sxs-lookup"><span data-stu-id="b233c-261">Complex services might require additional services.</span></span> <span data-ttu-id="b233c-262">V předchozím příkladu `DataAccess` může vyžadovat <xref:System.Net.Http.HttpClient> výchozí službu.</span><span class="sxs-lookup"><span data-stu-id="b233c-262">In the prior example, `DataAccess` might require the <xref:System.Net.Http.HttpClient> default service.</span></span> <span data-ttu-id="b233c-263">[`@inject`](xref:mvc/views/razor#inject)(nebo [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) atribut) není k dispozici pro použití ve službách.</span><span class="sxs-lookup"><span data-stu-id="b233c-263">[`@inject`](xref:mvc/views/razor#inject) (or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute) isn't available for use in services.</span></span> <span data-ttu-id="b233c-264">Místo toho se musí použít *Injektáže konstruktoru* .</span><span class="sxs-lookup"><span data-stu-id="b233c-264">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="b233c-265">Požadované služby jsou přidány přidáním parametrů do konstruktoru služby.</span><span class="sxs-lookup"><span data-stu-id="b233c-265">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="b233c-266">Když DI vytvoří službu, rozpoznává služby, které vyžaduje v konstruktoru, a odpovídajícím způsobem je poskytne.</span><span class="sxs-lookup"><span data-stu-id="b233c-266">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span>

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

<span data-ttu-id="b233c-267">Předpoklady pro vložení konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="b233c-267">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="b233c-268">Je nutné, aby jeden konstruktor existoval, jehož argumenty mohou být splněny pomocí DI.</span><span class="sxs-lookup"><span data-stu-id="b233c-268">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="b233c-269">Další parametry, které nejsou pokryty parametrem DI, jsou povoleny, pokud určují výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="b233c-269">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="b233c-270">Příslušný konstruktor musí být *veřejný*.</span><span class="sxs-lookup"><span data-stu-id="b233c-270">The applicable constructor must be *public*.</span></span>
* <span data-ttu-id="b233c-271">Musí existovat jeden použitelný konstruktor.</span><span class="sxs-lookup"><span data-stu-id="b233c-271">One applicable constructor must exist.</span></span> <span data-ttu-id="b233c-272">V případě nejednoznačnosti Vyvolá příkaz DI výjimku.</span><span class="sxs-lookup"><span data-stu-id="b233c-272">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="b233c-273">Základní třídy komponenty nástroje pro správu oboru DI</span><span class="sxs-lookup"><span data-stu-id="b233c-273">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="b233c-274">V aplikacích ASP.NET Core jsou oborové služby obvykle vymezeny na aktuální požadavek.</span><span class="sxs-lookup"><span data-stu-id="b233c-274">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="b233c-275">Po dokončení žádosti se v systému DI odstraní všechny obory nebo přechodné služby.</span><span class="sxs-lookup"><span data-stu-id="b233c-275">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="b233c-276">V rámci Blazor serverových aplikací je rozsah požadavků po dobu trvání připojení klienta, což může vést k přechodným a oborovým službám, které jsou delší, než se očekávalo.</span><span class="sxs-lookup"><span data-stu-id="b233c-276">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span> <span data-ttu-id="b233c-277">V Blazor aplikacích pro WebAssembly se služby zaregistrované s vymezeným životním cyklem považují za singleton, takže v typických aplikacích ASP.NET Core za provozu déle než vymezené služby.</span><span class="sxs-lookup"><span data-stu-id="b233c-277">In Blazor WebAssembly apps, services registered with a scoped lifetime are treated as singletons, so they live longer than scoped services in typical ASP.NET Core apps.</span></span>

<span data-ttu-id="b233c-278">Přístup, který omezuje dobu života služby v Blazor aplikacích, se používá pro daný <xref:Microsoft.AspNetCore.Components.OwningComponentBase> typ.</span><span class="sxs-lookup"><span data-stu-id="b233c-278">An approach that limits a service lifetime in Blazor apps is use of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type.</span></span> <span data-ttu-id="b233c-279"><xref:Microsoft.AspNetCore.Components.OwningComponentBase>je abstraktní typ odvozený z <xref:Microsoft.AspNetCore.Components.ComponentBase> , který vytvoří obor typu di odpovídající životnosti komponenty.</span><span class="sxs-lookup"><span data-stu-id="b233c-279"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract type derived from <xref:Microsoft.AspNetCore.Components.ComponentBase> that creates a DI scope corresponding to the lifetime of the component.</span></span> <span data-ttu-id="b233c-280">Pomocí tohoto oboru je možné používat DI Services s vymezeným životním cyklem a mít je živý, dokud bude komponenta.</span><span class="sxs-lookup"><span data-stu-id="b233c-280">Using this scope, it's possible to use DI services with a scoped lifetime and have them live as long as the component.</span></span> <span data-ttu-id="b233c-281">Po zničení součásti budou všechny služby z oboru poskytovatele služeb komponent odstraněny také.</span><span class="sxs-lookup"><span data-stu-id="b233c-281">When the component is destroyed, services from the component's scoped service provider are disposed as well.</span></span> <span data-ttu-id="b233c-282">To může být užitečné pro služby, které:</span><span class="sxs-lookup"><span data-stu-id="b233c-282">This can be useful for services that:</span></span>

* <span data-ttu-id="b233c-283">By měl být znovu použit v rámci součásti, protože přechodná životnost je nevhodná.</span><span class="sxs-lookup"><span data-stu-id="b233c-283">Should be reused within a component, as the transient lifetime is inappropriate.</span></span>
* <span data-ttu-id="b233c-284">By neměl být sdílen napříč komponentami, protože životnost singleton je nevhodná.</span><span class="sxs-lookup"><span data-stu-id="b233c-284">Shouldn't be shared across components, as the singleton lifetime is inappropriate.</span></span>

<span data-ttu-id="b233c-285"><xref:Microsoft.AspNetCore.Components.OwningComponentBase>K dispozici jsou dvě verze typu:</span><span class="sxs-lookup"><span data-stu-id="b233c-285">Two versions of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type are available:</span></span>

* <span data-ttu-id="b233c-286"><xref:Microsoft.AspNetCore.Components.OwningComponentBase>je abstraktní podřízený objekt <xref:Microsoft.AspNetCore.Components.ComponentBase> typu s chráněnou <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> vlastností typu <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="b233c-286"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract, disposable child of the <xref:Microsoft.AspNetCore.Components.ComponentBase> type with a protected <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property of type <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="b233c-287">Tento zprostředkovatel lze použít k překladu služeb, které jsou vymezeny na dobu života součásti.</span><span class="sxs-lookup"><span data-stu-id="b233c-287">This provider can be used to resolve services that are scoped to the lifetime of the component.</span></span>

  <span data-ttu-id="b233c-288">DI Services vložené do komponenty pomocí [`@inject`](xref:mvc/views/razor#inject) nebo [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) atribut nejsou vytvořeny v oboru komponenty.</span><span class="sxs-lookup"><span data-stu-id="b233c-288">DI services injected into the component using [`@inject`](xref:mvc/views/razor#inject) or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute aren't created in the component's scope.</span></span> <span data-ttu-id="b233c-289">Chcete-li použít rozsah komponenty, musí být služby přeloženy pomocí <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> nebo <xref:System.IServiceProvider.GetService%2A> .</span><span class="sxs-lookup"><span data-stu-id="b233c-289">To use the component's scope, services must be resolved using <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> or <xref:System.IServiceProvider.GetService%2A>.</span></span> <span data-ttu-id="b233c-290">Všechny služby vyřešené pomocí <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> poskytovatele mají své závislosti ze stejného oboru.</span><span class="sxs-lookup"><span data-stu-id="b233c-290">Any services resolved using the <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> provider have their dependencies provided from that same scope.</span></span>

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

* <span data-ttu-id="b233c-291"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601>je odvozen z <xref:Microsoft.AspNetCore.Components.OwningComponentBase> a přidává <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> vlastnost, která vrací instanci `T` z oboru typu di.</span><span class="sxs-lookup"><span data-stu-id="b233c-291"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> derives from <xref:Microsoft.AspNetCore.Components.OwningComponentBase> and adds a <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> property that returns an instance of `T` from the scoped DI provider.</span></span> <span data-ttu-id="b233c-292">Tento typ je pohodlný způsob přístupu k oboru služeb bez použití instance, <xref:System.IServiceProvider> Pokud existuje jedna primární služba, kterou aplikace vyžaduje z kontejneru di pomocí oboru komponenty.</span><span class="sxs-lookup"><span data-stu-id="b233c-292">This type is a convenient way to access scoped services without using an instance of <xref:System.IServiceProvider> when there's one primary service the app requires from the DI container using the component's scope.</span></span> <span data-ttu-id="b233c-293"><xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices>Vlastnost je k dispozici, takže aplikace může v případě potřeby získat služby jiných typů.</span><span class="sxs-lookup"><span data-stu-id="b233c-293">The <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property is available, so the app can get services of other types, if necessary.</span></span>

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

## <a name="use-of-entity-framework-dbcontext-from-di"></a><span data-ttu-id="b233c-294">Použití Entity Framework DbContext z DI</span><span class="sxs-lookup"><span data-stu-id="b233c-294">Use of Entity Framework DbContext from DI</span></span>

<span data-ttu-id="b233c-295">Jeden běžný typ služby k načtení z DI ve Web Apps je Entity Framework objektů (EF) <xref:Microsoft.EntityFrameworkCore.DbContext> .</span><span class="sxs-lookup"><span data-stu-id="b233c-295">One common service type to retrieve from DI in web apps is Entity Framework (EF) <xref:Microsoft.EntityFrameworkCore.DbContext> objects.</span></span> <span data-ttu-id="b233c-296">Při registraci služeb EF pomocí <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> se <xref:Microsoft.EntityFrameworkCore.DbContext> ve výchozím nastavení přidá služba jako vymezená.</span><span class="sxs-lookup"><span data-stu-id="b233c-296">Registering EF services using <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> adds the <xref:Microsoft.EntityFrameworkCore.DbContext> as a scoped service by default.</span></span> <span data-ttu-id="b233c-297">Registrace jako vymezená služba může vést k problémům v Blazor aplikacích, protože způsobí, že se <xref:Microsoft.EntityFrameworkCore.DbContext> instance budou dlouhodobě a sdílet napříč aplikací.</span><span class="sxs-lookup"><span data-stu-id="b233c-297">Registering as a scoped service can lead to problems in Blazor apps because it causes <xref:Microsoft.EntityFrameworkCore.DbContext> instances to be long-lived and shared across the app.</span></span> <span data-ttu-id="b233c-298"><xref:Microsoft.EntityFrameworkCore.DbContext>není bezpečná pro přístup z více vláken a nesmí se používat současně.</span><span class="sxs-lookup"><span data-stu-id="b233c-298"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread-safe and must not be used concurrently.</span></span>

<span data-ttu-id="b233c-299">V závislosti na aplikaci <xref:Microsoft.AspNetCore.Components.OwningComponentBase> může být problém vyřešen pomocí omezení rozsahu a <xref:Microsoft.EntityFrameworkCore.DbContext> na jednu komponentu. *may*</span><span class="sxs-lookup"><span data-stu-id="b233c-299">Depending on the app, using <xref:Microsoft.AspNetCore.Components.OwningComponentBase> to limit the scope of a <xref:Microsoft.EntityFrameworkCore.DbContext> to a single component *may* solve the issue.</span></span> <span data-ttu-id="b233c-300">Pokud komponenta nepoužívá <xref:Microsoft.EntityFrameworkCore.DbContext> paralelně, odvozování komponenty z <xref:Microsoft.AspNetCore.Components.OwningComponentBase> a načtení <xref:Microsoft.EntityFrameworkCore.DbContext> z <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> je dostačující, protože zajišťuje:</span><span class="sxs-lookup"><span data-stu-id="b233c-300">If a component doesn't use a <xref:Microsoft.EntityFrameworkCore.DbContext> in parallel, deriving the component from <xref:Microsoft.AspNetCore.Components.OwningComponentBase> and retrieving the <xref:Microsoft.EntityFrameworkCore.DbContext> from <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> is sufficient because it ensures that:</span></span>

* <span data-ttu-id="b233c-301">Samostatné součásti nesdílejí <xref:Microsoft.EntityFrameworkCore.DbContext> .</span><span class="sxs-lookup"><span data-stu-id="b233c-301">Separate components don't share a <xref:Microsoft.EntityFrameworkCore.DbContext>.</span></span>
* <span data-ttu-id="b233c-302"><xref:Microsoft.EntityFrameworkCore.DbContext>Žije pouze tak dlouho, dokud je komponenta v závislosti na ní.</span><span class="sxs-lookup"><span data-stu-id="b233c-302">The <xref:Microsoft.EntityFrameworkCore.DbContext> lives only as long as the component depending on it.</span></span>

<span data-ttu-id="b233c-303">Může-li jedna součást současně použít <xref:Microsoft.EntityFrameworkCore.DbContext> (například pokaždé, když uživatel vybere tlačítko), a to i v případě, že se <xref:Microsoft.AspNetCore.Components.OwningComponentBase> nevyhnete problémům s souběžnými operacemi EF.</span><span class="sxs-lookup"><span data-stu-id="b233c-303">If a single component might use a <xref:Microsoft.EntityFrameworkCore.DbContext> concurrently (for example, every time a user selects a button), even using <xref:Microsoft.AspNetCore.Components.OwningComponentBase> doesn't avoid issues with concurrent EF operations.</span></span> <span data-ttu-id="b233c-304">V takovém případě použijte jiný <xref:Microsoft.EntityFrameworkCore.DbContext> pro každou operaci logického EF.</span><span class="sxs-lookup"><span data-stu-id="b233c-304">In that case, use a different <xref:Microsoft.EntityFrameworkCore.DbContext> for each logical EF operation.</span></span> <span data-ttu-id="b233c-305">Použijte některý z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="b233c-305">Use either of the following approaches:</span></span>

* <span data-ttu-id="b233c-306">Vytvořte <xref:Microsoft.EntityFrameworkCore.DbContext> přímo pomocí <xref:Microsoft.EntityFrameworkCore.DbContextOptions%601> argumentu, který lze načíst z typu di a je bezpečný pro přístup z více vláken.</span><span class="sxs-lookup"><span data-stu-id="b233c-306">Create the <xref:Microsoft.EntityFrameworkCore.DbContext> directly using <xref:Microsoft.EntityFrameworkCore.DbContextOptions%601> as an argument, which can be retrieved from DI and is thread safe.</span></span>

    ```razor
    @page "/example"
    @inject DbContextOptions<AppDbContext> DbContextOptions

    <ul>
        @foreach (var item in data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> data = new List<string>();

        private async Task LoadData()
        {
            data = await GetAsync();
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

* <span data-ttu-id="b233c-307">Zaregistrujte <xref:Microsoft.EntityFrameworkCore.DbContext> v kontejneru služby s přechodným trváním:</span><span class="sxs-lookup"><span data-stu-id="b233c-307">Register the <xref:Microsoft.EntityFrameworkCore.DbContext> in the service container with a transient lifetime:</span></span>
  * <span data-ttu-id="b233c-308">Při registraci kontextu použijte <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="b233c-308">When registering the context, use <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType>.</span></span> <span data-ttu-id="b233c-309"><xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A>Metoda rozšíření používá dva nepovinné parametry typu <xref:Microsoft.Extensions.DependencyInjection.ServiceLifetime> .</span><span class="sxs-lookup"><span data-stu-id="b233c-309">The <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension method takes two optional parameters of type <xref:Microsoft.Extensions.DependencyInjection.ServiceLifetime>.</span></span> <span data-ttu-id="b233c-310">Chcete-li použít tento přístup, `contextLifetime` musí být pouze parametr <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="b233c-310">To use this approach, only the `contextLifetime` parameter needs to be <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType>.</span></span> <span data-ttu-id="b233c-311">`optionsLifetime`může zachovat výchozí hodnotu <xref:Microsoft.OData.ServiceLifetime.Scoped?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="b233c-311">`optionsLifetime` can keep its default value of <xref:Microsoft.OData.ServiceLifetime.Scoped?displayProperty=nameWithType>.</span></span>

    ```csharp
    services.AddDbContext<AppDbContext>(options =>
         options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")),
         ServiceLifetime.Transient);
    ```  

  * <span data-ttu-id="b233c-312">Přechodný <xref:Microsoft.EntityFrameworkCore.DbContext> lze vložit jako normální (pomocí [`@inject`](xref:mvc/views/razor#inject) ) do komponent, které nespustí paralelní provádění více operací EF.</span><span class="sxs-lookup"><span data-stu-id="b233c-312">The transient <xref:Microsoft.EntityFrameworkCore.DbContext> can be injected as normal (using [`@inject`](xref:mvc/views/razor#inject)) into components that will not execute multiple EF operations in parallel.</span></span> <span data-ttu-id="b233c-313">Ty, které mohou provádět více operací EF současně, mohou požadovat samostatné <xref:Microsoft.EntityFrameworkCore.DbContext> objekty pro každou paralelní operaci pomocí <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> .</span><span class="sxs-lookup"><span data-stu-id="b233c-313">Those that may perform multiple EF operations simultaneously can request separate <xref:Microsoft.EntityFrameworkCore.DbContext> objects for each parallel operation using <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A>.</span></span>

    ```razor
    @page "/example"
    @using Microsoft.Extensions.DependencyInjection
    @inject IServiceProvider ServiceProvider

    <ul>
        @foreach (var item in data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> data = new List<string>();

        private async Task LoadData()
        {
            data = await GetAsync();
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

## <a name="additional-resources"></a><span data-ttu-id="b233c-314">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="b233c-314">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>

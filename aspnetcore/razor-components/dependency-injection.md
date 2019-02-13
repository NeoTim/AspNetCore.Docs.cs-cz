---
title: Injektáž závislostí součásti syntaxe Razor
author: guardrex
description: Podívejte se jak Blazor a Razor komponenty aplikace můžou používat integrované služby tak, že je vloženy do komponenty.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/29/2019
uid: razor-components/dependency-injection
ms.openlocfilehash: 6ce8fa74f20145f48797d267c20ef2593368b941
ms.sourcegitcommit: af8a6eb5375ef547a52ffae22465e265837aa82b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56159501"
---
# <a name="razor-components-dependency-injection"></a><span data-ttu-id="4689b-103">Injektáž závislostí součásti syntaxe Razor</span><span class="sxs-lookup"><span data-stu-id="4689b-103">Razor Components dependency injection</span></span>

<span data-ttu-id="4689b-104">Podle [Rainer Stropek](https://www.timecockpit.com)</span><span class="sxs-lookup"><span data-stu-id="4689b-104">By [Rainer Stropek](https://www.timecockpit.com)</span></span>

<span data-ttu-id="4689b-105">[Injektáž závislostí (DI)](/aspnet/core/fundamentals/dependency-injection) je integrovaný.</span><span class="sxs-lookup"><span data-stu-id="4689b-105">[Dependency injection (DI)](/aspnet/core/fundamentals/dependency-injection) is built-in.</span></span> <span data-ttu-id="4689b-106">Aplikace můžou používat integrované služby tak, že je vloženy do komponenty.</span><span class="sxs-lookup"><span data-stu-id="4689b-106">Apps can use built-in services by having them injected into components.</span></span> <span data-ttu-id="4689b-107">Aplikace můžete také definovat vlastní služby a zpřístupnit prostřednictvím DI.</span><span class="sxs-lookup"><span data-stu-id="4689b-107">Apps can also define custom services and make them available via DI.</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="4689b-108">Injektáž závislostí</span><span class="sxs-lookup"><span data-stu-id="4689b-108">Dependency injection</span></span>

<span data-ttu-id="4689b-109">DI je technika, pro přístup ke službám, které jsou nakonfigurované v centrálním umístění.</span><span class="sxs-lookup"><span data-stu-id="4689b-109">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="4689b-110">To může být užitečné:</span><span class="sxs-lookup"><span data-stu-id="4689b-110">This can be useful to:</span></span>

* <span data-ttu-id="4689b-111">Sdílet jednu instanci třídy služby napříč mnoha komponenty (označované jako *singleton* služby).</span><span class="sxs-lookup"><span data-stu-id="4689b-111">Share a single instance of a service class across many components (known as a *singleton* service).</span></span>
* <span data-ttu-id="4689b-112">Oddělit součásti od tříd konkrétní konkrétní službu a odkazovat pouze na abstrakce.</span><span class="sxs-lookup"><span data-stu-id="4689b-112">Decouple components from particular concrete service classes and only reference abstractions.</span></span> <span data-ttu-id="4689b-113">Například rozhraní `IDataAccess` je implementováno konkrétní třída `DataAccess`.</span><span class="sxs-lookup"><span data-stu-id="4689b-113">For example, an interface `IDataAccess` is implemented by a concrete class `DataAccess`.</span></span> <span data-ttu-id="4689b-114">Pokud součást používá DI přijímat `IDataAccess` implementace, komponenta není spojeny s konkrétní typ.</span><span class="sxs-lookup"><span data-stu-id="4689b-114">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="4689b-115">Implementace, je možné Prohodit, možná na imitovanou implementaci při testech jednotek.</span><span class="sxs-lookup"><span data-stu-id="4689b-115">The implementation can be swapped, perhaps to a mock implementation in unit tests.</span></span>

<span data-ttu-id="4689b-116">Systém DI je odpovědný za poskytnutí instance služeb součástí.</span><span class="sxs-lookup"><span data-stu-id="4689b-116">The DI system is responsible for supplying instances of services to components.</span></span> <span data-ttu-id="4689b-117">DI také odstraňuje rekurzivně závislosti, takže můžete vlastních služeb závisí na dalších služeb.</span><span class="sxs-lookup"><span data-stu-id="4689b-117">DI also resolves dependencies recursively so that services themselves can depend on further services.</span></span> <span data-ttu-id="4689b-118">DI nastaven při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="4689b-118">DI is configured during startup of the app.</span></span> <span data-ttu-id="4689b-119">Příklad je uveden dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="4689b-119">An example is shown later in this topic.</span></span>

## <a name="add-services-to-di"></a><span data-ttu-id="4689b-120">Přidání služeb do DI</span><span class="sxs-lookup"><span data-stu-id="4689b-120">Add services to DI</span></span>

<span data-ttu-id="4689b-121">Po vytvoření nové aplikace, zkontrolujte `Startup.ConfigureServices` metody:</span><span class="sxs-lookup"><span data-stu-id="4689b-121">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

<span data-ttu-id="4689b-122">`ConfigureServices` Metodě se předává [IServiceCollection](/dotnet/api/microsoft.extensions.dependencyinjection.iservicecollection), což je seznam objektů služeb popisovač ([ServiceDescriptor](/dotnet/api/microsoft.extensions.dependencyinjection.servicedescriptor)).</span><span class="sxs-lookup"><span data-stu-id="4689b-122">The `ConfigureServices` method is passed an [IServiceCollection](/dotnet/api/microsoft.extensions.dependencyinjection.iservicecollection), which is a list of service descriptor objects ([ServiceDescriptor](/dotnet/api/microsoft.extensions.dependencyinjection.servicedescriptor)).</span></span> <span data-ttu-id="4689b-123">Služby jsou přidány tím, že poskytuje služby popisovače ke kolekci služby.</span><span class="sxs-lookup"><span data-stu-id="4689b-123">Services are added by providing service descriptors to the service collection.</span></span> <span data-ttu-id="4689b-124">Následující příklad kódu ukazuje koncept:</span><span class="sxs-lookup"><span data-stu-id="4689b-124">The following code sample demonstrates the concept:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

<span data-ttu-id="4689b-125">Služby můžete nakonfigurovat následující životní cyklus:</span><span class="sxs-lookup"><span data-stu-id="4689b-125">Services can be configured with the following lifetimes:</span></span>

| <span data-ttu-id="4689b-126">Metoda</span><span class="sxs-lookup"><span data-stu-id="4689b-126">Method</span></span>      | <span data-ttu-id="4689b-127">Popis</span><span class="sxs-lookup"><span data-stu-id="4689b-127">Description</span></span> |
| ----------- | ----------- |
| [<span data-ttu-id="4689b-128">Singleton</span><span class="sxs-lookup"><span data-stu-id="4689b-128">Singleton</span></span>](/dotnet/api/microsoft.extensions.dependencyinjection.servicedescriptor.singleton#Microsoft_Extensions_DependencyInjection_ServiceDescriptor_Singleton__1_System_Func_System_IServiceProvider___0__) | <span data-ttu-id="4689b-129">Vytvoří DI *jednu instanci* služby.</span><span class="sxs-lookup"><span data-stu-id="4689b-129">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="4689b-130">Všechny součásti, které vyžadují tuto službu zobrazí odkaz na tuto instanci.</span><span class="sxs-lookup"><span data-stu-id="4689b-130">All components requiring this service receive a reference to this instance.</span></span> |
| [<span data-ttu-id="4689b-131">Transient (přechodná)</span><span class="sxs-lookup"><span data-stu-id="4689b-131">Transient</span></span>](/dotnet/api/microsoft.extensions.dependencyinjection.servicedescriptor.transient) | <span data-ttu-id="4689b-132">Pokaždé, když komponenta vyžaduje této službě, obdrží *novou instanci* služby.</span><span class="sxs-lookup"><span data-stu-id="4689b-132">Whenever a component requires this service, it receives a *new instance* of the service.</span></span> |
| [<span data-ttu-id="4689b-133">Scoped (vymezená)</span><span class="sxs-lookup"><span data-stu-id="4689b-133">Scoped</span></span>](/dotnet/api/microsoft.extensions.dependencyinjection.servicedescriptor.scoped) | <span data-ttu-id="4689b-134">Na straně klienta Blazor aktuálně nemá koncept DI oborů.</span><span class="sxs-lookup"><span data-stu-id="4689b-134">Client-side Blazor doesn't currently have the concept of DI scopes.</span></span> <span data-ttu-id="4689b-135">`Scoped` se chová jako `Singleton`.</span><span class="sxs-lookup"><span data-stu-id="4689b-135">`Scoped` behaves like `Singleton`.</span></span> <span data-ttu-id="4689b-136">Ale podporují ASP.NET Core Razor komponenty `Scoped` životnost.</span><span class="sxs-lookup"><span data-stu-id="4689b-136">However, ASP.NET Core Razor Components support the `Scoped` lifetime.</span></span> <span data-ttu-id="4689b-137">V komponentě Razor registrace vymezené služby působí na připojení.</span><span class="sxs-lookup"><span data-stu-id="4689b-137">In a Razor Component, a scoped service registration is scoped to the connection.</span></span> <span data-ttu-id="4689b-138">Z tohoto důvodu vymezené služby je upřednostňována pro služby, které by měly být omezeny na aktuální uživatel (i v případě, že aktuální záměr je spustit na straně klienta v prohlížeči).</span><span class="sxs-lookup"><span data-stu-id="4689b-138">For this reason, using scoped services is preferred for services that should be scoped to the current user (even if the current intent is to run client-side in the browser).</span></span> |

<span data-ttu-id="4689b-139">Systém DI je založen na systému DI v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4689b-139">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="4689b-140">Další informace najdete v tématu [injektáž závislostí v ASP.NET Core](/aspnet/core/fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="4689b-140">For more information, see [Dependency injection in ASP.NET Core](/aspnet/core/fundamentals/dependency-injection).</span></span>

## <a name="default-services"></a><span data-ttu-id="4689b-141">Výchozí služby</span><span class="sxs-lookup"><span data-stu-id="4689b-141">Default services</span></span>

<span data-ttu-id="4689b-142">Výchozí služby se automaticky přidají ke kolekci služby aplikace.</span><span class="sxs-lookup"><span data-stu-id="4689b-142">Default services are automatically added to the service collection of an app.</span></span> <span data-ttu-id="4689b-143">V následující tabulce jsou uvedeny některé užitečné výchozí služby k dispozici.</span><span class="sxs-lookup"><span data-stu-id="4689b-143">The following table shows some of the useful default services provided.</span></span>

| <span data-ttu-id="4689b-144">Metoda</span><span class="sxs-lookup"><span data-stu-id="4689b-144">Method</span></span>       | <span data-ttu-id="4689b-145">Popis</span><span class="sxs-lookup"><span data-stu-id="4689b-145">Description</span></span> |
| ------------ | ----------- |
| [<span data-ttu-id="4689b-146">HttpClient</span><span class="sxs-lookup"><span data-stu-id="4689b-146">HttpClient</span></span>](/dotnet/api/system.net.http.httpclient) | <span data-ttu-id="4689b-147">Poskytuje metody pro odesílání požadavků HTTP a příjem odpovědí HTTP ze zdroje identifikovaného identifikátorem URI (singleton).</span><span class="sxs-lookup"><span data-stu-id="4689b-147">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI (singleton).</span></span> <span data-ttu-id="4689b-148">Všimněte si, že tato instance `HttpClient` používá prohlížeč pro zpracování provozu HTTP na pozadí.</span><span class="sxs-lookup"><span data-stu-id="4689b-148">Note that this instance of `HttpClient` uses the browser for handling the HTTP traffic in the background.</span></span> <span data-ttu-id="4689b-149">[HttpClient.BaseAddress](/dotnet/api/system.net.http.httpclient.baseaddress) se automaticky nastaví na základní identifikátor URI předponu aplikace.</span><span class="sxs-lookup"><span data-stu-id="4689b-149">[HttpClient.BaseAddress](/dotnet/api/system.net.http.httpclient.baseaddress) is automatically set to the base URI prefix of the app.</span></span> <span data-ttu-id="4689b-150">`HttpClient` je k dispozici pouze na straně klienta Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="4689b-150">`HttpClient` is only provided to client-side Blazor apps.</span></span> |
| `IJSRuntime` | <span data-ttu-id="4689b-151">Představuje instanci modulu runtime jazyka JavaScript, ke kterému může být odesláno volání.</span><span class="sxs-lookup"><span data-stu-id="4689b-151">Represents an instance of a JavaScript runtime to which calls may be dispatched.</span></span> <span data-ttu-id="4689b-152">Další informace naleznete v tématu <xref:razor-components/javascript-interop>.</span><span class="sxs-lookup"><span data-stu-id="4689b-152">For more information, see <xref:razor-components/javascript-interop>.</span></span> |
| `IUriHelper` | <span data-ttu-id="4689b-153">Pomocné rutiny pro práci se stavem identifikátory URI a navigace (jednotlivý prvek).</span><span class="sxs-lookup"><span data-stu-id="4689b-153">Helpers for working with URIs and navigation state (singleton).</span></span> <span data-ttu-id="4689b-154">`IUriHelper` je k dispozici pro obě aplikace Blazor a ASP.NET Core Razor komponenty na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="4689b-154">`IUriHelper` is provided to both client-side Blazor and ASP.NET Core Razor Components apps.</span></span> |

<span data-ttu-id="4689b-155">Všimněte si, že je možné použít vlastní služby poskytovatele namísto výchozího zprostředkovatele služby, která je přičtena pomocí výchozí šablony.</span><span class="sxs-lookup"><span data-stu-id="4689b-155">Note that it is possible to use a custom services provider instead of the default service provider that's added by the default template.</span></span> <span data-ttu-id="4689b-156">Vlastní poskytovatele neposkytuje automaticky výchozí služby uvedené v tabulce.</span><span class="sxs-lookup"><span data-stu-id="4689b-156">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="4689b-157">Tyto služby musíte explicitně přidat nového poskytovatele služby.</span><span class="sxs-lookup"><span data-stu-id="4689b-157">Those services must be added to the new service provider explicitly.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="4689b-158">Žádosti o službu v komponentě</span><span class="sxs-lookup"><span data-stu-id="4689b-158">Request a service in a component</span></span>

<span data-ttu-id="4689b-159">Jakmile služby jsou přidány do kolekce služeb, může být vloženy do komponenty šablony Razor pomocí `@inject` direktivu Razor.</span><span class="sxs-lookup"><span data-stu-id="4689b-159">Once services are added to the service collection, they can be injected into the components' Razor templates using the `@inject` Razor directive.</span></span> <span data-ttu-id="4689b-160">`@inject` má dva parametry:</span><span class="sxs-lookup"><span data-stu-id="4689b-160">`@inject` has two parameters:</span></span>

* <span data-ttu-id="4689b-161">Název typu: Typ služby k vložení.</span><span class="sxs-lookup"><span data-stu-id="4689b-161">Type name: The type of the service to inject.</span></span>
* <span data-ttu-id="4689b-162">Název vlastnosti: Název vlastnosti příjem service vložené aplikace.</span><span class="sxs-lookup"><span data-stu-id="4689b-162">Property name: The name of the property receiving the injected app service.</span></span> <span data-ttu-id="4689b-163">Všimněte si, že vlastnost nevyžaduje ruční vytvoření.</span><span class="sxs-lookup"><span data-stu-id="4689b-163">Note that the property doesn't require manual creation.</span></span> <span data-ttu-id="4689b-164">Kompilátor vytvoří vlastnost.</span><span class="sxs-lookup"><span data-stu-id="4689b-164">The compiler creates the property.</span></span>

<span data-ttu-id="4689b-165">Více `@inject` příkazy lze vložit různé služby.</span><span class="sxs-lookup"><span data-stu-id="4689b-165">Multiple `@inject` statements can be used to inject different services.</span></span>

<span data-ttu-id="4689b-166">Následující příklad ukazuje, jak používat `@inject`.</span><span class="sxs-lookup"><span data-stu-id="4689b-166">The following example shows how to use `@inject`.</span></span> <span data-ttu-id="4689b-167">Implementace služby `Services.IDataAccess` se vloží do komponenty vlastnosti `DataRepository`.</span><span class="sxs-lookup"><span data-stu-id="4689b-167">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="4689b-168">Všimněte si, jak kód využívá jenom `IDataAccess` abstrakce:</span><span class="sxs-lookup"><span data-stu-id="4689b-168">Note how the code is only using the `IDataAccess` abstraction:</span></span>

```csharp
@page "/customer-list"
@using Services
@inject IDataAccess DataRepository

<ul>
    @if (Customers != null)
    {
        @foreach (var customer in Customers)
        {
            <li>@customer.FirstName @customer.LastName</li>
        }
    }
</ul>

@functions {
    private IReadOnlyList<Customer> Customers;

    protected override async Task OnInitAsync()
    {
        // The property DataRepository received an implementation
        // of IDataAccess through dependency injection. Use 
        // DataRepository to obtain data from the server.
        Customers = await DataRepository.GetAllCustomersAsync();
    }
}
```

<span data-ttu-id="4689b-169">Interně jsou vygenerované vlastnosti (`DataRepository`) je upravená pomocí `InjectAttribute` atribut.</span><span class="sxs-lookup"><span data-stu-id="4689b-169">Internally, the generated property (`DataRepository`) is decorated with the `InjectAttribute` attribute.</span></span> <span data-ttu-id="4689b-170">Tento atribut se obvykle nepoužívá přímo.</span><span class="sxs-lookup"><span data-stu-id="4689b-170">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="4689b-171">Pokud základní třída je povinné pro součásti a vloženého vlastnosti se rovněž vyžadují pro základní třídu `InjectAttribute` můžete ručně přidat:</span><span class="sxs-lookup"><span data-stu-id="4689b-171">If a base class is required for components and injected properties are also required for the base class, `InjectAttribute` can be manually added:</span></span>

```csharp
public class ComponentBase : BlazorComponent
{
    // Dependency injection works even if using the
    // InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

<span data-ttu-id="4689b-172">V součásti odvozené ze základní třídy `@inject` – direktiva není povinné.</span><span class="sxs-lookup"><span data-stu-id="4689b-172">In components derived from the base class, the `@inject` directive isn't required.</span></span> <span data-ttu-id="4689b-173">`InjectAttribute` Základní třídy je dostačující:</span><span class="sxs-lookup"><span data-stu-id="4689b-173">The `InjectAttribute` of the base class is sufficient:</span></span>

```csharp
@page "/demo"
@inherits ComponentBase

<h1>...</h1>
...
```

## <a name="dependency-injection-in-services"></a><span data-ttu-id="4689b-174">Injektáž závislostí služby</span><span class="sxs-lookup"><span data-stu-id="4689b-174">Dependency injection in services</span></span>

<span data-ttu-id="4689b-175">Komplexní služby můžou vyžadovat další služby.</span><span class="sxs-lookup"><span data-stu-id="4689b-175">Complex services might require additional services.</span></span> <span data-ttu-id="4689b-176">V předchozím příkladu `DataAccess` může vyžadovat `HttpClient` výchozí služby.</span><span class="sxs-lookup"><span data-stu-id="4689b-176">In the prior example, `DataAccess` might require the `HttpClient` default service.</span></span> <span data-ttu-id="4689b-177">`@inject` nebo `InjectAttribute` nelze dají využívat ve službách.</span><span class="sxs-lookup"><span data-stu-id="4689b-177">`@inject` or the `InjectAttribute` can't be used in services.</span></span> <span data-ttu-id="4689b-178">*Konstruktor vkládání* musí použít.</span><span class="sxs-lookup"><span data-stu-id="4689b-178">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="4689b-179">Přidat parametry do konstruktoru služby přidají požadované služby.</span><span class="sxs-lookup"><span data-stu-id="4689b-179">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="4689b-180">Při vkládání závislostí vytvoří službu, rozpozná služby vyžaduje v konstruktoru a poskytuje je odpovídajícím způsobem.</span><span class="sxs-lookup"><span data-stu-id="4689b-180">When dependency injection creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span>

<span data-ttu-id="4689b-181">Následující příklad kódu ukazuje koncept:</span><span class="sxs-lookup"><span data-stu-id="4689b-181">The following code sample demonstrates the concept:</span></span>

```csharp
public class DataAccess : IDataAccess
{
    // The constructor receives an HttpClient via dependency
    // injection. HttpClient is a default service.
    public DataAccess(HttpClient client)
    {
        ...
    }
    ...
}
```

<span data-ttu-id="4689b-182">Vezměte na vědomí následující požadavky pro vkládání konstruktor:</span><span class="sxs-lookup"><span data-stu-id="4689b-182">Note the following prerequisites for constructor injection:</span></span>

* <span data-ttu-id="4689b-183">Musí existovat jeden konstruktor, jehož argumenty lze všechny splnit vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="4689b-183">There must be one constructor whose arguments can all be fulfilled by dependency injection.</span></span> <span data-ttu-id="4689b-184">Všimněte si, že není pokrytá DI další parametry jsou povoleny, pokud jsou výchozí hodnoty zadané pro ně.</span><span class="sxs-lookup"><span data-stu-id="4689b-184">Note that additional parameters not covered by DI are allowed if default values are specified for them.</span></span>
* <span data-ttu-id="4689b-185">Použít konstruktor musí být *veřejné*.</span><span class="sxs-lookup"><span data-stu-id="4689b-185">The applicable constructor must be *public*.</span></span>
* <span data-ttu-id="4689b-186">Musí existovat pouze jeden použít konstruktor.</span><span class="sxs-lookup"><span data-stu-id="4689b-186">There must only be one applicable constructor.</span></span> <span data-ttu-id="4689b-187">V případě nejednoznačnost DI vyvolá výjimku.</span><span class="sxs-lookup"><span data-stu-id="4689b-187">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4689b-188">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="4689b-188">Additional resources</span></span>

* [<span data-ttu-id="4689b-189">Injektáž závislostí v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4689b-189">Dependency injection in ASP.NET Core</span></span>](/aspnet/core/fundamentals/dependency-injection)

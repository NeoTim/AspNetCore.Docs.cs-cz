---
title: Injektáž závislostí součásti syntaxe Razor
author: guardrex
description: Podívejte se jak Blazor a Razor komponenty aplikace můžou používat služby ve vkládání do komponenty.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/19/2019
uid: razor-components/dependency-injection
ms.openlocfilehash: 804fdf0254723f5e5913f23c815f485bbf094321
ms.sourcegitcommit: d913bca90373c07f89b1d1df01af5fc01fc908ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2019
ms.locfileid: "57978500"
---
# <a name="razor-components-dependency-injection"></a><span data-ttu-id="bb309-103">Injektáž závislostí součásti syntaxe Razor</span><span class="sxs-lookup"><span data-stu-id="bb309-103">Razor Components dependency injection</span></span>

<span data-ttu-id="bb309-104">Podle [Rainer Stropek](https://www.timecockpit.com)</span><span class="sxs-lookup"><span data-stu-id="bb309-104">By [Rainer Stropek](https://www.timecockpit.com)</span></span>

<span data-ttu-id="bb309-105">Podporuje Razor komponenty [injektáž závislostí (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="bb309-105">Razor Components supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="bb309-106">Aplikace můžete použít integrované služby vkládání do komponenty.</span><span class="sxs-lookup"><span data-stu-id="bb309-106">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="bb309-107">Aplikace můžete také definovat a registrovat vlastní služby a zpřístupnit je v celé aplikaci prostřednictvím DI.</span><span class="sxs-lookup"><span data-stu-id="bb309-107">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="bb309-108">Injektáž závislostí</span><span class="sxs-lookup"><span data-stu-id="bb309-108">Dependency injection</span></span>

<span data-ttu-id="bb309-109">DI je technika, pro přístup ke službám, které jsou nakonfigurované v centrálním umístění.</span><span class="sxs-lookup"><span data-stu-id="bb309-109">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="bb309-110">To může být užitečné v Razor součásti aplikace:</span><span class="sxs-lookup"><span data-stu-id="bb309-110">This can be useful in Razor Components apps to:</span></span>

* <span data-ttu-id="bb309-111">Sdílet jednu instanci třídy služby mezi řadu součástí, označované jako *singleton* služby.</span><span class="sxs-lookup"><span data-stu-id="bb309-111">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="bb309-112">Pomocí odkazu abstrakce oddělte součásti od tříd konkrétní služby.</span><span class="sxs-lookup"><span data-stu-id="bb309-112">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="bb309-113">Představte si třeba rozhraní `IDataAccess` pro přístup k datům v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="bb309-113">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="bb309-114">Rozhraní je implementováno konkrétní `DataAccess` třídy a zaregistrováno jako služba v kontejneru aplikace služby.</span><span class="sxs-lookup"><span data-stu-id="bb309-114">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="bb309-115">Pokud součást používá DI přijímat `IDataAccess` implementace, komponenta není spojeny s konkrétní typ.</span><span class="sxs-lookup"><span data-stu-id="bb309-115">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="bb309-116">Implementace, je možné Prohodit, možná na imitovanou implementaci při testech jednotek.</span><span class="sxs-lookup"><span data-stu-id="bb309-116">The implementation can be swapped, perhaps to a mock implementation in unit tests.</span></span>

<span data-ttu-id="bb309-117">Další informace naleznete v tématu <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="bb309-117">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="add-services-to-di"></a><span data-ttu-id="bb309-118">Přidání služeb do DI</span><span class="sxs-lookup"><span data-stu-id="bb309-118">Add services to DI</span></span>

<span data-ttu-id="bb309-119">Po vytvoření nové aplikace, zkontrolujte `Startup.ConfigureServices` metody:</span><span class="sxs-lookup"><span data-stu-id="bb309-119">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

<span data-ttu-id="bb309-120">`ConfigureServices` Metodě se předává <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, což je seznam objektů služeb popisovač (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span><span class="sxs-lookup"><span data-stu-id="bb309-120">The `ConfigureServices` method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="bb309-121">Služby jsou přidány tím, že poskytuje služby popisovače ke kolekci služby.</span><span class="sxs-lookup"><span data-stu-id="bb309-121">Services are added by providing service descriptors to the service collection.</span></span> <span data-ttu-id="bb309-122">Následující příklad ukazuje neznají koncept `IDataAccess` rozhraní a jeho konkrétní implementaci `DataAccess`:</span><span class="sxs-lookup"><span data-stu-id="bb309-122">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

<span data-ttu-id="bb309-123">Služby můžete nakonfigurovat s životností je znázorněno v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="bb309-123">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="bb309-124">Životnost</span><span class="sxs-lookup"><span data-stu-id="bb309-124">Lifetime</span></span> | <span data-ttu-id="bb309-125">Popis</span><span class="sxs-lookup"><span data-stu-id="bb309-125">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton*> | <span data-ttu-id="bb309-126">Vytvoří DI *jednu instanci* služby.</span><span class="sxs-lookup"><span data-stu-id="bb309-126">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="bb309-127">Všechny součásti, které vyžadují tuto službu zobrazí odkaz na tuto instanci.</span><span class="sxs-lookup"><span data-stu-id="bb309-127">All components requiring this service receive a reference to this instance.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient*> | <span data-ttu-id="bb309-128">Pokaždé, když komponenta vyžaduje této službě, obdrží *novou instanci* služby.</span><span class="sxs-lookup"><span data-stu-id="bb309-128">Whenever a component requires this service, it receives a *new instance* of the service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped*> | <span data-ttu-id="bb309-129">Na straně klienta Blazor aktuálně nemá koncept DI oborů.</span><span class="sxs-lookup"><span data-stu-id="bb309-129">Client-side Blazor doesn't currently have the concept of DI scopes.</span></span> <span data-ttu-id="bb309-130">`Scoped` se chová jako `Singleton`.</span><span class="sxs-lookup"><span data-stu-id="bb309-130">`Scoped` behaves like `Singleton`.</span></span> <span data-ttu-id="bb309-131">Ale podporují ASP.NET Core Razor komponenty `Scoped` životnost.</span><span class="sxs-lookup"><span data-stu-id="bb309-131">However, ASP.NET Core Razor Components support the `Scoped` lifetime.</span></span> <span data-ttu-id="bb309-132">V komponentě Razor registrace vymezené služby působí na připojení.</span><span class="sxs-lookup"><span data-stu-id="bb309-132">In a Razor Component, a scoped service registration is scoped to the connection.</span></span> <span data-ttu-id="bb309-133">Z tohoto důvodu vymezené služby je upřednostňována pro služby, které by měly být omezeny aktuálního uživatele, i v případě, že aktuální záměr je spustit na straně klienta v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="bb309-133">For this reason, using scoped services is preferred for services that should be scoped to the current user, even if the current intent is to run client-side in the browser.</span></span> |

<span data-ttu-id="bb309-134">Systém DI je založen na systému DI v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bb309-134">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="bb309-135">Další informace naleznete v tématu <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="bb309-135">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="default-services"></a><span data-ttu-id="bb309-136">Výchozí služby</span><span class="sxs-lookup"><span data-stu-id="bb309-136">Default services</span></span>

<span data-ttu-id="bb309-137">Výchozí služby jsou automaticky přidány do aplikace služby kolekce.</span><span class="sxs-lookup"><span data-stu-id="bb309-137">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="bb309-138">Služba</span><span class="sxs-lookup"><span data-stu-id="bb309-138">Service</span></span> | <span data-ttu-id="bb309-139">Popis</span><span class="sxs-lookup"><span data-stu-id="bb309-139">Description</span></span> |
| ------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="bb309-140">Poskytuje metody pro odesílání požadavků HTTP a příjem odpovědí HTTP ze zdroje identifikovaného identifikátorem URI (singleton).</span><span class="sxs-lookup"><span data-stu-id="bb309-140">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI (singleton).</span></span> <span data-ttu-id="bb309-141">Všimněte si, že tato instance `HttpClient` používá prohlížeč pro zpracování provozu HTTP na pozadí.</span><span class="sxs-lookup"><span data-stu-id="bb309-141">Note that this instance of `HttpClient` uses the browser for handling the HTTP traffic in the background.</span></span> <span data-ttu-id="bb309-142">[HttpClient.BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress) se automaticky nastaví na základní identifikátor URI předponu aplikace.</span><span class="sxs-lookup"><span data-stu-id="bb309-142">[HttpClient.BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress) is automatically set to the base URI prefix of the app.</span></span> <span data-ttu-id="bb309-143">`HttpClient` je k dispozici pouze na straně klienta Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="bb309-143">`HttpClient` is only provided to client-side Blazor apps.</span></span> |
| `IJSRuntime` | <span data-ttu-id="bb309-144">Představuje instanci modulu runtime jazyka JavaScript, ke kterému může být odesláno volání.</span><span class="sxs-lookup"><span data-stu-id="bb309-144">Represents an instance of a JavaScript runtime to which calls may be dispatched.</span></span> <span data-ttu-id="bb309-145">Další informace naleznete v tématu <xref:razor-components/javascript-interop>.</span><span class="sxs-lookup"><span data-stu-id="bb309-145">For more information, see <xref:razor-components/javascript-interop>.</span></span> |
| `IUriHelper` | <span data-ttu-id="bb309-146">Obsahuje pomocné rutiny pro práci se stavem identifikátory URI a navigace (jednotlivý prvek).</span><span class="sxs-lookup"><span data-stu-id="bb309-146">Contains helpers for working with URIs and navigation state (singleton).</span></span> <span data-ttu-id="bb309-147">`IUriHelper` je k dispozici na Blazor a Razor součásti aplikace.</span><span class="sxs-lookup"><span data-stu-id="bb309-147">`IUriHelper` is provided to both Blazor and Razor Components apps.</span></span> |

<span data-ttu-id="bb309-148">Je možné použít vlastní poskytovatele namísto výchozího poskytovatele služby přidá výchozí šablony.</span><span class="sxs-lookup"><span data-stu-id="bb309-148">It's possible to use a custom service provider instead of the default service provider added by the default template.</span></span> <span data-ttu-id="bb309-149">Vlastní poskytovatele neposkytuje automaticky výchozí služby uvedené v tabulce.</span><span class="sxs-lookup"><span data-stu-id="bb309-149">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="bb309-150">Pokud používáte vlastní poskytovatele a kterékoli ze služeb, které jsou uvedené v tabulce vyžadují, přidejte do nového poskytovatele služby požadované služby.</span><span class="sxs-lookup"><span data-stu-id="bb309-150">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="bb309-151">Žádosti o službu v komponentě</span><span class="sxs-lookup"><span data-stu-id="bb309-151">Request a service in a component</span></span>

<span data-ttu-id="bb309-152">Po přidání služby do kolekce služeb, vložit do šablony Razor komponenty pomocí služeb [ @inject ](xref:mvc/views/razor#section-4) direktivu Razor.</span><span class="sxs-lookup"><span data-stu-id="bb309-152">After services are added to the service collection, inject the services into the components' Razor templates using the [@inject](xref:mvc/views/razor#section-4) Razor directive.</span></span> <span data-ttu-id="bb309-153">`@inject` má dva parametry:</span><span class="sxs-lookup"><span data-stu-id="bb309-153">`@inject` has two parameters:</span></span>

* <span data-ttu-id="bb309-154">Název typu: Typ služby k vložení.</span><span class="sxs-lookup"><span data-stu-id="bb309-154">Type name: The type of the service to inject.</span></span>
* <span data-ttu-id="bb309-155">Název vlastnosti: Název vlastnosti příjem service vložené aplikace.</span><span class="sxs-lookup"><span data-stu-id="bb309-155">Property name: The name of the property receiving the injected app service.</span></span> <span data-ttu-id="bb309-156">Všimněte si, že vlastnost nevyžaduje ruční vytvoření.</span><span class="sxs-lookup"><span data-stu-id="bb309-156">Note that the property doesn't require manual creation.</span></span> <span data-ttu-id="bb309-157">Kompilátor vytvoří vlastnost.</span><span class="sxs-lookup"><span data-stu-id="bb309-157">The compiler creates the property.</span></span>

<span data-ttu-id="bb309-158">Další informace naleznete v tématu <xref:mvc/views/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="bb309-158">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="bb309-159">Použití více `@inject` příkazy vkládat různé služby.</span><span class="sxs-lookup"><span data-stu-id="bb309-159">Use multiple `@inject` statements to inject different services.</span></span>

<span data-ttu-id="bb309-160">Následující příklad ukazuje, jak používat `@inject`.</span><span class="sxs-lookup"><span data-stu-id="bb309-160">The following example shows how to use `@inject`.</span></span> <span data-ttu-id="bb309-161">Implementace služby `Services.IDataAccess` se vloží do komponenty vlastnosti `DataRepository`.</span><span class="sxs-lookup"><span data-stu-id="bb309-161">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="bb309-162">Všimněte si, jak kód využívá jenom `IDataAccess` abstrakce:</span><span class="sxs-lookup"><span data-stu-id="bb309-162">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-cshtml[](dependency-injection/samples_snapshot/3.x/CustomerList.cshtml?highlight=2-3,23)]

<span data-ttu-id="bb309-163">Interně jsou vygenerované vlastnosti (`DataRepository`) je upravená pomocí `InjectAttribute` atribut.</span><span class="sxs-lookup"><span data-stu-id="bb309-163">Internally, the generated property (`DataRepository`) is decorated with the `InjectAttribute` attribute.</span></span> <span data-ttu-id="bb309-164">Tento atribut se obvykle nepoužívá přímo.</span><span class="sxs-lookup"><span data-stu-id="bb309-164">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="bb309-165">Pokud základní třída je povinné pro součásti a vloženého vlastnosti se rovněž vyžadují pro základní třídu `InjectAttribute` můžete ručně přidat:</span><span class="sxs-lookup"><span data-stu-id="bb309-165">If a base class is required for components and injected properties are also required for the base class, `InjectAttribute` can be manually added:</span></span>

```csharp
public class ComponentBase : IComponent
{
    // Dependency injection works even if using the
    // InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

<span data-ttu-id="bb309-166">V součásti odvozené ze základní třídy `@inject` – direktiva není povinné.</span><span class="sxs-lookup"><span data-stu-id="bb309-166">In components derived from the base class, the `@inject` directive isn't required.</span></span> <span data-ttu-id="bb309-167">`InjectAttribute` Základní třídy je dostačující:</span><span class="sxs-lookup"><span data-stu-id="bb309-167">The `InjectAttribute` of the base class is sufficient:</span></span>

```cshtml
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="dependency-injection-in-services"></a><span data-ttu-id="bb309-168">Injektáž závislostí služby</span><span class="sxs-lookup"><span data-stu-id="bb309-168">Dependency injection in services</span></span>

<span data-ttu-id="bb309-169">Komplexní služby můžou vyžadovat další služby.</span><span class="sxs-lookup"><span data-stu-id="bb309-169">Complex services might require additional services.</span></span> <span data-ttu-id="bb309-170">V předchozím příkladu `DataAccess` může vyžadovat `HttpClient` výchozí služby.</span><span class="sxs-lookup"><span data-stu-id="bb309-170">In the prior example, `DataAccess` might require the `HttpClient` default service.</span></span> <span data-ttu-id="bb309-171">`@inject` (nebo `InjectAttribute`) není k dispozici pro použití služby.</span><span class="sxs-lookup"><span data-stu-id="bb309-171">`@inject` (or the `InjectAttribute`) isn't available for use in services.</span></span> <span data-ttu-id="bb309-172">*Konstruktor vkládání* musí použít.</span><span class="sxs-lookup"><span data-stu-id="bb309-172">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="bb309-173">Přidat parametry do konstruktoru služby přidají požadované služby.</span><span class="sxs-lookup"><span data-stu-id="bb309-173">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="bb309-174">Při vkládání závislostí vytvoří službu, rozpozná služby vyžaduje v konstruktoru a poskytuje je odpovídajícím způsobem.</span><span class="sxs-lookup"><span data-stu-id="bb309-174">When dependency injection creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span>

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

<span data-ttu-id="bb309-175">Předpoklady pro vkládání konstruktor:</span><span class="sxs-lookup"><span data-stu-id="bb309-175">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="bb309-176">Musí existovat jeden konstruktor, jehož argumenty lze všechny splnit vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="bb309-176">There must be one constructor whose arguments can all be fulfilled by dependency injection.</span></span> <span data-ttu-id="bb309-177">Všimněte si, že není pokrytá DI další parametry jsou povoleny, pokud se určení výchozích hodnot.</span><span class="sxs-lookup"><span data-stu-id="bb309-177">Note that additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="bb309-178">Použít konstruktor musí být *veřejné*.</span><span class="sxs-lookup"><span data-stu-id="bb309-178">The applicable constructor must be *public*.</span></span>
* <span data-ttu-id="bb309-179">Musí existovat pouze jeden použít konstruktor.</span><span class="sxs-lookup"><span data-stu-id="bb309-179">There must only be one applicable constructor.</span></span> <span data-ttu-id="bb309-180">V případě nejednoznačnost DI vyvolá výjimku.</span><span class="sxs-lookup"><span data-stu-id="bb309-180">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bb309-181">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="bb309-181">Additional resources</span></span>

* <span data-ttu-id="bb309-182">< xref:fundamentals / injektáž závislostí</span><span class="sxs-lookup"><span data-stu-id="bb309-182"><xref:fundamentals/dependency-injection</span></span>
* <xref:mvc/views/dependency-injection>

---
title: Injektáž závislostí Blazor
author: guardrex
description: Podívejte se, jak aplikace Blazor služby vložit do komponenty.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/24/2019
uid: blazor/dependency-injection
ms.openlocfilehash: 49948cd8e31473a4901957356d372d49fc3b0f5f
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64898467"
---
# <a name="blazor-dependency-injection"></a><span data-ttu-id="bf4ff-103">Injektáž závislostí Blazor</span><span class="sxs-lookup"><span data-stu-id="bf4ff-103">Blazor dependency injection</span></span>

<span data-ttu-id="bf4ff-104">Podle [Rainer Stropek](https://www.timecockpit.com)</span><span class="sxs-lookup"><span data-stu-id="bf4ff-104">By [Rainer Stropek](https://www.timecockpit.com)</span></span>

<span data-ttu-id="bf4ff-105">Podporuje Blazor [injektáž závislostí (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="bf4ff-105">Blazor supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="bf4ff-106">Aplikace můžete použít integrované služby vkládání do komponenty.</span><span class="sxs-lookup"><span data-stu-id="bf4ff-106">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="bf4ff-107">Aplikace můžete také definovat a registrovat vlastní služby a zpřístupnit je v celé aplikaci prostřednictvím DI.</span><span class="sxs-lookup"><span data-stu-id="bf4ff-107">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="bf4ff-108">Injektáž závislostí</span><span class="sxs-lookup"><span data-stu-id="bf4ff-108">Dependency injection</span></span>

<span data-ttu-id="bf4ff-109">DI je technika, pro přístup ke službám, které jsou nakonfigurované v centrálním umístění.</span><span class="sxs-lookup"><span data-stu-id="bf4ff-109">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="bf4ff-110">To může být užitečné v Blazor aplikací:</span><span class="sxs-lookup"><span data-stu-id="bf4ff-110">This can be useful in Blazor apps to:</span></span>

* <span data-ttu-id="bf4ff-111">Sdílet jednu instanci třídy služby mezi řadu součástí, označované jako *singleton* služby.</span><span class="sxs-lookup"><span data-stu-id="bf4ff-111">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="bf4ff-112">Pomocí odkazu abstrakce oddělte součásti od tříd konkrétní služby.</span><span class="sxs-lookup"><span data-stu-id="bf4ff-112">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="bf4ff-113">Představte si třeba rozhraní `IDataAccess` pro přístup k datům v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="bf4ff-113">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="bf4ff-114">Rozhraní je implementováno konkrétní `DataAccess` třídy a zaregistrováno jako služba v kontejneru aplikace služby.</span><span class="sxs-lookup"><span data-stu-id="bf4ff-114">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="bf4ff-115">Pokud součást používá DI přijímat `IDataAccess` implementace, komponenta není spojeny s konkrétní typ.</span><span class="sxs-lookup"><span data-stu-id="bf4ff-115">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="bf4ff-116">Implementace, je možné Prohodit, možná na imitovanou implementaci při testech jednotek.</span><span class="sxs-lookup"><span data-stu-id="bf4ff-116">The implementation can be swapped, perhaps to a mock implementation in unit tests.</span></span>

<span data-ttu-id="bf4ff-117">Další informace naleznete v tématu <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="bf4ff-117">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="add-services-to-di"></a><span data-ttu-id="bf4ff-118">Přidání služeb do DI</span><span class="sxs-lookup"><span data-stu-id="bf4ff-118">Add services to DI</span></span>

<span data-ttu-id="bf4ff-119">Po vytvoření nové aplikace, zkontrolujte `Startup.ConfigureServices` metody:</span><span class="sxs-lookup"><span data-stu-id="bf4ff-119">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

<span data-ttu-id="bf4ff-120">`ConfigureServices` Metodě se předává <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, což je seznam objektů služeb popisovač (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span><span class="sxs-lookup"><span data-stu-id="bf4ff-120">The `ConfigureServices` method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="bf4ff-121">Služby jsou přidány tím, že poskytuje služby popisovače ke kolekci služby.</span><span class="sxs-lookup"><span data-stu-id="bf4ff-121">Services are added by providing service descriptors to the service collection.</span></span> <span data-ttu-id="bf4ff-122">Následující příklad ukazuje neznají koncept `IDataAccess` rozhraní a jeho konkrétní implementaci `DataAccess`:</span><span class="sxs-lookup"><span data-stu-id="bf4ff-122">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

<span data-ttu-id="bf4ff-123">Služby můžete nakonfigurovat s životností je znázorněno v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="bf4ff-123">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="bf4ff-124">Životnost</span><span class="sxs-lookup"><span data-stu-id="bf4ff-124">Lifetime</span></span> | <span data-ttu-id="bf4ff-125">Popis</span><span class="sxs-lookup"><span data-stu-id="bf4ff-125">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton*> | <span data-ttu-id="bf4ff-126">Vytvoří DI *jednu instanci* služby.</span><span class="sxs-lookup"><span data-stu-id="bf4ff-126">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="bf4ff-127">Všechny součásti, které vyžadují `Singleton` služba přijímat instance ve stejné službě.</span><span class="sxs-lookup"><span data-stu-id="bf4ff-127">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient*> | <span data-ttu-id="bf4ff-128">Vždy, když součást získá instanci `Transient` služby z kontejneru služeb přijme *novou instanci* služby.</span><span class="sxs-lookup"><span data-stu-id="bf4ff-128">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped*> | <span data-ttu-id="bf4ff-129">Blazor klientů aktuálně nemá koncept DI oborů.</span><span class="sxs-lookup"><span data-stu-id="bf4ff-129">Blazor client-side doesn't currently have the concept of DI scopes.</span></span> <span data-ttu-id="bf4ff-130">`Scoped` se chová jako `Singleton`.</span><span class="sxs-lookup"><span data-stu-id="bf4ff-130">`Scoped` behaves like `Singleton`.</span></span> <span data-ttu-id="bf4ff-131">Ale podporuje model hostingu na straně serveru `Scoped` životnost.</span><span class="sxs-lookup"><span data-stu-id="bf4ff-131">However, the server-side hosting model supports the `Scoped` lifetime.</span></span> <span data-ttu-id="bf4ff-132">V komponentě Razor registrace vymezené služby působí na připojení.</span><span class="sxs-lookup"><span data-stu-id="bf4ff-132">In a Razor component, a scoped service registration is scoped to the connection.</span></span> <span data-ttu-id="bf4ff-133">Z tohoto důvodu vymezené služby je upřednostňována pro služby, které by měly být omezeny aktuálního uživatele, i v případě, že aktuální záměr je spustit na straně klienta v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="bf4ff-133">For this reason, using scoped services is preferred for services that should be scoped to the current user, even if the current intent is to run client-side in the browser.</span></span> |

<span data-ttu-id="bf4ff-134">Systém DI je založen na systému DI v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bf4ff-134">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="bf4ff-135">Další informace naleznete v tématu <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="bf4ff-135">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="default-services"></a><span data-ttu-id="bf4ff-136">Výchozí služby</span><span class="sxs-lookup"><span data-stu-id="bf4ff-136">Default services</span></span>

<span data-ttu-id="bf4ff-137">Výchozí služby jsou automaticky přidány do aplikace služby kolekce.</span><span class="sxs-lookup"><span data-stu-id="bf4ff-137">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="bf4ff-138">Služba</span><span class="sxs-lookup"><span data-stu-id="bf4ff-138">Service</span></span> | <span data-ttu-id="bf4ff-139">Životnost</span><span class="sxs-lookup"><span data-stu-id="bf4ff-139">Lifetime</span></span> | <span data-ttu-id="bf4ff-140">Popis</span><span class="sxs-lookup"><span data-stu-id="bf4ff-140">Description</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="bf4ff-141">Singleton</span><span class="sxs-lookup"><span data-stu-id="bf4ff-141">Singleton</span></span> | <span data-ttu-id="bf4ff-142">Poskytuje metody pro odesílání požadavků HTTP a příjem odpovědí HTTP ze zdroje identifikovaného identifikátorem URI.</span><span class="sxs-lookup"><span data-stu-id="bf4ff-142">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span> <span data-ttu-id="bf4ff-143">Všimněte si, že tato instance `HttpClient` používá prohlížeč pro zpracování provozu HTTP na pozadí.</span><span class="sxs-lookup"><span data-stu-id="bf4ff-143">Note that this instance of `HttpClient` uses the browser for handling the HTTP traffic in the background.</span></span> <span data-ttu-id="bf4ff-144">[HttpClient.BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress) se automaticky nastaví na základní identifikátor URI předponu aplikace.</span><span class="sxs-lookup"><span data-stu-id="bf4ff-144">[HttpClient.BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress) is automatically set to the base URI prefix of the app.</span></span> <span data-ttu-id="bf4ff-145">`HttpClient` je k dispozici pouze na Blazor aplikace na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="bf4ff-145">`HttpClient` is only provided to Blazor client-side apps.</span></span> |
| `IJSRuntime` | <span data-ttu-id="bf4ff-146">Singleton</span><span class="sxs-lookup"><span data-stu-id="bf4ff-146">Singleton</span></span> | <span data-ttu-id="bf4ff-147">Představuje instanci modulu runtime jazyka JavaScript, kde jsou odeslány volání JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="bf4ff-147">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="bf4ff-148">Další informace naleznete v tématu <xref:blazor/javascript-interop>.</span><span class="sxs-lookup"><span data-stu-id="bf4ff-148">For more information, see <xref:blazor/javascript-interop>.</span></span> |
| `IUriHelper` | <span data-ttu-id="bf4ff-149">Singleton</span><span class="sxs-lookup"><span data-stu-id="bf4ff-149">Singleton</span></span> | <span data-ttu-id="bf4ff-150">Obsahuje pomocné rutiny pro práci s identifikátory URI a navigační stav.</span><span class="sxs-lookup"><span data-stu-id="bf4ff-150">Contains helpers for working with URIs and navigation state.</span></span> |

<span data-ttu-id="bf4ff-151">Vlastní poskytovatele neposkytuje automaticky výchozí služby uvedené v tabulce.</span><span class="sxs-lookup"><span data-stu-id="bf4ff-151">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="bf4ff-152">Pokud používáte vlastní poskytovatele a kterékoli ze služeb, které jsou uvedené v tabulce vyžadují, přidejte do nového poskytovatele služby požadované služby.</span><span class="sxs-lookup"><span data-stu-id="bf4ff-152">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="bf4ff-153">Žádosti o službu v komponentě</span><span class="sxs-lookup"><span data-stu-id="bf4ff-153">Request a service in a component</span></span>

<span data-ttu-id="bf4ff-154">Po přidání služby do kolekce služeb, vložit do šablony Razor komponenty pomocí služeb [ \@vložit](xref:mvc/views/razor#section-4) direktivu Razor.</span><span class="sxs-lookup"><span data-stu-id="bf4ff-154">After services are added to the service collection, inject the services into the components' Razor templates using the [\@inject](xref:mvc/views/razor#section-4) Razor directive.</span></span> <span data-ttu-id="bf4ff-155">`@inject` má dva parametry:</span><span class="sxs-lookup"><span data-stu-id="bf4ff-155">`@inject` has two parameters:</span></span>

* <span data-ttu-id="bf4ff-156">Zadejte: Typ služby k vložení.</span><span class="sxs-lookup"><span data-stu-id="bf4ff-156">Type: The type of the service to inject.</span></span>
* <span data-ttu-id="bf4ff-157">Vlastnost: Název vlastnosti příjem service vložené aplikace.</span><span class="sxs-lookup"><span data-stu-id="bf4ff-157">Property: The name of the property receiving the injected app service.</span></span> <span data-ttu-id="bf4ff-158">Vlastnost nevyžaduje ruční vytvoření.</span><span class="sxs-lookup"><span data-stu-id="bf4ff-158">The property doesn't require manual creation.</span></span> <span data-ttu-id="bf4ff-159">Kompilátor vytvoří vlastnost.</span><span class="sxs-lookup"><span data-stu-id="bf4ff-159">The compiler creates the property.</span></span>

<span data-ttu-id="bf4ff-160">Další informace naleznete v tématu <xref:mvc/views/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="bf4ff-160">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="bf4ff-161">Použití více `@inject` příkazy vkládat různé služby.</span><span class="sxs-lookup"><span data-stu-id="bf4ff-161">Use multiple `@inject` statements to inject different services.</span></span>

<span data-ttu-id="bf4ff-162">Následující příklad ukazuje, jak používat `@inject`.</span><span class="sxs-lookup"><span data-stu-id="bf4ff-162">The following example shows how to use `@inject`.</span></span> <span data-ttu-id="bf4ff-163">Implementace služby `Services.IDataAccess` se vloží do komponenty vlastnosti `DataRepository`.</span><span class="sxs-lookup"><span data-stu-id="bf4ff-163">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="bf4ff-164">Všimněte si, jak kód využívá jenom `IDataAccess` abstrakce:</span><span class="sxs-lookup"><span data-stu-id="bf4ff-164">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-cshtml[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,23)]

<span data-ttu-id="bf4ff-165">Interně jsou vygenerované vlastnosti (`DataRepository`) je upravená pomocí `InjectAttribute` atribut.</span><span class="sxs-lookup"><span data-stu-id="bf4ff-165">Internally, the generated property (`DataRepository`) is decorated with the `InjectAttribute` attribute.</span></span> <span data-ttu-id="bf4ff-166">Tento atribut se obvykle nepoužívá přímo.</span><span class="sxs-lookup"><span data-stu-id="bf4ff-166">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="bf4ff-167">Pokud základní třída je povinné pro součásti a vloženého vlastnosti se rovněž vyžadují pro základní třídu, ručně přidejte `InjectAttribute`:</span><span class="sxs-lookup"><span data-stu-id="bf4ff-167">If a base class is required for components and injected properties are also required for the base class, manually add the `InjectAttribute`:</span></span>

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

<span data-ttu-id="bf4ff-168">V součásti odvozené ze základní třídy `@inject` – direktiva není povinné.</span><span class="sxs-lookup"><span data-stu-id="bf4ff-168">In components derived from the base class, the `@inject` directive isn't required.</span></span> <span data-ttu-id="bf4ff-169">`InjectAttribute` Základní třídy je dostačující:</span><span class="sxs-lookup"><span data-stu-id="bf4ff-169">The `InjectAttribute` of the base class is sufficient:</span></span>

```cshtml
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="dependency-injection-in-services"></a><span data-ttu-id="bf4ff-170">Injektáž závislostí služby</span><span class="sxs-lookup"><span data-stu-id="bf4ff-170">Dependency injection in services</span></span>

<span data-ttu-id="bf4ff-171">Komplexní služby můžou vyžadovat další služby.</span><span class="sxs-lookup"><span data-stu-id="bf4ff-171">Complex services might require additional services.</span></span> <span data-ttu-id="bf4ff-172">V předchozím příkladu `DataAccess` může vyžadovat `HttpClient` výchozí služby.</span><span class="sxs-lookup"><span data-stu-id="bf4ff-172">In the prior example, `DataAccess` might require the `HttpClient` default service.</span></span> <span data-ttu-id="bf4ff-173">`@inject` (nebo `InjectAttribute`) není k dispozici pro použití služby.</span><span class="sxs-lookup"><span data-stu-id="bf4ff-173">`@inject` (or the `InjectAttribute`) isn't available for use in services.</span></span> <span data-ttu-id="bf4ff-174">*Konstruktor vkládání* musí použít.</span><span class="sxs-lookup"><span data-stu-id="bf4ff-174">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="bf4ff-175">Přidat parametry do konstruktoru služby přidají požadované služby.</span><span class="sxs-lookup"><span data-stu-id="bf4ff-175">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="bf4ff-176">Při vkládání závislostí vytvoří službu, rozpozná služby vyžaduje v konstruktoru a poskytuje je odpovídajícím způsobem.</span><span class="sxs-lookup"><span data-stu-id="bf4ff-176">When dependency injection creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span>

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

<span data-ttu-id="bf4ff-177">Předpoklady pro vkládání konstruktor:</span><span class="sxs-lookup"><span data-stu-id="bf4ff-177">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="bf4ff-178">Jeden konstruktor musí existovat, jehož argumenty lze všechny splnit vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="bf4ff-178">One constructor must exist whose arguments can all be fulfilled by dependency injection.</span></span> <span data-ttu-id="bf4ff-179">Další parametry, které se nevztahuje DI jsou povolené, pokud se určení výchozích hodnot.</span><span class="sxs-lookup"><span data-stu-id="bf4ff-179">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="bf4ff-180">Použít konstruktor musí být *veřejné*.</span><span class="sxs-lookup"><span data-stu-id="bf4ff-180">The applicable constructor must be *public*.</span></span>
* <span data-ttu-id="bf4ff-181">Jeden použít konstruktor, musí existovat.</span><span class="sxs-lookup"><span data-stu-id="bf4ff-181">One applicable constructor must exist.</span></span> <span data-ttu-id="bf4ff-182">V případě nejednoznačnost DI vyvolá výjimku.</span><span class="sxs-lookup"><span data-stu-id="bf4ff-182">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bf4ff-183">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="bf4ff-183">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>

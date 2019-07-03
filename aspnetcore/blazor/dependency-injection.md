---
title: Injektáž závislostí ASP.NET Core Blazor
author: guardrex
description: Podívejte se, jak aplikace Blazor služby vložit do komponenty.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/02/2019
uid: blazor/dependency-injection
ms.openlocfilehash: 394d99656ba52f6c561007121e63634c7cb84f37
ms.sourcegitcommit: 0b9e767a09beaaaa4301915cdda9ef69daaf3ff2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538474"
---
# <a name="aspnet-core-blazor-dependency-injection"></a><span data-ttu-id="33735-103">Injektáž závislostí ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="33735-103">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="33735-104">Podle [Rainer Stropek](https://www.timecockpit.com)</span><span class="sxs-lookup"><span data-stu-id="33735-104">By [Rainer Stropek](https://www.timecockpit.com)</span></span>

<span data-ttu-id="33735-105">Podporuje Blazor [injektáž závislostí (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="33735-105">Blazor supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="33735-106">Aplikace můžete použít integrované služby vkládání do komponenty.</span><span class="sxs-lookup"><span data-stu-id="33735-106">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="33735-107">Aplikace můžete také definovat a registrovat vlastní služby a zpřístupnit je v celé aplikaci prostřednictvím DI.</span><span class="sxs-lookup"><span data-stu-id="33735-107">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

<span data-ttu-id="33735-108">DI je technika, pro přístup ke službám, které jsou nakonfigurované v centrálním umístění.</span><span class="sxs-lookup"><span data-stu-id="33735-108">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="33735-109">To může být užitečné v Blazor aplikací:</span><span class="sxs-lookup"><span data-stu-id="33735-109">This can be useful in Blazor apps to:</span></span>

* <span data-ttu-id="33735-110">Sdílet jednu instanci třídy služby mezi řadu součástí, označované jako *singleton* služby.</span><span class="sxs-lookup"><span data-stu-id="33735-110">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="33735-111">Pomocí odkazu abstrakce oddělte součásti od tříd konkrétní služby.</span><span class="sxs-lookup"><span data-stu-id="33735-111">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="33735-112">Představte si třeba rozhraní `IDataAccess` pro přístup k datům v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="33735-112">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="33735-113">Rozhraní je implementováno konkrétní `DataAccess` třídy a zaregistrováno jako služba v kontejneru aplikace služby.</span><span class="sxs-lookup"><span data-stu-id="33735-113">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="33735-114">Pokud součást používá DI přijímat `IDataAccess` implementace, komponenta není spojeny s konkrétní typ.</span><span class="sxs-lookup"><span data-stu-id="33735-114">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="33735-115">Implementace, je možné Prohodit, třeba pro imitovanou implementaci při testech jednotek.</span><span class="sxs-lookup"><span data-stu-id="33735-115">The implementation can be swapped, perhaps for a mock implementation in unit tests.</span></span>

## <a name="default-services"></a><span data-ttu-id="33735-116">Výchozí služby</span><span class="sxs-lookup"><span data-stu-id="33735-116">Default services</span></span>

<span data-ttu-id="33735-117">Výchozí služby jsou automaticky přidány do aplikace služby kolekce.</span><span class="sxs-lookup"><span data-stu-id="33735-117">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="33735-118">Služba</span><span class="sxs-lookup"><span data-stu-id="33735-118">Service</span></span> | <span data-ttu-id="33735-119">Životnost</span><span class="sxs-lookup"><span data-stu-id="33735-119">Lifetime</span></span> | <span data-ttu-id="33735-120">Popis</span><span class="sxs-lookup"><span data-stu-id="33735-120">Description</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="33735-121">Singleton</span><span class="sxs-lookup"><span data-stu-id="33735-121">Singleton</span></span> | <span data-ttu-id="33735-122">Poskytuje metody pro odesílání požadavků HTTP a příjem odpovědí HTTP ze zdroje identifikovaného identifikátorem URI.</span><span class="sxs-lookup"><span data-stu-id="33735-122">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span> <span data-ttu-id="33735-123">Všimněte si, že tato instance `HttpClient` používá prohlížeč pro zpracování provozu HTTP na pozadí.</span><span class="sxs-lookup"><span data-stu-id="33735-123">Note that this instance of `HttpClient` uses the browser for handling the HTTP traffic in the background.</span></span> <span data-ttu-id="33735-124">[HttpClient.BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress) se automaticky nastaví na základní identifikátor URI předponu aplikace.</span><span class="sxs-lookup"><span data-stu-id="33735-124">[HttpClient.BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress) is automatically set to the base URI prefix of the app.</span></span> <span data-ttu-id="33735-125">Další informace naleznete v tématu <xref:blazor/call-web-api>.</span><span class="sxs-lookup"><span data-stu-id="33735-125">For more information, see <xref:blazor/call-web-api>.</span></span> |
| `IJSRuntime` | <span data-ttu-id="33735-126">Singleton</span><span class="sxs-lookup"><span data-stu-id="33735-126">Singleton</span></span> | <span data-ttu-id="33735-127">Představuje instanci modulu runtime jazyka JavaScript, kde jsou odeslány volání JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="33735-127">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="33735-128">Další informace naleznete v tématu <xref:blazor/javascript-interop>.</span><span class="sxs-lookup"><span data-stu-id="33735-128">For more information, see <xref:blazor/javascript-interop>.</span></span> |
| `IUriHelper` | <span data-ttu-id="33735-129">Singleton</span><span class="sxs-lookup"><span data-stu-id="33735-129">Singleton</span></span> | <span data-ttu-id="33735-130">Obsahuje pomocné rutiny pro práci s identifikátory URI a navigační stav.</span><span class="sxs-lookup"><span data-stu-id="33735-130">Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="33735-131">Další informace najdete v tématu [identifikátor URI a navigační stav pomocné rutiny](xref:blazor/routing#uri-and-navigation-state-helpers).</span><span class="sxs-lookup"><span data-stu-id="33735-131">For more information, see [URI and navigation state helpers](xref:blazor/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="33735-132">Vlastní poskytovatele neposkytuje automaticky výchozí služby uvedené v tabulce.</span><span class="sxs-lookup"><span data-stu-id="33735-132">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="33735-133">Pokud používáte vlastní poskytovatele a kterékoli ze služeb, které jsou uvedené v tabulce vyžadují, přidejte do nového poskytovatele služby požadované služby.</span><span class="sxs-lookup"><span data-stu-id="33735-133">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="33735-134">Přidání služby do aplikace</span><span class="sxs-lookup"><span data-stu-id="33735-134">Add services to an app</span></span>

<span data-ttu-id="33735-135">Po vytvoření nové aplikace, zkontrolujte `Startup.ConfigureServices` metody:</span><span class="sxs-lookup"><span data-stu-id="33735-135">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

<span data-ttu-id="33735-136">`ConfigureServices` Metodě se předává <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, což je seznam objektů služeb popisovač (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span><span class="sxs-lookup"><span data-stu-id="33735-136">The `ConfigureServices` method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="33735-137">Služby jsou přidány tím, že poskytuje služby popisovače ke kolekci služby.</span><span class="sxs-lookup"><span data-stu-id="33735-137">Services are added by providing service descriptors to the service collection.</span></span> <span data-ttu-id="33735-138">Následující příklad ukazuje neznají koncept `IDataAccess` rozhraní a jeho konkrétní implementaci `DataAccess`:</span><span class="sxs-lookup"><span data-stu-id="33735-138">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

<span data-ttu-id="33735-139">Služby můžete nakonfigurovat s životností je znázorněno v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="33735-139">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="33735-140">Životnost</span><span class="sxs-lookup"><span data-stu-id="33735-140">Lifetime</span></span> | <span data-ttu-id="33735-141">Popis</span><span class="sxs-lookup"><span data-stu-id="33735-141">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped*> | <span data-ttu-id="33735-142">Blazor klientů aktuálně nemá koncept DI obory.</span><span class="sxs-lookup"><span data-stu-id="33735-142">Blazor client-side doesn't currently have a concept of DI scopes.</span></span> <span data-ttu-id="33735-143">`Scoped`-registrovaných služeb se chovat jako `Singleton` služby.</span><span class="sxs-lookup"><span data-stu-id="33735-143">`Scoped`-registered services behave like `Singleton` services.</span></span> <span data-ttu-id="33735-144">Ale podporuje model hostingu na straně serveru `Scoped` životnost.</span><span class="sxs-lookup"><span data-stu-id="33735-144">However, the server-side hosting model supports the `Scoped` lifetime.</span></span> <span data-ttu-id="33735-145">V komponentě Razor registrace vymezené služby působí na připojení.</span><span class="sxs-lookup"><span data-stu-id="33735-145">In a Razor component, a scoped service registration is scoped to the connection.</span></span> <span data-ttu-id="33735-146">Z tohoto důvodu vymezené služby je upřednostňována pro služby, které by měly být omezeny aktuálního uživatele, i v případě, že aktuální záměr je spustit na straně klienta v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="33735-146">For this reason, using scoped services is preferred for services that should be scoped to the current user, even if the current intent is to run client-side in the browser.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton*> | <span data-ttu-id="33735-147">Vytvoří DI *jednu instanci* služby.</span><span class="sxs-lookup"><span data-stu-id="33735-147">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="33735-148">Všechny součásti, které vyžadují `Singleton` služba přijímat instance ve stejné službě.</span><span class="sxs-lookup"><span data-stu-id="33735-148">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient*> | <span data-ttu-id="33735-149">Vždy, když součást získá instanci `Transient` služby z kontejneru služeb přijme *novou instanci* služby.</span><span class="sxs-lookup"><span data-stu-id="33735-149">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="33735-150">Systém DI je založen na systému DI v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="33735-150">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="33735-151">Další informace naleznete v tématu <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="33735-151">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="33735-152">Žádosti o službu v komponentě</span><span class="sxs-lookup"><span data-stu-id="33735-152">Request a service in a component</span></span>

<span data-ttu-id="33735-153">Po přidání služby do kolekce služeb, vložit do komponenty pomocí služeb [ \@vložit](xref:mvc/views/razor#section-4) direktivu Razor.</span><span class="sxs-lookup"><span data-stu-id="33735-153">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#section-4) Razor directive.</span></span> <span data-ttu-id="33735-154">`@inject` má dva parametry:</span><span class="sxs-lookup"><span data-stu-id="33735-154">`@inject` has two parameters:</span></span>

* <span data-ttu-id="33735-155">Typ &ndash; typ služby k vložení.</span><span class="sxs-lookup"><span data-stu-id="33735-155">Type &ndash; The type of the service to inject.</span></span>
* <span data-ttu-id="33735-156">Vlastnost &ndash; název vlastnosti příjem service vložené aplikace.</span><span class="sxs-lookup"><span data-stu-id="33735-156">Property &ndash; The name of the property receiving the injected app service.</span></span> <span data-ttu-id="33735-157">Vlastnost nevyžaduje ruční vytvoření.</span><span class="sxs-lookup"><span data-stu-id="33735-157">The property doesn't require manual creation.</span></span> <span data-ttu-id="33735-158">Kompilátor vytvoří vlastnost.</span><span class="sxs-lookup"><span data-stu-id="33735-158">The compiler creates the property.</span></span>

<span data-ttu-id="33735-159">Další informace naleznete v tématu <xref:mvc/views/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="33735-159">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="33735-160">Použití více `@inject` příkazy vkládat různé služby.</span><span class="sxs-lookup"><span data-stu-id="33735-160">Use multiple `@inject` statements to inject different services.</span></span>

<span data-ttu-id="33735-161">Následující příklad ukazuje, jak používat `@inject`.</span><span class="sxs-lookup"><span data-stu-id="33735-161">The following example shows how to use `@inject`.</span></span> <span data-ttu-id="33735-162">Implementace služby `Services.IDataAccess` se vloží do komponenty vlastnosti `DataRepository`.</span><span class="sxs-lookup"><span data-stu-id="33735-162">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="33735-163">Všimněte si, jak kód využívá jenom `IDataAccess` abstrakce:</span><span class="sxs-lookup"><span data-stu-id="33735-163">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-cshtml[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,23)]

<span data-ttu-id="33735-164">Interně jsou vygenerované vlastnosti (`DataRepository`) je upravená pomocí `InjectAttribute` atribut.</span><span class="sxs-lookup"><span data-stu-id="33735-164">Internally, the generated property (`DataRepository`) is decorated with the `InjectAttribute` attribute.</span></span> <span data-ttu-id="33735-165">Tento atribut se obvykle nepoužívá přímo.</span><span class="sxs-lookup"><span data-stu-id="33735-165">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="33735-166">Pokud základní třída je povinné pro součásti a vloženého vlastnosti se rovněž vyžadují pro základní třídu, ručně přidejte `InjectAttribute`:</span><span class="sxs-lookup"><span data-stu-id="33735-166">If a base class is required for components and injected properties are also required for the base class, manually add the `InjectAttribute`:</span></span>

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

<span data-ttu-id="33735-167">V součásti odvozené ze základní třídy `@inject` – direktiva není povinné.</span><span class="sxs-lookup"><span data-stu-id="33735-167">In components derived from the base class, the `@inject` directive isn't required.</span></span> <span data-ttu-id="33735-168">`InjectAttribute` Základní třídy je dostačující:</span><span class="sxs-lookup"><span data-stu-id="33735-168">The `InjectAttribute` of the base class is sufficient:</span></span>

```cshtml
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="33735-169">Použití DI služby</span><span class="sxs-lookup"><span data-stu-id="33735-169">Use DI in services</span></span>

<span data-ttu-id="33735-170">Komplexní služby můžou vyžadovat další služby.</span><span class="sxs-lookup"><span data-stu-id="33735-170">Complex services might require additional services.</span></span> <span data-ttu-id="33735-171">V předchozím příkladu `DataAccess` může vyžadovat `HttpClient` výchozí služby.</span><span class="sxs-lookup"><span data-stu-id="33735-171">In the prior example, `DataAccess` might require the `HttpClient` default service.</span></span> <span data-ttu-id="33735-172">`@inject` (nebo `InjectAttribute`) není k dispozici pro použití služby.</span><span class="sxs-lookup"><span data-stu-id="33735-172">`@inject` (or the `InjectAttribute`) isn't available for use in services.</span></span> <span data-ttu-id="33735-173">*Konstruktor vkládání* musí použít.</span><span class="sxs-lookup"><span data-stu-id="33735-173">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="33735-174">Přidat parametry do konstruktoru služby přidají požadované služby.</span><span class="sxs-lookup"><span data-stu-id="33735-174">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="33735-175">Když DI vytvoří službu, rozpozná služby vyžaduje v konstruktoru a poskytuje je odpovídajícím způsobem.</span><span class="sxs-lookup"><span data-stu-id="33735-175">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span>

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

<span data-ttu-id="33735-176">Předpoklady pro vkládání konstruktor:</span><span class="sxs-lookup"><span data-stu-id="33735-176">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="33735-177">Jeden konstruktor musí existovat, jehož argumenty lze všechny splnit DI.</span><span class="sxs-lookup"><span data-stu-id="33735-177">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="33735-178">Další parametry, které se nevztahuje DI jsou povolené, pokud se určení výchozích hodnot.</span><span class="sxs-lookup"><span data-stu-id="33735-178">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="33735-179">Použít konstruktor musí být *veřejné*.</span><span class="sxs-lookup"><span data-stu-id="33735-179">The applicable constructor must be *public*.</span></span>
* <span data-ttu-id="33735-180">Jeden použít konstruktor, musí existovat.</span><span class="sxs-lookup"><span data-stu-id="33735-180">One applicable constructor must exist.</span></span> <span data-ttu-id="33735-181">V případě nejednoznačnost DI vyvolá výjimku.</span><span class="sxs-lookup"><span data-stu-id="33735-181">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="33735-182">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="33735-182">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>

---
title: Vkládání závislostí ASP.NET Core Blazor
author: guardrex
description: Podívejte se, jak aplikace Blazor můžou vkládat služby do součástí.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/06/2019
uid: blazor/dependency-injection
ms.openlocfilehash: 0b48cd0cbe14d2b07627f56ab78611bbd3209fa1
ms.sourcegitcommit: 43c6335b5859282f64d66a7696c5935a2bcdf966
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2019
ms.locfileid: "70800391"
---
# <a name="aspnet-core-blazor-dependency-injection"></a><span data-ttu-id="51a47-103">Vkládání závislostí ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="51a47-103">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="51a47-104">Od [Rainer Stropek](https://www.timecockpit.com)</span><span class="sxs-lookup"><span data-stu-id="51a47-104">By [Rainer Stropek](https://www.timecockpit.com)</span></span>

<span data-ttu-id="51a47-105">Blazor podporuje [vkládání závislostí (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="51a47-105">Blazor supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="51a47-106">Aplikace mohou používat vestavěné služby jejich vložením do komponent.</span><span class="sxs-lookup"><span data-stu-id="51a47-106">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="51a47-107">Aplikace můžou také definovat a registrovat vlastní služby a zpřístupnit je v celé aplikaci přes DI.</span><span class="sxs-lookup"><span data-stu-id="51a47-107">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

<span data-ttu-id="51a47-108">DI je technika přístupu ke službám nakonfigurovaným v centrálním umístění.</span><span class="sxs-lookup"><span data-stu-id="51a47-108">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="51a47-109">To může být užitečné v aplikacích Blazor k těmto akcím:</span><span class="sxs-lookup"><span data-stu-id="51a47-109">This can be useful in Blazor apps to:</span></span>

* <span data-ttu-id="51a47-110">Sdílejte jednu instanci třídy služby napříč mnoha komponentami, která se označuje jako služba typu *singleton* .</span><span class="sxs-lookup"><span data-stu-id="51a47-110">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="51a47-111">Oddělit komponenty od konkrétních tříd služeb pomocí abstrakcí odkazů.</span><span class="sxs-lookup"><span data-stu-id="51a47-111">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="51a47-112">Představte si třeba rozhraní `IDataAccess` pro přístup k datům v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="51a47-112">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="51a47-113">Rozhraní je implementováno konkrétní `DataAccess` třídou a registrováno jako služba v kontejneru služby aplikace.</span><span class="sxs-lookup"><span data-stu-id="51a47-113">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="51a47-114">Pokud komponenta používá di pro příjem `IDataAccess` implementace, komponenta není spojena se konkrétním typem.</span><span class="sxs-lookup"><span data-stu-id="51a47-114">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="51a47-115">Implementaci je možné prohodit, třeba pro podrobnější implementaci v testování částí.</span><span class="sxs-lookup"><span data-stu-id="51a47-115">The implementation can be swapped, perhaps for a mock implementation in unit tests.</span></span>

## <a name="default-services"></a><span data-ttu-id="51a47-116">Výchozí služby</span><span class="sxs-lookup"><span data-stu-id="51a47-116">Default services</span></span>

<span data-ttu-id="51a47-117">Výchozí služby se automaticky přidají do kolekce služeb aplikace.</span><span class="sxs-lookup"><span data-stu-id="51a47-117">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="51a47-118">Služba</span><span class="sxs-lookup"><span data-stu-id="51a47-118">Service</span></span> | <span data-ttu-id="51a47-119">Životnost</span><span class="sxs-lookup"><span data-stu-id="51a47-119">Lifetime</span></span> | <span data-ttu-id="51a47-120">Popis</span><span class="sxs-lookup"><span data-stu-id="51a47-120">Description</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="51a47-121">Singleton</span><span class="sxs-lookup"><span data-stu-id="51a47-121">Singleton</span></span> | <span data-ttu-id="51a47-122">Poskytuje metody pro posílání požadavků HTTP a příjem odpovědí HTTP z prostředku identifikovaného identifikátorem URI.</span><span class="sxs-lookup"><span data-stu-id="51a47-122">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span> <span data-ttu-id="51a47-123">Všimněte si, že tato `HttpClient` instance nástroje používá prohlížeč pro zpracování provozu http na pozadí.</span><span class="sxs-lookup"><span data-stu-id="51a47-123">Note that this instance of `HttpClient` uses the browser for handling the HTTP traffic in the background.</span></span> <span data-ttu-id="51a47-124">[HttpClient. BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress) se automaticky nastaví na základní PŘEDPONu identifikátoru URI aplikace.</span><span class="sxs-lookup"><span data-stu-id="51a47-124">[HttpClient.BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress) is automatically set to the base URI prefix of the app.</span></span> <span data-ttu-id="51a47-125">Další informace naleznete v tématu <xref:blazor/call-web-api>.</span><span class="sxs-lookup"><span data-stu-id="51a47-125">For more information, see <xref:blazor/call-web-api>.</span></span> |
| `IJSRuntime` | <span data-ttu-id="51a47-126">Singleton</span><span class="sxs-lookup"><span data-stu-id="51a47-126">Singleton</span></span> | <span data-ttu-id="51a47-127">Představuje instanci modulu runtime jazyka JavaScript, kde jsou odesílána volání jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="51a47-127">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="51a47-128">Další informace naleznete v tématu <xref:blazor/javascript-interop>.</span><span class="sxs-lookup"><span data-stu-id="51a47-128">For more information, see <xref:blazor/javascript-interop>.</span></span> |
| `NavigationManager` | <span data-ttu-id="51a47-129">Singleton</span><span class="sxs-lookup"><span data-stu-id="51a47-129">Singleton</span></span> | <span data-ttu-id="51a47-130">Obsahuje nápovědu pro práci s identifikátory URI a stavem navigace.</span><span class="sxs-lookup"><span data-stu-id="51a47-130">Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="51a47-131">Další informace najdete v tématu věnovaném [identifikátorům URI a nápovědě k informacím o stavu navigace](xref:blazor/routing#uri-and-navigation-state-helpers).</span><span class="sxs-lookup"><span data-stu-id="51a47-131">For more information, see [URI and navigation state helpers](xref:blazor/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="51a47-132">Vlastní zprostředkovatel služeb automaticky neposkytuje výchozí služby uvedené v tabulce.</span><span class="sxs-lookup"><span data-stu-id="51a47-132">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="51a47-133">Pokud používáte vlastního poskytovatele služeb a potřebujete některou ze služeb zobrazených v tabulce, přidejte požadované služby k novému poskytovateli služeb.</span><span class="sxs-lookup"><span data-stu-id="51a47-133">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="51a47-134">Přidání služeb do aplikace</span><span class="sxs-lookup"><span data-stu-id="51a47-134">Add services to an app</span></span>

<span data-ttu-id="51a47-135">Po vytvoření nové aplikace si Projděte tuto `Startup.ConfigureServices` metodu:</span><span class="sxs-lookup"><span data-stu-id="51a47-135">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

<span data-ttu-id="51a47-136">Metoda je předána, což je seznam objektů deskriptoru služby (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>). <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> `ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="51a47-136">The `ConfigureServices` method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="51a47-137">Služby se přidávají tím, že se do kolekce služeb poskytují popisovače služby.</span><span class="sxs-lookup"><span data-stu-id="51a47-137">Services are added by providing service descriptors to the service collection.</span></span> <span data-ttu-id="51a47-138">Následující příklad ukazuje koncept s `IDataAccess` rozhraním a jeho konkrétní implementací: `DataAccess`</span><span class="sxs-lookup"><span data-stu-id="51a47-138">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

<span data-ttu-id="51a47-139">Služby je možné konfigurovat s životností, která jsou uvedená v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="51a47-139">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="51a47-140">Životnost</span><span class="sxs-lookup"><span data-stu-id="51a47-140">Lifetime</span></span> | <span data-ttu-id="51a47-141">Popis</span><span class="sxs-lookup"><span data-stu-id="51a47-141">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped*> | <span data-ttu-id="51a47-142">Blazor aplikace pro WebAssembly aktuálně nemají koncept typu DI obory.</span><span class="sxs-lookup"><span data-stu-id="51a47-142">Blazor WebAssembly apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="51a47-143">`Scoped`– registrované služby se chovají jako `Singleton` služby.</span><span class="sxs-lookup"><span data-stu-id="51a47-143">`Scoped`-registered services behave like `Singleton` services.</span></span> <span data-ttu-id="51a47-144">Model hostování na straně serveru však podporuje `Scoped` dobu života.</span><span class="sxs-lookup"><span data-stu-id="51a47-144">However, the server-side hosting model supports the `Scoped` lifetime.</span></span> <span data-ttu-id="51a47-145">V aplikacích Blazor Server je obor registrace služby vymezená na *připojení*.</span><span class="sxs-lookup"><span data-stu-id="51a47-145">In Blazor Server apps, a scoped service registration is scoped to the *connection*.</span></span> <span data-ttu-id="51a47-146">Z tohoto důvodu je vhodnější použití oboru služeb pro služby, které by měly být vymezeny na aktuálního uživatele, a to i v případě, že aktuální záměr je spustit na straně klienta v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="51a47-146">For this reason, using scoped services is preferred for services that should be scoped to the current user, even if the current intent is to run client-side in the browser.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton*> | <span data-ttu-id="51a47-147">DI vytvoří *jednu instanci* služby.</span><span class="sxs-lookup"><span data-stu-id="51a47-147">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="51a47-148">Všechny součásti, které `Singleton` vyžadují službu, obdrží instanci stejné služby.</span><span class="sxs-lookup"><span data-stu-id="51a47-148">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient*> | <span data-ttu-id="51a47-149">Pokaždé, když komponenta získá instanci `Transient` služby z kontejneru služby, obdrží *novou instanci* služby.</span><span class="sxs-lookup"><span data-stu-id="51a47-149">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="51a47-150">Systém DI je založený na systému DI v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="51a47-150">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="51a47-151">Další informace naleznete v tématu <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="51a47-151">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="51a47-152">Vyžádání služby v součásti</span><span class="sxs-lookup"><span data-stu-id="51a47-152">Request a service in a component</span></span>

<span data-ttu-id="51a47-153">Po přidání služeb do kolekce služeb tyto služby vloží do komponent pomocí [ \@](xref:mvc/views/razor#inject) direktivy vložení Razor.</span><span class="sxs-lookup"><span data-stu-id="51a47-153">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) Razor directive.</span></span> <span data-ttu-id="51a47-154">`@inject`má dva parametry:</span><span class="sxs-lookup"><span data-stu-id="51a47-154">`@inject` has two parameters:</span></span>

* <span data-ttu-id="51a47-155">Zadejte &ndash; typ služby, kterou chcete vložit.</span><span class="sxs-lookup"><span data-stu-id="51a47-155">Type &ndash; The type of the service to inject.</span></span>
* <span data-ttu-id="51a47-156">Vlastnost &ndash; název vlastnosti, která přijímá vloženou službu App Service.</span><span class="sxs-lookup"><span data-stu-id="51a47-156">Property &ndash; The name of the property receiving the injected app service.</span></span> <span data-ttu-id="51a47-157">Vlastnost nevyžaduje ruční vytvoření.</span><span class="sxs-lookup"><span data-stu-id="51a47-157">The property doesn't require manual creation.</span></span> <span data-ttu-id="51a47-158">Kompilátor vytvoří vlastnost.</span><span class="sxs-lookup"><span data-stu-id="51a47-158">The compiler creates the property.</span></span>

<span data-ttu-id="51a47-159">Další informace naleznete v tématu <xref:mvc/views/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="51a47-159">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="51a47-160">Pro vložení `@inject` různých služeb použijte více příkazů.</span><span class="sxs-lookup"><span data-stu-id="51a47-160">Use multiple `@inject` statements to inject different services.</span></span>

<span data-ttu-id="51a47-161">Následující příklad ukazuje, jak použít `@inject`.</span><span class="sxs-lookup"><span data-stu-id="51a47-161">The following example shows how to use `@inject`.</span></span> <span data-ttu-id="51a47-162">Implementace `Services.IDataAccess` služby je vložena do vlastnosti `DataRepository`komponenty.</span><span class="sxs-lookup"><span data-stu-id="51a47-162">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="51a47-163">Všimněte si, jak kód používá `IDataAccess` abstrakci:</span><span class="sxs-lookup"><span data-stu-id="51a47-163">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-cshtml[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,23)]

<span data-ttu-id="51a47-164">Interně je vygenerovaná vlastnost`DataRepository`() upravena `InjectAttribute` atributem.</span><span class="sxs-lookup"><span data-stu-id="51a47-164">Internally, the generated property (`DataRepository`) is decorated with the `InjectAttribute` attribute.</span></span> <span data-ttu-id="51a47-165">Obvykle se tento atribut nepoužívá přímo.</span><span class="sxs-lookup"><span data-stu-id="51a47-165">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="51a47-166">Pokud je vyžadována základní třída pro součásti a vložené vlastnosti jsou také požadovány pro základní třídu, přidejte `InjectAttribute`ručně:</span><span class="sxs-lookup"><span data-stu-id="51a47-166">If a base class is required for components and injected properties are also required for the base class, manually add the `InjectAttribute`:</span></span>

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

<span data-ttu-id="51a47-167">V součástech odvozených ze základní třídy `@inject` není direktiva vyžadována.</span><span class="sxs-lookup"><span data-stu-id="51a47-167">In components derived from the base class, the `@inject` directive isn't required.</span></span> <span data-ttu-id="51a47-168">`InjectAttribute` Základní třída je dostačující:</span><span class="sxs-lookup"><span data-stu-id="51a47-168">The `InjectAttribute` of the base class is sufficient:</span></span>

```cshtml
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="51a47-169">Použití DI v službách</span><span class="sxs-lookup"><span data-stu-id="51a47-169">Use DI in services</span></span>

<span data-ttu-id="51a47-170">Komplexní služby můžou vyžadovat další služby.</span><span class="sxs-lookup"><span data-stu-id="51a47-170">Complex services might require additional services.</span></span> <span data-ttu-id="51a47-171">V předchozím příkladu `DataAccess` může `HttpClient` vyžadovat výchozí službu.</span><span class="sxs-lookup"><span data-stu-id="51a47-171">In the prior example, `DataAccess` might require the `HttpClient` default service.</span></span> <span data-ttu-id="51a47-172">`@inject`(nebo) `InjectAttribute`nejsou k dispozici pro použití v rámci služeb.</span><span class="sxs-lookup"><span data-stu-id="51a47-172">`@inject` (or the `InjectAttribute`) isn't available for use in services.</span></span> <span data-ttu-id="51a47-173">Místo toho se musí použít *Injektáže konstruktoru* .</span><span class="sxs-lookup"><span data-stu-id="51a47-173">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="51a47-174">Požadované služby jsou přidány přidáním parametrů do konstruktoru služby.</span><span class="sxs-lookup"><span data-stu-id="51a47-174">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="51a47-175">Když DI vytvoří službu, rozpoznává služby, které vyžaduje v konstruktoru, a odpovídajícím způsobem je poskytne.</span><span class="sxs-lookup"><span data-stu-id="51a47-175">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span>

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

<span data-ttu-id="51a47-176">Předpoklady pro vložení konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="51a47-176">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="51a47-177">Je nutné, aby jeden konstruktor existoval, jehož argumenty mohou být splněny pomocí DI.</span><span class="sxs-lookup"><span data-stu-id="51a47-177">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="51a47-178">Další parametry, které nejsou pokryty parametrem DI, jsou povoleny, pokud určují výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="51a47-178">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="51a47-179">Příslušný konstruktor musí být *veřejný*.</span><span class="sxs-lookup"><span data-stu-id="51a47-179">The applicable constructor must be *public*.</span></span>
* <span data-ttu-id="51a47-180">Musí existovat jeden použitelný konstruktor.</span><span class="sxs-lookup"><span data-stu-id="51a47-180">One applicable constructor must exist.</span></span> <span data-ttu-id="51a47-181">V případě nejednoznačnosti Vyvolá příkaz DI výjimku.</span><span class="sxs-lookup"><span data-stu-id="51a47-181">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="51a47-182">Základní třídy komponenty nástroje pro správu oboru DI</span><span class="sxs-lookup"><span data-stu-id="51a47-182">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="51a47-183">V aplikacích ASP.NET Core jsou oborové služby obvykle vymezeny na aktuální požadavek.</span><span class="sxs-lookup"><span data-stu-id="51a47-183">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="51a47-184">Po dokončení žádosti se v systému DI odstraní všechny obory nebo přechodné služby.</span><span class="sxs-lookup"><span data-stu-id="51a47-184">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="51a47-185">V aplikacích Blazor Server je rozsah požadavků po dobu trvání připojení klienta, což může vést k přechodným a oborům služeb, které jsou delší, než se očekávalo.</span><span class="sxs-lookup"><span data-stu-id="51a47-185">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span>

<span data-ttu-id="51a47-186">Chcete-li obor služeb omezit na životnost komponenty, lze použít `OwningComponentBase` základní třídy a. `OwningComponentBase<TService>`</span><span class="sxs-lookup"><span data-stu-id="51a47-186">To scope services to the lifetime of a component, can use the `OwningComponentBase` and `OwningComponentBase<TService>` base classes.</span></span> <span data-ttu-id="51a47-187">Tyto základní třídy zpřístupňují `ScopedServices` vlastnost typu `IServiceProvider` , která řeší služby s rozsahem životnosti komponenty.</span><span class="sxs-lookup"><span data-stu-id="51a47-187">These base classes expose a `ScopedServices` property of type `IServiceProvider` that resolve services that are scoped to the lifetime of the component.</span></span> <span data-ttu-id="51a47-188">Chcete-li vytvořit komponentu, která dědí ze základní třídy ve Razor, použijte `@inherits` direktivu.</span><span class="sxs-lookup"><span data-stu-id="51a47-188">To author a component that inherits from a base class in Razor, use the `@inherits` directive.</span></span>

```cshtml
@page "/users"
@attribute [Authorize]
@inherits OwningComponentBase<Data.ApplicationDbContext>

<h1>Users (@Service.Users.Count())</h1>
<ul>
    @foreach (var user in Service.Users)
    {
        <li>@user.UserName</li>
    }
</ul>
```

> [!NOTE]
> <span data-ttu-id="51a47-189">Služby vložené do komponenty pomocí `@inject` `InjectAttribute` nebo nejsou vytvořeny v oboru komponenty a jsou svázány s oborem požadavku.</span><span class="sxs-lookup"><span data-stu-id="51a47-189">Services injected into the component using `@inject` or the `InjectAttribute` aren't created in the component's scope and are tied to the request scope.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="51a47-190">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="51a47-190">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>

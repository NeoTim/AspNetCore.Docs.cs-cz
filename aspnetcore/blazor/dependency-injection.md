---
title: Vkládání závislostí ASP.NET Core Blazor
author: guardrex
description: Podívejte se, jak Blazor aplikace můžou vkládat služby do součástí.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/08/2020
no-loc:
- Blazor
- SignalR
uid: blazor/dependency-injection
ms.openlocfilehash: fa6762522c831c7fbe2742dbfe4e25a377988e1e
ms.sourcegitcommit: fe41cff0b99f3920b727286944e5b652ca301640
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76869561"
---
# <a name="aspnet-core-blazor-dependency-injection"></a><span data-ttu-id="32c81-103">Vkládání závislostí ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="32c81-103">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="32c81-104">Od [Rainer Stropek](https://www.timecockpit.com)</span><span class="sxs-lookup"><span data-stu-id="32c81-104">By [Rainer Stropek](https://www.timecockpit.com)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="32c81-105">Blazor podporuje [vkládání závislostí (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="32c81-105">Blazor supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="32c81-106">Aplikace mohou používat vestavěné služby jejich vložením do komponent.</span><span class="sxs-lookup"><span data-stu-id="32c81-106">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="32c81-107">Aplikace můžou také definovat a registrovat vlastní služby a zpřístupnit je v celé aplikaci přes DI.</span><span class="sxs-lookup"><span data-stu-id="32c81-107">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

<span data-ttu-id="32c81-108">DI je technika přístupu ke službám nakonfigurovaným v centrálním umístění.</span><span class="sxs-lookup"><span data-stu-id="32c81-108">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="32c81-109">To může být užitečné v aplikacích Blazor k těmto akcím:</span><span class="sxs-lookup"><span data-stu-id="32c81-109">This can be useful in Blazor apps to:</span></span>

* <span data-ttu-id="32c81-110">Sdílejte jednu instanci třídy služby napříč mnoha komponentami, která se označuje jako služba typu *singleton* .</span><span class="sxs-lookup"><span data-stu-id="32c81-110">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="32c81-111">Oddělit komponenty od konkrétních tříd služeb pomocí abstrakcí odkazů.</span><span class="sxs-lookup"><span data-stu-id="32c81-111">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="32c81-112">Představte si třeba rozhraní `IDataAccess` pro přístup k datům v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="32c81-112">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="32c81-113">Rozhraní je implementováno konkrétní `DataAccess`ou třídou a registrováno jako služba v kontejneru služby aplikace.</span><span class="sxs-lookup"><span data-stu-id="32c81-113">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="32c81-114">Pokud komponenta používá DI pro příjem `IDataAccess` implementace, komponenta není spojena se konkrétním typem.</span><span class="sxs-lookup"><span data-stu-id="32c81-114">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="32c81-115">Implementaci je možné prohodit, třeba pro podrobnější implementaci v testování částí.</span><span class="sxs-lookup"><span data-stu-id="32c81-115">The implementation can be swapped, perhaps for a mock implementation in unit tests.</span></span>

## <a name="default-services"></a><span data-ttu-id="32c81-116">Výchozí služby</span><span class="sxs-lookup"><span data-stu-id="32c81-116">Default services</span></span>

<span data-ttu-id="32c81-117">Výchozí služby se automaticky přidají do kolekce služeb aplikace.</span><span class="sxs-lookup"><span data-stu-id="32c81-117">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="32c81-118">Service</span><span class="sxs-lookup"><span data-stu-id="32c81-118">Service</span></span> | <span data-ttu-id="32c81-119">Doba platnosti</span><span class="sxs-lookup"><span data-stu-id="32c81-119">Lifetime</span></span> | <span data-ttu-id="32c81-120">Popis</span><span class="sxs-lookup"><span data-stu-id="32c81-120">Description</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="32c81-121">Singleton</span><span class="sxs-lookup"><span data-stu-id="32c81-121">Singleton</span></span> | <span data-ttu-id="32c81-122">Poskytuje metody pro posílání požadavků HTTP a příjem odpovědí HTTP z prostředku identifikovaného identifikátorem URI.</span><span class="sxs-lookup"><span data-stu-id="32c81-122">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span><br><br><span data-ttu-id="32c81-123">Instance `HttpClient` v aplikaci WebAssembly v Blazor používá prohlížeč pro zpracování provozu HTTP na pozadí.</span><span class="sxs-lookup"><span data-stu-id="32c81-123">The instance of `HttpClient` in a Blazor WebAssembly app uses the browser for handling the HTTP traffic in the background.</span></span><br><br><span data-ttu-id="32c81-124">Aplikace Blazor Server ve výchozím nastavení nezahrnují `HttpClient` nakonfigurovanou jako službu.</span><span class="sxs-lookup"><span data-stu-id="32c81-124">Blazor Server apps don't include an `HttpClient` configured as a service by default.</span></span> <span data-ttu-id="32c81-125">Poskytněte `HttpClient` aplikaci Blazor serveru.</span><span class="sxs-lookup"><span data-stu-id="32c81-125">Provide an `HttpClient` to a Blazor Server app.</span></span><br><br><span data-ttu-id="32c81-126">Další informace najdete v tématu <xref:blazor/call-web-api>.</span><span class="sxs-lookup"><span data-stu-id="32c81-126">For more information, see <xref:blazor/call-web-api>.</span></span> |
| `IJSRuntime` | <span data-ttu-id="32c81-127">Singleton (Blazor WebAssembly)</span><span class="sxs-lookup"><span data-stu-id="32c81-127">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="32c81-128">Vymezený obor (Blazor Server)</span><span class="sxs-lookup"><span data-stu-id="32c81-128">Scoped (Blazor Server)</span></span> | <span data-ttu-id="32c81-129">Představuje instanci modulu runtime jazyka JavaScript, kde jsou odesílána volání jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="32c81-129">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="32c81-130">Další informace najdete v tématu <xref:blazor/javascript-interop>.</span><span class="sxs-lookup"><span data-stu-id="32c81-130">For more information, see <xref:blazor/javascript-interop>.</span></span> |
| `NavigationManager` | <span data-ttu-id="32c81-131">Singleton (Blazor WebAssembly)</span><span class="sxs-lookup"><span data-stu-id="32c81-131">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="32c81-132">Vymezený obor (Blazor Server)</span><span class="sxs-lookup"><span data-stu-id="32c81-132">Scoped (Blazor Server)</span></span> | <span data-ttu-id="32c81-133">Obsahuje nápovědu pro práci s identifikátory URI a stavem navigace.</span><span class="sxs-lookup"><span data-stu-id="32c81-133">Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="32c81-134">Další informace najdete v tématu věnovaném [identifikátorům URI a nápovědě k informacím o stavu navigace](xref:blazor/routing#uri-and-navigation-state-helpers).</span><span class="sxs-lookup"><span data-stu-id="32c81-134">For more information, see [URI and navigation state helpers](xref:blazor/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="32c81-135">Vlastní zprostředkovatel služeb automaticky neposkytuje výchozí služby uvedené v tabulce.</span><span class="sxs-lookup"><span data-stu-id="32c81-135">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="32c81-136">Pokud používáte vlastního poskytovatele služeb a potřebujete některou ze služeb zobrazených v tabulce, přidejte požadované služby k novému poskytovateli služeb.</span><span class="sxs-lookup"><span data-stu-id="32c81-136">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="32c81-137">Přidání služeb do aplikace</span><span class="sxs-lookup"><span data-stu-id="32c81-137">Add services to an app</span></span>

<span data-ttu-id="32c81-138">Po vytvoření nové aplikace Projděte metodu `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="32c81-138">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

<span data-ttu-id="32c81-139">Metodě `ConfigureServices` se předává <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, což je seznam objektů deskriptoru služby (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span><span class="sxs-lookup"><span data-stu-id="32c81-139">The `ConfigureServices` method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="32c81-140">Služby se přidávají tím, že se do kolekce služeb poskytují popisovače služby.</span><span class="sxs-lookup"><span data-stu-id="32c81-140">Services are added by providing service descriptors to the service collection.</span></span> <span data-ttu-id="32c81-141">Následující příklad ukazuje koncept s rozhraním `IDataAccess` a jeho konkrétní implementací `DataAccess`:</span><span class="sxs-lookup"><span data-stu-id="32c81-141">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

<span data-ttu-id="32c81-142">Služby je možné konfigurovat s životností, která jsou uvedená v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="32c81-142">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="32c81-143">Doba platnosti</span><span class="sxs-lookup"><span data-stu-id="32c81-143">Lifetime</span></span> | <span data-ttu-id="32c81-144">Popis</span><span class="sxs-lookup"><span data-stu-id="32c81-144">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped*> | <span data-ttu-id="32c81-145">aplikace Blazor WebAssembly aktuálně nemají koncept typu DI scopes.</span><span class="sxs-lookup"><span data-stu-id="32c81-145">Blazor WebAssembly apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="32c81-146">služby registrované `Scoped`se chovají jako služby `Singleton`.</span><span class="sxs-lookup"><span data-stu-id="32c81-146">`Scoped`-registered services behave like `Singleton` services.</span></span> <span data-ttu-id="32c81-147">Model hostování Blazor serveru však podporuje `Scoped` životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="32c81-147">However, the Blazor Server hosting model supports the `Scoped` lifetime.</span></span> <span data-ttu-id="32c81-148">V Blazorch serverových aplikacích je vymezená registrace služby vymezená na *připojení*.</span><span class="sxs-lookup"><span data-stu-id="32c81-148">In Blazor Server apps, a scoped service registration is scoped to the *connection*.</span></span> <span data-ttu-id="32c81-149">Z tohoto důvodu je vhodnější použití oboru služeb pro služby, které by měly být vymezeny na aktuálního uživatele, a to i v případě, že aktuální záměr je spustit na straně klienta v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="32c81-149">For this reason, using scoped services is preferred for services that should be scoped to the current user, even if the current intent is to run client-side in the browser.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton*> | <span data-ttu-id="32c81-150">DI vytvoří *jednu instanci* služby.</span><span class="sxs-lookup"><span data-stu-id="32c81-150">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="32c81-151">Všechny součásti, které vyžadují službu `Singleton`, obdrží instanci stejné služby.</span><span class="sxs-lookup"><span data-stu-id="32c81-151">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient*> | <span data-ttu-id="32c81-152">Pokaždé, když komponenta získá instanci `Transient` služby z kontejneru služby, obdrží *novou instanci* služby.</span><span class="sxs-lookup"><span data-stu-id="32c81-152">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="32c81-153">Systém DI je založený na systému DI v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="32c81-153">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="32c81-154">Další informace najdete v tématu <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="32c81-154">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="32c81-155">Vyžádání služby v součásti</span><span class="sxs-lookup"><span data-stu-id="32c81-155">Request a service in a component</span></span>

<span data-ttu-id="32c81-156">Po přidání služeb do kolekce služeb tyto služby vloží do součástí pomocí direktivy [\@vkládání](xref:mvc/views/razor#inject) Razor.</span><span class="sxs-lookup"><span data-stu-id="32c81-156">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) Razor directive.</span></span> <span data-ttu-id="32c81-157">`@inject` má dva parametry:</span><span class="sxs-lookup"><span data-stu-id="32c81-157">`@inject` has two parameters:</span></span>

* <span data-ttu-id="32c81-158">Zadejte &ndash; typ služby, kterou chcete vložit.</span><span class="sxs-lookup"><span data-stu-id="32c81-158">Type &ndash; The type of the service to inject.</span></span>
* <span data-ttu-id="32c81-159">Vlastnost &ndash; název vlastnosti, která přijímá vloženou službu App Service.</span><span class="sxs-lookup"><span data-stu-id="32c81-159">Property &ndash; The name of the property receiving the injected app service.</span></span> <span data-ttu-id="32c81-160">Vlastnost nevyžaduje ruční vytvoření.</span><span class="sxs-lookup"><span data-stu-id="32c81-160">The property doesn't require manual creation.</span></span> <span data-ttu-id="32c81-161">Kompilátor vytvoří vlastnost.</span><span class="sxs-lookup"><span data-stu-id="32c81-161">The compiler creates the property.</span></span>

<span data-ttu-id="32c81-162">Další informace najdete v tématu <xref:mvc/views/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="32c81-162">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="32c81-163">Pro vložení různých služeb použijte více příkazů `@inject`.</span><span class="sxs-lookup"><span data-stu-id="32c81-163">Use multiple `@inject` statements to inject different services.</span></span>

<span data-ttu-id="32c81-164">Následující příklad ukazuje, jak použít `@inject`.</span><span class="sxs-lookup"><span data-stu-id="32c81-164">The following example shows how to use `@inject`.</span></span> <span data-ttu-id="32c81-165">Služba implementující `Services.IDataAccess` je vložená do `DataRepository`vlastností komponenty.</span><span class="sxs-lookup"><span data-stu-id="32c81-165">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="32c81-166">Všimněte si, jak kód používá `IDataAccess` abstrakce:</span><span class="sxs-lookup"><span data-stu-id="32c81-166">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,23)]

<span data-ttu-id="32c81-167">Interně, generovaná vlastnost (`DataRepository`) používá atribut `InjectAttribute`.</span><span class="sxs-lookup"><span data-stu-id="32c81-167">Internally, the generated property (`DataRepository`) uses the `InjectAttribute` attribute.</span></span> <span data-ttu-id="32c81-168">Obvykle se tento atribut nepoužívá přímo.</span><span class="sxs-lookup"><span data-stu-id="32c81-168">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="32c81-169">Pokud je vyžadována základní třída pro součásti a vložené vlastnosti jsou také požadovány pro základní třídu, přidejte `InjectAttribute`ručně:</span><span class="sxs-lookup"><span data-stu-id="32c81-169">If a base class is required for components and injected properties are also required for the base class, manually add the `InjectAttribute`:</span></span>

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

<span data-ttu-id="32c81-170">V součástech odvozených ze základní třídy není `@inject` direktiva vyžadována.</span><span class="sxs-lookup"><span data-stu-id="32c81-170">In components derived from the base class, the `@inject` directive isn't required.</span></span> <span data-ttu-id="32c81-171">`InjectAttribute` základní třídy jsou dostatečné:</span><span class="sxs-lookup"><span data-stu-id="32c81-171">The `InjectAttribute` of the base class is sufficient:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="32c81-172">Použití DI v službách</span><span class="sxs-lookup"><span data-stu-id="32c81-172">Use DI in services</span></span>

<span data-ttu-id="32c81-173">Komplexní služby můžou vyžadovat další služby.</span><span class="sxs-lookup"><span data-stu-id="32c81-173">Complex services might require additional services.</span></span> <span data-ttu-id="32c81-174">V předchozím příkladu `DataAccess` může vyžadovat `HttpClient` výchozí službu.</span><span class="sxs-lookup"><span data-stu-id="32c81-174">In the prior example, `DataAccess` might require the `HttpClient` default service.</span></span> <span data-ttu-id="32c81-175">`@inject` (nebo `InjectAttribute`) nejsou k dispozici pro použití v rámci služeb.</span><span class="sxs-lookup"><span data-stu-id="32c81-175">`@inject` (or the `InjectAttribute`) isn't available for use in services.</span></span> <span data-ttu-id="32c81-176">Místo toho se musí použít *Injektáže konstruktoru* .</span><span class="sxs-lookup"><span data-stu-id="32c81-176">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="32c81-177">Požadované služby jsou přidány přidáním parametrů do konstruktoru služby.</span><span class="sxs-lookup"><span data-stu-id="32c81-177">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="32c81-178">Když DI vytvoří službu, rozpoznává služby, které vyžaduje v konstruktoru, a odpovídajícím způsobem je poskytne.</span><span class="sxs-lookup"><span data-stu-id="32c81-178">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span>

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

<span data-ttu-id="32c81-179">Předpoklady pro vložení konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="32c81-179">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="32c81-180">Je nutné, aby jeden konstruktor existoval, jehož argumenty mohou být splněny pomocí DI.</span><span class="sxs-lookup"><span data-stu-id="32c81-180">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="32c81-181">Další parametry, které nejsou pokryty parametrem DI, jsou povoleny, pokud určují výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="32c81-181">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="32c81-182">Příslušný konstruktor musí být *veřejný*.</span><span class="sxs-lookup"><span data-stu-id="32c81-182">The applicable constructor must be *public*.</span></span>
* <span data-ttu-id="32c81-183">Musí existovat jeden použitelný konstruktor.</span><span class="sxs-lookup"><span data-stu-id="32c81-183">One applicable constructor must exist.</span></span> <span data-ttu-id="32c81-184">V případě nejednoznačnosti Vyvolá příkaz DI výjimku.</span><span class="sxs-lookup"><span data-stu-id="32c81-184">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="32c81-185">Základní třídy komponenty nástroje pro správu oboru DI</span><span class="sxs-lookup"><span data-stu-id="32c81-185">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="32c81-186">V aplikacích ASP.NET Core jsou oborové služby obvykle vymezeny na aktuální požadavek.</span><span class="sxs-lookup"><span data-stu-id="32c81-186">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="32c81-187">Po dokončení žádosti se v systému DI odstraní všechny obory nebo přechodné služby.</span><span class="sxs-lookup"><span data-stu-id="32c81-187">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="32c81-188">V Blazorch serverových aplikací je rozsah požadavků po dobu trvání připojení klienta, což může vést k přechodným a oborovým službám, které jsou mnohem delší, než se očekávalo.</span><span class="sxs-lookup"><span data-stu-id="32c81-188">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span>

<span data-ttu-id="32c81-189">Chcete-li obor služeb omezit na životnost komponenty, můžete použít základní třídy `OwningComponentBase` a `OwningComponentBase<TService>`.</span><span class="sxs-lookup"><span data-stu-id="32c81-189">To scope services to the lifetime of a component, you can use the `OwningComponentBase` and `OwningComponentBase<TService>` base classes.</span></span> <span data-ttu-id="32c81-190">Tyto základní třídy zpřístupňují vlastnost `ScopedServices` typu `IServiceProvider`, která řeší služby s vymezenou životností součásti.</span><span class="sxs-lookup"><span data-stu-id="32c81-190">These base classes expose a `ScopedServices` property of type `IServiceProvider` that resolve services that are scoped to the lifetime of the component.</span></span> <span data-ttu-id="32c81-191">Chcete-li vytvořit komponentu, která dědí ze základní třídy ve Razor, použijte direktivu `@inherits`.</span><span class="sxs-lookup"><span data-stu-id="32c81-191">To author a component that inherits from a base class in Razor, use the `@inherits` directive.</span></span>

```razor
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
> <span data-ttu-id="32c81-192">Služby vložené do komponenty pomocí `@inject` nebo `InjectAttribute` nejsou vytvořeny v oboru komponenty a jsou svázané s oborem požadavku.</span><span class="sxs-lookup"><span data-stu-id="32c81-192">Services injected into the component using `@inject` or the `InjectAttribute` aren't created in the component's scope and are tied to the request scope.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="32c81-193">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="32c81-193">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>

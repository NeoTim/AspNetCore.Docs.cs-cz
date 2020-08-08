---
title: Použití filtrů centra v ASP.NET CoreSignalR
author: brecon
description: Naučte se používat filtry centra v ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-5.0'
ms.author: brecon
ms.custom: mvc
ms.date: 05/22/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/hub-filters
ms.openlocfilehash: db6439ab4e61d0412148c5ea72b52d429367491b
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022144"
---
# <a name="use-hub-filters-in-aspnet-core-no-locsignalr"></a><span data-ttu-id="f607b-103">Použití filtrů centra v ASP.NET CoreSignalR</span><span class="sxs-lookup"><span data-stu-id="f607b-103">Use hub filters in ASP.NET Core SignalR</span></span>

<span data-ttu-id="f607b-104">Filtry centra:</span><span class="sxs-lookup"><span data-stu-id="f607b-104">Hub filters:</span></span>

* <span data-ttu-id="f607b-105">Jsou k dispozici v ASP.NET Core 5,0 nebo novějším.</span><span class="sxs-lookup"><span data-stu-id="f607b-105">Are available in ASP.NET Core 5.0 or later.</span></span>
* <span data-ttu-id="f607b-106">Povolí spuštění logiky před a po vyvoláním metod centra klienty.</span><span class="sxs-lookup"><span data-stu-id="f607b-106">Allow logic to run before and after hub methods are invoked by clients.</span></span>

<span data-ttu-id="f607b-107">Tento článek poskytuje pokyny pro psaní a používání filtrů centra.</span><span class="sxs-lookup"><span data-stu-id="f607b-107">This article provides guidance for writing and using hub filters.</span></span>

## <a name="configure-hub-filters"></a><span data-ttu-id="f607b-108">Konfigurace filtrů centra</span><span class="sxs-lookup"><span data-stu-id="f607b-108">Configure hub filters</span></span>

<span data-ttu-id="f607b-109">Filtry centra lze použít globálně nebo podle typu rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="f607b-109">Hub filters can be applied globally or per hub type.</span></span> <span data-ttu-id="f607b-110">Pořadí, ve kterém jsou filtry přidány, je pořadí, ve kterém jsou filtry spouštěny.</span><span class="sxs-lookup"><span data-stu-id="f607b-110">The order in which filters are added is the order in which the filters run.</span></span> <span data-ttu-id="f607b-111">Globální filtry centra se spouštějí před filtry místních rozbočovačů.</span><span class="sxs-lookup"><span data-stu-id="f607b-111">Global hub filters run before local hub filters.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(options =>
    {
        // Global filters will run first
        options.AddFilter<CustomFilter>();
    }).AddHubOptions<ChatHub>(options =>
    {
        // Local filters will run second
        options.AddFilter<CustomFilter2>();
    });
}
```

<span data-ttu-id="f607b-112">Filtr centra lze přidat jedním z následujících způsobů:</span><span class="sxs-lookup"><span data-stu-id="f607b-112">A hub filter can be added in one of the following ways:</span></span>

* <span data-ttu-id="f607b-113">Přidejte filtr podle konkrétního typu:</span><span class="sxs-lookup"><span data-stu-id="f607b-113">Add a filter by concrete type:</span></span>

    ```csharp
    hubOptions.AddFilter<TFilter>();
    ```

    <span data-ttu-id="f607b-114">Tato akce bude vyřešena z vkládání závislostí (DI) nebo aktivovaného typu.</span><span class="sxs-lookup"><span data-stu-id="f607b-114">This will be resolved from dependency injection (DI) or type activated.</span></span>

* <span data-ttu-id="f607b-115">Přidat filtr podle typu modulu runtime:</span><span class="sxs-lookup"><span data-stu-id="f607b-115">Add a filter by runtime type:</span></span>

    ```csharp
    hubOptions.AddFilter(typeof(TFilter));
    ```

    <span data-ttu-id="f607b-116">Tato akce bude vyřešena z typu DI nebo aktivované.</span><span class="sxs-lookup"><span data-stu-id="f607b-116">This will be resolved from DI or type activated.</span></span>

* <span data-ttu-id="f607b-117">Přidejte filtr podle instance:</span><span class="sxs-lookup"><span data-stu-id="f607b-117">Add a filter by instance:</span></span>

    ```csharp
    hubOptions.AddFilter(new MyFilter());
    ```

    <span data-ttu-id="f607b-118">Tato instance bude použita jako typ singleton.</span><span class="sxs-lookup"><span data-stu-id="f607b-118">This instance will be used like a singleton.</span></span> <span data-ttu-id="f607b-119">Všechna volání metod centra budou používat stejnou instanci.</span><span class="sxs-lookup"><span data-stu-id="f607b-119">All hub method invocations will use the same instance.</span></span>

<span data-ttu-id="f607b-120">Filtry centra jsou vytvářeny a uvolněny pro každé vyvolání centra.</span><span class="sxs-lookup"><span data-stu-id="f607b-120">Hub filters are created and disposed per hub invocation.</span></span> <span data-ttu-id="f607b-121">Pokud chcete uložit globální stav do filtru nebo žádný stav, přidejte typ filtru centra jako typ "DI" jako typ singleton pro lepší výkon.</span><span class="sxs-lookup"><span data-stu-id="f607b-121">If you want to store global state in the filter, or no state, add the hub filter type to DI as a singleton for better performance.</span></span> <span data-ttu-id="f607b-122">Případně přidejte filtr jako instanci, pokud můžete.</span><span class="sxs-lookup"><span data-stu-id="f607b-122">Alternatively, add the filter as an instance if you can.</span></span>

## <a name="create-hub-filters"></a><span data-ttu-id="f607b-123">Vytváření filtrů centra</span><span class="sxs-lookup"><span data-stu-id="f607b-123">Create hub filters</span></span>

<span data-ttu-id="f607b-124">Vytvořte filtr tím, že deklarujete třídu, která dědí z `IHubFilter` , a přidejte `InvokeMethodAsync` metodu.</span><span class="sxs-lookup"><span data-stu-id="f607b-124">Create a filter by declaring a class that inherits from `IHubFilter`, and add the `InvokeMethodAsync` method.</span></span> <span data-ttu-id="f607b-125">K dispozici je také `OnConnectedAsync` `OnDisconnectedAsync` možnost, která může být volitelně implementována pro zabalení `OnConnectedAsync` metody a v `OnDisconnectedAsync` uvedeném pořadí.</span><span class="sxs-lookup"><span data-stu-id="f607b-125">There is also `OnConnectedAsync` and `OnDisconnectedAsync` that can optionally be implemented to wrap the `OnConnectedAsync` and `OnDisconnectedAsync` hub methods respectively.</span></span>

```csharp
public class CustomFilter : IHubFilter
{
    public async ValueTask<object> InvokeMethodAsync(
        HubInvocationContext invocationContext, Func<HubInvocationContext, ValueTask<object>> next)
    {
        Console.WriteLine($"Calling hub method '{invocationContext.HubMethodName}'");
        try
        {
            return await next(invocationContext);
        }
        catch (Exception ex)
        {
            Console.WriteLine($"Exception calling '{invocationContext.HubMethodName}'");
            throw ex;
        }
    }

    // Optional method
    public Task OnConnectedAsync(HubLifetimeContext context, Func<HubLifetimeContext, Task> next)
    {
        return next(context);
    }

    // Optional method
    public Task OnDisconnectedAsync(
        HubLifetimeContext context, Exception exception, Func<HubLifetimeContext, Exception, Task> next)
    {
        return next(context, exception);
    }
}
```

<span data-ttu-id="f607b-126">Filtry jsou velmi podobné middlewaru.</span><span class="sxs-lookup"><span data-stu-id="f607b-126">Filters are very similar to middleware.</span></span> <span data-ttu-id="f607b-127">`next`Metoda vyvolá další filtr.</span><span class="sxs-lookup"><span data-stu-id="f607b-127">The `next` method invokes the next filter.</span></span> <span data-ttu-id="f607b-128">Výsledný filtr vyvolá metodu hub.</span><span class="sxs-lookup"><span data-stu-id="f607b-128">The final filter will invoke the hub method.</span></span> <span data-ttu-id="f607b-129">Filtry mohou také uložit výsledek z `next` logice and run Logic po volání metody centra před vrácením výsledku z `next` .</span><span class="sxs-lookup"><span data-stu-id="f607b-129">Filters can also store the result from awaiting `next` and run logic after the hub method has been called before returning the result from `next`.</span></span>

<span data-ttu-id="f607b-130">Chcete-li přeskočit vyvolání metody centra ve filtru, vyvolejte výjimku typu `HubException` místo volání `next` .</span><span class="sxs-lookup"><span data-stu-id="f607b-130">To skip a hub method invocation in a filter, throw an exception of type `HubException` instead of calling `next`.</span></span> <span data-ttu-id="f607b-131">Klient obdrží chybu, pokud očekával výsledek.</span><span class="sxs-lookup"><span data-stu-id="f607b-131">The client will receive an error if it was expecting a result.</span></span>

## <a name="use-hub-filters"></a><span data-ttu-id="f607b-132">Použití filtrů centra</span><span class="sxs-lookup"><span data-stu-id="f607b-132">Use hub filters</span></span>

<span data-ttu-id="f607b-133">Při psaní logiky filtru se pokuste vytvořit obecné pomocí atributů na metodách rozbočovače namísto kontroly názvů metod rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="f607b-133">When writing the filter logic, try to make it generic by using attributes on hub methods instead of checking for hub method names.</span></span>

<span data-ttu-id="f607b-134">Vezměte v úvahu filtr, který zkontroluje argument metody centra pro zakázané fráze a nahradí všechny fráze, které nalezne `***` .</span><span class="sxs-lookup"><span data-stu-id="f607b-134">Consider a filter that will check a hub method argument for banned phrases and replace any phrases it finds with `***`.</span></span>
<span data-ttu-id="f607b-135">V tomto příkladu předpokládáme, že `LanguageFilterAttribute` je definována třída.</span><span class="sxs-lookup"><span data-stu-id="f607b-135">For this example, assume a `LanguageFilterAttribute` class is defined.</span></span> <span data-ttu-id="f607b-136">Třída má vlastnost s názvem `FilterArgument` , která může být nastavena při použití atributu.</span><span class="sxs-lookup"><span data-stu-id="f607b-136">The class has a property named `FilterArgument` that can be set when using the attribute.</span></span>

1. <span data-ttu-id="f607b-137">Umístěte atribut na metodu centra s řetězcovým argumentem, který se má vyčistit:</span><span class="sxs-lookup"><span data-stu-id="f607b-137">Place the attribute on the hub method that has a string argument to be cleaned:</span></span>

    ```csharp
    public class ChatHub
    {
        [LanguageFilter(filterArgument: 0)]
        public async Task SendMessage(string message, string username)
        {
            await Clients.All.SendAsync("SendMessage", $"{username} says: {message}");
        }
    }
    ```

1. <span data-ttu-id="f607b-138">Definujte filtr centra pro kontrolu atributu a nahraďte zakázané fráze v argumentu metody centra pomocí `***` :</span><span class="sxs-lookup"><span data-stu-id="f607b-138">Define a hub filter to check for the attribute and replace banned phrases in a hub method argument with `***`:</span></span>

    ```csharp
    public class LanguageFilter : IHubFilter
    {
        // populated from a file or inline
        private List<string> bannedPhrases = new List<string> { "async void", ".Result" };

        public async ValueTask<object> InvokeMethodAsync(HubInvocationContext invocationContext, 
            Func<HubInvocationContext, ValueTask<object>> next)
        {
            var languageFilter = (LanguageFilterAttribute)Attribute.GetCustomAttribute(
                invocationContext.HubMethod, typeof(LanguageFilterAttribute));
            if (languageFilter != null &&
                invocationContext.HubMethodArguments.Count > languageFilter.FilterArgument &&
                invocationContext.HubMethodArguments[languageFilter.FilterArgument] is string str)
            {
                foreach (var bannedPhrase in bannedPhrases)
                {
                    str = str.Replace(bannedPhrase, "***");
                }

                arguments = invocationContext.HubMethodArguments.ToArray();
                arguments[languageFilter.FilterArgument] = str;
                invocationContext = new HubInvocationContext(invocationContext.Context,
                    invocationContext.ServiceProvider,
                    invocationContext.Hub,
                    invocationContext.HubMethod,
                    arguments);
            }

            return await next(invocationContext);
        }
    }
    ```

1. <span data-ttu-id="f607b-139">Zaregistrujte filtr centra v `Startup.ConfigureServices` metodě.</span><span class="sxs-lookup"><span data-stu-id="f607b-139">Register the hub filter in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="f607b-140">Aby se zabránilo opětovné inicializaci seznamu zakázaných frází pro každé vyvolání, je filtr rozbočovače zaregistrován jako typ singleton:</span><span class="sxs-lookup"><span data-stu-id="f607b-140">To avoid reinitializing the banned phrases list for every invocation, the hub filter is registered as a singleton:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSignalR(hubOptions =>
        {
            hubOptions.AddFilter<LanguageFilter>();
        });
    
        services.AddSingleton<LanguageFilter>();
    }
    ```

## <a name="the-hubinvocationcontext-object"></a><span data-ttu-id="f607b-141">Objekt HubInvocationContext</span><span class="sxs-lookup"><span data-stu-id="f607b-141">The HubInvocationContext object</span></span>

<span data-ttu-id="f607b-142">`HubInvocationContext`Obsahuje informace pro aktuální vyvolání metody centra.</span><span class="sxs-lookup"><span data-stu-id="f607b-142">The `HubInvocationContext` contains information for the current hub method invocation.</span></span>

| <span data-ttu-id="f607b-143">Vlastnost</span><span class="sxs-lookup"><span data-stu-id="f607b-143">Property</span></span> | <span data-ttu-id="f607b-144">Popis</span><span class="sxs-lookup"><span data-stu-id="f607b-144">Description</span></span> | <span data-ttu-id="f607b-145">Typ</span><span class="sxs-lookup"><span data-stu-id="f607b-145">Type</span></span> |
| ------ | ------ | ----------- |
| `Context ` | <span data-ttu-id="f607b-146">`HubCallerContext`Obsahuje informace o připojení.</span><span class="sxs-lookup"><span data-stu-id="f607b-146">The `HubCallerContext` contains information about the connection.</span></span> | `HubCallerContext` |
| `Hub` | <span data-ttu-id="f607b-147">Instance centra, která se používá pro volání této metody rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="f607b-147">The instance of the Hub being used for this hub method invocation.</span></span> | `Hub` |
| `HubMethodName` | <span data-ttu-id="f607b-148">Název vyvolané metody centra.</span><span class="sxs-lookup"><span data-stu-id="f607b-148">The name of the hub method being invoked.</span></span> | `string` |
| `HubMethodArguments` | <span data-ttu-id="f607b-149">Seznam argumentů předávaných metodě centra</span><span class="sxs-lookup"><span data-stu-id="f607b-149">The list of arguments being passed to the hub method.</span></span> | `IReadOnlyList<string>` |
| `ServiceProvider` | <span data-ttu-id="f607b-150">Poskytovatel služeb v oboru pro toto vyvolání metody rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="f607b-150">The scoped service provider for this hub method invocation.</span></span> | `IServiceProvider` |
| `HubMethod` | <span data-ttu-id="f607b-151">Informace o metodě centra</span><span class="sxs-lookup"><span data-stu-id="f607b-151">The hub method information.</span></span> | `MethodInfo` |

## <a name="the-hublifetimecontext-object"></a><span data-ttu-id="f607b-152">Objekt HubLifetimeContext</span><span class="sxs-lookup"><span data-stu-id="f607b-152">The HubLifetimeContext object</span></span>

<span data-ttu-id="f607b-153">`HubLifetimeContext`Obsahuje informace pro `OnConnectedAsync` `OnDisconnectedAsync` metody a centra.</span><span class="sxs-lookup"><span data-stu-id="f607b-153">The `HubLifetimeContext` contains information for the `OnConnectedAsync` and `OnDisconnectedAsync` hub methods.</span></span>

| <span data-ttu-id="f607b-154">Vlastnost</span><span class="sxs-lookup"><span data-stu-id="f607b-154">Property</span></span> | <span data-ttu-id="f607b-155">Popis</span><span class="sxs-lookup"><span data-stu-id="f607b-155">Description</span></span> | <span data-ttu-id="f607b-156">Typ</span><span class="sxs-lookup"><span data-stu-id="f607b-156">Type</span></span> |
| ------ | ------ | ----------- |
| `Context ` | <span data-ttu-id="f607b-157">`HubCallerContext`Obsahuje informace o připojení.</span><span class="sxs-lookup"><span data-stu-id="f607b-157">The `HubCallerContext` contains information about the connection.</span></span> | `HubCallerContext` |
| `Hub` | <span data-ttu-id="f607b-158">Instance centra, která se používá pro volání této metody rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="f607b-158">The instance of the Hub being used for this hub method invocation.</span></span> | `Hub` |
| `ServiceProvider` | <span data-ttu-id="f607b-159">Poskytovatel služeb v oboru pro toto vyvolání metody rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="f607b-159">The scoped service provider for this hub method invocation.</span></span> | `IServiceProvider` |

## <a name="authorization-and-filters"></a><span data-ttu-id="f607b-160">Autorizace a filtry</span><span class="sxs-lookup"><span data-stu-id="f607b-160">Authorization and filters</span></span>

<span data-ttu-id="f607b-161">[Autorizace atributů v metodách centra](xref:signalr/authn-and-authz#use-authorization-handlers-to-customize-hub-method-authorization) se spouští před filtry centra.</span><span class="sxs-lookup"><span data-stu-id="f607b-161">[Authorize attributes on hub methods](xref:signalr/authn-and-authz#use-authorization-handlers-to-customize-hub-method-authorization) run before hub filters.</span></span>

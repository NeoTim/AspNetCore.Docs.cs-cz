---
title: Částí aplikace v ASP.NET Core
author: ardalis
description: Další informace o použití částí aplikace, které jsou abstrakce nad prostředky, které aplikace, vyhledat nebo Vyhněte se načítání funkcí ze sestavení.
ms.author: riande
ms.date: 01/04/2017
uid: mvc/extensibility/app-parts
ms.openlocfilehash: 9d0b4b5fadcc287172f23fa36c421f04ca2ade4a
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815485"
---
# <a name="application-parts-in-aspnet-core"></a><span data-ttu-id="e7a27-103">Částí aplikace v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e7a27-103">Application Parts in ASP.NET Core</span></span>

<span data-ttu-id="e7a27-104">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e7a27-104">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="e7a27-105">*Aplikace část* je abstrakcí nad prostředky, které aplikace, ze kterého MVC funkce, jako je řadiče zobrazení komponenty, nebo může být zjištěny pomocných rutin značek.</span><span class="sxs-lookup"><span data-stu-id="e7a27-105">An *Application Part* is an abstraction over the resources of an application, from which MVC features like controllers, view components, or tag helpers may be discovered.</span></span> <span data-ttu-id="e7a27-106">Jedním z příkladů části aplikace je AssemblyPart, který zapouzdřuje odkaz na sestavení a zpřístupňuje typy a odkazy na sestavení.</span><span class="sxs-lookup"><span data-stu-id="e7a27-106">One example of an application part is an AssemblyPart, which encapsulates an assembly reference and exposes types and compilation references.</span></span> <span data-ttu-id="e7a27-107">*Funkce poskytovatele* fungují s částí aplikace k naplnění funkce aplikace ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="e7a27-107">*Feature providers* work with application parts to populate the features of an ASP.NET Core MVC app.</span></span> <span data-ttu-id="e7a27-108">Případ použití hlavní částí aplikace je k tomu, abyste do konfigurace aplikace pro zjišťování (nebo vyloučit načítání) funkcemi technologie MVC ze sestavení.</span><span class="sxs-lookup"><span data-stu-id="e7a27-108">The main use case for application parts is to allow you to configure your app to discover (or avoid loading) MVC features from an assembly.</span></span>

## <a name="introducing-application-parts"></a><span data-ttu-id="e7a27-109">Úvod do částí aplikace</span><span class="sxs-lookup"><span data-stu-id="e7a27-109">Introducing Application Parts</span></span>

<span data-ttu-id="e7a27-110">Aplikace MVC načítat jejich funkcí z [částí aplikace](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.applicationpart).</span><span class="sxs-lookup"><span data-stu-id="e7a27-110">MVC apps load their features from [application parts](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.applicationpart).</span></span> <span data-ttu-id="e7a27-111">Zejména v případě [AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart) třída reprezentuje aplikace část, která je založená na sestavení.</span><span class="sxs-lookup"><span data-stu-id="e7a27-111">In particular, the [AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart) class represents an application part that's backed by an assembly.</span></span> <span data-ttu-id="e7a27-112">Tyto třídy můžete zjišťovat a načíst funkcemi technologie MVC., jako jsou řadiče, zobrazení komponenty, pomocných rutin značek a razor kompilace zdrojů.</span><span class="sxs-lookup"><span data-stu-id="e7a27-112">You can use these classes to discover and load MVC features, such as controllers, view components, tag helpers, and razor compilation sources.</span></span> <span data-ttu-id="e7a27-113">[ApplicationPartManager](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.applicationpartmanager) zodpovídá za sledování částí aplikace a poskytovatelů funkcí, které jsou k dispozici pro aplikaci MVC.</span><span class="sxs-lookup"><span data-stu-id="e7a27-113">The [ApplicationPartManager](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.applicationpartmanager) is responsible for tracking the application parts and feature providers available to the MVC app.</span></span> <span data-ttu-id="e7a27-114">Můžete pracovat `ApplicationPartManager` v `Startup` při konfiguraci MVC:</span><span class="sxs-lookup"><span data-stu-id="e7a27-114">You can interact with the `ApplicationPartManager` in `Startup` when you configure MVC:</span></span>

```csharp
// create an assembly part from a class's assembly
var assembly = typeof(Startup).GetTypeInfo().Assembly;
services.AddMvc()
    .AddApplicationPart(assembly);

// OR
var assembly = typeof(Startup).GetTypeInfo().Assembly;
var part = new AssemblyPart(assembly);
services.AddMvc()
    .ConfigureApplicationPartManager(apm => apm.ApplicationParts.Add(part));
```

<span data-ttu-id="e7a27-115">Ve výchozím nastavení bude MVC vyhledání stromu závislostí a vyhledat řadiče (i v jiných sestavení).</span><span class="sxs-lookup"><span data-stu-id="e7a27-115">By default MVC will search the dependency tree and find controllers (even in other assemblies).</span></span> <span data-ttu-id="e7a27-116">Načtení libovolného sestavení (například z modulu plug-in, který se neodkazuje v době kompilace), můžete použít jako součást aplikace.</span><span class="sxs-lookup"><span data-stu-id="e7a27-116">To load an arbitrary assembly (for instance, from a plugin that isn't referenced at compile time), you can use an application part.</span></span>

<span data-ttu-id="e7a27-117">Můžete použít částí aplikace na *vyhnout* hledáte řadiče v konkrétní sestavení nebo umístění.</span><span class="sxs-lookup"><span data-stu-id="e7a27-117">You can use application parts to *avoid* looking for controllers in a particular assembly or location.</span></span> <span data-ttu-id="e7a27-118">Můžete řídit, které části (nebo sestavení) jsou k dispozici pro aplikaci tak, že upravíte `ApplicationParts` kolekce `ApplicationPartManager`.</span><span class="sxs-lookup"><span data-stu-id="e7a27-118">You can control which parts (or assemblies) are available to the app by modifying the `ApplicationParts` collection of the `ApplicationPartManager`.</span></span> <span data-ttu-id="e7a27-119">Pořadí položek v `ApplicationParts` kolekce není důležité.</span><span class="sxs-lookup"><span data-stu-id="e7a27-119">The order of the entries in the `ApplicationParts` collection isn't important.</span></span> <span data-ttu-id="e7a27-120">Je potřeba nakonfigurovat plně `ApplicationPartManager` před použitím konfigurace služby v kontejneru.</span><span class="sxs-lookup"><span data-stu-id="e7a27-120">It's important to fully configure the `ApplicationPartManager` before using it to configure services in the container.</span></span> <span data-ttu-id="e7a27-121">Například byste měli plně nakonfigurovat `ApplicationPartManager` před vyvoláním `AddControllersAsServices`.</span><span class="sxs-lookup"><span data-stu-id="e7a27-121">For example, you should fully configure the `ApplicationPartManager` before invoking `AddControllersAsServices`.</span></span> <span data-ttu-id="e7a27-122">Pokud tak neučiníte, znamená, že je, že to nebude mít vliv volání metody řadiče v částí aplikace přidá za (nebude zaregistrovat jako služba) což může způsobit nesprávné chování vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="e7a27-122">Failing to do so, will mean that controllers in application parts added after that method call won't be affected (won't get registered as services) which might result in incorrect behavior of your application.</span></span>

<span data-ttu-id="e7a27-123">Pokud máte sestavení, která obsahuje řadiče nechcete používat, odeberte ho z `ApplicationPartManager`:</span><span class="sxs-lookup"><span data-stu-id="e7a27-123">If you have an assembly that contains controllers you don't want to be used, remove it from the `ApplicationPartManager`:</span></span>

```csharp
services.AddMvc()
    .ConfigureApplicationPartManager(apm =>
    {
        var dependentLibrary = apm.ApplicationParts
            .FirstOrDefault(part => part.Name == "DependentLibrary");

        if (dependentLibrary != null)
        {
           apm.ApplicationParts.Remove(dependentLibrary);
        }
    })
```

<span data-ttu-id="e7a27-124">Kromě sestavení vašeho projektu a jeho závislých sestavení `ApplicationPartManager` bude obsahovat části pro `Microsoft.AspNetCore.Mvc.TagHelpers` a `Microsoft.AspNetCore.Mvc.Razor` ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="e7a27-124">In addition to your project's assembly and its dependent assemblies, the `ApplicationPartManager` will include parts for `Microsoft.AspNetCore.Mvc.TagHelpers` and `Microsoft.AspNetCore.Mvc.Razor` by default.</span></span>

## <a name="application-feature-providers"></a><span data-ttu-id="e7a27-125">Poskytovatelé funkce aplikací</span><span class="sxs-lookup"><span data-stu-id="e7a27-125">Application Feature Providers</span></span>

<span data-ttu-id="e7a27-126">Funkce poskytovatelů aplikací zkontrolujte částí aplikace a poskytují funkce pro tyto součásti.</span><span class="sxs-lookup"><span data-stu-id="e7a27-126">Application Feature Providers examine application parts and provide features for those parts.</span></span> <span data-ttu-id="e7a27-127">Existují zprostředkovatelé integrovaná funkce pro následující funkce MVC:</span><span class="sxs-lookup"><span data-stu-id="e7a27-127">There are built-in feature providers for the following MVC features:</span></span>

* [<span data-ttu-id="e7a27-128">Kontrolery</span><span class="sxs-lookup"><span data-stu-id="e7a27-128">Controllers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [<span data-ttu-id="e7a27-129">Odkaz na metadata</span><span class="sxs-lookup"><span data-stu-id="e7a27-129">Metadata Reference</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.razor.compilation.metadatareferencefeatureprovider)
* [<span data-ttu-id="e7a27-130">Pomocné rutiny značek</span><span class="sxs-lookup"><span data-stu-id="e7a27-130">Tag Helpers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [<span data-ttu-id="e7a27-131">Komponenty zobrazení</span><span class="sxs-lookup"><span data-stu-id="e7a27-131">View Components</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

<span data-ttu-id="e7a27-132">Poskytovatelé funkce dědit z `IApplicationFeatureProvider<T>`, kde `T` je typ funkce.</span><span class="sxs-lookup"><span data-stu-id="e7a27-132">Feature providers inherit from `IApplicationFeatureProvider<T>`, where `T` is the type of the feature.</span></span> <span data-ttu-id="e7a27-133">Můžete implementovat vlastní funkce, kterou poskytovatelů pro všechny typy MVC funkce uvedené výše.</span><span class="sxs-lookup"><span data-stu-id="e7a27-133">You can implement your own feature providers for any of MVC's feature types listed above.</span></span> <span data-ttu-id="e7a27-134">Pořadí poskytovatelů funkcí v `ApplicationPartManager.FeatureProviders` kolekce může být důležité, od poskytovatelů novější můžete reagovat na akce provedené předchozí poskytovatelů.</span><span class="sxs-lookup"><span data-stu-id="e7a27-134">The order of feature providers in the `ApplicationPartManager.FeatureProviders` collection can be important, since later providers can react to actions taken by previous providers.</span></span>

### <a name="sample-generic-controller-feature"></a><span data-ttu-id="e7a27-135">Ukázka: Funkce obecného adaptér</span><span class="sxs-lookup"><span data-stu-id="e7a27-135">Sample: Generic controller feature</span></span>

<span data-ttu-id="e7a27-136">Ve výchozím nastavení, ASP.NET Core MVC ignoruje obecný řadiče (například `SomeController<T>`).</span><span class="sxs-lookup"><span data-stu-id="e7a27-136">By default, ASP.NET Core MVC ignores generic controllers (for example, `SomeController<T>`).</span></span> <span data-ttu-id="e7a27-137">Tato ukázka používá poskytovatele funkce kontroleru, který se spustí po výchozího zprostředkovatele a přidá instance obecného kontroleru pro zadaný seznam typů (definované v `EntityTypes.Types`):</span><span class="sxs-lookup"><span data-stu-id="e7a27-137">This sample uses a controller feature provider that runs after the default provider and adds generic controller instances for a specified list of types (defined in `EntityTypes.Types`):</span></span>

[!code-csharp[](./app-parts/sample/AppPartsSample/GenericControllerFeatureProvider.cs?highlight=13&range=18-36)]

<span data-ttu-id="e7a27-138">Typy entit:</span><span class="sxs-lookup"><span data-stu-id="e7a27-138">The entity types:</span></span>

[!code-csharp[](./app-parts/sample/AppPartsSample/Model/EntityTypes.cs?range=6-16)]

<span data-ttu-id="e7a27-139">Poskytovatel funkce bude přidána do `Startup`:</span><span class="sxs-lookup"><span data-stu-id="e7a27-139">The feature provider is added in `Startup`:</span></span>

```csharp
services.AddMvc()
    .ConfigureApplicationPartManager(apm => 
        apm.FeatureProviders.Add(new GenericControllerFeatureProvider()));
```

<span data-ttu-id="e7a27-140">Ve výchozím nastavení, názvy obecný kontroleru používaný ke směrování by formuláře *GenericController'1 [widgetu]* místo *Widget*.</span><span class="sxs-lookup"><span data-stu-id="e7a27-140">By default, the generic controller names used for routing would be of the form *GenericController\`1[Widget]* instead of *Widget*.</span></span> <span data-ttu-id="e7a27-141">Tento atribut se používá k úpravě názvu tak, aby odpovídaly obecný typ používaný řadičem:</span><span class="sxs-lookup"><span data-stu-id="e7a27-141">The following attribute is used to modify the name to correspond to the generic type used by the controller:</span></span>

[!code-csharp[](./app-parts/sample/AppPartsSample/GenericControllerNameConvention.cs)]

<span data-ttu-id="e7a27-142">Třída `GenericController`:</span><span class="sxs-lookup"><span data-stu-id="e7a27-142">The `GenericController` class:</span></span>

[!code-csharp[](./app-parts/sample/AppPartsSample/GenericController.cs?highlight=5-6)]

<span data-ttu-id="e7a27-143">Výsledek, pokud se požaduje odpovídající trasy:</span><span class="sxs-lookup"><span data-stu-id="e7a27-143">The result, when a matching route is requested:</span></span>

![Příklad výstupu z ukázkové aplikace načte "Hello z obecného velkými ozubenými řadiče."](app-parts/_static/generic-controller.png)

### <a name="sample-display-available-features"></a><span data-ttu-id="e7a27-145">Ukázka: Zobrazit dostupné funkce</span><span class="sxs-lookup"><span data-stu-id="e7a27-145">Sample: Display available features</span></span>

<span data-ttu-id="e7a27-146">Můžete iterovat mají údaj vyplněný funkce, které jsou do vaší aplikace pomocí žádosti `ApplicationPartManager` prostřednictvím [injektáž závislostí](../../fundamentals/dependency-injection.md) a použít ho k naplnění instancí odpovídající funkce:</span><span class="sxs-lookup"><span data-stu-id="e7a27-146">You can iterate through the populated features available to your app by requesting an `ApplicationPartManager` through [dependency injection](../../fundamentals/dependency-injection.md) and using it to populate instances of the appropriate features:</span></span>

[!code-csharp[](./app-parts/sample/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

<span data-ttu-id="e7a27-147">Příklad výstupu:</span><span class="sxs-lookup"><span data-stu-id="e7a27-147">Example output:</span></span>

![Ukázkový výstup z ukázkové aplikace](app-parts/_static/available-features.png)

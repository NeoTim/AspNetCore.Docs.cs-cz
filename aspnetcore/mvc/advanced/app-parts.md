---
title: Části aplikace v ASP.NET Core
author: ardalis
description: Naučte se, jak používat části aplikace, které jsou abstrakcemi pro prostředky aplikace, ke zjišťování nebo zamezení načítání funkcí ze sestavení.
ms.author: riande
ms.date: 01/04/2017
uid: mvc/extensibility/app-parts
ms.openlocfilehash: 4900ccf5589500db076f8cecd9da198c6a7ceea4
ms.sourcegitcommit: 41f2c1a6b316e6e368a4fd27a8b18d157cef91e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2019
ms.locfileid: "69886467"
---
<!-- DO NOT MAKE CHANGES BEFORE https://github.com/aspnet/AspNetCore.Docs/pull/12376 Merges -->

# <a name="application-parts-in-aspnet-core"></a><span data-ttu-id="aebc1-103">Části aplikace v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="aebc1-103">Application Parts in ASP.NET Core</span></span>

<span data-ttu-id="aebc1-104">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="aebc1-104">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="aebc1-105">*Část aplikace* je abstrakcí pro prostředky aplikace, ze kterých mohou být zjištěny funkce MVC, jako jsou řadiče, zobrazit součásti nebo pomocníky značek.</span><span class="sxs-lookup"><span data-stu-id="aebc1-105">An *Application Part* is an abstraction over the resources of an application, from which MVC features like controllers, view components, or tag helpers may be discovered.</span></span> <span data-ttu-id="aebc1-106">Jedním z příkladů části aplikace je AssemblyPart, který zapouzdřuje odkaz na sestavení a zpřístupňuje typy a odkazy na kompilaci.</span><span class="sxs-lookup"><span data-stu-id="aebc1-106">One example of an application part is an AssemblyPart, which encapsulates an assembly reference and exposes types and compilation references.</span></span> <span data-ttu-id="aebc1-107">*Poskytovatelé funkcí* fungují s součástmi aplikace k naplnění funkcí ASP.NET Core aplikace MVC.</span><span class="sxs-lookup"><span data-stu-id="aebc1-107">*Feature providers* work with application parts to populate the features of an ASP.NET Core MVC app.</span></span> <span data-ttu-id="aebc1-108">Hlavním případem použití součástí aplikace je umožnění konfigurace aplikace pro zjišťování (nebo zamezení načítání) funkcí MVC ze sestavení.</span><span class="sxs-lookup"><span data-stu-id="aebc1-108">The main use case for application parts is to allow you to configure your app to discover (or avoid loading) MVC features from an assembly.</span></span>

## <a name="introducing-application-parts"></a><span data-ttu-id="aebc1-109">Představení částí aplikace</span><span class="sxs-lookup"><span data-stu-id="aebc1-109">Introducing Application Parts</span></span>

<span data-ttu-id="aebc1-110">Aplikace MVC načítají své funkce z [částí aplikace](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.applicationpart).</span><span class="sxs-lookup"><span data-stu-id="aebc1-110">MVC apps load their features from [application parts](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.applicationpart).</span></span> <span data-ttu-id="aebc1-111">Konkrétně třída [AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart) představuje součást aplikace, která je zajištěna sestavením.</span><span class="sxs-lookup"><span data-stu-id="aebc1-111">In particular, the [AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart) class represents an application part that's backed by an assembly.</span></span> <span data-ttu-id="aebc1-112">Tyto třídy můžete použít ke zjišťování a načítání funkcí MVC, například řadičů, zobrazení komponent, pomocníků značek a zdrojů kompilace Razor.</span><span class="sxs-lookup"><span data-stu-id="aebc1-112">You can use these classes to discover and load MVC features, such as controllers, view components, tag helpers, and razor compilation sources.</span></span> <span data-ttu-id="aebc1-113">[ApplicationPartManager](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.applicationpartmanager) zodpovídá za sledování částí aplikace a poskytovatelů funkcí dostupných pro aplikaci MVC.</span><span class="sxs-lookup"><span data-stu-id="aebc1-113">The [ApplicationPartManager](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.applicationpartmanager) is responsible for tracking the application parts and feature providers available to the MVC app.</span></span> <span data-ttu-id="aebc1-114">Při konfiguraci MVC můžete s `ApplicationPartManager` nástrojem pracovat: `Startup`</span><span class="sxs-lookup"><span data-stu-id="aebc1-114">You can interact with the `ApplicationPartManager` in `Startup` when you configure MVC:</span></span>

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

<span data-ttu-id="aebc1-115">Ve výchozím nastavení MVC bude hledat strom závislosti a vyhledat řadiče (dokonce i v jiných sestaveních).</span><span class="sxs-lookup"><span data-stu-id="aebc1-115">By default MVC will search the dependency tree and find controllers (even in other assemblies).</span></span> <span data-ttu-id="aebc1-116">Chcete-li načíst libovolné sestavení (například z modulu plug-in, který není odkazován v době kompilace), můžete použít část aplikace.</span><span class="sxs-lookup"><span data-stu-id="aebc1-116">To load an arbitrary assembly (for instance, from a plugin that isn't referenced at compile time), you can use an application part.</span></span>

<span data-ttu-id="aebc1-117">Můžete použít části aplikace, abyste *se* vyhnuli hledání řadičů v konkrétním sestavení nebo umístění.</span><span class="sxs-lookup"><span data-stu-id="aebc1-117">You can use application parts to *avoid* looking for controllers in a particular assembly or location.</span></span> <span data-ttu-id="aebc1-118">Můžete určit, které části (nebo sestavení) jsou k dispozici pro aplikaci úpravou `ApplicationParts` kolekce. `ApplicationPartManager`</span><span class="sxs-lookup"><span data-stu-id="aebc1-118">You can control which parts (or assemblies) are available to the app by modifying the `ApplicationParts` collection of the `ApplicationPartManager`.</span></span> <span data-ttu-id="aebc1-119">Pořadí položek v `ApplicationParts` kolekci není důležité.</span><span class="sxs-lookup"><span data-stu-id="aebc1-119">The order of the entries in the `ApplicationParts` collection isn't important.</span></span> <span data-ttu-id="aebc1-120">Je důležité, abyste před tím, `ApplicationPartManager` než je použijete ke konfiguraci služeb v kontejneru, plně nakonfigurovali.</span><span class="sxs-lookup"><span data-stu-id="aebc1-120">It's important to fully configure the `ApplicationPartManager` before using it to configure services in the container.</span></span> <span data-ttu-id="aebc1-121">Například je třeba `ApplicationPartManager` před vyvoláním `AddControllersAsServices`plně nakonfigurovat.</span><span class="sxs-lookup"><span data-stu-id="aebc1-121">For example, you should fully configure the `ApplicationPartManager` before invoking `AddControllersAsServices`.</span></span> <span data-ttu-id="aebc1-122">Pokud se to nepovede, znamená to, že řadiče v součástech aplikace přidané po volání této metody nebudou ovlivněny (nebudou se registrovat jako služby), což by mohlo vést k nesprávnému chování vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="aebc1-122">Failing to do so, will mean that controllers in application parts added after that method call won't be affected (won't get registered as services) which might result in incorrect behavior of your application.</span></span>

<span data-ttu-id="aebc1-123">Pokud máte sestavení, které obsahuje řadiče, které nechcete používat, odeberte ho z `ApplicationPartManager`:</span><span class="sxs-lookup"><span data-stu-id="aebc1-123">If you have an assembly that contains controllers you don't want to be used, remove it from the `ApplicationPartManager`:</span></span>

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

<span data-ttu-id="aebc1-124">Kromě sestavení projektu a jeho závislých sestavení `ApplicationPartManager` bude obsahovat části pro `Microsoft.AspNetCore.Mvc.TagHelpers` a `Microsoft.AspNetCore.Mvc.Razor` ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="aebc1-124">In addition to your project's assembly and its dependent assemblies, the `ApplicationPartManager` will include parts for `Microsoft.AspNetCore.Mvc.TagHelpers` and `Microsoft.AspNetCore.Mvc.Razor` by default.</span></span>

## <a name="application-feature-providers"></a><span data-ttu-id="aebc1-125">Poskytovatelé funkcí aplikace</span><span class="sxs-lookup"><span data-stu-id="aebc1-125">Application Feature Providers</span></span>

<span data-ttu-id="aebc1-126">Poskytovatelé funkcí aplikací zkoumají části aplikace a poskytují funkce pro tyto části.</span><span class="sxs-lookup"><span data-stu-id="aebc1-126">Application Feature Providers examine application parts and provide features for those parts.</span></span> <span data-ttu-id="aebc1-127">K dispozici jsou předdefinovaná poskytovatelé funkcí pro následující funkce MVC:</span><span class="sxs-lookup"><span data-stu-id="aebc1-127">There are built-in feature providers for the following MVC features:</span></span>

* [<span data-ttu-id="aebc1-128">Kontrolery</span><span class="sxs-lookup"><span data-stu-id="aebc1-128">Controllers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [<span data-ttu-id="aebc1-129">Odkaz na metadata</span><span class="sxs-lookup"><span data-stu-id="aebc1-129">Metadata Reference</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.razor.compilation.metadatareferencefeatureprovider)
* [<span data-ttu-id="aebc1-130">Pomocné rutiny značek</span><span class="sxs-lookup"><span data-stu-id="aebc1-130">Tag Helpers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [<span data-ttu-id="aebc1-131">Zobrazit součásti</span><span class="sxs-lookup"><span data-stu-id="aebc1-131">View Components</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

<span data-ttu-id="aebc1-132">Poskytovatelé funkcí dědí `IApplicationFeatureProvider<T>`z, `T` kde je typ funkce.</span><span class="sxs-lookup"><span data-stu-id="aebc1-132">Feature providers inherit from `IApplicationFeatureProvider<T>`, where `T` is the type of the feature.</span></span> <span data-ttu-id="aebc1-133">Můžete implementovat své vlastní poskytovatele funkcí pro všechny typy funkcí MVC uvedené výše.</span><span class="sxs-lookup"><span data-stu-id="aebc1-133">You can implement your own feature providers for any of MVC's feature types listed above.</span></span> <span data-ttu-id="aebc1-134">Pořadí poskytovatelů funkcí v `ApplicationPartManager.FeatureProviders` kolekci může být důležité, protože další poskytovatelé můžou reagovat na akce prováděné předchozími poskytovateli.</span><span class="sxs-lookup"><span data-stu-id="aebc1-134">The order of feature providers in the `ApplicationPartManager.FeatureProviders` collection can be important, since later providers can react to actions taken by previous providers.</span></span>

### <a name="sample-generic-controller-feature"></a><span data-ttu-id="aebc1-135">Vzorku Funkce obecného kontroleru</span><span class="sxs-lookup"><span data-stu-id="aebc1-135">Sample: Generic controller feature</span></span>

<span data-ttu-id="aebc1-136">Ve výchozím nastavení ASP.NET Core MVC ignoruje obecné řadiče (například `SomeController<T>`).</span><span class="sxs-lookup"><span data-stu-id="aebc1-136">By default, ASP.NET Core MVC ignores generic controllers (for example, `SomeController<T>`).</span></span> <span data-ttu-id="aebc1-137">Tato ukázka používá poskytovatele funkcí kontroleru, který se spouští po výchozím zprostředkovateli a přidává instance obecného kontroleru pro zadaný seznam typů (definované v `EntityTypes.Types`):</span><span class="sxs-lookup"><span data-stu-id="aebc1-137">This sample uses a controller feature provider that runs after the default provider and adds generic controller instances for a specified list of types (defined in `EntityTypes.Types`):</span></span>

[!code-csharp[](./app-parts/sample/AppPartsSample/GenericControllerFeatureProvider.cs?highlight=13&range=18-36)]

<span data-ttu-id="aebc1-138">Typy entit:</span><span class="sxs-lookup"><span data-stu-id="aebc1-138">The entity types:</span></span>

[!code-csharp[](./app-parts/sample/AppPartsSample/Model/EntityTypes.cs?range=6-16)]

<span data-ttu-id="aebc1-139">Poskytovatel funkce je přidaný v `Startup`:</span><span class="sxs-lookup"><span data-stu-id="aebc1-139">The feature provider is added in `Startup`:</span></span>

```csharp
services.AddMvc()
    .ConfigureApplicationPartManager(apm => 
        apm.FeatureProviders.Add(new GenericControllerFeatureProvider()));
```

<span data-ttu-id="aebc1-140">Ve výchozím nastavení mají názvy obecného kontroleru používané pro směrování tvar *GenericController ' 1 [widget]* namísto *widgetu*.</span><span class="sxs-lookup"><span data-stu-id="aebc1-140">By default, the generic controller names used for routing would be of the form *GenericController\`1[Widget]* instead of *Widget*.</span></span> <span data-ttu-id="aebc1-141">Následující atribut se používá ke změně názvu tak, aby odpovídal obecnému typu používanému řadičem:</span><span class="sxs-lookup"><span data-stu-id="aebc1-141">The following attribute is used to modify the name to correspond to the generic type used by the controller:</span></span>

[!code-csharp[](./app-parts/sample/AppPartsSample/GenericControllerNameConvention.cs)]

<span data-ttu-id="aebc1-142">Třída `GenericController`:</span><span class="sxs-lookup"><span data-stu-id="aebc1-142">The `GenericController` class:</span></span>

[!code-csharp[](./app-parts/sample/AppPartsSample/GenericController.cs?highlight=5-6)]

<span data-ttu-id="aebc1-143">Výsledek, pokud je požadována vyhovující trasa:</span><span class="sxs-lookup"><span data-stu-id="aebc1-143">The result, when a matching route is requested:</span></span>

![Ukázkový výstup z ukázkového čtení aplikace "Hello z obecného Sprocket kontroleru"](app-parts/_static/generic-controller.png)

### <a name="sample-display-available-features"></a><span data-ttu-id="aebc1-145">Vzorku Zobrazit dostupné funkce</span><span class="sxs-lookup"><span data-stu-id="aebc1-145">Sample: Display available features</span></span>

<span data-ttu-id="aebc1-146">Vyplněné funkce, které jsou k dispozici pro vaši aplikaci, `ApplicationPartManager` můžete iterovat vyžádáním [vložením závislosti](../../fundamentals/dependency-injection.md) a jejím použitím k naplnění instancí příslušných funkcí:</span><span class="sxs-lookup"><span data-stu-id="aebc1-146">You can iterate through the populated features available to your app by requesting an `ApplicationPartManager` through [dependency injection](../../fundamentals/dependency-injection.md) and using it to populate instances of the appropriate features:</span></span>

[!code-csharp[](./app-parts/sample/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

<span data-ttu-id="aebc1-147">Příklad výstupu:</span><span class="sxs-lookup"><span data-stu-id="aebc1-147">Example output:</span></span>

![Příklad výstupu ukázkové aplikace](app-parts/_static/available-features.png)

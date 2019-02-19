---
title: Injektáž závislostí do kontrolerů v ASP.NET Core
author: ardalis
description: Zjistěte, jak ASP.NET Core MVC řadiče vyžádat jejich závislosti explicitně prostřednictvím jejich konstruktory s injektáž závislostí v ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
uid: mvc/controllers/dependency-injection
ms.openlocfilehash: 9d9d0a68927da62fad8df72c868eaf4b8ada440d
ms.sourcegitcommit: d75d8eb26c2cce19876c8d5b65ac8a4b21f625ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56410268"
---
# <a name="dependency-injection-into-controllers-in-aspnet-core"></a><span data-ttu-id="97306-103">Injektáž závislostí do kontrolerů v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="97306-103">Dependency injection into controllers in ASP.NET Core</span></span>

<a name="dependency-injection-controllers"></a>

<span data-ttu-id="97306-104">Podle [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="97306-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="97306-105">Kontrolery ASP.NET Core MVC by měl požádat o jejich závislosti explicitně prostřednictvím jejich konstruktory.</span><span class="sxs-lookup"><span data-stu-id="97306-105">ASP.NET Core MVC controllers should request their dependencies explicitly via their constructors.</span></span> <span data-ttu-id="97306-106">V některých případech akce jednotlivých kontroleru může vyžadovat služby a nemusí mít smysl požadavku na úrovni kontroleru.</span><span class="sxs-lookup"><span data-stu-id="97306-106">In some instances, individual controller actions may require a service, and it may not make sense to request at the controller level.</span></span> <span data-ttu-id="97306-107">V takovém případě můžete vložit službu jako parametr v metodě akce.</span><span class="sxs-lookup"><span data-stu-id="97306-107">In this case, you can also choose to inject a service as a parameter on the action method.</span></span>

<span data-ttu-id="97306-108">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="97306-108">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="97306-109">Injektáž závislostí</span><span class="sxs-lookup"><span data-stu-id="97306-109">Dependency Injection</span></span>

<span data-ttu-id="97306-110">Má integrovanou podporu pro ASP.NET Core [injektáž závislostí](../../fundamentals/dependency-injection.md), což usnadňuje aplikace pro testování a udržovat.</span><span class="sxs-lookup"><span data-stu-id="97306-110">ASP.NET Core has built-in support for [dependency injection](../../fundamentals/dependency-injection.md), which makes applications easier to test and maintain.</span></span>

## <a name="constructor-injection"></a><span data-ttu-id="97306-111">Vkládání konstruktor</span><span class="sxs-lookup"><span data-stu-id="97306-111">Constructor Injection</span></span>

<span data-ttu-id="97306-112">ASP.NET Core integrovanou podporu pro vkládání závislostí na základě konstruktoru rozšiřuje na kontrolery MVC.</span><span class="sxs-lookup"><span data-stu-id="97306-112">ASP.NET Core's built-in support for constructor-based dependency injection extends to MVC controllers.</span></span> <span data-ttu-id="97306-113">Pouhým přidáním typ služby k řadiči jako parametr konstruktoru, ASP.NET Core se pokus o vyřešení tohoto typu pomocí jeho sestavení v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="97306-113">By simply adding a service type to your controller as a constructor parameter, ASP.NET Core will attempt to resolve that type using its built in service container.</span></span> <span data-ttu-id="97306-114">Služby jsou obvykle, ale ne vždy definovány, pomocí rozhraní.</span><span class="sxs-lookup"><span data-stu-id="97306-114">Services are typically, but not always, defined using interfaces.</span></span> <span data-ttu-id="97306-115">Například pokud aplikace obsahuje obchodní logiky, která závisí na aktuální čas, můžete vložit službu, která načte čas (nezadávejte napevno-ji), který by umožnilo testy a zajistěte tak předání implementace, které používají nastavit čas.</span><span class="sxs-lookup"><span data-stu-id="97306-115">For example, if your application has business logic that depends on the current time, you can inject a service that retrieves the time (rather than hard-coding it), which would allow your tests to pass in implementations that use a set time.</span></span>

[!code-csharp[](dependency-injection/sample/src/ControllerDI/Interfaces/IDateTime.cs)]


<span data-ttu-id="97306-116">Je triviální implementace rozhraní jako je ten tak, aby používala systémové hodiny za běhu:</span><span class="sxs-lookup"><span data-stu-id="97306-116">Implementing an interface like this one so that it uses the system clock at runtime is trivial:</span></span>

[!code-csharp[](dependency-injection/sample/src/ControllerDI/Services/SystemDateTime.cs)]


<span data-ttu-id="97306-117">Díky tomu na místě můžete použít službu v kontroleru.</span><span class="sxs-lookup"><span data-stu-id="97306-117">With this in place, we can use the service in our controller.</span></span> <span data-ttu-id="97306-118">V tomto případě jsme přidali nějaké logiky do `HomeController` `Index` metodu pro zobrazení pozdrav uživateli na základě času dne.</span><span class="sxs-lookup"><span data-stu-id="97306-118">In this case, we have added some logic to the `HomeController` `Index` method to display a greeting to the user based on the time of day.</span></span>

[!code-csharp[](./dependency-injection/sample/src/ControllerDI/Controllers/HomeController.cs?highlight=8,10,12,17,18,19,20,21,22,23,24,25,26,27,28,29,30&range=1-31,51-52)]

<span data-ttu-id="97306-119">Pokud provozujeme aplikace nyní, jsme se pravděpodobně dojde k chybě:</span><span class="sxs-lookup"><span data-stu-id="97306-119">If we run the application now, we will most likely encounter an error:</span></span>

```
An unhandled exception occurred while processing the request.

InvalidOperationException: Unable to resolve service for type 'ControllerDI.Interfaces.IDateTime' while attempting to activate 'ControllerDI.Controllers.HomeController'.
Microsoft.Extensions.DependencyInjection.ActivatorUtilities.GetService(IServiceProvider sp, Type type, Type requiredBy, Boolean isDefaultParameterRequired)
```

<span data-ttu-id="97306-120">K této chybě dochází, když jsme neprovedli konfiguraci služby ve službě `ConfigureServices` metoda v našich `Startup` třídy.</span><span class="sxs-lookup"><span data-stu-id="97306-120">This error occurs when we have not configured a service in the `ConfigureServices` method in our `Startup` class.</span></span> <span data-ttu-id="97306-121">K určení, že žádosti pro `IDateTime` měla být vyřešena pomocí instance `SystemDateTime`, přidejte zvýrazněný řádek v ukázce níže na vaše `ConfigureServices` metody:</span><span class="sxs-lookup"><span data-stu-id="97306-121">To specify that requests for `IDateTime` should be resolved using an instance of `SystemDateTime`, add the highlighted line in the listing below to your `ConfigureServices` method:</span></span>

[!code-csharp[](./dependency-injection/sample/src/ControllerDI/Startup.cs?highlight=4&range=26-27,42-44)]

> [!NOTE]
> <span data-ttu-id="97306-122">Tento konkrétní službu může implementovat některou z několika možností různé doby života (`Transient`, `Scoped`, nebo `Singleton`).</span><span class="sxs-lookup"><span data-stu-id="97306-122">This particular service could be implemented using any of several different lifetime options (`Transient`, `Scoped`, or `Singleton`).</span></span> <span data-ttu-id="97306-123">Zobrazit [injektáž závislostí](../../fundamentals/dependency-injection.md) pochopit, jak každá z těchto možností oboru ovlivní chování služby.</span><span class="sxs-lookup"><span data-stu-id="97306-123">See [Dependency Injection](../../fundamentals/dependency-injection.md) to understand how each of these scope options will affect the behavior of your service.</span></span>

<span data-ttu-id="97306-124">Jakmile služba byla nakonfigurována, spuštění aplikace a přejdete na domovskou stránku by měla zobrazit zprávy podle času podle očekávání:</span><span class="sxs-lookup"><span data-stu-id="97306-124">Once the service has been configured, running the application and navigating to the home page should display the time-based message as expected:</span></span>

![Pozdrav serveru](dependency-injection/_static/server-greeting.png)

>[!TIP]
> <span data-ttu-id="97306-126">V tématu [testovací kontroler logiku](testing.md) se naučíte usnadňují otestovat explicitní vyžádání závislostí do kontrolerů kódu.</span><span class="sxs-lookup"><span data-stu-id="97306-126">See [Test controller logic](testing.md) to learn how to make code easier to test by explicitly requesting dependencies in controllers.</span></span>

<span data-ttu-id="97306-127">Vkládání předdefinovaných závislostí ASP.NET Core podporuje s pouze jediný konstruktor třídy žádosti o služby.</span><span class="sxs-lookup"><span data-stu-id="97306-127">ASP.NET Core's built-in dependency injection supports having only a single constructor for classes requesting services.</span></span> <span data-ttu-id="97306-128">Pokud máte více než jeden konstruktor, můžete být s oznámením výjimce:</span><span class="sxs-lookup"><span data-stu-id="97306-128">If you have more than one constructor, you may get an exception stating:</span></span>

```
An unhandled exception occurred while processing the request.

InvalidOperationException: Multiple constructors accepting all given argument types have been found in type 'ControllerDI.Controllers.HomeController'. There should only be one applicable constructor.
Microsoft.Extensions.DependencyInjection.ActivatorUtilities.FindApplicableConstructor(Type instanceType, Type[] argumentTypes, ConstructorInfo& matchingConstructor, Nullable`1[]& parameterMap)
```

<span data-ttu-id="97306-129">Jak uvádí chybová zpráva, můžete opravit tento problém s použitím jediný konstruktor.</span><span class="sxs-lookup"><span data-stu-id="97306-129">As the error message states, you can correct this problem with the use of a single constructor.</span></span> <span data-ttu-id="97306-130">Můžete také [nahraďte výchozí kontejner vkládání závislostí třetích stran implementace](xref:fundamentals/dependency-injection#default-service-container-replacement), řadu, které podporují víc konstruktorů.</span><span class="sxs-lookup"><span data-stu-id="97306-130">You can also [replace the default dependency injection container with a third party implementation](xref:fundamentals/dependency-injection#default-service-container-replacement), many of which support multiple constructors.</span></span>

## <a name="action-injection-with-fromservices"></a><span data-ttu-id="97306-131">Vkládání akce s FromServices</span><span class="sxs-lookup"><span data-stu-id="97306-131">Action Injection with FromServices</span></span>

<span data-ttu-id="97306-132">Někdy není nutné služba pro více než jednu akci v rámci vašeho kontroleru.</span><span class="sxs-lookup"><span data-stu-id="97306-132">Sometimes you don't need a service for more than one action within your controller.</span></span> <span data-ttu-id="97306-133">V takovém případě může být vhodné vložit službu jako parametr do metody akce.</span><span class="sxs-lookup"><span data-stu-id="97306-133">In this case, it may make sense to inject the service as a parameter to the action method.</span></span> <span data-ttu-id="97306-134">Uděláte to pomocí označení parametr s atributem `[FromServices]` jak je znázorněno zde:</span><span class="sxs-lookup"><span data-stu-id="97306-134">This is done by marking the parameter with the attribute `[FromServices]` as shown here:</span></span>

[!code-csharp[](./dependency-injection/sample/src/ControllerDI/Controllers/HomeController.cs?highlight=1&range=33-38)]

## <a name="accessing-settings-from-a-controller"></a><span data-ttu-id="97306-135">Přístup k nastavení z Kontroleru</span><span class="sxs-lookup"><span data-stu-id="97306-135">Accessing Settings from a Controller</span></span>

<span data-ttu-id="97306-136">Přístup k aplikaci nebo konfigurace nastavení z v rámci kontroleru je běžný vzor.</span><span class="sxs-lookup"><span data-stu-id="97306-136">Accessing application or configuration settings from within a controller is a common pattern.</span></span> <span data-ttu-id="97306-137">Tento přístup měli použít možnosti vzor, podle [konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="97306-137">This access should use the Options pattern described in [configuration](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="97306-138">Obecně by neměl požadovat nastavení přímo z řadiče pomocí vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="97306-138">You generally shouldn't request settings directly from your controller using dependency injection.</span></span> <span data-ttu-id="97306-139">Lepším řešením je žádost `IOptions<T>` instance, kde `T` je třída konfigurace budete potřebovat.</span><span class="sxs-lookup"><span data-stu-id="97306-139">A better approach is to request an `IOptions<T>` instance, where `T` is the configuration class you need.</span></span>

<span data-ttu-id="97306-140">Pro práci s možností vzoru, musíte vytvořit třídu, která představuje možnosti, jako je například tento:</span><span class="sxs-lookup"><span data-stu-id="97306-140">To work with the options pattern, you need to create a class that represents the options, such as this one:</span></span>

[!code-csharp[](dependency-injection/sample/src/ControllerDI/Model/SampleWebSettings.cs)]

<span data-ttu-id="97306-141">Pak je potřeba nakonfigurovat aplikaci pro použití možnosti modelu a přidat do kolekce služby ve své třídě configuration `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="97306-141">Then you need to configure the application to use the options model and add your configuration class to the services collection in `ConfigureServices`:</span></span>

[!code-csharp[](./dependency-injection/sample/src/ControllerDI/Startup.cs?highlight=3,4,5,6,9,16,19&range=14-44)]

> [!NOTE]
> <span data-ttu-id="97306-142">V seznamu výše jsme se konfigurace aplikace pro čtení nastavení ze souboru ve formátu JSON.</span><span class="sxs-lookup"><span data-stu-id="97306-142">In the above listing, we are configuring the application to read the settings from a JSON-formatted file.</span></span> <span data-ttu-id="97306-143">Můžete také nakonfigurovat nastavení zcela v kódu, jak je uvedeno ve výše uvedeném komentářem kódu.</span><span class="sxs-lookup"><span data-stu-id="97306-143">You can also configure the settings entirely in code, as is shown in the commented code above.</span></span> <span data-ttu-id="97306-144">Zobrazit [konfigurace](xref:fundamentals/configuration/index) pro další možnosti konfigurace.</span><span class="sxs-lookup"><span data-stu-id="97306-144">See [Configuration](xref:fundamentals/configuration/index) for further configuration options.</span></span>

<span data-ttu-id="97306-145">Po zadání objekt konfigurace silného typu (v tomto případě `SampleWebSettings`) a jeho přidání do kolekce služeb, můžete vyžádejte si ho z jakékoli Kontroleru nebo metodě akce požaduje instanci `IOptions<T>` (v tomto případě `IOptions<SampleWebSettings>`) .</span><span class="sxs-lookup"><span data-stu-id="97306-145">Once you've specified a strongly-typed configuration object (in this case, `SampleWebSettings`) and added it to the services collection, you can request it from any Controller or Action method by requesting an instance of `IOptions<T>` (in this case, `IOptions<SampleWebSettings>`).</span></span> <span data-ttu-id="97306-146">Následující kód ukazuje, jak jeden požadovat nastavení z kontroleru:</span><span class="sxs-lookup"><span data-stu-id="97306-146">The following code shows how one would request the settings from a controller:</span></span>

[!code-csharp[](./dependency-injection/sample/src/ControllerDI/Controllers/SettingsController.cs?highlight=3,5,7&range=7-22)]

<span data-ttu-id="97306-147">Následující vzorek možnosti umožňuje nastavení a konfigurace odděleném od sebe a zajišťuje následující kontroler [oddělení oblastí zájmu](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns), protože nepotřebuje vědět, jak a kde najít nastavení informace.</span><span class="sxs-lookup"><span data-stu-id="97306-147">Following the Options pattern allows settings and configuration to be decoupled from one another, and ensures the controller is following [separation of concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns), since it doesn't need to know how or where to find the settings information.</span></span> <span data-ttu-id="97306-148">Také udržuje kontroler usnadňuje [Jednotkový test](testing.md), protože neexistuje žádné přímé vytváření instancí třídy nastavení v rámci třídy kontroleru.</span><span class="sxs-lookup"><span data-stu-id="97306-148">It also makes the controller easier to [unit test](testing.md), since there's no direct instantiation of settings classes within the controller class.</span></span>

---
title: Vzor možnosti v ASP.NET Core
author: guardrex
description: Objevte, jak použít model možnosti k reprezentování skupiny související nastavení v aplikacích ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 11/09/2018
uid: fundamentals/configuration/options
ms.openlocfilehash: 99aa5028a8704c7e9e3010415137e2560213a2ad
ms.sourcegitcommit: edb9d2d78c9a4d68b397e74ae2aff088b325a143
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2018
ms.locfileid: "51505789"
---
# <a name="options-pattern-in-aspnet-core"></a><span data-ttu-id="998e7-103">Vzor možnosti v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="998e7-103">Options pattern in ASP.NET Core</span></span>

<span data-ttu-id="998e7-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="998e7-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="998e7-105">Možnosti vzor používá k reprezentování skupiny související nastavení třídy.</span><span class="sxs-lookup"><span data-stu-id="998e7-105">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="998e7-106">Když [nastavení konfigurace](xref:fundamentals/configuration/index) jsou izolované aplikace dodržuje podle scénáře do samostatné třídy, dvě důležité softwarového inženýrství zásady:</span><span class="sxs-lookup"><span data-stu-id="998e7-106">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="998e7-107">[Princip oddělení rozhraní (ISP)](http://deviq.com/interface-segregation-principle/): scénáře (třídy), které závisí na nastavení konfigurace záviset pouze na nastavení konfigurace, které používají.</span><span class="sxs-lookup"><span data-stu-id="998e7-107">The [Interface Segregation Principle (ISP)](http://deviq.com/interface-segregation-principle/): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="998e7-108">[Oddělení oblastí zájmu](http://deviq.com/separation-of-concerns/): nastavení pro různé části aplikace nejsou závislé nebo propojených mezi sebou.</span><span class="sxs-lookup"><span data-stu-id="998e7-108">[Separation of Concerns](http://deviq.com/separation-of-concerns/): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="998e7-109">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/configuration/options/sample) ([stažení](xref:index#how-to-download-a-sample)) Tento článek je usnadňuje její sledování s ukázkovou aplikací.</span><span class="sxs-lookup"><span data-stu-id="998e7-109">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/configuration/options/sample) ([how to download](xref:index#how-to-download-a-sample)) This article is easier to follow with the sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="998e7-110">Požadavky</span><span class="sxs-lookup"><span data-stu-id="998e7-110">Prerequisites</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="998e7-111">Odkaz [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app) nebo přidat odkaz na balíček [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) balíčku.</span><span class="sxs-lookup"><span data-stu-id="998e7-111">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="998e7-112">Odkaz [metabalíček Microsoft.aspnetcore.all](xref:fundamentals/metapackage) nebo přidat odkaz na balíček [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) balíčku.</span><span class="sxs-lookup"><span data-stu-id="998e7-112">Reference the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="998e7-113">Přidejte odkaz na balíček [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) balíčku.</span><span class="sxs-lookup"><span data-stu-id="998e7-113">Add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

::: moniker-end

## <a name="basic-options-configuration"></a><span data-ttu-id="998e7-114">Základní možnosti konfigurace</span><span class="sxs-lookup"><span data-stu-id="998e7-114">Basic options configuration</span></span>

<span data-ttu-id="998e7-115">Základní možnosti konfigurace je znázorněn příklad &num;1 [ukázkovou aplikaci](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/configuration/options/sample).</span><span class="sxs-lookup"><span data-stu-id="998e7-115">Basic options configuration is demonstrated as Example &num;1 in the [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/configuration/options/sample).</span></span>

<span data-ttu-id="998e7-116">Třída možností musí být neabstraktní s veřejným konstruktorem bez parametrů.</span><span class="sxs-lookup"><span data-stu-id="998e7-116">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="998e7-117">Následující třídy `MyOptions`, má dvě vlastnosti `Option1` a `Option2`.</span><span class="sxs-lookup"><span data-stu-id="998e7-117">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="998e7-118">Nastavení výchozích hodnot je nepovinný, ale konstruktoru třídy v následujícím příkladu nastaví na výchozí hodnotu `Option1`.</span><span class="sxs-lookup"><span data-stu-id="998e7-118">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="998e7-119">`Option2` má výchozí hodnotu nastavenou vlastnost inicializace přímo (*Models/MyOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="998e7-119">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/sample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="998e7-120">`MyOptions` Třídy se přidá do kontejneru služby s [konfigurovat&lt;TOptions&gt;(IServiceCollection, parametry IConfiguration)](/dotnet/api/microsoft.extensions.dependencyinjection.optionsconfigurationservicecollectionextensions.configure#Microsoft_Extensions_DependencyInjection_OptionsConfigurationServiceCollectionExtensions_Configure__1_Microsoft_Extensions_DependencyInjection_IServiceCollection_Microsoft_Extensions_Configuration_IConfiguration_) a vázán ke konfiguraci:</span><span class="sxs-lookup"><span data-stu-id="998e7-120">The `MyOptions` class is added to the service container with [Configure&lt;TOptions&gt;(IServiceCollection, IConfiguration)](/dotnet/api/microsoft.extensions.dependencyinjection.optionsconfigurationservicecollectionextensions.configure#Microsoft_Extensions_DependencyInjection_OptionsConfigurationServiceCollectionExtensions_Configure__1_Microsoft_Extensions_DependencyInjection_IServiceCollection_Microsoft_Extensions_Configuration_IConfiguration_) and bound to configuration:</span></span>

[!code-csharp[](options/sample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="998e7-121">Na následující stránce používá model [injektáž závislostí konstruktor](xref:mvc/controllers/dependency-injection) s [IOptions&lt;TOptions&gt; ](/dotnet/api/Microsoft.Extensions.Options.IOptions-1) pro přístup k nastavení (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="998e7-121">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with [IOptions&lt;TOptions&gt;](/dotnet/api/Microsoft.Extensions.Options.IOptions-1) to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/sample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/sample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/sample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="998e7-122">Ukázkové *appsettings.json* souboru určuje hodnoty pro `option1` a `option2`:</span><span class="sxs-lookup"><span data-stu-id="998e7-122">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/sample/appsettings.json?highlight=2-3)]

<span data-ttu-id="998e7-123">Při spuštění aplikace, model stránky `OnGet` metoda vrátí řetězec zobrazující možnost hodnoty třídy:</span><span class="sxs-lookup"><span data-stu-id="998e7-123">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="998e7-124">Při použití vlastního [ConfigurationBuilder](/dotnet/api/system.configuration.configurationbuilder) Pokud chcete načíst ze souboru nastavení konfigurace možností, potvrďte, že je správně nastavena základní cesta:</span><span class="sxs-lookup"><span data-stu-id="998e7-124">When using a custom [ConfigurationBuilder](/dotnet/api/system.configuration.configurationbuilder) to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
>
> ```csharp
> var configBuilder = new ConfigurationBuilder()
>    .SetBasePath(Directory.GetCurrentDirectory())
>    .AddJsonFile("appsettings.json", optional: true);
> var config = configBuilder.Build();
>
> services.Configure<MyOptions>(config);
> ```
>
> <span data-ttu-id="998e7-125">Explicitním nastavením základní cesta není nutné při načítání konfigurace možností z nastavení souboru prostřednictvím [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder).</span><span class="sxs-lookup"><span data-stu-id="998e7-125">Explicitly setting the base path isn't required when loading options configuration from the settings file via [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder).</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="998e7-126">Konfigurace jednoduchých možností s delegáta</span><span class="sxs-lookup"><span data-stu-id="998e7-126">Configure simple options with a delegate</span></span>

<span data-ttu-id="998e7-127">Konfigurace jednoduchých možností s delegáta je znázorněn příklad &num;v 2 [ukázkovou aplikaci](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/configuration/options/sample).</span><span class="sxs-lookup"><span data-stu-id="998e7-127">Configuring simple options with a delegate is demonstrated as Example &num;2 in the [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/configuration/options/sample).</span></span>

<span data-ttu-id="998e7-128">Použití delegáta k nastavení hodnot možností.</span><span class="sxs-lookup"><span data-stu-id="998e7-128">Use a delegate to set options values.</span></span> <span data-ttu-id="998e7-129">Tato ukázková aplikace používá `MyOptionsWithDelegateConfig` třídy (*Models/MyOptionsWithDelegateConfig.cs*):</span><span class="sxs-lookup"><span data-stu-id="998e7-129">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/sample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="998e7-130">V následujícím kódu druhý `IConfigureOptions<TOptions>` služby se přidá do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="998e7-130">In the following code, a second `IConfigureOptions<TOptions>` service is added to the service container.</span></span> <span data-ttu-id="998e7-131">Delegát používá ke konfiguraci vazby s `MyOptionsWithDelegateConfig`:</span><span class="sxs-lookup"><span data-stu-id="998e7-131">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/sample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="998e7-132">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="998e7-132">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/sample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/sample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/sample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="998e7-133">Můžete přidat několik poskytovatelů konfigurace.</span><span class="sxs-lookup"><span data-stu-id="998e7-133">You can add multiple configuration providers.</span></span> <span data-ttu-id="998e7-134">Poskytovatelé konfigurace jsou k dispozici v balíčcích NuGet.</span><span class="sxs-lookup"><span data-stu-id="998e7-134">Configuration providers are available in NuGet packages.</span></span> <span data-ttu-id="998e7-135">Se použijí v pořadí, které jsou registrovány.</span><span class="sxs-lookup"><span data-stu-id="998e7-135">They're applied in the order that they're registered.</span></span>

<span data-ttu-id="998e7-136">Každé volání [konfigurovat&lt;TOptions&gt; ](/dotnet/api/microsoft.extensions.options.iconfigureoptions-1.configure) přidá `IConfigureOptions<TOptions>` služby service container.</span><span class="sxs-lookup"><span data-stu-id="998e7-136">Each call to [Configure&lt;TOptions&gt;](/dotnet/api/microsoft.extensions.options.iconfigureoptions-1.configure) adds an `IConfigureOptions<TOptions>` service to the service container.</span></span> <span data-ttu-id="998e7-137">V předchozím příkladu hodnoty `Option1` a `Option2` jsou určené v *appsettings.json*, ale hodnoty `Option1` a `Option2` jsou přepsány nakonfigurované delegáta.</span><span class="sxs-lookup"><span data-stu-id="998e7-137">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="998e7-138">Pokud je povoleno více než jedna služba konfigurace, poslední zdroj konfigurace zadaná *wins* a nastaví hodnotu konfigurace.</span><span class="sxs-lookup"><span data-stu-id="998e7-138">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="998e7-139">Při spuštění aplikace, model stránky `OnGet` metoda vrátí řetězec zobrazující možnost hodnoty třídy:</span><span class="sxs-lookup"><span data-stu-id="998e7-139">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delgate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="998e7-140">Konfigurace suboptions</span><span class="sxs-lookup"><span data-stu-id="998e7-140">Suboptions configuration</span></span>

<span data-ttu-id="998e7-141">Konfigurace suboptions je znázorněn příklad &num;3 [ukázkovou aplikaci](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/configuration/options/sample).</span><span class="sxs-lookup"><span data-stu-id="998e7-141">Suboptions configuration is demonstrated as Example &num;3 in the [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/configuration/options/sample).</span></span>

<span data-ttu-id="998e7-142">Aplikace by měl vytvořit třídy možnosti, které se vztahují na konkrétní scénář skupiny (třídy) v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="998e7-142">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="998e7-143">Části aplikace, které vyžadují konfigurační hodnoty by měl mít přístup pouze na hodnoty konfigurace, které používají.</span><span class="sxs-lookup"><span data-stu-id="998e7-143">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="998e7-144">Při vytváření vazby možnosti konfigurace, každou vlastnost v typu možnosti je vázán na konfigurační klíč ve formátu `property[:sub-property:]`.</span><span class="sxs-lookup"><span data-stu-id="998e7-144">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="998e7-145">Například `MyOptions.Option1` vlastnost je vázána na klíč `Option1`, který je pro čtení z `option1` vlastnost v *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="998e7-145">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="998e7-146">V následujícím kódu, třetí `IConfigureOptions<TOptions>` služby se přidá do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="998e7-146">In the following code, a third `IConfigureOptions<TOptions>` service is added to the service container.</span></span> <span data-ttu-id="998e7-147">Vytvoří vazbu mezi `MySubOptions` do části `subsection` z *appsettings.json* souboru:</span><span class="sxs-lookup"><span data-stu-id="998e7-147">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/sample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="998e7-148">`GetSection` – Metoda rozšíření vyžaduje [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="998e7-148">The `GetSection` extension method requires the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) NuGet package.</span></span> <span data-ttu-id="998e7-149">Pokud aplikace využívá [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 nebo novější), balíček je automaticky přidána.</span><span class="sxs-lookup"><span data-stu-id="998e7-149">If the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later), the package is automatically included.</span></span>

<span data-ttu-id="998e7-150">Ukázkové *appsettings.json* soubor definuje `subsection` člena s klíči pro `suboption1` a `suboption2`:</span><span class="sxs-lookup"><span data-stu-id="998e7-150">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/sample/appsettings.json?highlight=4-7)]

<span data-ttu-id="998e7-151">`MySubOptions` Třídy definuje vlastnosti, `SubOption1` a `SubOption2`, pro uchování hodnoty možnosti (*Models/MySubOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="998e7-151">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/sample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="998e7-152">Model stránky `OnGet` metoda vrátí řetězec s hodnotami možnosti (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="998e7-152">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/sample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/sample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/sample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="998e7-153">Při spuštění aplikace `OnGet` metoda vrátí řetězec zobrazující možnost dílčí třída hodnoty:</span><span class="sxs-lookup"><span data-stu-id="998e7-153">When the app is run, the `OnGet` method returns a string showing the sub-option class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="998e7-154">Možnosti zobrazení modelu nebo pomocí vkládání přímé zobrazení</span><span class="sxs-lookup"><span data-stu-id="998e7-154">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="998e7-155">Možnosti zobrazení modelu nebo s přístupem zobrazení vkládání je znázorněn příklad &num;4 [ukázkovou aplikaci](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/configuration/options/sample).</span><span class="sxs-lookup"><span data-stu-id="998e7-155">Options provided by a view model or with direct view injection is demonstrated as Example &num;4 in the [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/configuration/options/sample).</span></span>

<span data-ttu-id="998e7-156">Možnosti může být zadán v zobrazení modelu nebo vložením `IOptions<TOptions>` přímo do zobrazení (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="998e7-156">Options can be supplied in a view model or by injecting `IOptions<TOptions>` directly into a view (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/sample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/sample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/sample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="998e7-157">Vkládání s přímým přístupem, Vložit `IOptions<MyOptions>` s `@inject` – direktiva:</span><span class="sxs-lookup"><span data-stu-id="998e7-157">For direct injection, inject `IOptions<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/sample/Pages/Index.cshtml?range=1-10&highlight=5)]

<span data-ttu-id="998e7-158">Při spuštění aplikace se hodnoty možnosti jsou zobrazeny na vykreslené stránce:</span><span class="sxs-lookup"><span data-stu-id="998e7-158">When the app is run, the options values are shown in the rendered page:</span></span>

![Možnosti hodnot možnost1: value1_from_json a možnost2: -1 se načítají z modelu a vkládání do zobrazení.](options/_static/view.png)

::: moniker range=">= aspnetcore-1.1"

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="998e7-160">Znovu načíst konfigurační data s IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="998e7-160">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="998e7-161">Probíhá opětovné načtení dat konfigurace pomocí `IOptionsSnapshot` je ukázáno v příkladu &num;5 [ukázkovou aplikaci](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/configuration/options/sample).</span><span class="sxs-lookup"><span data-stu-id="998e7-161">Reloading configuration data with `IOptionsSnapshot` is demonstrated in Example &num;5 in the [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/configuration/options/sample).</span></span>

<span data-ttu-id="998e7-162">[IOptionsSnapshot](/dotnet/api/microsoft.extensions.options.ioptionssnapshot-1) podporuje možnosti s minimální režie zpracování znovu načíst.</span><span class="sxs-lookup"><span data-stu-id="998e7-162">[IOptionsSnapshot](/dotnet/api/microsoft.extensions.options.ioptionssnapshot-1) supports reloading options with minimal processing overhead.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="998e7-163">Možnosti jsou vypočten jednou každý požadavek při přistupovat a uložili do mezipaměti po dobu platnosti požadavku.</span><span class="sxs-lookup"><span data-stu-id="998e7-163">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="998e7-164">`IOptionsSnapshot` je snímek [IOptionsMonitor&lt;TOptions&gt; ](/dotnet/api/microsoft.extensions.options.ioptionsmonitor-1) a aktualizace automaticky pokaždé, když monitorování aktivuje upravovat podle aktuálních zdroj dat změny.</span><span class="sxs-lookup"><span data-stu-id="998e7-164">`IOptionsSnapshot` is a snapshot of [IOptionsMonitor&lt;TOptions&gt;](/dotnet/api/microsoft.extensions.options.ioptionsmonitor-1) and updates automatically whenever the monitor triggers changes based on the data source changing.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-1.1"

<span data-ttu-id="998e7-165">Následující příklad ukazuje, jak nové `IOptionsSnapshot` vytvořené po *appsettings.json* změny (*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="998e7-165">The following example demonstrates how a new `IOptionsSnapshot` is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="998e7-166">Více požadavků na server vrátit konstantní hodnoty podle *appsettings.json* souborů, dokud je soubor změněn a konfigurace znovu načte.</span><span class="sxs-lookup"><span data-stu-id="998e7-166">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/sample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/sample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/sample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="998e7-167">Následující obrázek ukazuje úvodní `option1` a `option2` hodnoty načtené z *appsettings.json* souboru:</span><span class="sxs-lookup"><span data-stu-id="998e7-167">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="998e7-168">Změňte hodnoty *appsettings.json* do souboru `value1_from_json UPDATED` a `200`.</span><span class="sxs-lookup"><span data-stu-id="998e7-168">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="998e7-169">Uložit *appsettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="998e7-169">Save the *appsettings.json* file.</span></span> <span data-ttu-id="998e7-170">Aktualizujte prohlížeč, pokud chcete zobrazit, že jsou aktualizované hodnoty možnosti:</span><span class="sxs-lookup"><span data-stu-id="998e7-170">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="998e7-171">Možnosti podpory s IConfigureNamedOptions s názvem</span><span class="sxs-lookup"><span data-stu-id="998e7-171">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="998e7-172">Možnosti podpory s názvem [IConfigureNamedOptions](/dotnet/api/microsoft.extensions.options.iconfigurenamedoptions-1) je znázorněn příklad &num;6 v [ukázkovou aplikaci](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/configuration/options/sample).</span><span class="sxs-lookup"><span data-stu-id="998e7-172">Named options support with [IConfigureNamedOptions](/dotnet/api/microsoft.extensions.options.iconfigurenamedoptions-1) is demonstrated as Example &num;6 in the [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/configuration/options/sample).</span></span>

<span data-ttu-id="998e7-173">*S názvem možnosti* podpora umožňuje, aby aplikace k rozlišení mezi pojmenované možnosti konfigurace.</span><span class="sxs-lookup"><span data-stu-id="998e7-173">*Named options* support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="998e7-174">V ukázkové aplikaci s názvem možnosti jsou deklarovány pomocí [OptionsServiceCollectionExtensions.Configure&lt;TOptions&gt;(IServiceCollection, řetězec, akce&lt;TOptions&gt;)](/dotnet/api/microsoft.extensions.dependencyinjection.optionsservicecollectionextensions.configure)která pak volá metodu rozšíření [ConfigureNamedOptions&lt;TOptions&gt;. Konfigurace](/dotnet/api/microsoft.extensions.options.configurenamedoptions-1.configure) metody:</span><span class="sxs-lookup"><span data-stu-id="998e7-174">In the sample app, named options are declared with the [OptionsServiceCollectionExtensions.Configure&lt;TOptions&gt;(IServiceCollection, String, Action&lt;TOptions&gt;)](/dotnet/api/microsoft.extensions.dependencyinjection.optionsservicecollectionextensions.configure) which in turn calls the extension method [ConfigureNamedOptions&lt;TOptions&gt;.Configure](/dotnet/api/microsoft.extensions.options.configurenamedoptions-1.configure) method:</span></span>

[!code-csharp[](options/sample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="998e7-175">Ukázková aplikace přistupuje k pojmenované možnosti s [IOptionsSnapshot&lt;TOptions&gt;. Získat](/dotnet/api/microsoft.extensions.options.ioptionssnapshot-1.get) (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="998e7-175">The sample app accesses the named options with [IOptionsSnapshot&lt;TOptions&gt;.Get](/dotnet/api/microsoft.extensions.options.ioptionssnapshot-1.get) (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/sample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/sample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/sample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="998e7-176">Spuštěním ukázkové aplikace, jsou vráceny pojmenované možnosti:</span><span class="sxs-lookup"><span data-stu-id="998e7-176">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="998e7-177">`named_options_1` hodnoty jsou k dispozici z konfigurace, které jsou načteny z *appsettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="998e7-177">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="998e7-178">`named_options_2` hodnoty jsou k dispozici podle:</span><span class="sxs-lookup"><span data-stu-id="998e7-178">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="998e7-179">`named_options_2` Delegování v `ConfigureServices` pro `Option1`.</span><span class="sxs-lookup"><span data-stu-id="998e7-179">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="998e7-180">Výchozí hodnota pro `Option2` poskytované `MyOptions` třídy.</span><span class="sxs-lookup"><span data-stu-id="998e7-180">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="998e7-181">Všechny možnosti nakonfigurovat ConfigureAll – metoda</span><span class="sxs-lookup"><span data-stu-id="998e7-181">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="998e7-182">Nakonfigurujte všechny možnosti instance s [OptionsServiceCollectionExtensions.ConfigureAll](/dotnet/api/microsoft.extensions.dependencyinjection.optionsservicecollectionextensions.configureall) metody.</span><span class="sxs-lookup"><span data-stu-id="998e7-182">Configure all options instances with the [OptionsServiceCollectionExtensions.ConfigureAll](/dotnet/api/microsoft.extensions.dependencyinjection.optionsservicecollectionextensions.configureall) method.</span></span> <span data-ttu-id="998e7-183">Následující kód konfiguruje `Option1` pro všechny konfigurace instance s hodnotou běžné.</span><span class="sxs-lookup"><span data-stu-id="998e7-183">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="998e7-184">Přidejte následující kód do ručně `ConfigureServices` metody:</span><span class="sxs-lookup"><span data-stu-id="998e7-184">Add the following code manually to the `ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="998e7-185">Spuštěním ukázkové aplikace po přidání kódu vytvoří následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="998e7-185">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="998e7-186">Všechny možnosti jsou pojmenované instance.</span><span class="sxs-lookup"><span data-stu-id="998e7-186">All options are named instances.</span></span> <span data-ttu-id="998e7-187">Existující `IConfigureOption` instancí jsou považovány za cílení `Options.DefaultName` instanci, která je `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="998e7-187">Existing `IConfigureOption` instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="998e7-188">`IConfigureNamedOptions` také implementuje `IConfigureOptions`.</span><span class="sxs-lookup"><span data-stu-id="998e7-188">`IConfigureNamedOptions` also implements `IConfigureOptions`.</span></span> <span data-ttu-id="998e7-189">Výchozí implementace [IOptionsFactory&lt;TOptions&gt; ](/dotnet/api/microsoft.extensions.options.ioptionsfactory-1) ([zdroj odkazu](https://github.com/aspnet/Options/blob/release/2.0/src/Microsoft.Extensions.Options/IOptionsFactory.cs) obsahuje logiku pro každý odpovídajícím způsobem používat.</span><span class="sxs-lookup"><span data-stu-id="998e7-189">The default implementation of the [IOptionsFactory&lt;TOptions&gt;](/dotnet/api/microsoft.extensions.options.ioptionsfactory-1) ([reference source](https://github.com/aspnet/Options/blob/release/2.0/src/Microsoft.Extensions.Options/IOptionsFactory.cs) has logic to use each appropriately.</span></span> <span data-ttu-id="998e7-190">`null` Pojmenované možnost se používá cílit na všechny pojmenované instance místo konkrétní pojmenovanou instanci ([ConfigureAll](/dotnet/api/microsoft.extensions.dependencyinjection.optionsservicecollectionextensions.configureall) a [PostConfigureAll](/dotnet/api/microsoft.extensions.dependencyinjection.optionsservicecollectionextensions.postconfigureall) Tato konvence).</span><span class="sxs-lookup"><span data-stu-id="998e7-190">The `null` named option is used to target all of the named instances instead of a specific named instance ([ConfigureAll](/dotnet/api/microsoft.extensions.dependencyinjection.optionsservicecollectionextensions.configureall) and [PostConfigureAll](/dotnet/api/microsoft.extensions.dependencyinjection.optionsservicecollectionextensions.postconfigureall) use this convention).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

## <a name="optionsbuilder-api"></a><span data-ttu-id="998e7-191">OptionsBuilder rozhraní API</span><span class="sxs-lookup"><span data-stu-id="998e7-191">OptionsBuilder API</span></span>

<span data-ttu-id="998e7-192"><xref:Microsoft.Extensions.Options.OptionsBuilder`1> slouží ke konfiguraci `TOptions` instancí.</span><span class="sxs-lookup"><span data-stu-id="998e7-192"><xref:Microsoft.Extensions.Options.OptionsBuilder`1> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="998e7-193">`OptionsBuilder` zjednodušuje vytváření, s názvem možnosti, jako je pouze jeden parametr do původní `AddOptions<TOptions>(string optionsName)` volat bez povolí, všechny následné volání.</span><span class="sxs-lookup"><span data-stu-id="998e7-193">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="998e7-194">Možnosti ověřování a `ConfigureOptions` přetížení, které přijímají závislostí služby jsou dostupné jen přes `OptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="998e7-194">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");
    
services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="configurelttoptions-tdep1--tdep4gt-method"></a><span data-ttu-id="998e7-195">Konfigurace&lt;TOptions, TDep1,... TDep4&gt; – metoda</span><span class="sxs-lookup"><span data-stu-id="998e7-195">Configure&lt;TOptions, TDep1, ... TDep4&gt; method</span></span>

<span data-ttu-id="998e7-196">Konfigurovat možnosti implementací pomocí služby od DI `IConfigure[Named]Options` standardní způsobem je úplné.</span><span class="sxs-lookup"><span data-stu-id="998e7-196">Using services from DI to configure options by implementing `IConfigure[Named]Options` in a boilerplate manner is verbose.</span></span> <span data-ttu-id="998e7-197">Přetížení pro `ConfigureOptions` na `OptionsBuilder<TOptions>` umožňují používat až pět služeb můžete nakonfigurovat možnosti:</span><span class="sxs-lookup"><span data-stu-id="998e7-197">Overloads for `ConfigureOptions` on `OptionsBuilder<TOptions>` allow you to use up to five services to configure options:</span></span>

```csharp
services.AddOptions<MyOptions>("optionalName")
    .Configure<Service1, Service2, Service3, Service4, Service5>(
        (o, s, s2, s3, s4, s5) => 
            o.Property = DoSomethingWith(s, s2, s3, s4, s5));
```

<span data-ttu-id="998e7-198">Přetížení zaregistruje přechodné obecný <xref:Microsoft.Extensions.Options.IConfigureNamedOptions`1>, které má konstruktor, který přijímá typy obecné služby určené.</span><span class="sxs-lookup"><span data-stu-id="998e7-198">The overload registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions`1>, which has a constructor that accepts the generic service types specified.</span></span> 

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="options-validation"></a><span data-ttu-id="998e7-199">Možnosti ověřování</span><span class="sxs-lookup"><span data-stu-id="998e7-199">Options validation</span></span>

<span data-ttu-id="998e7-200">Možnosti ověřování umožňuje ověřit možnosti při nakonfigurování možností.</span><span class="sxs-lookup"><span data-stu-id="998e7-200">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="998e7-201">Volání `Validate` s metodu ověřování, která vrátí `true` Pokud možnosti jsou platné a `false` Pokud nejsou platné:</span><span class="sxs-lookup"><span data-stu-id="998e7-201">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

```csharp
// Registration
services.AddOptions<MyOptions>("optionalOptionsName")
    .Configure(o => { }) // Configure the options
    .Validate(o => YourValidationShouldReturnTrueIfValid(o), 
        "custom error");
        
// Consumption
var monitor = services.BuildServiceProvider()
    .GetService<IOptionsMonitor<MyOptions>>();
  
try
{
    var options = monitor.Get("optionalOptionsName");
} 
catch (OptionsValidationException e) 
{
   // e.OptionsName returns "optionalOptionsName"
   // e.OptionsType returns typeof(MyOptions)
   // e.Failures returns a list of errors, which would contain 
   //     "custom error"
}
```

<span data-ttu-id="998e7-202">V předchozím příkladu nastaví možnosti pojmenovanou instanci na `optionalOptionsName`.</span><span class="sxs-lookup"><span data-stu-id="998e7-202">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="998e7-203">Výchozí možnosti instance je `Options.DefaultName`.</span><span class="sxs-lookup"><span data-stu-id="998e7-203">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="998e7-204">Ověření se spustí, jakmile se vytvoří instance možností.</span><span class="sxs-lookup"><span data-stu-id="998e7-204">Validation runs when the options instance is created.</span></span> <span data-ttu-id="998e7-205">Je zaručeno, že vaše instance možnosti předat čas ověření první, který je přístupný.</span><span class="sxs-lookup"><span data-stu-id="998e7-205">Your options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="998e7-206">Možnosti ověřování nepodporuje ochranu proti možnosti úprav po možnosti počáteční konfiguraci a ověřit.</span><span class="sxs-lookup"><span data-stu-id="998e7-206">Options validation doesn't guard against options modifications after the options are initially configured and validated.</span></span>

<span data-ttu-id="998e7-207">`Validate` Metoda přijímá `Func<TOptions, bool>`.</span><span class="sxs-lookup"><span data-stu-id="998e7-207">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="998e7-208">Chcete-li plně přizpůsobit ověřování, implementovat `IValidateOptions<TOptions>`, která umožňuje:</span><span class="sxs-lookup"><span data-stu-id="998e7-208">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="998e7-209">Ověření více typů možnosti: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="998e7-209">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="998e7-210">Ověření, který závisí na jiný typ možnosti: `public DependsOnAnotherOptionValidator(IOptions<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="998e7-210">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptions<AnotherOption> options)`</span></span>

<span data-ttu-id="998e7-211">`IValidateOptions` ověří:</span><span class="sxs-lookup"><span data-stu-id="998e7-211">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="998e7-212">Konkrétní pojmenované instance možností.</span><span class="sxs-lookup"><span data-stu-id="998e7-212">A specific named options instance.</span></span>
* <span data-ttu-id="998e7-213">Všechny možnosti `name` je `null`.</span><span class="sxs-lookup"><span data-stu-id="998e7-213">All options when `name` is `null`.</span></span>

<span data-ttu-id="998e7-214">Vrátit `ValidateOptionsResult` od implementace rozhraní:</span><span class="sxs-lookup"><span data-stu-id="998e7-214">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="998e7-215">Data ověřování na základě poznámek je k dispozici [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) balíčku voláním `ValidateDataAnnotations` metodu na `OptionsBuilder<TOptions>`:</span><span class="sxs-lookup"><span data-stu-id="998e7-215">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the `ValidateDataAnnotations` method on `OptionsBuilder<TOptions>`:</span></span>

```csharp
private class AnnotatedOptions
{
    [Required]
    public string Required { get; set; }

    [StringLength(5, ErrorMessage = "Too long.")]
    public string StringLength { get; set; }

    [Range(-5, 5, ErrorMessage = "Out of range.")]
    public int IntRange { get; set; }
}
    
[Fact]
public void CanValidateDataAnnotations()
{
    var services = new ServiceCollection();
    services.AddOptions<AnnotatedOptions>()
        .Configure(o =>
        {
            o.StringLength = "111111";
            o.IntRange = 10;
            o.Custom = "nowhere";
        })
        .ValidateDataAnnotations();

    var sp = services.BuildServiceProvider();

    var error = Assert.Throws<OptionsValidationException>(() => 
        sp.GetRequiredService<IOptions<AnnotatedOptions>>().Value);
    ValidateFailure<AnnotatedOptions>(error, Options.DefaultName, 1,
        "DataAnnotation validation failed for members Required " +
            "with the error 'The Required field is required.'.",
        "DataAnnotation validation failed for members StringLength " +
            "with the error 'Too long.'.",
        "DataAnnotation validation failed for members IntRange " +
            "with the error 'Out of range.'.");
}    
```

<span data-ttu-id="998e7-216">V úvahu pro budoucí verzi se nemůžou dočkat, až ověřování (selhání rychle při spuštění).</span><span class="sxs-lookup"><span data-stu-id="998e7-216">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

## <a name="ipostconfigureoptions"></a><span data-ttu-id="998e7-217">IPostConfigureOptions</span><span class="sxs-lookup"><span data-stu-id="998e7-217">IPostConfigureOptions</span></span>

<span data-ttu-id="998e7-218">Nastavte postconfiguration s [IPostConfigureOptions&lt;TOptions&gt;](/dotnet/api/microsoft.extensions.options.ipostconfigureoptions-1).</span><span class="sxs-lookup"><span data-stu-id="998e7-218">Set postconfiguration with [IPostConfigureOptions&lt;TOptions&gt;](/dotnet/api/microsoft.extensions.options.ipostconfigureoptions-1).</span></span> <span data-ttu-id="998e7-219">Postconfiguration běží po všech [IConfigureOptions&lt;TOptions&gt; ](/dotnet/api/microsoft.extensions.options.iconfigureoptions-1) vyvolá konfigurace:</span><span class="sxs-lookup"><span data-stu-id="998e7-219">Postconfiguration runs after all [IConfigureOptions&lt;TOptions&gt;](/dotnet/api/microsoft.extensions.options.iconfigureoptions-1) configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="998e7-220">[PostConfigure&lt;TOptions&gt; ](/dotnet/api/microsoft.extensions.options.ipostconfigureoptions-1.postconfigure) je dostupné pro následující po konfiguraci s názvem možnosti:</span><span class="sxs-lookup"><span data-stu-id="998e7-220">[PostConfigure&lt;TOptions&gt;](/dotnet/api/microsoft.extensions.options.ipostconfigureoptions-1.postconfigure) is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="998e7-221">Použití [PostConfigureAll&lt;TOptions&gt; ](/dotnet/api/microsoft.extensions.dependencyinjection.optionsservicecollectionextensions.postconfigureall) po konfiguraci všechny instance konfigurace:</span><span class="sxs-lookup"><span data-stu-id="998e7-221">Use [PostConfigureAll&lt;TOptions&gt;](/dotnet/api/microsoft.extensions.dependencyinjection.optionsservicecollectionextensions.postconfigureall) to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

::: moniker-end

## <a name="options-factory-monitoring-and-cache"></a><span data-ttu-id="998e7-222">Možnosti objekt pro vytváření, monitorování a mezipaměť</span><span class="sxs-lookup"><span data-stu-id="998e7-222">Options factory, monitoring, and cache</span></span>

<span data-ttu-id="998e7-223">[IOptionsMonitor](/dotnet/api/microsoft.extensions.options.ioptionsmonitor-1) se používá pro oznámení při `TOptions` instance změnit.</span><span class="sxs-lookup"><span data-stu-id="998e7-223">[IOptionsMonitor](/dotnet/api/microsoft.extensions.options.ioptionsmonitor-1) is used for notifications when `TOptions` instances change.</span></span> <span data-ttu-id="998e7-224">`IOptionsMonitor` podporuje možnosti opětovně načítá, oznámení, změn a `IPostConfigureOptions`.</span><span class="sxs-lookup"><span data-stu-id="998e7-224">`IOptionsMonitor` supports reloadable options, change notifications, and `IPostConfigureOptions`.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="998e7-225">[IOptionsFactory&lt;TOptions&gt; ](/dotnet/api/microsoft.extensions.options.ioptionsfactory-1) zodpovídá za vytvoření nové možnosti instancí.</span><span class="sxs-lookup"><span data-stu-id="998e7-225">[IOptionsFactory&lt;TOptions&gt;](/dotnet/api/microsoft.extensions.options.ioptionsfactory-1) is responsible for creating new options instances.</span></span> <span data-ttu-id="998e7-226">Má jediný [vytvořit](/dotnet/api/microsoft.extensions.options.ioptionsfactory-1.create) metody.</span><span class="sxs-lookup"><span data-stu-id="998e7-226">It has a single [Create](/dotnet/api/microsoft.extensions.options.ioptionsfactory-1.create) method.</span></span> <span data-ttu-id="998e7-227">Výchozí implementace používá všech registrovaných `IConfigureOptions` a `IPostConfigureOptions` a spustí všechny nakonfiguruje nejprve, za nímž následuje po konfiguruje.</span><span class="sxs-lookup"><span data-stu-id="998e7-227">The default implementation takes all registered `IConfigureOptions` and `IPostConfigureOptions` and runs all the configures first, followed by the post-configures.</span></span> <span data-ttu-id="998e7-228">Rozlišuje mezi `IConfigureNamedOptions` a `IConfigureOptions` a jen volá odpovídající rozhraní.</span><span class="sxs-lookup"><span data-stu-id="998e7-228">It distinguishes between `IConfigureNamedOptions` and `IConfigureOptions` and only calls the appropriate interface.</span></span>

<span data-ttu-id="998e7-229">[IOptionsMonitorCache&lt;TOptions&gt; ](/dotnet/api/microsoft.extensions.options.ioptionsmonitorcache-1) používá `IOptionsMonitor` do mezipaměti `TOptions` instancí.</span><span class="sxs-lookup"><span data-stu-id="998e7-229">[IOptionsMonitorCache&lt;TOptions&gt;](/dotnet/api/microsoft.extensions.options.ioptionsmonitorcache-1) is used by `IOptionsMonitor` to cache `TOptions` instances.</span></span> <span data-ttu-id="998e7-230">`IOptionsMonitorCache` Zruší platnost instancí možnosti v monitorování, tak, aby přepočítány hodnoty ([TryRemove](/dotnet/api/microsoft.extensions.options.ioptionsmonitorcache-1.tryremove)).</span><span class="sxs-lookup"><span data-stu-id="998e7-230">The `IOptionsMonitorCache` invalidates options instances in the monitor so that the value is recomputed ([TryRemove](/dotnet/api/microsoft.extensions.options.ioptionsmonitorcache-1.tryremove)).</span></span> <span data-ttu-id="998e7-231">Hodnoty mohou být ručně nepředchází funkce také [TryAdd](/dotnet/api/microsoft.extensions.options.ioptionsmonitorcache-1.tryadd).</span><span class="sxs-lookup"><span data-stu-id="998e7-231">Values can be manually introduced as well with [TryAdd](/dotnet/api/microsoft.extensions.options.ioptionsmonitorcache-1.tryadd).</span></span> <span data-ttu-id="998e7-232">[Vymazat](/dotnet/api/microsoft.extensions.options.ioptionsmonitorcache-1.clear) metoda se používá, když všechny pojmenované instance by měl být znovu vytvořit na vyžádání.</span><span class="sxs-lookup"><span data-stu-id="998e7-232">The [Clear](/dotnet/api/microsoft.extensions.options.ioptionsmonitorcache-1.clear) method is used when all named instances should be recreated on demand.</span></span>

::: moniker-end

## <a name="accessing-options-during-startup"></a><span data-ttu-id="998e7-233">Přístup k možnosti při spuštění</span><span class="sxs-lookup"><span data-stu-id="998e7-233">Accessing options during startup</span></span>

<span data-ttu-id="998e7-234">`IOptions` je možné v `Startup.Configure`, protože služby jsou sestaveny dříve, než `Configure` metody.</span><span class="sxs-lookup"><span data-stu-id="998e7-234">`IOptions` can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.Value.Option1;
}
```

<span data-ttu-id="998e7-235">`IOptions` neměli byste používat v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="998e7-235">`IOptions` shouldn't be used in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="998e7-236">Příčinou je pořadí registrace služby můžou existovat nekonzistentní možnosti dostane do stavu.</span><span class="sxs-lookup"><span data-stu-id="998e7-236">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="998e7-237">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="998e7-237">Additional resources</span></span>

* <xref:fundamentals/configuration/index>

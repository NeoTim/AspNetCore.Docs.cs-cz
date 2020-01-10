---
title: Vzor možností v ASP.NET Core
author: guardrex
description: Zjistěte, jak pomocí vzoru možností znázornit skupiny souvisejících nastavení v aplikacích ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/07/2019
uid: fundamentals/configuration/options
ms.openlocfilehash: 98fe30fbc424dd51ce8f8319b7ce959fd755c480
ms.sourcegitcommit: da2fb2d78ce70accdba903ccbfdcfffdd0112123
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2020
ms.locfileid: "75722736"
---
# <a name="options-pattern-in-aspnet-core"></a><span data-ttu-id="9ae32-103">Vzor možností v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9ae32-103">Options pattern in ASP.NET Core</span></span>

<span data-ttu-id="9ae32-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="9ae32-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9ae32-105">Vzor možností nastavení využívá třídy pro reprezentaci skupin souvisejících nastavení.</span><span class="sxs-lookup"><span data-stu-id="9ae32-105">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="9ae32-106">Když jsou [nastavení konfigurace](xref:fundamentals/configuration/index) izolována scénářem do samostatných tříd, aplikace dodržuje dva důležité principy technického řízení:</span><span class="sxs-lookup"><span data-stu-id="9ae32-106">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="9ae32-107">[Princip oddělování rozhraní (ISP) nebo zapouzdření](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; scénáře (třídy), které závisí na nastavení konfigurace, závisí jenom na nastavení konfigurace, která používají.</span><span class="sxs-lookup"><span data-stu-id="9ae32-107">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="9ae32-108">[Oddělení](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) potíží s tím, jak &ndash; nastavení různých částí aplikace nejsou závislá nebo vzájemně závislá na sobě.</span><span class="sxs-lookup"><span data-stu-id="9ae32-108">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="9ae32-109">Možnosti také poskytují mechanismus pro ověření konfiguračních dat.</span><span class="sxs-lookup"><span data-stu-id="9ae32-109">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="9ae32-110">Další informace najdete v části [ověřování možností](#options-validation) .</span><span class="sxs-lookup"><span data-stu-id="9ae32-110">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="9ae32-111">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9ae32-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="package"></a><span data-ttu-id="9ae32-112">Balíček</span><span class="sxs-lookup"><span data-stu-id="9ae32-112">Package</span></span>

<span data-ttu-id="9ae32-113">Na balíček [Microsoft. Extensions. Options. ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) se implicitně odkazuje v aplikacích ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9ae32-113">The [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package is implicitly referenced in ASP.NET Core apps.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="9ae32-114">Možnosti rozhraní</span><span class="sxs-lookup"><span data-stu-id="9ae32-114">Options interfaces</span></span>

<span data-ttu-id="9ae32-115"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> slouží k načtení možností a správě oznámení možností pro `TOptions` instance.</span><span class="sxs-lookup"><span data-stu-id="9ae32-115"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="9ae32-116"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> podporuje následující scénáře:</span><span class="sxs-lookup"><span data-stu-id="9ae32-116"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="9ae32-117">Oznámení změny</span><span class="sxs-lookup"><span data-stu-id="9ae32-117">Change notifications</span></span>
* [<span data-ttu-id="9ae32-118">Pojmenované možnosti</span><span class="sxs-lookup"><span data-stu-id="9ae32-118">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="9ae32-119">Znovu Načítatelné konfigurace</span><span class="sxs-lookup"><span data-stu-id="9ae32-119">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="9ae32-120">Neplatné selektivní možnosti (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="9ae32-120">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="9ae32-121">Scénáře po [konfiguraci](#options-post-configuration) umožňují nastavit nebo změnit možnosti po <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ke konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="9ae32-121">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="9ae32-122"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> zodpovídá za vytváření nových instancí možností.</span><span class="sxs-lookup"><span data-stu-id="9ae32-122"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="9ae32-123">Má jednu <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> metodu.</span><span class="sxs-lookup"><span data-stu-id="9ae32-123">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="9ae32-124">Výchozí implementace přebírá všechny registrované <xref:Microsoft.Extensions.Options.IConfigureOptions%601> a <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> a předá všechny konfigurace a potom po konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="9ae32-124">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="9ae32-125">Rozlišuje mezi <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> a <xref:Microsoft.Extensions.Options.IConfigureOptions%601> a volá pouze příslušné rozhraní.</span><span class="sxs-lookup"><span data-stu-id="9ae32-125">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="9ae32-126"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> používá <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> k ukládání instancí `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="9ae32-126"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="9ae32-127"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> zruší platnost instancí možností v monitorování tak, aby se hodnota přepočítala (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="9ae32-127">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="9ae32-128">Hodnoty lze ručně zavést pomocí <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span><span class="sxs-lookup"><span data-stu-id="9ae32-128">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="9ae32-129">Metoda <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> se používá v případě, že by se všechny pojmenované instance měly znovu vytvořit na vyžádání.</span><span class="sxs-lookup"><span data-stu-id="9ae32-129">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="9ae32-130"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> je užitečná ve scénářích, ve kterých by se měly přepočítat možnosti pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="9ae32-130"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="9ae32-131">Další informace najdete v části [opětovné načtení konfiguračních dat pomocí IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) .</span><span class="sxs-lookup"><span data-stu-id="9ae32-131">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="9ae32-132"><xref:Microsoft.Extensions.Options.IOptions%601> lze použít k podpoře možností.</span><span class="sxs-lookup"><span data-stu-id="9ae32-132"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="9ae32-133"><xref:Microsoft.Extensions.Options.IOptions%601> ale nepodporuje předchozí scénáře <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="9ae32-133">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="9ae32-134">Můžete nadále používat <xref:Microsoft.Extensions.Options.IOptions%601> ve stávajících rozhraních a knihovnách, které již používají rozhraní <xref:Microsoft.Extensions.Options.IOptions%601> a nevyžadují scénáře poskytované <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="9ae32-134">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="9ae32-135">Konfigurace obecných možností</span><span class="sxs-lookup"><span data-stu-id="9ae32-135">General options configuration</span></span>

<span data-ttu-id="9ae32-136">Konfigurace obecných možností se v ukázkové aplikaci ukazuje jako příklad 1.</span><span class="sxs-lookup"><span data-stu-id="9ae32-136">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="9ae32-137">Třída Options musí být neabstraktní s veřejným konstruktorem bez parametrů.</span><span class="sxs-lookup"><span data-stu-id="9ae32-137">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="9ae32-138">Následující třída `MyOptions`má dvě vlastnosti, `Option1` a `Option2`.</span><span class="sxs-lookup"><span data-stu-id="9ae32-138">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="9ae32-139">Nastavení výchozích hodnot je volitelné, ale konstruktor třídy v následujícím příkladu nastaví výchozí hodnotu `Option1`.</span><span class="sxs-lookup"><span data-stu-id="9ae32-139">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="9ae32-140">`Option2` má výchozí hodnotu nastavenou pomocí přímé inicializace vlastnosti (*modely/MyOptions. cs*):</span><span class="sxs-lookup"><span data-stu-id="9ae32-140">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="9ae32-141">Třída `MyOptions` je přidána do kontejneru služby s <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> a je svázána s konfigurací:</span><span class="sxs-lookup"><span data-stu-id="9ae32-141">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="9ae32-142">Následující model stránky používá [vkládání závislostí konstruktoru](xref:mvc/controllers/dependency-injection) s <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> pro přístup k nastavení (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="9ae32-142">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="9ae32-143">Soubor *appSettings. JSON* v ukázce určuje hodnoty pro `option1` a `option2`:</span><span class="sxs-lookup"><span data-stu-id="9ae32-143">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/3.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="9ae32-144">Při spuštění aplikace vrátí `OnGet` metoda modelu stránky řetězec zobrazující hodnoty třídy možností:</span><span class="sxs-lookup"><span data-stu-id="9ae32-144">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="9ae32-145">Při načítání konfigurace možností pomocí vlastního <xref:System.Configuration.ConfigurationBuilder> v souboru nastavení potvrďte, že je správně nastavená základní cesta:</span><span class="sxs-lookup"><span data-stu-id="9ae32-145">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="9ae32-146">Při načítání konfigurace možností ze souboru nastavení prostřednictvím <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>není nutné explicitně nastavit základní cestu.</span><span class="sxs-lookup"><span data-stu-id="9ae32-146">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="9ae32-147">Konfigurace jednoduchých možností pomocí delegáta</span><span class="sxs-lookup"><span data-stu-id="9ae32-147">Configure simple options with a delegate</span></span>

<span data-ttu-id="9ae32-148">Konfigurace jednoduchých možností s delegátem je znázorněna jako příklad 2 v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9ae32-148">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="9ae32-149">K nastavení hodnot možností použijte delegáta.</span><span class="sxs-lookup"><span data-stu-id="9ae32-149">Use a delegate to set options values.</span></span> <span data-ttu-id="9ae32-150">Ukázková aplikace používá třídu `MyOptionsWithDelegateConfig` (*modely/MyOptionsWithDelegateConfig. cs*):</span><span class="sxs-lookup"><span data-stu-id="9ae32-150">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="9ae32-151">V následujícím kódu se do kontejneru služby přidá druhá <xref:Microsoft.Extensions.Options.IConfigureOptions%601> služba.</span><span class="sxs-lookup"><span data-stu-id="9ae32-151">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="9ae32-152">Pomocí delegáta konfiguruje vazbu s `MyOptionsWithDelegateConfig`:</span><span class="sxs-lookup"><span data-stu-id="9ae32-152">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="9ae32-153">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="9ae32-153">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="9ae32-154">Můžete přidat více poskytovatelů konfigurace.</span><span class="sxs-lookup"><span data-stu-id="9ae32-154">You can add multiple configuration providers.</span></span> <span data-ttu-id="9ae32-155">Poskytovatelé konfigurace jsou k dispozici z balíčků NuGet a jsou aplikováni v pořadí, v jakém jsou registrováni.</span><span class="sxs-lookup"><span data-stu-id="9ae32-155">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="9ae32-156">Další informace najdete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="9ae32-156">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="9ae32-157">Každé volání <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> přidá <xref:Microsoft.Extensions.Options.IConfigureOptions%601> službu do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="9ae32-157">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="9ae32-158">V předchozím příkladu jsou hodnoty `Option1` a `Option2` zadány v souboru *appSettings. JSON*, ale hodnoty `Option1` a `Option2` jsou přepsány nakonfigurovaným delegátem.</span><span class="sxs-lookup"><span data-stu-id="9ae32-158">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="9ae32-159">Je-li povolena více než jedna služba konfigurace, je v posledním zdroji konfigurace zadaný *Server WINS* a nastavena hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="9ae32-159">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="9ae32-160">Při spuštění aplikace vrátí `OnGet` metoda modelu stránky řetězec zobrazující hodnoty třídy možností:</span><span class="sxs-lookup"><span data-stu-id="9ae32-160">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="9ae32-161">Konfigurace podNastavení</span><span class="sxs-lookup"><span data-stu-id="9ae32-161">Suboptions configuration</span></span>

<span data-ttu-id="9ae32-162">Konfigurace dílčích možností je v ukázkové aplikaci znázorněna jako příklad 3.</span><span class="sxs-lookup"><span data-stu-id="9ae32-162">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="9ae32-163">Aplikace by měly vytvořit třídy možností, které se týkají konkrétních skupin scénářů (tříd) v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9ae32-163">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="9ae32-164">Části aplikace, které vyžadují konfigurační hodnoty, by měly mít přístup jenom k hodnotám konfigurace, které používají.</span><span class="sxs-lookup"><span data-stu-id="9ae32-164">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="9ae32-165">Při vytváření vazby mezi možnostmi konfigurace je každá vlastnost v typu možnosti vázána na konfigurační klíč formuláře `property[:sub-property:]`.</span><span class="sxs-lookup"><span data-stu-id="9ae32-165">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="9ae32-166">Například vlastnost `MyOptions.Option1` je vázána na klíčovou `Option1`, která je čtena z vlastnosti `option1` v souboru *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="9ae32-166">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="9ae32-167">V následujícím kódu je do kontejneru služby přidána třetí <xref:Microsoft.Extensions.Options.IConfigureOptions%601> služba.</span><span class="sxs-lookup"><span data-stu-id="9ae32-167">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="9ae32-168">Vytvoří vazby `MySubOptions` k oddílu `subsection` souboru *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="9ae32-168">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="9ae32-169">Metoda `GetSection` vyžaduje obor názvů <xref:Microsoft.Extensions.Configuration?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="9ae32-169">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="9ae32-170">Soubor *appSettings. JSON* ve vzorci definuje `subsection`ho člena s klíči pro `suboption1` a `suboption2`:</span><span class="sxs-lookup"><span data-stu-id="9ae32-170">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/3.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="9ae32-171">Třída `MySubOptions` definuje vlastnosti, `SubOption1` a `SubOption2`pro uchovávání hodnot možností (*modely/MySubOptions. cs*):</span><span class="sxs-lookup"><span data-stu-id="9ae32-171">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="9ae32-172">Metoda `OnGet` modelu stránky vrací řetězec s hodnotami možností (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="9ae32-172">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="9ae32-173">Když je aplikace spuštěna, metoda `OnGet` vrátí řetězec zobrazující hodnoty třídy dílčích možností:</span><span class="sxs-lookup"><span data-stu-id="9ae32-173">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-injection"></a><span data-ttu-id="9ae32-174">Vkládání možností</span><span class="sxs-lookup"><span data-stu-id="9ae32-174">Options injection</span></span>

<span data-ttu-id="9ae32-175">Vkládání možností je znázorněno jako příklad 4 v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9ae32-175">Options injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="9ae32-176">Vložit <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> do:</span><span class="sxs-lookup"><span data-stu-id="9ae32-176">Inject <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into:</span></span>

* <span data-ttu-id="9ae32-177">Stránka Razor nebo zobrazení MVC s direktivou [`@inject`](xref:mvc/views/razor#inject) Razor</span><span class="sxs-lookup"><span data-stu-id="9ae32-177">A Razor page or MVC view with the [`@inject`](xref:mvc/views/razor#inject) Razor directive.</span></span>
* <span data-ttu-id="9ae32-178">Model stránky nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="9ae32-178">A page or view model.</span></span>

<span data-ttu-id="9ae32-179">Následující příklad z ukázkové aplikace vloží <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> do modelu stránky (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="9ae32-179">The following example from the sample app injects <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into a page model (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="9ae32-180">Ukázková aplikace ukazuje, jak vložit `IOptionsMonitor<MyOptions>` s direktivou `@inject`:</span><span class="sxs-lookup"><span data-stu-id="9ae32-180">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/3.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="9ae32-181">Po spuštění aplikace se hodnoty možností zobrazí na vykreslené stránce:</span><span class="sxs-lookup"><span data-stu-id="9ae32-181">When the app is run, the options values are shown in the rendered page:</span></span>

![Hodnoty možností možnost1: value1_from_json a Možnost2:-1 jsou načteny z modelu a zavstřikem do zobrazení.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="9ae32-183">Znovu načíst konfigurační data pomocí IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="9ae32-183">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="9ae32-184">Opětovné načtení konfiguračních dat pomocí <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> je v ukázkové aplikaci znázorněno v příkladu 5.</span><span class="sxs-lookup"><span data-stu-id="9ae32-184">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="9ae32-185">Pomocí <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>jsou možnosti vypočítány jednou za požadavek při otevření a ukládání do mezipaměti po dobu života žádosti.</span><span class="sxs-lookup"><span data-stu-id="9ae32-185">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="9ae32-186">Rozdíl mezi `IOptionsMonitor` a `IOptionsSnapshot` je následující:</span><span class="sxs-lookup"><span data-stu-id="9ae32-186">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="9ae32-187">`IOptionsMonitor` je [Služba typu Singleton](xref:fundamentals/dependency-injection#singleton) , která načte aktuální hodnoty možností kdykoli, což je zvláště užitečné v závislostech singleton.</span><span class="sxs-lookup"><span data-stu-id="9ae32-187">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="9ae32-188">`IOptionsSnapshot` je [Oborová služba](xref:fundamentals/dependency-injection#scoped) a poskytuje snímek možností v době, kdy je objekt `IOptionsSnapshot<T>` vytvořen.</span><span class="sxs-lookup"><span data-stu-id="9ae32-188">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="9ae32-189">Snímky možností jsou navržené pro použití s přechodnými a vymezenými závislostmi.</span><span class="sxs-lookup"><span data-stu-id="9ae32-189">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="9ae32-190">Následující příklad ukazuje, jak se vytvoří nový <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> po změnách *appSettings. JSON* (*pages/index. cshtml. cs*).</span><span class="sxs-lookup"><span data-stu-id="9ae32-190">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="9ae32-191">Více požadavků na server vrací konstantní hodnoty, které poskytuje soubor *appSettings. JSON* , dokud nedojde ke změně souboru a opětovnému načtení konfigurace.</span><span class="sxs-lookup"><span data-stu-id="9ae32-191">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="9ae32-192">Následující obrázek ukazuje počáteční `option1` a hodnoty `option2` načtené ze souboru *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="9ae32-192">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="9ae32-193">Změňte hodnoty v souboru *appSettings. JSON* na `value1_from_json UPDATED` a `200`.</span><span class="sxs-lookup"><span data-stu-id="9ae32-193">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="9ae32-194">Uložte soubor *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="9ae32-194">Save the *appsettings.json* file.</span></span> <span data-ttu-id="9ae32-195">Aktualizujte prohlížeč, aby se zobrazily hodnoty možností, které jsou aktualizované:</span><span class="sxs-lookup"><span data-stu-id="9ae32-195">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="9ae32-196">Podpora pojmenovaných možností s IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="9ae32-196">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="9ae32-197">Pojmenovaná podpora pro <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> je v ukázkové aplikaci znázorněná jako příklad 6.</span><span class="sxs-lookup"><span data-stu-id="9ae32-197">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="9ae32-198">Podpora pojmenovaných možností umožňuje aplikaci rozlišovat konfigurace pojmenovaných možností.</span><span class="sxs-lookup"><span data-stu-id="9ae32-198">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="9ae32-199">V ukázkové aplikaci jsou pojmenované možnosti deklarovány pomocí [OptionsServiceCollectionExtensions. Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), který volá [ConfigureNamedOptions >\<TOptions. Nakonfigurujte](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) metodu rozšíření.</span><span class="sxs-lookup"><span data-stu-id="9ae32-199">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="9ae32-200">U pojmenovaných možností se rozlišují velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="9ae32-200">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="9ae32-201">Ukázková aplikace přistupuje k pojmenovaným možnostem pomocí <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="9ae32-201">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="9ae32-202">Když spustíte ukázkovou aplikaci, vrátí se pojmenované možnosti:</span><span class="sxs-lookup"><span data-stu-id="9ae32-202">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="9ae32-203">hodnoty `named_options_1` se poskytují z konfigurace, které se načítají ze souboru *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="9ae32-203">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="9ae32-204">hodnoty `named_options_2` poskytují:</span><span class="sxs-lookup"><span data-stu-id="9ae32-204">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="9ae32-205">`named_options_2` delegát v `ConfigureServices` pro `Option1`.</span><span class="sxs-lookup"><span data-stu-id="9ae32-205">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="9ae32-206">Výchozí hodnota pro `Option2` poskytovaná `MyOptions`ou třídou.</span><span class="sxs-lookup"><span data-stu-id="9ae32-206">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="9ae32-207">Konfigurace všech možností pomocí metody ConfigureAll</span><span class="sxs-lookup"><span data-stu-id="9ae32-207">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="9ae32-208">Nakonfigurujte všechny instance možností pomocí metody <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*>.</span><span class="sxs-lookup"><span data-stu-id="9ae32-208">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="9ae32-209">Následující kód konfiguruje `Option1` pro všechny instance konfigurace se společnou hodnotou.</span><span class="sxs-lookup"><span data-stu-id="9ae32-209">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="9ae32-210">Do metody `Startup.ConfigureServices` přidejte ručně následující kód:</span><span class="sxs-lookup"><span data-stu-id="9ae32-210">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="9ae32-211">Spuštění ukázkové aplikace po přidání kódu vytvoří následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="9ae32-211">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="9ae32-212">Všechny možnosti jsou pojmenované instance.</span><span class="sxs-lookup"><span data-stu-id="9ae32-212">All options are named instances.</span></span> <span data-ttu-id="9ae32-213">Existující instance <xref:Microsoft.Extensions.Options.IConfigureOptions%601> jsou považovány za cílení na instanci `Options.DefaultName`, která je `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="9ae32-213">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="9ae32-214"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> také implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="9ae32-214"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="9ae32-215">Výchozí implementace <xref:Microsoft.Extensions.Options.IOptionsFactory%601> má logiku odpovídajícím způsobem použít.</span><span class="sxs-lookup"><span data-stu-id="9ae32-215">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="9ae32-216">Možnost `null` s názvem slouží k cílení na všechny pojmenované instance místo konkrétní pojmenované instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> a <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> použít tuto konvenci).</span><span class="sxs-lookup"><span data-stu-id="9ae32-216">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="9ae32-217">Rozhraní API pro OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="9ae32-217">OptionsBuilder API</span></span>

<span data-ttu-id="9ae32-218"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> se používá ke konfiguraci `TOptions` instancí.</span><span class="sxs-lookup"><span data-stu-id="9ae32-218"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="9ae32-219">`OptionsBuilder` zjednodušuje vytváření pojmenovaných možností, protože je pouze jedním parametrem počátečního `AddOptions<TOptions>(string optionsName)`ho volání namísto zobrazení ve všech následujících voláních.</span><span class="sxs-lookup"><span data-stu-id="9ae32-219">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="9ae32-220">Ověřování možností a `ConfigureOptions` přetížení, která přijímají závislosti služby, jsou k dispozici pouze prostřednictvím `OptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="9ae32-220">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="9ae32-221">Ke konfiguraci možností použijte DI Services.</span><span class="sxs-lookup"><span data-stu-id="9ae32-221">Use DI services to configure options</span></span>

<span data-ttu-id="9ae32-222">K dalším službám můžete přistupovat pomocí injektáže závislosti při konfiguraci možností dvěma způsoby:</span><span class="sxs-lookup"><span data-stu-id="9ae32-222">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="9ae32-223">Předejte delegáta konfigurace [na](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) [OptionsBuilder\<TOptions >](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="9ae32-223">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="9ae32-224">`OptionsBuilder<TOptions>` poskytuje přetížení [Konfigurace](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) , která umožňují používat až pět služeb ke konfiguraci možností:</span><span class="sxs-lookup"><span data-stu-id="9ae32-224">`OptionsBuilder<TOptions>` provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="9ae32-225">Vytvořte vlastní typ, který implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> nebo <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> a zaregistrujte typ jako službu.</span><span class="sxs-lookup"><span data-stu-id="9ae32-225">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="9ae32-226">Doporučujeme předat delegáta [konfigurace, protože](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)vytvoření služby je složitější.</span><span class="sxs-lookup"><span data-stu-id="9ae32-226">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="9ae32-227">Vytvoření vlastního typu je ekvivalentní s tím, k čemu architektura slouží při použití možnosti [Konfigurovat](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="9ae32-227">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="9ae32-228">Volání metody [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) obsahuje přechodný obecný <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, který má konstruktor, který přijímá obecné zadané typy služeb.</span><span class="sxs-lookup"><span data-stu-id="9ae32-228">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="9ae32-229">Ověřování možností</span><span class="sxs-lookup"><span data-stu-id="9ae32-229">Options validation</span></span>

<span data-ttu-id="9ae32-230">Ověřování možností umožňuje ověřit možnosti při konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="9ae32-230">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="9ae32-231">Zavolejte `Validate` s metodou ověřování, která vrátí `true`, pokud jsou možnosti platné a `false`, pokud nejsou platné:</span><span class="sxs-lookup"><span data-stu-id="9ae32-231">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

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

<span data-ttu-id="9ae32-232">Předchozí příklad nastaví instanci pojmenované možnosti na `optionalOptionsName`.</span><span class="sxs-lookup"><span data-stu-id="9ae32-232">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="9ae32-233">Instance výchozích možností je `Options.DefaultName`.</span><span class="sxs-lookup"><span data-stu-id="9ae32-233">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="9ae32-234">Ověřování se spustí při vytvoření instance Options.</span><span class="sxs-lookup"><span data-stu-id="9ae32-234">Validation runs when the options instance is created.</span></span> <span data-ttu-id="9ae32-235">Při prvním použití vaší instance možností je zaručeno předání ověření.</span><span class="sxs-lookup"><span data-stu-id="9ae32-235">Your options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="9ae32-236">Ověřování možností nechrání proti změnám možností po počáteční konfiguraci a ověření možností.</span><span class="sxs-lookup"><span data-stu-id="9ae32-236">Options validation doesn't guard against options modifications after the options are initially configured and validated.</span></span>

<span data-ttu-id="9ae32-237">Metoda `Validate` přijímá `Func<TOptions, bool>`.</span><span class="sxs-lookup"><span data-stu-id="9ae32-237">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="9ae32-238">K úplnému přizpůsobení ověřování implementujte `IValidateOptions<TOptions>`, která umožňuje:</span><span class="sxs-lookup"><span data-stu-id="9ae32-238">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="9ae32-239">Ověřování více typů možností: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="9ae32-239">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="9ae32-240">Ověření, které závisí na jiném typu možnosti: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="9ae32-240">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="9ae32-241">`IValidateOptions` ověří:</span><span class="sxs-lookup"><span data-stu-id="9ae32-241">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="9ae32-242">Konkrétní instance pojmenované možnosti.</span><span class="sxs-lookup"><span data-stu-id="9ae32-242">A specific named options instance.</span></span>
* <span data-ttu-id="9ae32-243">Všechny možnosti, pokud je `name` `null`.</span><span class="sxs-lookup"><span data-stu-id="9ae32-243">All options when `name` is `null`.</span></span>

<span data-ttu-id="9ae32-244">Vrácení `ValidateOptionsResult` z implementace rozhraní:</span><span class="sxs-lookup"><span data-stu-id="9ae32-244">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="9ae32-245">Ověřování založené na datových anotacích je k dispozici v balíčku [Microsoft. Extensions. Options. DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) voláním metody <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> v `OptionsBuilder<TOptions>`.</span><span class="sxs-lookup"><span data-stu-id="9ae32-245">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="9ae32-246">na `Microsoft.Extensions.Options.DataAnnotations` se implicitně odkazuje v ASP.NET Corech aplikacích.</span><span class="sxs-lookup"><span data-stu-id="9ae32-246">`Microsoft.Extensions.Options.DataAnnotations` is implicitly referenced in ASP.NET Core apps.</span></span>

```csharp
using System.ComponentModel.DataAnnotations;
using Microsoft.Extensions.DependencyInjection;

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
        sp.GetRequiredService<IOptionsMonitor<AnnotatedOptions>>().CurrentValue);
    ValidateFailure<AnnotatedOptions>(error, Options.DefaultName, 1,
        "DataAnnotation validation failed for members Required " +
            "with the error 'The Required field is required.'.",
        "DataAnnotation validation failed for members StringLength " +
            "with the error 'Too long.'.",
        "DataAnnotation validation failed for members IntRange " +
            "with the error 'Out of range.'.");
}
```

<span data-ttu-id="9ae32-247">Ověření Eager (při spuštění selže) je v budoucí verzi zváženo.</span><span class="sxs-lookup"><span data-stu-id="9ae32-247">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="9ae32-248">Možnosti po konfiguraci</span><span class="sxs-lookup"><span data-stu-id="9ae32-248">Options post-configuration</span></span>

<span data-ttu-id="9ae32-249">Nastavte po konfiguraci <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="9ae32-249">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="9ae32-250">Po dokončení konfigurace <xref:Microsoft.Extensions.Options.IConfigureOptions%601> se spustí po konfiguraci:</span><span class="sxs-lookup"><span data-stu-id="9ae32-250">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="9ae32-251"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> je k dispozici pro nastavení po konfiguraci s názvem:</span><span class="sxs-lookup"><span data-stu-id="9ae32-251"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="9ae32-252">Pomocí <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> vystavte všechny instance konfigurace:</span><span class="sxs-lookup"><span data-stu-id="9ae32-252">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="9ae32-253">Přístup k možnostem při spuštění</span><span class="sxs-lookup"><span data-stu-id="9ae32-253">Accessing options during startup</span></span>

<span data-ttu-id="9ae32-254"><xref:Microsoft.Extensions.Options.IOptions%601> a <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> lze použít v `Startup.Configure`, protože služby jsou vytvořeny před spuštěním metody `Configure`.</span><span class="sxs-lookup"><span data-stu-id="9ae32-254"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, 
    IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="9ae32-255">V `Startup.ConfigureServices`nepoužívejte <xref:Microsoft.Extensions.Options.IOptions%601> ani <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="9ae32-255">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="9ae32-256">Stav nekonzistentních možností může existovat v důsledku řazení registrací služby.</span><span class="sxs-lookup"><span data-stu-id="9ae32-256">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="9ae32-257">Vzor možností nastavení využívá třídy pro reprezentaci skupin souvisejících nastavení.</span><span class="sxs-lookup"><span data-stu-id="9ae32-257">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="9ae32-258">Když jsou [nastavení konfigurace](xref:fundamentals/configuration/index) izolována scénářem do samostatných tříd, aplikace dodržuje dva důležité principy technického řízení:</span><span class="sxs-lookup"><span data-stu-id="9ae32-258">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="9ae32-259">[Princip oddělování rozhraní (ISP) nebo zapouzdření](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; scénáře (třídy), které závisí na nastavení konfigurace, závisí jenom na nastavení konfigurace, která používají.</span><span class="sxs-lookup"><span data-stu-id="9ae32-259">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="9ae32-260">[Oddělení](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) potíží s tím, jak &ndash; nastavení různých částí aplikace nejsou závislá nebo vzájemně závislá na sobě.</span><span class="sxs-lookup"><span data-stu-id="9ae32-260">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="9ae32-261">Možnosti také poskytují mechanismus pro ověření konfiguračních dat.</span><span class="sxs-lookup"><span data-stu-id="9ae32-261">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="9ae32-262">Další informace najdete v části [ověřování možností](#options-validation) .</span><span class="sxs-lookup"><span data-stu-id="9ae32-262">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="9ae32-263">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9ae32-263">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9ae32-264">Požadavky</span><span class="sxs-lookup"><span data-stu-id="9ae32-264">Prerequisites</span></span>

<span data-ttu-id="9ae32-265">Odkaz na balíček [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) nebo přidejte odkaz na balíček do balíčku [Microsoft. Extensions. Options. ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) .</span><span class="sxs-lookup"><span data-stu-id="9ae32-265">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="9ae32-266">Možnosti rozhraní</span><span class="sxs-lookup"><span data-stu-id="9ae32-266">Options interfaces</span></span>

<span data-ttu-id="9ae32-267"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> slouží k načtení možností a správě oznámení možností pro `TOptions` instance.</span><span class="sxs-lookup"><span data-stu-id="9ae32-267"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="9ae32-268"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> podporuje následující scénáře:</span><span class="sxs-lookup"><span data-stu-id="9ae32-268"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="9ae32-269">Oznámení změny</span><span class="sxs-lookup"><span data-stu-id="9ae32-269">Change notifications</span></span>
* [<span data-ttu-id="9ae32-270">Pojmenované možnosti</span><span class="sxs-lookup"><span data-stu-id="9ae32-270">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="9ae32-271">Znovu Načítatelné konfigurace</span><span class="sxs-lookup"><span data-stu-id="9ae32-271">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="9ae32-272">Neplatné selektivní možnosti (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="9ae32-272">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="9ae32-273">Scénáře po [konfiguraci](#options-post-configuration) umožňují nastavit nebo změnit možnosti po <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ke konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="9ae32-273">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="9ae32-274"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> zodpovídá za vytváření nových instancí možností.</span><span class="sxs-lookup"><span data-stu-id="9ae32-274"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="9ae32-275">Má jednu <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> metodu.</span><span class="sxs-lookup"><span data-stu-id="9ae32-275">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="9ae32-276">Výchozí implementace přebírá všechny registrované <xref:Microsoft.Extensions.Options.IConfigureOptions%601> a <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> a předá všechny konfigurace a potom po konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="9ae32-276">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="9ae32-277">Rozlišuje mezi <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> a <xref:Microsoft.Extensions.Options.IConfigureOptions%601> a volá pouze příslušné rozhraní.</span><span class="sxs-lookup"><span data-stu-id="9ae32-277">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="9ae32-278"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> používá <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> k ukládání instancí `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="9ae32-278"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="9ae32-279"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> zruší platnost instancí možností v monitorování tak, aby se hodnota přepočítala (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="9ae32-279">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="9ae32-280">Hodnoty lze ručně zavést pomocí <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span><span class="sxs-lookup"><span data-stu-id="9ae32-280">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="9ae32-281">Metoda <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> se používá v případě, že by se všechny pojmenované instance měly znovu vytvořit na vyžádání.</span><span class="sxs-lookup"><span data-stu-id="9ae32-281">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="9ae32-282"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> je užitečná ve scénářích, ve kterých by se měly přepočítat možnosti pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="9ae32-282"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="9ae32-283">Další informace najdete v části [opětovné načtení konfiguračních dat pomocí IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) .</span><span class="sxs-lookup"><span data-stu-id="9ae32-283">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="9ae32-284"><xref:Microsoft.Extensions.Options.IOptions%601> lze použít k podpoře možností.</span><span class="sxs-lookup"><span data-stu-id="9ae32-284"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="9ae32-285"><xref:Microsoft.Extensions.Options.IOptions%601> ale nepodporuje předchozí scénáře <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="9ae32-285">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="9ae32-286">Můžete nadále používat <xref:Microsoft.Extensions.Options.IOptions%601> ve stávajících rozhraních a knihovnách, které již používají rozhraní <xref:Microsoft.Extensions.Options.IOptions%601> a nevyžadují scénáře poskytované <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="9ae32-286">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="9ae32-287">Konfigurace obecných možností</span><span class="sxs-lookup"><span data-stu-id="9ae32-287">General options configuration</span></span>

<span data-ttu-id="9ae32-288">Konfigurace obecných možností se v ukázkové aplikaci ukazuje jako příklad 1.</span><span class="sxs-lookup"><span data-stu-id="9ae32-288">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="9ae32-289">Třída Options musí být neabstraktní s veřejným konstruktorem bez parametrů.</span><span class="sxs-lookup"><span data-stu-id="9ae32-289">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="9ae32-290">Následující třída `MyOptions`má dvě vlastnosti, `Option1` a `Option2`.</span><span class="sxs-lookup"><span data-stu-id="9ae32-290">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="9ae32-291">Nastavení výchozích hodnot je volitelné, ale konstruktor třídy v následujícím příkladu nastaví výchozí hodnotu `Option1`.</span><span class="sxs-lookup"><span data-stu-id="9ae32-291">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="9ae32-292">`Option2` má výchozí hodnotu nastavenou pomocí přímé inicializace vlastnosti (*modely/MyOptions. cs*):</span><span class="sxs-lookup"><span data-stu-id="9ae32-292">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="9ae32-293">Třída `MyOptions` je přidána do kontejneru služby s <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> a je svázána s konfigurací:</span><span class="sxs-lookup"><span data-stu-id="9ae32-293">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="9ae32-294">Následující model stránky používá [vkládání závislostí konstruktoru](xref:mvc/controllers/dependency-injection) s <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> pro přístup k nastavení (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="9ae32-294">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="9ae32-295">Soubor *appSettings. JSON* v ukázce určuje hodnoty pro `option1` a `option2`:</span><span class="sxs-lookup"><span data-stu-id="9ae32-295">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="9ae32-296">Při spuštění aplikace vrátí `OnGet` metoda modelu stránky řetězec zobrazující hodnoty třídy možností:</span><span class="sxs-lookup"><span data-stu-id="9ae32-296">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="9ae32-297">Při načítání konfigurace možností pomocí vlastního <xref:System.Configuration.ConfigurationBuilder> v souboru nastavení potvrďte, že je správně nastavená základní cesta:</span><span class="sxs-lookup"><span data-stu-id="9ae32-297">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="9ae32-298">Při načítání konfigurace možností ze souboru nastavení prostřednictvím <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>není nutné explicitně nastavit základní cestu.</span><span class="sxs-lookup"><span data-stu-id="9ae32-298">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="9ae32-299">Konfigurace jednoduchých možností pomocí delegáta</span><span class="sxs-lookup"><span data-stu-id="9ae32-299">Configure simple options with a delegate</span></span>

<span data-ttu-id="9ae32-300">Konfigurace jednoduchých možností s delegátem je znázorněna jako příklad 2 v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9ae32-300">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="9ae32-301">K nastavení hodnot možností použijte delegáta.</span><span class="sxs-lookup"><span data-stu-id="9ae32-301">Use a delegate to set options values.</span></span> <span data-ttu-id="9ae32-302">Ukázková aplikace používá třídu `MyOptionsWithDelegateConfig` (*modely/MyOptionsWithDelegateConfig. cs*):</span><span class="sxs-lookup"><span data-stu-id="9ae32-302">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="9ae32-303">V následujícím kódu se do kontejneru služby přidá druhá <xref:Microsoft.Extensions.Options.IConfigureOptions%601> služba.</span><span class="sxs-lookup"><span data-stu-id="9ae32-303">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="9ae32-304">Pomocí delegáta konfiguruje vazbu s `MyOptionsWithDelegateConfig`:</span><span class="sxs-lookup"><span data-stu-id="9ae32-304">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="9ae32-305">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="9ae32-305">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="9ae32-306">Můžete přidat více poskytovatelů konfigurace.</span><span class="sxs-lookup"><span data-stu-id="9ae32-306">You can add multiple configuration providers.</span></span> <span data-ttu-id="9ae32-307">Poskytovatelé konfigurace jsou k dispozici z balíčků NuGet a jsou aplikováni v pořadí, v jakém jsou registrováni.</span><span class="sxs-lookup"><span data-stu-id="9ae32-307">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="9ae32-308">Další informace najdete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="9ae32-308">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="9ae32-309">Každé volání <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> přidá <xref:Microsoft.Extensions.Options.IConfigureOptions%601> službu do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="9ae32-309">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="9ae32-310">V předchozím příkladu jsou hodnoty `Option1` a `Option2` zadány v souboru *appSettings. JSON*, ale hodnoty `Option1` a `Option2` jsou přepsány nakonfigurovaným delegátem.</span><span class="sxs-lookup"><span data-stu-id="9ae32-310">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="9ae32-311">Je-li povolena více než jedna služba konfigurace, je v posledním zdroji konfigurace zadaný *Server WINS* a nastavena hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="9ae32-311">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="9ae32-312">Při spuštění aplikace vrátí `OnGet` metoda modelu stránky řetězec zobrazující hodnoty třídy možností:</span><span class="sxs-lookup"><span data-stu-id="9ae32-312">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="9ae32-313">Konfigurace podNastavení</span><span class="sxs-lookup"><span data-stu-id="9ae32-313">Suboptions configuration</span></span>

<span data-ttu-id="9ae32-314">Konfigurace dílčích možností je v ukázkové aplikaci znázorněna jako příklad 3.</span><span class="sxs-lookup"><span data-stu-id="9ae32-314">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="9ae32-315">Aplikace by měly vytvořit třídy možností, které se týkají konkrétních skupin scénářů (tříd) v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9ae32-315">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="9ae32-316">Části aplikace, které vyžadují konfigurační hodnoty, by měly mít přístup jenom k hodnotám konfigurace, které používají.</span><span class="sxs-lookup"><span data-stu-id="9ae32-316">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="9ae32-317">Při vytváření vazby mezi možnostmi konfigurace je každá vlastnost v typu možnosti vázána na konfigurační klíč formuláře `property[:sub-property:]`.</span><span class="sxs-lookup"><span data-stu-id="9ae32-317">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="9ae32-318">Například vlastnost `MyOptions.Option1` je vázána na klíčovou `Option1`, která je čtena z vlastnosti `option1` v souboru *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="9ae32-318">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="9ae32-319">V následujícím kódu je do kontejneru služby přidána třetí <xref:Microsoft.Extensions.Options.IConfigureOptions%601> služba.</span><span class="sxs-lookup"><span data-stu-id="9ae32-319">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="9ae32-320">Vytvoří vazby `MySubOptions` k oddílu `subsection` souboru *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="9ae32-320">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="9ae32-321">Metoda `GetSection` vyžaduje obor názvů <xref:Microsoft.Extensions.Configuration?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="9ae32-321">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="9ae32-322">Soubor *appSettings. JSON* ve vzorci definuje `subsection`ho člena s klíči pro `suboption1` a `suboption2`:</span><span class="sxs-lookup"><span data-stu-id="9ae32-322">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="9ae32-323">Třída `MySubOptions` definuje vlastnosti, `SubOption1` a `SubOption2`pro uchovávání hodnot možností (*modely/MySubOptions. cs*):</span><span class="sxs-lookup"><span data-stu-id="9ae32-323">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="9ae32-324">Metoda `OnGet` modelu stránky vrací řetězec s hodnotami možností (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="9ae32-324">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="9ae32-325">Když je aplikace spuštěna, metoda `OnGet` vrátí řetězec zobrazující hodnoty třídy dílčích možností:</span><span class="sxs-lookup"><span data-stu-id="9ae32-325">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-injection"></a><span data-ttu-id="9ae32-326">Vkládání možností</span><span class="sxs-lookup"><span data-stu-id="9ae32-326">Options injection</span></span>

<span data-ttu-id="9ae32-327">Vkládání možností je znázorněno jako příklad 4 v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9ae32-327">Options injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="9ae32-328">Vložit <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> do:</span><span class="sxs-lookup"><span data-stu-id="9ae32-328">Inject <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into:</span></span>

* <span data-ttu-id="9ae32-329">Stránka Razor nebo zobrazení MVC s direktivou [`@inject`](xref:mvc/views/razor#inject) Razor</span><span class="sxs-lookup"><span data-stu-id="9ae32-329">A Razor page or MVC view with the [`@inject`](xref:mvc/views/razor#inject) Razor directive.</span></span>
* <span data-ttu-id="9ae32-330">Model stránky nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="9ae32-330">A page or view model.</span></span>

<span data-ttu-id="9ae32-331">Následující příklad z ukázkové aplikace vloží <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> do modelu stránky (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="9ae32-331">The following example from the sample app injects <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into a page model (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="9ae32-332">Ukázková aplikace ukazuje, jak vložit `IOptionsMonitor<MyOptions>` s direktivou `@inject`:</span><span class="sxs-lookup"><span data-stu-id="9ae32-332">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="9ae32-333">Po spuštění aplikace se hodnoty možností zobrazí na vykreslené stránce:</span><span class="sxs-lookup"><span data-stu-id="9ae32-333">When the app is run, the options values are shown in the rendered page:</span></span>

![Hodnoty možností možnost1: value1_from_json a Možnost2:-1 jsou načteny z modelu a zavstřikem do zobrazení.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="9ae32-335">Znovu načíst konfigurační data pomocí IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="9ae32-335">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="9ae32-336">Opětovné načtení konfiguračních dat pomocí <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> je v ukázkové aplikaci znázorněno v příkladu 5.</span><span class="sxs-lookup"><span data-stu-id="9ae32-336">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="9ae32-337">Pomocí <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>jsou možnosti vypočítány jednou za požadavek při otevření a ukládání do mezipaměti po dobu života žádosti.</span><span class="sxs-lookup"><span data-stu-id="9ae32-337">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="9ae32-338">Rozdíl mezi `IOptionsMonitor` a `IOptionsSnapshot` je následující:</span><span class="sxs-lookup"><span data-stu-id="9ae32-338">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="9ae32-339">`IOptionsMonitor` je [Služba typu Singleton](xref:fundamentals/dependency-injection#singleton) , která načte aktuální hodnoty možností kdykoli, což je zvláště užitečné v závislostech singleton.</span><span class="sxs-lookup"><span data-stu-id="9ae32-339">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="9ae32-340">`IOptionsSnapshot` je [Oborová služba](xref:fundamentals/dependency-injection#scoped) a poskytuje snímek možností v době, kdy je objekt `IOptionsSnapshot<T>` vytvořen.</span><span class="sxs-lookup"><span data-stu-id="9ae32-340">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="9ae32-341">Snímky možností jsou navržené pro použití s přechodnými a vymezenými závislostmi.</span><span class="sxs-lookup"><span data-stu-id="9ae32-341">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="9ae32-342">Následující příklad ukazuje, jak se vytvoří nový <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> po změnách *appSettings. JSON* (*pages/index. cshtml. cs*).</span><span class="sxs-lookup"><span data-stu-id="9ae32-342">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="9ae32-343">Více požadavků na server vrací konstantní hodnoty, které poskytuje soubor *appSettings. JSON* , dokud nedojde ke změně souboru a opětovnému načtení konfigurace.</span><span class="sxs-lookup"><span data-stu-id="9ae32-343">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="9ae32-344">Následující obrázek ukazuje počáteční `option1` a hodnoty `option2` načtené ze souboru *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="9ae32-344">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="9ae32-345">Změňte hodnoty v souboru *appSettings. JSON* na `value1_from_json UPDATED` a `200`.</span><span class="sxs-lookup"><span data-stu-id="9ae32-345">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="9ae32-346">Uložte soubor *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="9ae32-346">Save the *appsettings.json* file.</span></span> <span data-ttu-id="9ae32-347">Aktualizujte prohlížeč, aby se zobrazily hodnoty možností, které jsou aktualizované:</span><span class="sxs-lookup"><span data-stu-id="9ae32-347">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="9ae32-348">Podpora pojmenovaných možností s IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="9ae32-348">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="9ae32-349">Pojmenovaná podpora pro <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> je v ukázkové aplikaci znázorněná jako příklad 6.</span><span class="sxs-lookup"><span data-stu-id="9ae32-349">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="9ae32-350">Podpora pojmenovaných možností umožňuje aplikaci rozlišovat konfigurace pojmenovaných možností.</span><span class="sxs-lookup"><span data-stu-id="9ae32-350">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="9ae32-351">V ukázkové aplikaci jsou pojmenované možnosti deklarovány pomocí [OptionsServiceCollectionExtensions. Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), který volá [ConfigureNamedOptions >\<TOptions. Nakonfigurujte](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) metodu rozšíření.</span><span class="sxs-lookup"><span data-stu-id="9ae32-351">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="9ae32-352">U pojmenovaných možností se rozlišují velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="9ae32-352">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="9ae32-353">Ukázková aplikace přistupuje k pojmenovaným možnostem pomocí <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="9ae32-353">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="9ae32-354">Když spustíte ukázkovou aplikaci, vrátí se pojmenované možnosti:</span><span class="sxs-lookup"><span data-stu-id="9ae32-354">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="9ae32-355">hodnoty `named_options_1` se poskytují z konfigurace, které se načítají ze souboru *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="9ae32-355">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="9ae32-356">hodnoty `named_options_2` poskytují:</span><span class="sxs-lookup"><span data-stu-id="9ae32-356">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="9ae32-357">`named_options_2` delegát v `ConfigureServices` pro `Option1`.</span><span class="sxs-lookup"><span data-stu-id="9ae32-357">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="9ae32-358">Výchozí hodnota pro `Option2` poskytovaná `MyOptions`ou třídou.</span><span class="sxs-lookup"><span data-stu-id="9ae32-358">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="9ae32-359">Konfigurace všech možností pomocí metody ConfigureAll</span><span class="sxs-lookup"><span data-stu-id="9ae32-359">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="9ae32-360">Nakonfigurujte všechny instance možností pomocí metody <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*>.</span><span class="sxs-lookup"><span data-stu-id="9ae32-360">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="9ae32-361">Následující kód konfiguruje `Option1` pro všechny instance konfigurace se společnou hodnotou.</span><span class="sxs-lookup"><span data-stu-id="9ae32-361">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="9ae32-362">Do metody `Startup.ConfigureServices` přidejte ručně následující kód:</span><span class="sxs-lookup"><span data-stu-id="9ae32-362">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="9ae32-363">Spuštění ukázkové aplikace po přidání kódu vytvoří následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="9ae32-363">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="9ae32-364">Všechny možnosti jsou pojmenované instance.</span><span class="sxs-lookup"><span data-stu-id="9ae32-364">All options are named instances.</span></span> <span data-ttu-id="9ae32-365">Existující instance <xref:Microsoft.Extensions.Options.IConfigureOptions%601> jsou považovány za cílení na instanci `Options.DefaultName`, která je `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="9ae32-365">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="9ae32-366"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> také implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="9ae32-366"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="9ae32-367">Výchozí implementace <xref:Microsoft.Extensions.Options.IOptionsFactory%601> má logiku odpovídajícím způsobem použít.</span><span class="sxs-lookup"><span data-stu-id="9ae32-367">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="9ae32-368">Možnost `null` s názvem slouží k cílení na všechny pojmenované instance místo konkrétní pojmenované instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> a <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> použít tuto konvenci).</span><span class="sxs-lookup"><span data-stu-id="9ae32-368">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="9ae32-369">Rozhraní API pro OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="9ae32-369">OptionsBuilder API</span></span>

<span data-ttu-id="9ae32-370"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> se používá ke konfiguraci `TOptions` instancí.</span><span class="sxs-lookup"><span data-stu-id="9ae32-370"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="9ae32-371">`OptionsBuilder` zjednodušuje vytváření pojmenovaných možností, protože je pouze jedním parametrem počátečního `AddOptions<TOptions>(string optionsName)`ho volání namísto zobrazení ve všech následujících voláních.</span><span class="sxs-lookup"><span data-stu-id="9ae32-371">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="9ae32-372">Ověřování možností a `ConfigureOptions` přetížení, která přijímají závislosti služby, jsou k dispozici pouze prostřednictvím `OptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="9ae32-372">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="9ae32-373">Ke konfiguraci možností použijte DI Services.</span><span class="sxs-lookup"><span data-stu-id="9ae32-373">Use DI services to configure options</span></span>

<span data-ttu-id="9ae32-374">K dalším službám můžete přistupovat pomocí injektáže závislosti při konfiguraci možností dvěma způsoby:</span><span class="sxs-lookup"><span data-stu-id="9ae32-374">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="9ae32-375">Předejte delegáta konfigurace [na](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) [OptionsBuilder\<TOptions >](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="9ae32-375">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="9ae32-376">[OptionsBuilder\<TOptions >](xref:Microsoft.Extensions.Options.OptionsBuilder`1) poskytuje přetížení [Konfigurace](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) , která umožňují použít až pět služeb ke konfiguraci možností:</span><span class="sxs-lookup"><span data-stu-id="9ae32-376">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="9ae32-377">Vytvořte vlastní typ, který implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> nebo <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> a zaregistrujte typ jako službu.</span><span class="sxs-lookup"><span data-stu-id="9ae32-377">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="9ae32-378">Doporučujeme předat delegáta [konfigurace, protože](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)vytvoření služby je složitější.</span><span class="sxs-lookup"><span data-stu-id="9ae32-378">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="9ae32-379">Vytvoření vlastního typu je ekvivalentní s tím, k čemu architektura slouží při použití možnosti [Konfigurovat](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="9ae32-379">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="9ae32-380">Volání metody [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) obsahuje přechodný obecný <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, který má konstruktor, který přijímá obecné zadané typy služeb.</span><span class="sxs-lookup"><span data-stu-id="9ae32-380">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="9ae32-381">Ověřování možností</span><span class="sxs-lookup"><span data-stu-id="9ae32-381">Options validation</span></span>

<span data-ttu-id="9ae32-382">Ověřování možností umožňuje ověřit možnosti při konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="9ae32-382">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="9ae32-383">Zavolejte `Validate` s metodou ověřování, která vrátí `true`, pokud jsou možnosti platné a `false`, pokud nejsou platné:</span><span class="sxs-lookup"><span data-stu-id="9ae32-383">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

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

<span data-ttu-id="9ae32-384">Předchozí příklad nastaví instanci pojmenované možnosti na `optionalOptionsName`.</span><span class="sxs-lookup"><span data-stu-id="9ae32-384">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="9ae32-385">Instance výchozích možností je `Options.DefaultName`.</span><span class="sxs-lookup"><span data-stu-id="9ae32-385">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="9ae32-386">Ověřování se spustí při vytvoření instance Options.</span><span class="sxs-lookup"><span data-stu-id="9ae32-386">Validation runs when the options instance is created.</span></span> <span data-ttu-id="9ae32-387">Při prvním použití vaší instance možností je zaručeno předání ověření.</span><span class="sxs-lookup"><span data-stu-id="9ae32-387">Your options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="9ae32-388">Ověřování možností nechrání proti změnám možností po počáteční konfiguraci a ověření možností.</span><span class="sxs-lookup"><span data-stu-id="9ae32-388">Options validation doesn't guard against options modifications after the options are initially configured and validated.</span></span>

<span data-ttu-id="9ae32-389">Metoda `Validate` přijímá `Func<TOptions, bool>`.</span><span class="sxs-lookup"><span data-stu-id="9ae32-389">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="9ae32-390">K úplnému přizpůsobení ověřování implementujte `IValidateOptions<TOptions>`, která umožňuje:</span><span class="sxs-lookup"><span data-stu-id="9ae32-390">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="9ae32-391">Ověřování více typů možností: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="9ae32-391">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="9ae32-392">Ověření, které závisí na jiném typu možnosti: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="9ae32-392">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="9ae32-393">`IValidateOptions` ověří:</span><span class="sxs-lookup"><span data-stu-id="9ae32-393">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="9ae32-394">Konkrétní instance pojmenované možnosti.</span><span class="sxs-lookup"><span data-stu-id="9ae32-394">A specific named options instance.</span></span>
* <span data-ttu-id="9ae32-395">Všechny možnosti, pokud je `name` `null`.</span><span class="sxs-lookup"><span data-stu-id="9ae32-395">All options when `name` is `null`.</span></span>

<span data-ttu-id="9ae32-396">Vrácení `ValidateOptionsResult` z implementace rozhraní:</span><span class="sxs-lookup"><span data-stu-id="9ae32-396">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="9ae32-397">Ověřování založené na datových anotacích je k dispozici v balíčku [Microsoft. Extensions. Options. DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) voláním metody <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> v `OptionsBuilder<TOptions>`.</span><span class="sxs-lookup"><span data-stu-id="9ae32-397">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="9ae32-398">`Microsoft.Extensions.Options.DataAnnotations` je součástí [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="9ae32-398">`Microsoft.Extensions.Options.DataAnnotations` is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

```csharp
using Microsoft.Extensions.DependencyInjection;

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
        sp.GetRequiredService<IOptionsMonitor<AnnotatedOptions>>().CurrentValue);
    ValidateFailure<AnnotatedOptions>(error, Options.DefaultName, 1,
        "DataAnnotation validation failed for members Required " +
            "with the error 'The Required field is required.'.",
        "DataAnnotation validation failed for members StringLength " +
            "with the error 'Too long.'.",
        "DataAnnotation validation failed for members IntRange " +
            "with the error 'Out of range.'.");
}
```

<span data-ttu-id="9ae32-399">Ověření Eager (při spuštění selže) je v budoucí verzi zváženo.</span><span class="sxs-lookup"><span data-stu-id="9ae32-399">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="9ae32-400">Možnosti po konfiguraci</span><span class="sxs-lookup"><span data-stu-id="9ae32-400">Options post-configuration</span></span>

<span data-ttu-id="9ae32-401">Nastavte po konfiguraci <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="9ae32-401">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="9ae32-402">Po dokončení konfigurace <xref:Microsoft.Extensions.Options.IConfigureOptions%601> se spustí po konfiguraci:</span><span class="sxs-lookup"><span data-stu-id="9ae32-402">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="9ae32-403"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> je k dispozici pro nastavení po konfiguraci s názvem:</span><span class="sxs-lookup"><span data-stu-id="9ae32-403"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="9ae32-404">Pomocí <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> vystavte všechny instance konfigurace:</span><span class="sxs-lookup"><span data-stu-id="9ae32-404">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="9ae32-405">Přístup k možnostem při spuštění</span><span class="sxs-lookup"><span data-stu-id="9ae32-405">Accessing options during startup</span></span>

<span data-ttu-id="9ae32-406"><xref:Microsoft.Extensions.Options.IOptions%601> a <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> lze použít v `Startup.Configure`, protože služby jsou vytvořeny před spuštěním metody `Configure`.</span><span class="sxs-lookup"><span data-stu-id="9ae32-406"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="9ae32-407">V `Startup.ConfigureServices`nepoužívejte <xref:Microsoft.Extensions.Options.IOptions%601> ani <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="9ae32-407">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="9ae32-408">Stav nekonzistentních možností může existovat v důsledku řazení registrací služby.</span><span class="sxs-lookup"><span data-stu-id="9ae32-408">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="9ae32-409">Vzor možností nastavení využívá třídy pro reprezentaci skupin souvisejících nastavení.</span><span class="sxs-lookup"><span data-stu-id="9ae32-409">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="9ae32-410">Když jsou [nastavení konfigurace](xref:fundamentals/configuration/index) izolována scénářem do samostatných tříd, aplikace dodržuje dva důležité principy technického řízení:</span><span class="sxs-lookup"><span data-stu-id="9ae32-410">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="9ae32-411">[Princip oddělování rozhraní (ISP) nebo zapouzdření](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; scénáře (třídy), které závisí na nastavení konfigurace, závisí jenom na nastavení konfigurace, která používají.</span><span class="sxs-lookup"><span data-stu-id="9ae32-411">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="9ae32-412">[Oddělení](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) potíží s tím, jak &ndash; nastavení různých částí aplikace nejsou závislá nebo vzájemně závislá na sobě.</span><span class="sxs-lookup"><span data-stu-id="9ae32-412">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="9ae32-413">Možnosti také poskytují mechanismus pro ověření konfiguračních dat.</span><span class="sxs-lookup"><span data-stu-id="9ae32-413">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="9ae32-414">Další informace najdete v části [ověřování možností](#options-validation) .</span><span class="sxs-lookup"><span data-stu-id="9ae32-414">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="9ae32-415">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9ae32-415">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9ae32-416">Požadavky</span><span class="sxs-lookup"><span data-stu-id="9ae32-416">Prerequisites</span></span>

<span data-ttu-id="9ae32-417">Odkaz na balíček [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) nebo přidejte odkaz na balíček do balíčku [Microsoft. Extensions. Options. ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) .</span><span class="sxs-lookup"><span data-stu-id="9ae32-417">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="9ae32-418">Možnosti rozhraní</span><span class="sxs-lookup"><span data-stu-id="9ae32-418">Options interfaces</span></span>

<span data-ttu-id="9ae32-419"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> slouží k načtení možností a správě oznámení možností pro `TOptions` instance.</span><span class="sxs-lookup"><span data-stu-id="9ae32-419"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="9ae32-420"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> podporuje následující scénáře:</span><span class="sxs-lookup"><span data-stu-id="9ae32-420"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="9ae32-421">Oznámení změny</span><span class="sxs-lookup"><span data-stu-id="9ae32-421">Change notifications</span></span>
* [<span data-ttu-id="9ae32-422">Pojmenované možnosti</span><span class="sxs-lookup"><span data-stu-id="9ae32-422">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="9ae32-423">Znovu Načítatelné konfigurace</span><span class="sxs-lookup"><span data-stu-id="9ae32-423">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="9ae32-424">Neplatné selektivní možnosti (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="9ae32-424">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="9ae32-425">Scénáře po [konfiguraci](#options-post-configuration) umožňují nastavit nebo změnit možnosti po <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ke konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="9ae32-425">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="9ae32-426"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> zodpovídá za vytváření nových instancí možností.</span><span class="sxs-lookup"><span data-stu-id="9ae32-426"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="9ae32-427">Má jednu <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> metodu.</span><span class="sxs-lookup"><span data-stu-id="9ae32-427">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="9ae32-428">Výchozí implementace přebírá všechny registrované <xref:Microsoft.Extensions.Options.IConfigureOptions%601> a <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> a předá všechny konfigurace a potom po konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="9ae32-428">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="9ae32-429">Rozlišuje mezi <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> a <xref:Microsoft.Extensions.Options.IConfigureOptions%601> a volá pouze příslušné rozhraní.</span><span class="sxs-lookup"><span data-stu-id="9ae32-429">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="9ae32-430"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> používá <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> k ukládání instancí `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="9ae32-430"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="9ae32-431"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> zruší platnost instancí možností v monitorování tak, aby se hodnota přepočítala (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="9ae32-431">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="9ae32-432">Hodnoty lze ručně zavést pomocí <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span><span class="sxs-lookup"><span data-stu-id="9ae32-432">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="9ae32-433">Metoda <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> se používá v případě, že by se všechny pojmenované instance měly znovu vytvořit na vyžádání.</span><span class="sxs-lookup"><span data-stu-id="9ae32-433">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="9ae32-434"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> je užitečná ve scénářích, ve kterých by se měly přepočítat možnosti pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="9ae32-434"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="9ae32-435">Další informace najdete v části [opětovné načtení konfiguračních dat pomocí IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) .</span><span class="sxs-lookup"><span data-stu-id="9ae32-435">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="9ae32-436"><xref:Microsoft.Extensions.Options.IOptions%601> lze použít k podpoře možností.</span><span class="sxs-lookup"><span data-stu-id="9ae32-436"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="9ae32-437"><xref:Microsoft.Extensions.Options.IOptions%601> ale nepodporuje předchozí scénáře <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="9ae32-437">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="9ae32-438">Můžete nadále používat <xref:Microsoft.Extensions.Options.IOptions%601> ve stávajících rozhraních a knihovnách, které již používají rozhraní <xref:Microsoft.Extensions.Options.IOptions%601> a nevyžadují scénáře poskytované <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="9ae32-438">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="9ae32-439">Konfigurace obecných možností</span><span class="sxs-lookup"><span data-stu-id="9ae32-439">General options configuration</span></span>

<span data-ttu-id="9ae32-440">Konfigurace obecných možností se v ukázkové aplikaci ukazuje jako příklad 1.</span><span class="sxs-lookup"><span data-stu-id="9ae32-440">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="9ae32-441">Třída Options musí být neabstraktní s veřejným konstruktorem bez parametrů.</span><span class="sxs-lookup"><span data-stu-id="9ae32-441">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="9ae32-442">Následující třída `MyOptions`má dvě vlastnosti, `Option1` a `Option2`.</span><span class="sxs-lookup"><span data-stu-id="9ae32-442">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="9ae32-443">Nastavení výchozích hodnot je volitelné, ale konstruktor třídy v následujícím příkladu nastaví výchozí hodnotu `Option1`.</span><span class="sxs-lookup"><span data-stu-id="9ae32-443">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="9ae32-444">`Option2` má výchozí hodnotu nastavenou pomocí přímé inicializace vlastnosti (*modely/MyOptions. cs*):</span><span class="sxs-lookup"><span data-stu-id="9ae32-444">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="9ae32-445">Třída `MyOptions` je přidána do kontejneru služby s <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> a je svázána s konfigurací:</span><span class="sxs-lookup"><span data-stu-id="9ae32-445">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="9ae32-446">Následující model stránky používá [vkládání závislostí konstruktoru](xref:mvc/controllers/dependency-injection) s <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> pro přístup k nastavení (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="9ae32-446">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="9ae32-447">Soubor *appSettings. JSON* v ukázce určuje hodnoty pro `option1` a `option2`:</span><span class="sxs-lookup"><span data-stu-id="9ae32-447">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="9ae32-448">Při spuštění aplikace vrátí `OnGet` metoda modelu stránky řetězec zobrazující hodnoty třídy možností:</span><span class="sxs-lookup"><span data-stu-id="9ae32-448">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="9ae32-449">Při načítání konfigurace možností pomocí vlastního <xref:System.Configuration.ConfigurationBuilder> v souboru nastavení potvrďte, že je správně nastavená základní cesta:</span><span class="sxs-lookup"><span data-stu-id="9ae32-449">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="9ae32-450">Při načítání konfigurace možností ze souboru nastavení prostřednictvím <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>není nutné explicitně nastavit základní cestu.</span><span class="sxs-lookup"><span data-stu-id="9ae32-450">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="9ae32-451">Konfigurace jednoduchých možností pomocí delegáta</span><span class="sxs-lookup"><span data-stu-id="9ae32-451">Configure simple options with a delegate</span></span>

<span data-ttu-id="9ae32-452">Konfigurace jednoduchých možností s delegátem je znázorněna jako příklad 2 v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9ae32-452">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="9ae32-453">K nastavení hodnot možností použijte delegáta.</span><span class="sxs-lookup"><span data-stu-id="9ae32-453">Use a delegate to set options values.</span></span> <span data-ttu-id="9ae32-454">Ukázková aplikace používá třídu `MyOptionsWithDelegateConfig` (*modely/MyOptionsWithDelegateConfig. cs*):</span><span class="sxs-lookup"><span data-stu-id="9ae32-454">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="9ae32-455">V následujícím kódu se do kontejneru služby přidá druhá <xref:Microsoft.Extensions.Options.IConfigureOptions%601> služba.</span><span class="sxs-lookup"><span data-stu-id="9ae32-455">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="9ae32-456">Pomocí delegáta konfiguruje vazbu s `MyOptionsWithDelegateConfig`:</span><span class="sxs-lookup"><span data-stu-id="9ae32-456">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="9ae32-457">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="9ae32-457">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="9ae32-458">Můžete přidat více poskytovatelů konfigurace.</span><span class="sxs-lookup"><span data-stu-id="9ae32-458">You can add multiple configuration providers.</span></span> <span data-ttu-id="9ae32-459">Poskytovatelé konfigurace jsou k dispozici z balíčků NuGet a jsou aplikováni v pořadí, v jakém jsou registrováni.</span><span class="sxs-lookup"><span data-stu-id="9ae32-459">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="9ae32-460">Další informace najdete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="9ae32-460">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="9ae32-461">Každé volání <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> přidá <xref:Microsoft.Extensions.Options.IConfigureOptions%601> službu do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="9ae32-461">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="9ae32-462">V předchozím příkladu jsou hodnoty `Option1` a `Option2` zadány v souboru *appSettings. JSON*, ale hodnoty `Option1` a `Option2` jsou přepsány nakonfigurovaným delegátem.</span><span class="sxs-lookup"><span data-stu-id="9ae32-462">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="9ae32-463">Je-li povolena více než jedna služba konfigurace, je v posledním zdroji konfigurace zadaný *Server WINS* a nastavena hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="9ae32-463">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="9ae32-464">Při spuštění aplikace vrátí `OnGet` metoda modelu stránky řetězec zobrazující hodnoty třídy možností:</span><span class="sxs-lookup"><span data-stu-id="9ae32-464">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="9ae32-465">Konfigurace podNastavení</span><span class="sxs-lookup"><span data-stu-id="9ae32-465">Suboptions configuration</span></span>

<span data-ttu-id="9ae32-466">Konfigurace dílčích možností je v ukázkové aplikaci znázorněna jako příklad 3.</span><span class="sxs-lookup"><span data-stu-id="9ae32-466">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="9ae32-467">Aplikace by měly vytvořit třídy možností, které se týkají konkrétních skupin scénářů (tříd) v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9ae32-467">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="9ae32-468">Části aplikace, které vyžadují konfigurační hodnoty, by měly mít přístup jenom k hodnotám konfigurace, které používají.</span><span class="sxs-lookup"><span data-stu-id="9ae32-468">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="9ae32-469">Při vytváření vazby mezi možnostmi konfigurace je každá vlastnost v typu možnosti vázána na konfigurační klíč formuláře `property[:sub-property:]`.</span><span class="sxs-lookup"><span data-stu-id="9ae32-469">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="9ae32-470">Například vlastnost `MyOptions.Option1` je vázána na klíčovou `Option1`, která je čtena z vlastnosti `option1` v souboru *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="9ae32-470">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="9ae32-471">V následujícím kódu je do kontejneru služby přidána třetí <xref:Microsoft.Extensions.Options.IConfigureOptions%601> služba.</span><span class="sxs-lookup"><span data-stu-id="9ae32-471">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="9ae32-472">Vytvoří vazby `MySubOptions` k oddílu `subsection` souboru *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="9ae32-472">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="9ae32-473">Metoda `GetSection` vyžaduje obor názvů <xref:Microsoft.Extensions.Configuration?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="9ae32-473">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="9ae32-474">Soubor *appSettings. JSON* ve vzorci definuje `subsection`ho člena s klíči pro `suboption1` a `suboption2`:</span><span class="sxs-lookup"><span data-stu-id="9ae32-474">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="9ae32-475">Třída `MySubOptions` definuje vlastnosti, `SubOption1` a `SubOption2`pro uchovávání hodnot možností (*modely/MySubOptions. cs*):</span><span class="sxs-lookup"><span data-stu-id="9ae32-475">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="9ae32-476">Metoda `OnGet` modelu stránky vrací řetězec s hodnotami možností (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="9ae32-476">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="9ae32-477">Když je aplikace spuštěna, metoda `OnGet` vrátí řetězec zobrazující hodnoty třídy dílčích možností:</span><span class="sxs-lookup"><span data-stu-id="9ae32-477">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="9ae32-478">Možnosti poskytované modelem zobrazení nebo vkládáním přímých zobrazení</span><span class="sxs-lookup"><span data-stu-id="9ae32-478">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="9ae32-479">Možnosti poskytované modelem zobrazení nebo vkládáním přímých zobrazení jsou v ukázkové aplikaci znázorněné jako příklad 4.</span><span class="sxs-lookup"><span data-stu-id="9ae32-479">Options provided by a view model or with direct view injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="9ae32-480">Možnosti lze zadat v modelu zobrazení nebo vložením <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> přímo do zobrazení (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="9ae32-480">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="9ae32-481">Ukázková aplikace ukazuje, jak vložit `IOptionsMonitor<MyOptions>` s direktivou `@inject`:</span><span class="sxs-lookup"><span data-stu-id="9ae32-481">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="9ae32-482">Po spuštění aplikace se hodnoty možností zobrazí na vykreslené stránce:</span><span class="sxs-lookup"><span data-stu-id="9ae32-482">When the app is run, the options values are shown in the rendered page:</span></span>

![Hodnoty možností možnost1: value1_from_json a Možnost2:-1 jsou načteny z modelu a zavstřikem do zobrazení.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="9ae32-484">Znovu načíst konfigurační data pomocí IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="9ae32-484">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="9ae32-485">Opětovné načtení konfiguračních dat pomocí <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> je v ukázkové aplikaci znázorněno v příkladu 5.</span><span class="sxs-lookup"><span data-stu-id="9ae32-485">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="9ae32-486"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> podporuje opětovné načítání možností s minimální režií zpracování.</span><span class="sxs-lookup"><span data-stu-id="9ae32-486"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="9ae32-487">Možnosti jsou vypočítány jednou za požadavek při otevření a ukládání do mezipaměti po dobu života žádosti.</span><span class="sxs-lookup"><span data-stu-id="9ae32-487">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="9ae32-488">Následující příklad ukazuje, jak se vytvoří nový <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> po změnách *appSettings. JSON* (*pages/index. cshtml. cs*).</span><span class="sxs-lookup"><span data-stu-id="9ae32-488">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="9ae32-489">Více požadavků na server vrací konstantní hodnoty, které poskytuje soubor *appSettings. JSON* , dokud nedojde ke změně souboru a opětovnému načtení konfigurace.</span><span class="sxs-lookup"><span data-stu-id="9ae32-489">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="9ae32-490">Následující obrázek ukazuje počáteční `option1` a hodnoty `option2` načtené ze souboru *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="9ae32-490">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="9ae32-491">Změňte hodnoty v souboru *appSettings. JSON* na `value1_from_json UPDATED` a `200`.</span><span class="sxs-lookup"><span data-stu-id="9ae32-491">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="9ae32-492">Uložte soubor *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="9ae32-492">Save the *appsettings.json* file.</span></span> <span data-ttu-id="9ae32-493">Aktualizujte prohlížeč, aby se zobrazily hodnoty možností, které jsou aktualizované:</span><span class="sxs-lookup"><span data-stu-id="9ae32-493">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="9ae32-494">Podpora pojmenovaných možností s IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="9ae32-494">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="9ae32-495">Pojmenovaná podpora pro <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> je v ukázkové aplikaci znázorněná jako příklad 6.</span><span class="sxs-lookup"><span data-stu-id="9ae32-495">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="9ae32-496">Podpora pojmenovaných možností umožňuje aplikaci rozlišovat konfigurace pojmenovaných možností.</span><span class="sxs-lookup"><span data-stu-id="9ae32-496">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="9ae32-497">V ukázkové aplikaci jsou pojmenované možnosti deklarovány pomocí [OptionsServiceCollectionExtensions. Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), který volá [ConfigureNamedOptions >\<TOptions. Nakonfigurujte](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) metodu rozšíření.</span><span class="sxs-lookup"><span data-stu-id="9ae32-497">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="9ae32-498">U pojmenovaných možností se rozlišují velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="9ae32-498">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="9ae32-499">Ukázková aplikace přistupuje k pojmenovaným možnostem pomocí <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="9ae32-499">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="9ae32-500">Když spustíte ukázkovou aplikaci, vrátí se pojmenované možnosti:</span><span class="sxs-lookup"><span data-stu-id="9ae32-500">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="9ae32-501">hodnoty `named_options_1` se poskytují z konfigurace, které se načítají ze souboru *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="9ae32-501">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="9ae32-502">hodnoty `named_options_2` poskytují:</span><span class="sxs-lookup"><span data-stu-id="9ae32-502">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="9ae32-503">`named_options_2` delegát v `ConfigureServices` pro `Option1`.</span><span class="sxs-lookup"><span data-stu-id="9ae32-503">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="9ae32-504">Výchozí hodnota pro `Option2` poskytovaná `MyOptions`ou třídou.</span><span class="sxs-lookup"><span data-stu-id="9ae32-504">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="9ae32-505">Konfigurace všech možností pomocí metody ConfigureAll</span><span class="sxs-lookup"><span data-stu-id="9ae32-505">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="9ae32-506">Nakonfigurujte všechny instance možností pomocí metody <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*>.</span><span class="sxs-lookup"><span data-stu-id="9ae32-506">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="9ae32-507">Následující kód konfiguruje `Option1` pro všechny instance konfigurace se společnou hodnotou.</span><span class="sxs-lookup"><span data-stu-id="9ae32-507">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="9ae32-508">Do metody `Startup.ConfigureServices` přidejte ručně následující kód:</span><span class="sxs-lookup"><span data-stu-id="9ae32-508">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="9ae32-509">Spuštění ukázkové aplikace po přidání kódu vytvoří následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="9ae32-509">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="9ae32-510">Všechny možnosti jsou pojmenované instance.</span><span class="sxs-lookup"><span data-stu-id="9ae32-510">All options are named instances.</span></span> <span data-ttu-id="9ae32-511">Existující instance <xref:Microsoft.Extensions.Options.IConfigureOptions%601> jsou považovány za cílení na instanci `Options.DefaultName`, která je `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="9ae32-511">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="9ae32-512"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> také implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="9ae32-512"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="9ae32-513">Výchozí implementace <xref:Microsoft.Extensions.Options.IOptionsFactory%601> má logiku odpovídajícím způsobem použít.</span><span class="sxs-lookup"><span data-stu-id="9ae32-513">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="9ae32-514">Možnost `null` s názvem slouží k cílení na všechny pojmenované instance místo konkrétní pojmenované instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> a <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> použít tuto konvenci).</span><span class="sxs-lookup"><span data-stu-id="9ae32-514">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="9ae32-515">Rozhraní API pro OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="9ae32-515">OptionsBuilder API</span></span>

<span data-ttu-id="9ae32-516"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> se používá ke konfiguraci `TOptions` instancí.</span><span class="sxs-lookup"><span data-stu-id="9ae32-516"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="9ae32-517">`OptionsBuilder` zjednodušuje vytváření pojmenovaných možností, protože je pouze jedním parametrem počátečního `AddOptions<TOptions>(string optionsName)`ho volání namísto zobrazení ve všech následujících voláních.</span><span class="sxs-lookup"><span data-stu-id="9ae32-517">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="9ae32-518">Ověřování možností a `ConfigureOptions` přetížení, která přijímají závislosti služby, jsou k dispozici pouze prostřednictvím `OptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="9ae32-518">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="9ae32-519">Ke konfiguraci možností použijte DI Services.</span><span class="sxs-lookup"><span data-stu-id="9ae32-519">Use DI services to configure options</span></span>

<span data-ttu-id="9ae32-520">K dalším službám můžete přistupovat pomocí injektáže závislosti při konfiguraci možností dvěma způsoby:</span><span class="sxs-lookup"><span data-stu-id="9ae32-520">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="9ae32-521">Předejte delegáta konfigurace [na](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) [OptionsBuilder\<TOptions >](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="9ae32-521">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="9ae32-522">[OptionsBuilder\<TOptions >](xref:Microsoft.Extensions.Options.OptionsBuilder`1) poskytuje přetížení [Konfigurace](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) , která umožňují použít až pět služeb ke konfiguraci možností:</span><span class="sxs-lookup"><span data-stu-id="9ae32-522">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="9ae32-523">Vytvořte vlastní typ, který implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> nebo <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> a zaregistrujte typ jako službu.</span><span class="sxs-lookup"><span data-stu-id="9ae32-523">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="9ae32-524">Doporučujeme předat delegáta [konfigurace, protože](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)vytvoření služby je složitější.</span><span class="sxs-lookup"><span data-stu-id="9ae32-524">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="9ae32-525">Vytvoření vlastního typu je ekvivalentní s tím, k čemu architektura slouží při použití možnosti [Konfigurovat](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="9ae32-525">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="9ae32-526">Volání metody [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) obsahuje přechodný obecný <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, který má konstruktor, který přijímá obecné zadané typy služeb.</span><span class="sxs-lookup"><span data-stu-id="9ae32-526">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-post-configuration"></a><span data-ttu-id="9ae32-527">Možnosti po konfiguraci</span><span class="sxs-lookup"><span data-stu-id="9ae32-527">Options post-configuration</span></span>

<span data-ttu-id="9ae32-528">Nastavte po konfiguraci <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="9ae32-528">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="9ae32-529">Po dokončení konfigurace <xref:Microsoft.Extensions.Options.IConfigureOptions%601> se spustí po konfiguraci:</span><span class="sxs-lookup"><span data-stu-id="9ae32-529">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="9ae32-530"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> je k dispozici pro nastavení po konfiguraci s názvem:</span><span class="sxs-lookup"><span data-stu-id="9ae32-530"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="9ae32-531">Pomocí <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> vystavte všechny instance konfigurace:</span><span class="sxs-lookup"><span data-stu-id="9ae32-531">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="9ae32-532">Přístup k možnostem při spuštění</span><span class="sxs-lookup"><span data-stu-id="9ae32-532">Accessing options during startup</span></span>

<span data-ttu-id="9ae32-533"><xref:Microsoft.Extensions.Options.IOptions%601> a <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> lze použít v `Startup.Configure`, protože služby jsou vytvořeny před spuštěním metody `Configure`.</span><span class="sxs-lookup"><span data-stu-id="9ae32-533"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="9ae32-534">V `Startup.ConfigureServices`nepoužívejte <xref:Microsoft.Extensions.Options.IOptions%601> ani <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="9ae32-534">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="9ae32-535">Stav nekonzistentních možností může existovat v důsledku řazení registrací služby.</span><span class="sxs-lookup"><span data-stu-id="9ae32-535">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="9ae32-536">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="9ae32-536">Additional resources</span></span>

* <xref:fundamentals/configuration/index>

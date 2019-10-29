---
title: Vzor možností v ASP.NET Core
author: guardrex
description: Zjistěte, jak pomocí vzoru možností znázornit skupiny souvisejících nastavení v aplikacích ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/28/2019
uid: fundamentals/configuration/options
ms.openlocfilehash: f9e94e8d1736b7ffaa2640aba03da6b239a34f0a
ms.sourcegitcommit: 16cf016035f0c9acf3ff0ad874c56f82e013d415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2019
ms.locfileid: "73034017"
---
# <a name="options-pattern-in-aspnet-core"></a><span data-ttu-id="36753-103">Vzor možností v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="36753-103">Options pattern in ASP.NET Core</span></span>

<span data-ttu-id="36753-104">Od [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="36753-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="36753-105">Vzor možností používá třídy, které reprezentují skupiny souvisejících nastavení.</span><span class="sxs-lookup"><span data-stu-id="36753-105">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="36753-106">Když jsou [nastavení konfigurace](xref:fundamentals/configuration/index) izolována scénářem do samostatných tříd, aplikace dodržuje dva důležité principy technického řízení:</span><span class="sxs-lookup"><span data-stu-id="36753-106">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="36753-107">[Princip oddělování rozhraní (ISP) nebo zapouzdření](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; scénáře (třídy), které závisí na nastavení konfigurace, závisí jenom na nastavení konfigurace, která používají.</span><span class="sxs-lookup"><span data-stu-id="36753-107">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="36753-108">[Oddělení](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) potíží, které se týkají &ndash; pro různé části aplikace, není závislé nebo vzájemně nezávisle na sobě.</span><span class="sxs-lookup"><span data-stu-id="36753-108">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="36753-109">Možnosti také poskytují mechanismus pro ověření konfiguračních dat.</span><span class="sxs-lookup"><span data-stu-id="36753-109">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="36753-110">Další informace najdete v části [ověřování možností](#options-validation) .</span><span class="sxs-lookup"><span data-stu-id="36753-110">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="36753-111">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="36753-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="package"></a><span data-ttu-id="36753-112">Balíček</span><span class="sxs-lookup"><span data-stu-id="36753-112">Package</span></span>

<span data-ttu-id="36753-113">Na balíček [Microsoft. Extensions. Options. ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) se implicitně odkazuje v aplikacích ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="36753-113">The [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package is implicitly referenced in ASP.NET Core apps.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="36753-114">Možnosti rozhraní</span><span class="sxs-lookup"><span data-stu-id="36753-114">Options interfaces</span></span>

<span data-ttu-id="36753-115"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> slouží k načtení možností a správě oznámení možností pro `TOptions` instance.</span><span class="sxs-lookup"><span data-stu-id="36753-115"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="36753-116"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> podporuje následující scénáře:</span><span class="sxs-lookup"><span data-stu-id="36753-116"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="36753-117">Oznámení změny</span><span class="sxs-lookup"><span data-stu-id="36753-117">Change notifications</span></span>
* [<span data-ttu-id="36753-118">Pojmenované možnosti</span><span class="sxs-lookup"><span data-stu-id="36753-118">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="36753-119">Znovu Načítatelné konfigurace</span><span class="sxs-lookup"><span data-stu-id="36753-119">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="36753-120">Neplatné selektivní možnosti (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="36753-120">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="36753-121">Scénáře po [konfiguraci](#options-post-configuration) umožňují nastavit nebo změnit možnosti po <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ke konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="36753-121">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="36753-122"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> zodpovídá za vytváření nových instancí možností.</span><span class="sxs-lookup"><span data-stu-id="36753-122"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="36753-123">Má jednu metodu <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*>.</span><span class="sxs-lookup"><span data-stu-id="36753-123">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="36753-124">Výchozí implementace přebírá všechny registrované <xref:Microsoft.Extensions.Options.IConfigureOptions%601> a <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> a předá všechny konfigurace a potom po konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="36753-124">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="36753-125">Rozlišuje mezi <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> a <xref:Microsoft.Extensions.Options.IConfigureOptions%601> a volá pouze příslušné rozhraní.</span><span class="sxs-lookup"><span data-stu-id="36753-125">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="36753-126"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> používá <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> k ukládání instancí `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="36753-126"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="36753-127"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> zruší platnost instancí možností v monitorování tak, aby se hodnota přepočítala (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="36753-127">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="36753-128">Hodnoty lze ručně zavést pomocí <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span><span class="sxs-lookup"><span data-stu-id="36753-128">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="36753-129">Metoda <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> se používá v případě, že by se všechny pojmenované instance měly znovu vytvořit na vyžádání.</span><span class="sxs-lookup"><span data-stu-id="36753-129">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="36753-130"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> je užitečná ve scénářích, ve kterých by se měly přepočítat možnosti pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="36753-130"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="36753-131">Další informace najdete v části [opětovné načtení konfiguračních dat pomocí IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) .</span><span class="sxs-lookup"><span data-stu-id="36753-131">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="36753-132"><xref:Microsoft.Extensions.Options.IOptions%601> lze použít k podpoře možností.</span><span class="sxs-lookup"><span data-stu-id="36753-132"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="36753-133"><xref:Microsoft.Extensions.Options.IOptions%601> ale nepodporuje předchozí scénáře <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="36753-133">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="36753-134">Můžete nadále používat <xref:Microsoft.Extensions.Options.IOptions%601> ve stávajících rozhraních a knihovnách, které již používají rozhraní <xref:Microsoft.Extensions.Options.IOptions%601> a nevyžadují scénáře poskytované <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="36753-134">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="36753-135">Konfigurace obecných možností</span><span class="sxs-lookup"><span data-stu-id="36753-135">General options configuration</span></span>

<span data-ttu-id="36753-136">Konfigurace obecných možností se v ukázkové aplikaci ukazuje jako příklad &num;1.</span><span class="sxs-lookup"><span data-stu-id="36753-136">General options configuration is demonstrated as Example &num;1 in the sample app.</span></span>

<span data-ttu-id="36753-137">Třída Options musí být neabstraktní s veřejným konstruktorem bez parametrů.</span><span class="sxs-lookup"><span data-stu-id="36753-137">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="36753-138">Následující třída `MyOptions` má dvě vlastnosti `Option1` a `Option2`.</span><span class="sxs-lookup"><span data-stu-id="36753-138">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="36753-139">Nastavení výchozích hodnot je volitelné, ale konstruktor třídy v následujícím příkladu nastaví výchozí hodnotu `Option1`.</span><span class="sxs-lookup"><span data-stu-id="36753-139">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="36753-140">`Option2` má výchozí hodnotu nastavenou tak, že vlastnost inicializuje přímo (*modely/MyOptions. cs*):</span><span class="sxs-lookup"><span data-stu-id="36753-140">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="36753-141">Třída `MyOptions` je přidána do kontejneru služby s <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> a je svázána s konfigurací:</span><span class="sxs-lookup"><span data-stu-id="36753-141">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="36753-142">Následující model stránky používá [vkládání závislostí konstruktoru](xref:mvc/controllers/dependency-injection) s <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> pro přístup k nastavení (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="36753-142">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="36753-143">Soubor *appSettings. JSON* v ukázce určuje hodnoty pro `option1` a `option2`:</span><span class="sxs-lookup"><span data-stu-id="36753-143">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/3.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="36753-144">Když je aplikace spuštěna, metoda `OnGet` modelu stránky vrátí řetězec zobrazující hodnoty třídy možností:</span><span class="sxs-lookup"><span data-stu-id="36753-144">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="36753-145">Když pomocí vlastního <xref:System.Configuration.ConfigurationBuilder> načtěte konfiguraci možností ze souboru nastavení, zkontrolujte, jestli je správně nastavená základní cesta:</span><span class="sxs-lookup"><span data-stu-id="36753-145">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="36753-146">Při načítání konfigurace možností ze souboru nastavení prostřednictvím <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> není nutné explicitně nastavit základní cestu.</span><span class="sxs-lookup"><span data-stu-id="36753-146">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="36753-147">Konfigurace jednoduchých možností pomocí delegáta</span><span class="sxs-lookup"><span data-stu-id="36753-147">Configure simple options with a delegate</span></span>

<span data-ttu-id="36753-148">Konfigurace jednoduchých možností s delegátem je znázorněna jako příklad &num;2 v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="36753-148">Configuring simple options with a delegate is demonstrated as Example &num;2 in the sample app.</span></span>

<span data-ttu-id="36753-149">K nastavení hodnot možností použijte delegáta.</span><span class="sxs-lookup"><span data-stu-id="36753-149">Use a delegate to set options values.</span></span> <span data-ttu-id="36753-150">Ukázková aplikace používá třídu `MyOptionsWithDelegateConfig` (*modely/MyOptionsWithDelegateConfig. cs*):</span><span class="sxs-lookup"><span data-stu-id="36753-150">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="36753-151">V následujícím kódu se do kontejneru služby přidá druhá <xref:Microsoft.Extensions.Options.IConfigureOptions%601> služba.</span><span class="sxs-lookup"><span data-stu-id="36753-151">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="36753-152">Pomocí delegáta konfiguruje vazbu s `MyOptionsWithDelegateConfig`:</span><span class="sxs-lookup"><span data-stu-id="36753-152">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="36753-153">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="36753-153">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="36753-154">Můžete přidat více poskytovatelů konfigurace.</span><span class="sxs-lookup"><span data-stu-id="36753-154">You can add multiple configuration providers.</span></span> <span data-ttu-id="36753-155">Poskytovatelé konfigurace jsou k dispozici z balíčků NuGet a jsou aplikováni v pořadí, v jakém jsou registrováni.</span><span class="sxs-lookup"><span data-stu-id="36753-155">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="36753-156">Další informace najdete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="36753-156">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="36753-157">Každé volání <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> přidá <xref:Microsoft.Extensions.Options.IConfigureOptions%601> službu do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="36753-157">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="36753-158">V předchozím příkladu jsou hodnoty `Option1` a `Option2` zadány v souboru *appSettings. JSON*, ale hodnoty `Option1` a `Option2` jsou přepsány nakonfigurovaným delegátem.</span><span class="sxs-lookup"><span data-stu-id="36753-158">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="36753-159">Je-li povolena více než jedna služba konfigurace, je v posledním zdroji konfigurace zadaný *Server WINS* a nastavena hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="36753-159">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="36753-160">Když je aplikace spuštěna, metoda `OnGet` modelu stránky vrátí řetězec zobrazující hodnoty třídy možností:</span><span class="sxs-lookup"><span data-stu-id="36753-160">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="36753-161">Konfigurace podNastavení</span><span class="sxs-lookup"><span data-stu-id="36753-161">Suboptions configuration</span></span>

<span data-ttu-id="36753-162">Konfigurace dílčích možností se v ukázkové aplikaci ukazuje jako příklad &num;3.</span><span class="sxs-lookup"><span data-stu-id="36753-162">Suboptions configuration is demonstrated as Example &num;3 in the sample app.</span></span>

<span data-ttu-id="36753-163">Aplikace by měly vytvořit třídy možností, které se týkají konkrétních skupin scénářů (tříd) v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="36753-163">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="36753-164">Části aplikace, které vyžadují konfigurační hodnoty, by měly mít přístup jenom k hodnotám konfigurace, které používají.</span><span class="sxs-lookup"><span data-stu-id="36753-164">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="36753-165">Při vytváření vazby mezi možnostmi konfigurace je každá vlastnost v typu možnosti vázána na konfigurační klíč formuláře `property[:sub-property:]`.</span><span class="sxs-lookup"><span data-stu-id="36753-165">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="36753-166">Například vlastnost `MyOptions.Option1` je vázána na klíč `Option1`, který je načten z vlastnosti `option1` v souboru *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="36753-166">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="36753-167">V následujícím kódu je do kontejneru služby přidána třetí <xref:Microsoft.Extensions.Options.IConfigureOptions%601> služba.</span><span class="sxs-lookup"><span data-stu-id="36753-167">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="36753-168">Vytvoří vazby `MySubOptions` na oddíl `subsection` souboru *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="36753-168">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="36753-169">Metoda `GetSection` vyžaduje obor názvů <xref:Microsoft.Extensions.Configuration?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="36753-169">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="36753-170">Soubor *appSettings. JSON* ve vzorci definuje člena `subsection` s klíči pro `suboption1` a `suboption2`:</span><span class="sxs-lookup"><span data-stu-id="36753-170">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/3.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="36753-171">Třída `MySubOptions` definuje vlastnosti, `SubOption1` a `SubOption2`, aby obsahovaly hodnoty možností (*modely/MySubOptions. cs*):</span><span class="sxs-lookup"><span data-stu-id="36753-171">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="36753-172">Metoda `OnGet` modelu stránky vrací řetězec s hodnotami možností (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="36753-172">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="36753-173">Při spuštění aplikace vrátí metoda `OnGet` řetězec zobrazující hodnoty třídy dílčích možností:</span><span class="sxs-lookup"><span data-stu-id="36753-173">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="36753-174">Možnosti poskytované modelem zobrazení nebo vkládáním přímých zobrazení</span><span class="sxs-lookup"><span data-stu-id="36753-174">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="36753-175">Možnosti poskytované modelem zobrazení nebo vkládáním přímých zobrazení jsou v ukázkové aplikaci znázorněné jako příklad &num;4.</span><span class="sxs-lookup"><span data-stu-id="36753-175">Options provided by a view model or with direct view injection is demonstrated as Example &num;4 in the sample app.</span></span>

<span data-ttu-id="36753-176">Možnosti lze zadat v modelu zobrazení nebo vložením <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> přímo do zobrazení (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="36753-176">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="36753-177">Ukázková aplikace ukazuje, jak vložit `IOptionsMonitor<MyOptions>` s direktivou `@inject`:</span><span class="sxs-lookup"><span data-stu-id="36753-177">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/3.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="36753-178">Po spuštění aplikace se hodnoty možností zobrazí na vykreslené stránce:</span><span class="sxs-lookup"><span data-stu-id="36753-178">When the app is run, the options values are shown in the rendered page:</span></span>

![Hodnoty možností možnost1: value1_from_json a Možnost2:-1 jsou načteny z modelu a vložením do zobrazení.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="36753-180">Znovu načíst konfigurační data pomocí IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="36753-180">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="36753-181">Opětovné načtení konfiguračních dat pomocí <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> je v ukázkové aplikaci znázorněno v příkladu &num;5.</span><span class="sxs-lookup"><span data-stu-id="36753-181">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example &num;5 in the sample app.</span></span>

<span data-ttu-id="36753-182"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> podporuje opětovné načítání možností s minimální režií zpracování.</span><span class="sxs-lookup"><span data-stu-id="36753-182"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="36753-183">Možnosti jsou vypočítány jednou za požadavek při otevření a ukládání do mezipaměti po dobu života žádosti.</span><span class="sxs-lookup"><span data-stu-id="36753-183">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="36753-184">Následující příklad ukazuje, jak se vytvoří nový <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> po změnách *appSettings. JSON* (*pages/index. cshtml. cs*).</span><span class="sxs-lookup"><span data-stu-id="36753-184">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="36753-185">Více požadavků na server vrací konstantní hodnoty, které poskytuje soubor *appSettings. JSON* , dokud nedojde ke změně souboru a opětovnému načtení konfigurace.</span><span class="sxs-lookup"><span data-stu-id="36753-185">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="36753-186">Následující obrázek ukazuje počáteční hodnoty `option1` a `option2` načtené ze souboru *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="36753-186">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="36753-187">Změňte hodnoty v souboru *appSettings. JSON* na `value1_from_json UPDATED` a `200`.</span><span class="sxs-lookup"><span data-stu-id="36753-187">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="36753-188">Uložte soubor *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="36753-188">Save the *appsettings.json* file.</span></span> <span data-ttu-id="36753-189">Aktualizujte prohlížeč, aby se zobrazily hodnoty možností, které jsou aktualizované:</span><span class="sxs-lookup"><span data-stu-id="36753-189">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="36753-190">Podpora pojmenovaných možností s IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="36753-190">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="36753-191">Podpora pojmenovaných možností s <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> je v ukázkové aplikaci znázorněna jako příklad &num;6.</span><span class="sxs-lookup"><span data-stu-id="36753-191">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example &num;6 in the sample app.</span></span>

<span data-ttu-id="36753-192">Podpora *pojmenovaných možností* umožňuje aplikaci rozlišovat konfigurace pojmenovaných možností.</span><span class="sxs-lookup"><span data-stu-id="36753-192">*Named options* support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="36753-193">V ukázkové aplikaci jsou pojmenované možnosti deklarovány pomocí [OptionsServiceCollectionExtensions. Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), který volá [ConfigureNamedOptions >\<TOptions. Konfigurace](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) metody rozšíření:</span><span class="sxs-lookup"><span data-stu-id="36753-193">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="36753-194">Ukázková aplikace přistupuje k pojmenovaným možnostem <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="36753-194">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="36753-195">Když spustíte ukázkovou aplikaci, vrátí se pojmenované možnosti:</span><span class="sxs-lookup"><span data-stu-id="36753-195">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="36753-196">hodnoty `named_options_1` se poskytují z konfigurace, které se načítají ze souboru *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="36753-196">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="36753-197">hodnoty `named_options_2` poskytují:</span><span class="sxs-lookup"><span data-stu-id="36753-197">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="36753-198">`named_options_2` delegát v `ConfigureServices` pro `Option1`.</span><span class="sxs-lookup"><span data-stu-id="36753-198">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="36753-199">Výchozí hodnota pro `Option2` poskytovaná třídou `MyOptions`.</span><span class="sxs-lookup"><span data-stu-id="36753-199">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="36753-200">Konfigurace všech možností pomocí metody ConfigureAll</span><span class="sxs-lookup"><span data-stu-id="36753-200">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="36753-201">Nakonfigurujte všechny instance možností pomocí metody <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*>.</span><span class="sxs-lookup"><span data-stu-id="36753-201">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="36753-202">Následující kód konfiguruje `Option1` pro všechny instance konfigurace se společnou hodnotou.</span><span class="sxs-lookup"><span data-stu-id="36753-202">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="36753-203">Přidejte následující kód ručně do metody `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="36753-203">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="36753-204">Spuštění ukázkové aplikace po přidání kódu vytvoří následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="36753-204">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="36753-205">Všechny možnosti jsou pojmenované instance.</span><span class="sxs-lookup"><span data-stu-id="36753-205">All options are named instances.</span></span> <span data-ttu-id="36753-206">Existující instance <xref:Microsoft.Extensions.Options.IConfigureOptions%601> jsou považovány za cílení na instanci `Options.DefaultName`, která je `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="36753-206">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="36753-207"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> také implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="36753-207"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="36753-208">Výchozí implementace <xref:Microsoft.Extensions.Options.IOptionsFactory%601> má logiku odpovídajícím způsobem použít.</span><span class="sxs-lookup"><span data-stu-id="36753-208">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="36753-209">Pojmenovaná možnost `null` se používá k cílení na všechny pojmenované instance místo konkrétní pojmenované instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> a <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> používají tuto konvenci).</span><span class="sxs-lookup"><span data-stu-id="36753-209">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="36753-210">Rozhraní API pro OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="36753-210">OptionsBuilder API</span></span>

<span data-ttu-id="36753-211"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> se používá ke konfiguraci `TOptions` instancí.</span><span class="sxs-lookup"><span data-stu-id="36753-211"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="36753-212">`OptionsBuilder` zjednodušuje vytváření pojmenovaných možností, protože se jedná pouze o jediný parametr počátečního volání `AddOptions<TOptions>(string optionsName)` namísto zobrazení ve všech následujících voláních.</span><span class="sxs-lookup"><span data-stu-id="36753-212">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="36753-213">Ověřování možností a přetížení `ConfigureOptions`, které přijímají závislosti služby, jsou k dispozici pouze prostřednictvím `OptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="36753-213">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="36753-214">Ke konfiguraci možností použijte DI Services.</span><span class="sxs-lookup"><span data-stu-id="36753-214">Use DI services to configure options</span></span>

<span data-ttu-id="36753-215">K dalším službám můžete přistupovat pomocí injektáže závislosti při konfiguraci možností dvěma způsoby:</span><span class="sxs-lookup"><span data-stu-id="36753-215">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="36753-216">Předejte delegáta konfigurace [na](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) [OptionsBuilder\<TOptions >](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="36753-216">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="36753-217">[OptionsBuilder\<TOptions >](xref:Microsoft.Extensions.Options.OptionsBuilder`1) poskytuje přetížení [Konfigurace](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) , která umožňují použít až pět služeb ke konfiguraci možností:</span><span class="sxs-lookup"><span data-stu-id="36753-217">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="36753-218">Vytvořte vlastní typ, který implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> nebo <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> a zaregistrujte typ jako službu.</span><span class="sxs-lookup"><span data-stu-id="36753-218">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="36753-219">Doporučujeme předat delegáta [konfigurace, protože](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)vytvoření služby je složitější.</span><span class="sxs-lookup"><span data-stu-id="36753-219">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="36753-220">Vytvoření vlastního typu je ekvivalentní s tím, k čemu architektura slouží při použití možnosti [Konfigurovat](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="36753-220">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="36753-221">Volání metody [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) obsahuje přechodný obecný <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, který má konstruktor, který přijímá obecné zadané typy služeb.</span><span class="sxs-lookup"><span data-stu-id="36753-221">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="36753-222">Ověřování možností</span><span class="sxs-lookup"><span data-stu-id="36753-222">Options validation</span></span>

<span data-ttu-id="36753-223">Ověřování možností umožňuje ověřit možnosti při konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="36753-223">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="36753-224">Volejte `Validate` s metodou ověřování, která vrátí `true`, pokud jsou možnosti platné a `false`, pokud nejsou platné:</span><span class="sxs-lookup"><span data-stu-id="36753-224">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

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

<span data-ttu-id="36753-225">Předchozí příklad nastaví instanci pojmenovaných možností na `optionalOptionsName`.</span><span class="sxs-lookup"><span data-stu-id="36753-225">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="36753-226">Instance výchozích možností je `Options.DefaultName`.</span><span class="sxs-lookup"><span data-stu-id="36753-226">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="36753-227">Ověřování se spustí při vytvoření instance Options.</span><span class="sxs-lookup"><span data-stu-id="36753-227">Validation runs when the options instance is created.</span></span> <span data-ttu-id="36753-228">Při prvním použití vaší instance možností je zaručeno předání ověření.</span><span class="sxs-lookup"><span data-stu-id="36753-228">Your options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="36753-229">Ověřování možností nechrání proti změnám možností po počáteční konfiguraci a ověření možností.</span><span class="sxs-lookup"><span data-stu-id="36753-229">Options validation doesn't guard against options modifications after the options are initially configured and validated.</span></span>

<span data-ttu-id="36753-230">Metoda `Validate` přijímá hodnotu `Func<TOptions, bool>`.</span><span class="sxs-lookup"><span data-stu-id="36753-230">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="36753-231">Chcete-li plně přizpůsobit ověřování, implementujte `IValidateOptions<TOptions>`, což umožňuje:</span><span class="sxs-lookup"><span data-stu-id="36753-231">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="36753-232">Ověřování více typů možností: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="36753-232">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="36753-233">Ověření, které závisí na jiném typu možnosti: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="36753-233">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="36753-234">`IValidateOptions` ověří:</span><span class="sxs-lookup"><span data-stu-id="36753-234">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="36753-235">Konkrétní instance pojmenované možnosti.</span><span class="sxs-lookup"><span data-stu-id="36753-235">A specific named options instance.</span></span>
* <span data-ttu-id="36753-236">Všechny možnosti, pokud `name` je `null`.</span><span class="sxs-lookup"><span data-stu-id="36753-236">All options when `name` is `null`.</span></span>

<span data-ttu-id="36753-237">Vrácení `ValidateOptionsResult` z vaší implementace rozhraní:</span><span class="sxs-lookup"><span data-stu-id="36753-237">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="36753-238">Ověřování na základě datových poznámek je k dispozici v balíčku [Microsoft. Extensions. Options. DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) pomocí volání metody <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> v `OptionsBuilder<TOptions>`.</span><span class="sxs-lookup"><span data-stu-id="36753-238">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="36753-239">na `Microsoft.Extensions.Options.DataAnnotations` se implicitně odkazuje v ASP.NET Corech aplikacích.</span><span class="sxs-lookup"><span data-stu-id="36753-239">`Microsoft.Extensions.Options.DataAnnotations` is implicitly referenced in ASP.NET Core apps.</span></span>

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

<span data-ttu-id="36753-240">Ověření Eager (při spuštění selže) je v budoucí verzi zváženo.</span><span class="sxs-lookup"><span data-stu-id="36753-240">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="36753-241">Možnosti po konfiguraci</span><span class="sxs-lookup"><span data-stu-id="36753-241">Options post-configuration</span></span>

<span data-ttu-id="36753-242">Nastavte po konfiguraci <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="36753-242">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="36753-243">Po dokončení konfigurace <xref:Microsoft.Extensions.Options.IConfigureOptions%601> se spustí po konfiguraci:</span><span class="sxs-lookup"><span data-stu-id="36753-243">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="36753-244"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> je k dispozici pro nastavení po konfiguraci s názvem:</span><span class="sxs-lookup"><span data-stu-id="36753-244"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="36753-245">Po dokončení konfigurace všech instancí konfigurace použijte <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*>:</span><span class="sxs-lookup"><span data-stu-id="36753-245">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="36753-246">Přístup k možnostem při spuštění</span><span class="sxs-lookup"><span data-stu-id="36753-246">Accessing options during startup</span></span>

<span data-ttu-id="36753-247"><xref:Microsoft.Extensions.Options.IOptions%601> a <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> lze použít v `Startup.Configure`, protože služby jsou vytvořeny před spuštěním metody `Configure`.</span><span class="sxs-lookup"><span data-stu-id="36753-247"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, 
    IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="36753-248">V `Startup.ConfigureServices`nepoužívejte <xref:Microsoft.Extensions.Options.IOptions%601> ani <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="36753-248">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="36753-249">Stav nekonzistentních možností může existovat v důsledku řazení registrací služby.</span><span class="sxs-lookup"><span data-stu-id="36753-249">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="36753-250">Vzor možností používá třídy, které reprezentují skupiny souvisejících nastavení.</span><span class="sxs-lookup"><span data-stu-id="36753-250">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="36753-251">Když jsou [nastavení konfigurace](xref:fundamentals/configuration/index) izolována scénářem do samostatných tříd, aplikace dodržuje dva důležité principy technického řízení:</span><span class="sxs-lookup"><span data-stu-id="36753-251">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="36753-252">[Princip oddělování rozhraní (ISP) nebo zapouzdření](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; scénáře (třídy), které závisí na nastavení konfigurace, závisí jenom na nastavení konfigurace, která používají.</span><span class="sxs-lookup"><span data-stu-id="36753-252">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="36753-253">[Oddělení](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) potíží, které se týkají &ndash; pro různé části aplikace, není závislé nebo vzájemně nezávisle na sobě.</span><span class="sxs-lookup"><span data-stu-id="36753-253">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="36753-254">Možnosti také poskytují mechanismus pro ověření konfiguračních dat.</span><span class="sxs-lookup"><span data-stu-id="36753-254">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="36753-255">Další informace najdete v části [ověřování možností](#options-validation) .</span><span class="sxs-lookup"><span data-stu-id="36753-255">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="36753-256">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="36753-256">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="36753-257">Požadavky</span><span class="sxs-lookup"><span data-stu-id="36753-257">Prerequisites</span></span>

<span data-ttu-id="36753-258">Odkaz na balíček [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) nebo přidejte odkaz na balíček do balíčku [Microsoft. Extensions. Options. ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) .</span><span class="sxs-lookup"><span data-stu-id="36753-258">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="36753-259">Možnosti rozhraní</span><span class="sxs-lookup"><span data-stu-id="36753-259">Options interfaces</span></span>

<span data-ttu-id="36753-260"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> slouží k načtení možností a správě oznámení možností pro `TOptions` instance.</span><span class="sxs-lookup"><span data-stu-id="36753-260"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="36753-261"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> podporuje následující scénáře:</span><span class="sxs-lookup"><span data-stu-id="36753-261"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="36753-262">Oznámení změny</span><span class="sxs-lookup"><span data-stu-id="36753-262">Change notifications</span></span>
* [<span data-ttu-id="36753-263">Pojmenované možnosti</span><span class="sxs-lookup"><span data-stu-id="36753-263">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="36753-264">Znovu Načítatelné konfigurace</span><span class="sxs-lookup"><span data-stu-id="36753-264">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="36753-265">Neplatné selektivní možnosti (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="36753-265">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="36753-266">Scénáře po [konfiguraci](#options-post-configuration) umožňují nastavit nebo změnit možnosti po <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ke konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="36753-266">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="36753-267"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> zodpovídá za vytváření nových instancí možností.</span><span class="sxs-lookup"><span data-stu-id="36753-267"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="36753-268">Má jednu metodu <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*>.</span><span class="sxs-lookup"><span data-stu-id="36753-268">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="36753-269">Výchozí implementace přebírá všechny registrované <xref:Microsoft.Extensions.Options.IConfigureOptions%601> a <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> a předá všechny konfigurace a potom po konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="36753-269">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="36753-270">Rozlišuje mezi <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> a <xref:Microsoft.Extensions.Options.IConfigureOptions%601> a volá pouze příslušné rozhraní.</span><span class="sxs-lookup"><span data-stu-id="36753-270">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="36753-271"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> používá <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> k ukládání instancí `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="36753-271"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="36753-272"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> zruší platnost instancí možností v monitorování tak, aby se hodnota přepočítala (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="36753-272">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="36753-273">Hodnoty lze ručně zavést pomocí <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span><span class="sxs-lookup"><span data-stu-id="36753-273">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="36753-274">Metoda <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> se používá v případě, že by se všechny pojmenované instance měly znovu vytvořit na vyžádání.</span><span class="sxs-lookup"><span data-stu-id="36753-274">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="36753-275"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> je užitečná ve scénářích, ve kterých by se měly přepočítat možnosti pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="36753-275"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="36753-276">Další informace najdete v části [opětovné načtení konfiguračních dat pomocí IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) .</span><span class="sxs-lookup"><span data-stu-id="36753-276">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="36753-277"><xref:Microsoft.Extensions.Options.IOptions%601> lze použít k podpoře možností.</span><span class="sxs-lookup"><span data-stu-id="36753-277"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="36753-278"><xref:Microsoft.Extensions.Options.IOptions%601> ale nepodporuje předchozí scénáře <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="36753-278">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="36753-279">Můžete nadále používat <xref:Microsoft.Extensions.Options.IOptions%601> ve stávajících rozhraních a knihovnách, které již používají rozhraní <xref:Microsoft.Extensions.Options.IOptions%601> a nevyžadují scénáře poskytované <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="36753-279">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="36753-280">Konfigurace obecných možností</span><span class="sxs-lookup"><span data-stu-id="36753-280">General options configuration</span></span>

<span data-ttu-id="36753-281">Konfigurace obecných možností se v ukázkové aplikaci ukazuje jako příklad &num;1.</span><span class="sxs-lookup"><span data-stu-id="36753-281">General options configuration is demonstrated as Example &num;1 in the sample app.</span></span>

<span data-ttu-id="36753-282">Třída Options musí být neabstraktní s veřejným konstruktorem bez parametrů.</span><span class="sxs-lookup"><span data-stu-id="36753-282">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="36753-283">Následující třída `MyOptions` má dvě vlastnosti `Option1` a `Option2`.</span><span class="sxs-lookup"><span data-stu-id="36753-283">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="36753-284">Nastavení výchozích hodnot je volitelné, ale konstruktor třídy v následujícím příkladu nastaví výchozí hodnotu `Option1`.</span><span class="sxs-lookup"><span data-stu-id="36753-284">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="36753-285">`Option2` má výchozí hodnotu nastavenou tak, že vlastnost inicializuje přímo (*modely/MyOptions. cs*):</span><span class="sxs-lookup"><span data-stu-id="36753-285">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="36753-286">Třída `MyOptions` je přidána do kontejneru služby s <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> a je svázána s konfigurací:</span><span class="sxs-lookup"><span data-stu-id="36753-286">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="36753-287">Následující model stránky používá [vkládání závislostí konstruktoru](xref:mvc/controllers/dependency-injection) s <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> pro přístup k nastavení (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="36753-287">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="36753-288">Soubor *appSettings. JSON* v ukázce určuje hodnoty pro `option1` a `option2`:</span><span class="sxs-lookup"><span data-stu-id="36753-288">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="36753-289">Když je aplikace spuštěna, metoda `OnGet` modelu stránky vrátí řetězec zobrazující hodnoty třídy možností:</span><span class="sxs-lookup"><span data-stu-id="36753-289">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="36753-290">Když pomocí vlastního <xref:System.Configuration.ConfigurationBuilder> načtěte konfiguraci možností ze souboru nastavení, zkontrolujte, jestli je správně nastavená základní cesta:</span><span class="sxs-lookup"><span data-stu-id="36753-290">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="36753-291">Při načítání konfigurace možností ze souboru nastavení prostřednictvím <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> není nutné explicitně nastavit základní cestu.</span><span class="sxs-lookup"><span data-stu-id="36753-291">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="36753-292">Konfigurace jednoduchých možností pomocí delegáta</span><span class="sxs-lookup"><span data-stu-id="36753-292">Configure simple options with a delegate</span></span>

<span data-ttu-id="36753-293">Konfigurace jednoduchých možností s delegátem je znázorněna jako příklad &num;2 v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="36753-293">Configuring simple options with a delegate is demonstrated as Example &num;2 in the sample app.</span></span>

<span data-ttu-id="36753-294">K nastavení hodnot možností použijte delegáta.</span><span class="sxs-lookup"><span data-stu-id="36753-294">Use a delegate to set options values.</span></span> <span data-ttu-id="36753-295">Ukázková aplikace používá třídu `MyOptionsWithDelegateConfig` (*modely/MyOptionsWithDelegateConfig. cs*):</span><span class="sxs-lookup"><span data-stu-id="36753-295">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="36753-296">V následujícím kódu se do kontejneru služby přidá druhá <xref:Microsoft.Extensions.Options.IConfigureOptions%601> služba.</span><span class="sxs-lookup"><span data-stu-id="36753-296">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="36753-297">Pomocí delegáta konfiguruje vazbu s `MyOptionsWithDelegateConfig`:</span><span class="sxs-lookup"><span data-stu-id="36753-297">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="36753-298">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="36753-298">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="36753-299">Můžete přidat více poskytovatelů konfigurace.</span><span class="sxs-lookup"><span data-stu-id="36753-299">You can add multiple configuration providers.</span></span> <span data-ttu-id="36753-300">Poskytovatelé konfigurace jsou k dispozici z balíčků NuGet a jsou aplikováni v pořadí, v jakém jsou registrováni.</span><span class="sxs-lookup"><span data-stu-id="36753-300">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="36753-301">Další informace najdete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="36753-301">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="36753-302">Každé volání <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> přidá <xref:Microsoft.Extensions.Options.IConfigureOptions%601> službu do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="36753-302">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="36753-303">V předchozím příkladu jsou hodnoty `Option1` a `Option2` zadány v souboru *appSettings. JSON*, ale hodnoty `Option1` a `Option2` jsou přepsány nakonfigurovaným delegátem.</span><span class="sxs-lookup"><span data-stu-id="36753-303">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="36753-304">Je-li povolena více než jedna služba konfigurace, je v posledním zdroji konfigurace zadaný *Server WINS* a nastavena hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="36753-304">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="36753-305">Když je aplikace spuštěna, metoda `OnGet` modelu stránky vrátí řetězec zobrazující hodnoty třídy možností:</span><span class="sxs-lookup"><span data-stu-id="36753-305">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="36753-306">Konfigurace podNastavení</span><span class="sxs-lookup"><span data-stu-id="36753-306">Suboptions configuration</span></span>

<span data-ttu-id="36753-307">Konfigurace dílčích možností se v ukázkové aplikaci ukazuje jako příklad &num;3.</span><span class="sxs-lookup"><span data-stu-id="36753-307">Suboptions configuration is demonstrated as Example &num;3 in the sample app.</span></span>

<span data-ttu-id="36753-308">Aplikace by měly vytvořit třídy možností, které se týkají konkrétních skupin scénářů (tříd) v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="36753-308">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="36753-309">Části aplikace, které vyžadují konfigurační hodnoty, by měly mít přístup jenom k hodnotám konfigurace, které používají.</span><span class="sxs-lookup"><span data-stu-id="36753-309">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="36753-310">Při vytváření vazby mezi možnostmi konfigurace je každá vlastnost v typu možnosti vázána na konfigurační klíč formuláře `property[:sub-property:]`.</span><span class="sxs-lookup"><span data-stu-id="36753-310">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="36753-311">Například vlastnost `MyOptions.Option1` je vázána na klíč `Option1`, který je načten z vlastnosti `option1` v souboru *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="36753-311">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="36753-312">V následujícím kódu je do kontejneru služby přidána třetí <xref:Microsoft.Extensions.Options.IConfigureOptions%601> služba.</span><span class="sxs-lookup"><span data-stu-id="36753-312">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="36753-313">Vytvoří vazby `MySubOptions` na oddíl `subsection` souboru *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="36753-313">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="36753-314">Metoda `GetSection` vyžaduje obor názvů <xref:Microsoft.Extensions.Configuration?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="36753-314">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="36753-315">Soubor *appSettings. JSON* ve vzorci definuje člena `subsection` s klíči pro `suboption1` a `suboption2`:</span><span class="sxs-lookup"><span data-stu-id="36753-315">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="36753-316">Třída `MySubOptions` definuje vlastnosti, `SubOption1` a `SubOption2`, aby obsahovaly hodnoty možností (*modely/MySubOptions. cs*):</span><span class="sxs-lookup"><span data-stu-id="36753-316">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="36753-317">Metoda `OnGet` modelu stránky vrací řetězec s hodnotami možností (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="36753-317">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="36753-318">Při spuštění aplikace vrátí metoda `OnGet` řetězec zobrazující hodnoty třídy dílčích možností:</span><span class="sxs-lookup"><span data-stu-id="36753-318">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="36753-319">Možnosti poskytované modelem zobrazení nebo vkládáním přímých zobrazení</span><span class="sxs-lookup"><span data-stu-id="36753-319">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="36753-320">Možnosti poskytované modelem zobrazení nebo vkládáním přímých zobrazení jsou v ukázkové aplikaci znázorněné jako příklad &num;4.</span><span class="sxs-lookup"><span data-stu-id="36753-320">Options provided by a view model or with direct view injection is demonstrated as Example &num;4 in the sample app.</span></span>

<span data-ttu-id="36753-321">Možnosti lze zadat v modelu zobrazení nebo vložením <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> přímo do zobrazení (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="36753-321">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="36753-322">Ukázková aplikace ukazuje, jak vložit `IOptionsMonitor<MyOptions>` s direktivou `@inject`:</span><span class="sxs-lookup"><span data-stu-id="36753-322">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="36753-323">Po spuštění aplikace se hodnoty možností zobrazí na vykreslené stránce:</span><span class="sxs-lookup"><span data-stu-id="36753-323">When the app is run, the options values are shown in the rendered page:</span></span>

![Hodnoty možností možnost1: value1_from_json a Možnost2:-1 jsou načteny z modelu a vložením do zobrazení.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="36753-325">Znovu načíst konfigurační data pomocí IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="36753-325">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="36753-326">Opětovné načtení konfiguračních dat pomocí <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> je v ukázkové aplikaci znázorněno v příkladu &num;5.</span><span class="sxs-lookup"><span data-stu-id="36753-326">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example &num;5 in the sample app.</span></span>

<span data-ttu-id="36753-327"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> podporuje opětovné načítání možností s minimální režií zpracování.</span><span class="sxs-lookup"><span data-stu-id="36753-327"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="36753-328">Možnosti jsou vypočítány jednou za požadavek při otevření a ukládání do mezipaměti po dobu života žádosti.</span><span class="sxs-lookup"><span data-stu-id="36753-328">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="36753-329">Následující příklad ukazuje, jak se vytvoří nový <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> po změnách *appSettings. JSON* (*pages/index. cshtml. cs*).</span><span class="sxs-lookup"><span data-stu-id="36753-329">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="36753-330">Více požadavků na server vrací konstantní hodnoty, které poskytuje soubor *appSettings. JSON* , dokud nedojde ke změně souboru a opětovnému načtení konfigurace.</span><span class="sxs-lookup"><span data-stu-id="36753-330">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="36753-331">Následující obrázek ukazuje počáteční hodnoty `option1` a `option2` načtené ze souboru *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="36753-331">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="36753-332">Změňte hodnoty v souboru *appSettings. JSON* na `value1_from_json UPDATED` a `200`.</span><span class="sxs-lookup"><span data-stu-id="36753-332">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="36753-333">Uložte soubor *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="36753-333">Save the *appsettings.json* file.</span></span> <span data-ttu-id="36753-334">Aktualizujte prohlížeč, aby se zobrazily hodnoty možností, které jsou aktualizované:</span><span class="sxs-lookup"><span data-stu-id="36753-334">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="36753-335">Podpora pojmenovaných možností s IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="36753-335">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="36753-336">Podpora pojmenovaných možností s <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> je v ukázkové aplikaci znázorněna jako příklad &num;6.</span><span class="sxs-lookup"><span data-stu-id="36753-336">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example &num;6 in the sample app.</span></span>

<span data-ttu-id="36753-337">Podpora *pojmenovaných možností* umožňuje aplikaci rozlišovat konfigurace pojmenovaných možností.</span><span class="sxs-lookup"><span data-stu-id="36753-337">*Named options* support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="36753-338">V ukázkové aplikaci jsou pojmenované možnosti deklarovány pomocí [OptionsServiceCollectionExtensions. Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), který volá [ConfigureNamedOptions >\<TOptions. Konfigurace](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) metody rozšíření:</span><span class="sxs-lookup"><span data-stu-id="36753-338">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="36753-339">Ukázková aplikace přistupuje k pojmenovaným možnostem <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="36753-339">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="36753-340">Když spustíte ukázkovou aplikaci, vrátí se pojmenované možnosti:</span><span class="sxs-lookup"><span data-stu-id="36753-340">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="36753-341">hodnoty `named_options_1` se poskytují z konfigurace, které se načítají ze souboru *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="36753-341">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="36753-342">hodnoty `named_options_2` poskytují:</span><span class="sxs-lookup"><span data-stu-id="36753-342">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="36753-343">`named_options_2` delegát v `ConfigureServices` pro `Option1`.</span><span class="sxs-lookup"><span data-stu-id="36753-343">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="36753-344">Výchozí hodnota pro `Option2` poskytovaná třídou `MyOptions`.</span><span class="sxs-lookup"><span data-stu-id="36753-344">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="36753-345">Konfigurace všech možností pomocí metody ConfigureAll</span><span class="sxs-lookup"><span data-stu-id="36753-345">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="36753-346">Nakonfigurujte všechny instance možností pomocí metody <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*>.</span><span class="sxs-lookup"><span data-stu-id="36753-346">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="36753-347">Následující kód konfiguruje `Option1` pro všechny instance konfigurace se společnou hodnotou.</span><span class="sxs-lookup"><span data-stu-id="36753-347">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="36753-348">Přidejte následující kód ručně do metody `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="36753-348">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="36753-349">Spuštění ukázkové aplikace po přidání kódu vytvoří následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="36753-349">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="36753-350">Všechny možnosti jsou pojmenované instance.</span><span class="sxs-lookup"><span data-stu-id="36753-350">All options are named instances.</span></span> <span data-ttu-id="36753-351">Existující instance <xref:Microsoft.Extensions.Options.IConfigureOptions%601> jsou považovány za cílení na instanci `Options.DefaultName`, která je `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="36753-351">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="36753-352"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> také implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="36753-352"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="36753-353">Výchozí implementace <xref:Microsoft.Extensions.Options.IOptionsFactory%601> má logiku odpovídajícím způsobem použít.</span><span class="sxs-lookup"><span data-stu-id="36753-353">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="36753-354">Pojmenovaná možnost `null` se používá k cílení na všechny pojmenované instance místo konkrétní pojmenované instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> a <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> používají tuto konvenci).</span><span class="sxs-lookup"><span data-stu-id="36753-354">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="36753-355">Rozhraní API pro OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="36753-355">OptionsBuilder API</span></span>

<span data-ttu-id="36753-356"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> se používá ke konfiguraci `TOptions` instancí.</span><span class="sxs-lookup"><span data-stu-id="36753-356"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="36753-357">`OptionsBuilder` zjednodušuje vytváření pojmenovaných možností, protože se jedná pouze o jediný parametr počátečního volání `AddOptions<TOptions>(string optionsName)` namísto zobrazení ve všech následujících voláních.</span><span class="sxs-lookup"><span data-stu-id="36753-357">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="36753-358">Ověřování možností a přetížení `ConfigureOptions`, které přijímají závislosti služby, jsou k dispozici pouze prostřednictvím `OptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="36753-358">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="36753-359">Ke konfiguraci možností použijte DI Services.</span><span class="sxs-lookup"><span data-stu-id="36753-359">Use DI services to configure options</span></span>

<span data-ttu-id="36753-360">K dalším službám můžete přistupovat pomocí injektáže závislosti při konfiguraci možností dvěma způsoby:</span><span class="sxs-lookup"><span data-stu-id="36753-360">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="36753-361">Předejte delegáta konfigurace [na](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) [OptionsBuilder\<TOptions >](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="36753-361">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="36753-362">[OptionsBuilder\<TOptions >](xref:Microsoft.Extensions.Options.OptionsBuilder`1) poskytuje přetížení [Konfigurace](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) , která umožňují použít až pět služeb ke konfiguraci možností:</span><span class="sxs-lookup"><span data-stu-id="36753-362">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="36753-363">Vytvořte vlastní typ, který implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> nebo <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> a zaregistrujte typ jako službu.</span><span class="sxs-lookup"><span data-stu-id="36753-363">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="36753-364">Doporučujeme předat delegáta [konfigurace, protože](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)vytvoření služby je složitější.</span><span class="sxs-lookup"><span data-stu-id="36753-364">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="36753-365">Vytvoření vlastního typu je ekvivalentní s tím, k čemu architektura slouží při použití možnosti [Konfigurovat](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="36753-365">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="36753-366">Volání metody [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) obsahuje přechodný obecný <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, který má konstruktor, který přijímá obecné zadané typy služeb.</span><span class="sxs-lookup"><span data-stu-id="36753-366">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="36753-367">Ověřování možností</span><span class="sxs-lookup"><span data-stu-id="36753-367">Options validation</span></span>

<span data-ttu-id="36753-368">Ověřování možností umožňuje ověřit možnosti při konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="36753-368">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="36753-369">Volejte `Validate` s metodou ověřování, která vrátí `true`, pokud jsou možnosti platné a `false`, pokud nejsou platné:</span><span class="sxs-lookup"><span data-stu-id="36753-369">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

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

<span data-ttu-id="36753-370">Předchozí příklad nastaví instanci pojmenovaných možností na `optionalOptionsName`.</span><span class="sxs-lookup"><span data-stu-id="36753-370">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="36753-371">Instance výchozích možností je `Options.DefaultName`.</span><span class="sxs-lookup"><span data-stu-id="36753-371">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="36753-372">Ověřování se spustí při vytvoření instance Options.</span><span class="sxs-lookup"><span data-stu-id="36753-372">Validation runs when the options instance is created.</span></span> <span data-ttu-id="36753-373">Při prvním použití vaší instance možností je zaručeno předání ověření.</span><span class="sxs-lookup"><span data-stu-id="36753-373">Your options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="36753-374">Ověřování možností nechrání proti změnám možností po počáteční konfiguraci a ověření možností.</span><span class="sxs-lookup"><span data-stu-id="36753-374">Options validation doesn't guard against options modifications after the options are initially configured and validated.</span></span>

<span data-ttu-id="36753-375">Metoda `Validate` přijímá hodnotu `Func<TOptions, bool>`.</span><span class="sxs-lookup"><span data-stu-id="36753-375">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="36753-376">Chcete-li plně přizpůsobit ověřování, implementujte `IValidateOptions<TOptions>`, což umožňuje:</span><span class="sxs-lookup"><span data-stu-id="36753-376">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="36753-377">Ověřování více typů možností: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="36753-377">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="36753-378">Ověření, které závisí na jiném typu možnosti: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="36753-378">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="36753-379">`IValidateOptions` ověří:</span><span class="sxs-lookup"><span data-stu-id="36753-379">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="36753-380">Konkrétní instance pojmenované možnosti.</span><span class="sxs-lookup"><span data-stu-id="36753-380">A specific named options instance.</span></span>
* <span data-ttu-id="36753-381">Všechny možnosti, pokud `name` je `null`.</span><span class="sxs-lookup"><span data-stu-id="36753-381">All options when `name` is `null`.</span></span>

<span data-ttu-id="36753-382">Vrácení `ValidateOptionsResult` z vaší implementace rozhraní:</span><span class="sxs-lookup"><span data-stu-id="36753-382">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="36753-383">Ověřování na základě datových poznámek je k dispozici v balíčku [Microsoft. Extensions. Options. DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) pomocí volání metody <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> v `OptionsBuilder<TOptions>`.</span><span class="sxs-lookup"><span data-stu-id="36753-383">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="36753-384">`Microsoft.Extensions.Options.DataAnnotations` je součástí [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="36753-384">`Microsoft.Extensions.Options.DataAnnotations` is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

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

<span data-ttu-id="36753-385">Ověření Eager (při spuštění selže) je v budoucí verzi zváženo.</span><span class="sxs-lookup"><span data-stu-id="36753-385">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="36753-386">Možnosti po konfiguraci</span><span class="sxs-lookup"><span data-stu-id="36753-386">Options post-configuration</span></span>

<span data-ttu-id="36753-387">Nastavte po konfiguraci <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="36753-387">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="36753-388">Po dokončení konfigurace <xref:Microsoft.Extensions.Options.IConfigureOptions%601> se spustí po konfiguraci:</span><span class="sxs-lookup"><span data-stu-id="36753-388">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="36753-389"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> je k dispozici pro nastavení po konfiguraci s názvem:</span><span class="sxs-lookup"><span data-stu-id="36753-389"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="36753-390">Po dokončení konfigurace všech instancí konfigurace použijte <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*>:</span><span class="sxs-lookup"><span data-stu-id="36753-390">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="36753-391">Přístup k možnostem při spuštění</span><span class="sxs-lookup"><span data-stu-id="36753-391">Accessing options during startup</span></span>

<span data-ttu-id="36753-392"><xref:Microsoft.Extensions.Options.IOptions%601> a <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> lze použít v `Startup.Configure`, protože služby jsou vytvořeny před spuštěním metody `Configure`.</span><span class="sxs-lookup"><span data-stu-id="36753-392"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="36753-393">V `Startup.ConfigureServices`nepoužívejte <xref:Microsoft.Extensions.Options.IOptions%601> ani <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="36753-393">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="36753-394">Stav nekonzistentních možností může existovat v důsledku řazení registrací služby.</span><span class="sxs-lookup"><span data-stu-id="36753-394">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="36753-395">Vzor možností používá třídy, které reprezentují skupiny souvisejících nastavení.</span><span class="sxs-lookup"><span data-stu-id="36753-395">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="36753-396">Když jsou [nastavení konfigurace](xref:fundamentals/configuration/index) izolována scénářem do samostatných tříd, aplikace dodržuje dva důležité principy technického řízení:</span><span class="sxs-lookup"><span data-stu-id="36753-396">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="36753-397">[Princip oddělování rozhraní (ISP) nebo zapouzdření](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; scénáře (třídy), které závisí na nastavení konfigurace, závisí jenom na nastavení konfigurace, která používají.</span><span class="sxs-lookup"><span data-stu-id="36753-397">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="36753-398">[Oddělení](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) potíží, které se týkají &ndash; pro různé části aplikace, není závislé nebo vzájemně nezávisle na sobě.</span><span class="sxs-lookup"><span data-stu-id="36753-398">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="36753-399">Možnosti také poskytují mechanismus pro ověření konfiguračních dat.</span><span class="sxs-lookup"><span data-stu-id="36753-399">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="36753-400">Další informace najdete v části [ověřování možností](#options-validation) .</span><span class="sxs-lookup"><span data-stu-id="36753-400">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="36753-401">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="36753-401">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="36753-402">Požadavky</span><span class="sxs-lookup"><span data-stu-id="36753-402">Prerequisites</span></span>

<span data-ttu-id="36753-403">Odkaz na balíček [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) nebo přidejte odkaz na balíček do balíčku [Microsoft. Extensions. Options. ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) .</span><span class="sxs-lookup"><span data-stu-id="36753-403">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="36753-404">Možnosti rozhraní</span><span class="sxs-lookup"><span data-stu-id="36753-404">Options interfaces</span></span>

<span data-ttu-id="36753-405"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> slouží k načtení možností a správě oznámení možností pro `TOptions` instance.</span><span class="sxs-lookup"><span data-stu-id="36753-405"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="36753-406"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> podporuje následující scénáře:</span><span class="sxs-lookup"><span data-stu-id="36753-406"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="36753-407">Oznámení změny</span><span class="sxs-lookup"><span data-stu-id="36753-407">Change notifications</span></span>
* [<span data-ttu-id="36753-408">Pojmenované možnosti</span><span class="sxs-lookup"><span data-stu-id="36753-408">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="36753-409">Znovu Načítatelné konfigurace</span><span class="sxs-lookup"><span data-stu-id="36753-409">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="36753-410">Neplatné selektivní možnosti (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="36753-410">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="36753-411">Scénáře po [konfiguraci](#options-post-configuration) umožňují nastavit nebo změnit možnosti po <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ke konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="36753-411">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="36753-412"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> zodpovídá za vytváření nových instancí možností.</span><span class="sxs-lookup"><span data-stu-id="36753-412"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="36753-413">Má jednu metodu <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*>.</span><span class="sxs-lookup"><span data-stu-id="36753-413">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="36753-414">Výchozí implementace přebírá všechny registrované <xref:Microsoft.Extensions.Options.IConfigureOptions%601> a <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> a předá všechny konfigurace a potom po konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="36753-414">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="36753-415">Rozlišuje mezi <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> a <xref:Microsoft.Extensions.Options.IConfigureOptions%601> a volá pouze příslušné rozhraní.</span><span class="sxs-lookup"><span data-stu-id="36753-415">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="36753-416"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> používá <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> k ukládání instancí `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="36753-416"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="36753-417"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> zruší platnost instancí možností v monitorování tak, aby se hodnota přepočítala (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="36753-417">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="36753-418">Hodnoty lze ručně zavést pomocí <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span><span class="sxs-lookup"><span data-stu-id="36753-418">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="36753-419">Metoda <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> se používá v případě, že by se všechny pojmenované instance měly znovu vytvořit na vyžádání.</span><span class="sxs-lookup"><span data-stu-id="36753-419">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="36753-420"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> je užitečná ve scénářích, ve kterých by se měly přepočítat možnosti pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="36753-420"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="36753-421">Další informace najdete v části [opětovné načtení konfiguračních dat pomocí IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) .</span><span class="sxs-lookup"><span data-stu-id="36753-421">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="36753-422"><xref:Microsoft.Extensions.Options.IOptions%601> lze použít k podpoře možností.</span><span class="sxs-lookup"><span data-stu-id="36753-422"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="36753-423"><xref:Microsoft.Extensions.Options.IOptions%601> ale nepodporuje předchozí scénáře <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="36753-423">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="36753-424">Můžete nadále používat <xref:Microsoft.Extensions.Options.IOptions%601> ve stávajících rozhraních a knihovnách, které již používají rozhraní <xref:Microsoft.Extensions.Options.IOptions%601> a nevyžadují scénáře poskytované <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="36753-424">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="36753-425">Konfigurace obecných možností</span><span class="sxs-lookup"><span data-stu-id="36753-425">General options configuration</span></span>

<span data-ttu-id="36753-426">Konfigurace obecných možností se v ukázkové aplikaci ukazuje jako příklad &num;1.</span><span class="sxs-lookup"><span data-stu-id="36753-426">General options configuration is demonstrated as Example &num;1 in the sample app.</span></span>

<span data-ttu-id="36753-427">Třída Options musí být neabstraktní s veřejným konstruktorem bez parametrů.</span><span class="sxs-lookup"><span data-stu-id="36753-427">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="36753-428">Následující třída `MyOptions` má dvě vlastnosti `Option1` a `Option2`.</span><span class="sxs-lookup"><span data-stu-id="36753-428">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="36753-429">Nastavení výchozích hodnot je volitelné, ale konstruktor třídy v následujícím příkladu nastaví výchozí hodnotu `Option1`.</span><span class="sxs-lookup"><span data-stu-id="36753-429">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="36753-430">`Option2` má výchozí hodnotu nastavenou tak, že vlastnost inicializuje přímo (*modely/MyOptions. cs*):</span><span class="sxs-lookup"><span data-stu-id="36753-430">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="36753-431">Třída `MyOptions` je přidána do kontejneru služby s <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> a je svázána s konfigurací:</span><span class="sxs-lookup"><span data-stu-id="36753-431">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="36753-432">Následující model stránky používá [vkládání závislostí konstruktoru](xref:mvc/controllers/dependency-injection) s <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> pro přístup k nastavení (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="36753-432">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="36753-433">Soubor *appSettings. JSON* v ukázce určuje hodnoty pro `option1` a `option2`:</span><span class="sxs-lookup"><span data-stu-id="36753-433">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="36753-434">Když je aplikace spuštěna, metoda `OnGet` modelu stránky vrátí řetězec zobrazující hodnoty třídy možností:</span><span class="sxs-lookup"><span data-stu-id="36753-434">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="36753-435">Když pomocí vlastního <xref:System.Configuration.ConfigurationBuilder> načtěte konfiguraci možností ze souboru nastavení, zkontrolujte, jestli je správně nastavená základní cesta:</span><span class="sxs-lookup"><span data-stu-id="36753-435">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="36753-436">Při načítání konfigurace možností ze souboru nastavení prostřednictvím <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> není nutné explicitně nastavit základní cestu.</span><span class="sxs-lookup"><span data-stu-id="36753-436">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="36753-437">Konfigurace jednoduchých možností pomocí delegáta</span><span class="sxs-lookup"><span data-stu-id="36753-437">Configure simple options with a delegate</span></span>

<span data-ttu-id="36753-438">Konfigurace jednoduchých možností s delegátem je znázorněna jako příklad &num;2 v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="36753-438">Configuring simple options with a delegate is demonstrated as Example &num;2 in the sample app.</span></span>

<span data-ttu-id="36753-439">K nastavení hodnot možností použijte delegáta.</span><span class="sxs-lookup"><span data-stu-id="36753-439">Use a delegate to set options values.</span></span> <span data-ttu-id="36753-440">Ukázková aplikace používá třídu `MyOptionsWithDelegateConfig` (*modely/MyOptionsWithDelegateConfig. cs*):</span><span class="sxs-lookup"><span data-stu-id="36753-440">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="36753-441">V následujícím kódu se do kontejneru služby přidá druhá <xref:Microsoft.Extensions.Options.IConfigureOptions%601> služba.</span><span class="sxs-lookup"><span data-stu-id="36753-441">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="36753-442">Pomocí delegáta konfiguruje vazbu s `MyOptionsWithDelegateConfig`:</span><span class="sxs-lookup"><span data-stu-id="36753-442">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="36753-443">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="36753-443">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="36753-444">Můžete přidat více poskytovatelů konfigurace.</span><span class="sxs-lookup"><span data-stu-id="36753-444">You can add multiple configuration providers.</span></span> <span data-ttu-id="36753-445">Poskytovatelé konfigurace jsou k dispozici z balíčků NuGet a jsou aplikováni v pořadí, v jakém jsou registrováni.</span><span class="sxs-lookup"><span data-stu-id="36753-445">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="36753-446">Další informace najdete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="36753-446">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="36753-447">Každé volání <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> přidá <xref:Microsoft.Extensions.Options.IConfigureOptions%601> službu do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="36753-447">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="36753-448">V předchozím příkladu jsou hodnoty `Option1` a `Option2` zadány v souboru *appSettings. JSON*, ale hodnoty `Option1` a `Option2` jsou přepsány nakonfigurovaným delegátem.</span><span class="sxs-lookup"><span data-stu-id="36753-448">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="36753-449">Je-li povolena více než jedna služba konfigurace, je v posledním zdroji konfigurace zadaný *Server WINS* a nastavena hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="36753-449">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="36753-450">Když je aplikace spuštěna, metoda `OnGet` modelu stránky vrátí řetězec zobrazující hodnoty třídy možností:</span><span class="sxs-lookup"><span data-stu-id="36753-450">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="36753-451">Konfigurace podNastavení</span><span class="sxs-lookup"><span data-stu-id="36753-451">Suboptions configuration</span></span>

<span data-ttu-id="36753-452">Konfigurace dílčích možností se v ukázkové aplikaci ukazuje jako příklad &num;3.</span><span class="sxs-lookup"><span data-stu-id="36753-452">Suboptions configuration is demonstrated as Example &num;3 in the sample app.</span></span>

<span data-ttu-id="36753-453">Aplikace by měly vytvořit třídy možností, které se týkají konkrétních skupin scénářů (tříd) v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="36753-453">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="36753-454">Části aplikace, které vyžadují konfigurační hodnoty, by měly mít přístup jenom k hodnotám konfigurace, které používají.</span><span class="sxs-lookup"><span data-stu-id="36753-454">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="36753-455">Při vytváření vazby mezi možnostmi konfigurace je každá vlastnost v typu možnosti vázána na konfigurační klíč formuláře `property[:sub-property:]`.</span><span class="sxs-lookup"><span data-stu-id="36753-455">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="36753-456">Například vlastnost `MyOptions.Option1` je vázána na klíč `Option1`, který je načten z vlastnosti `option1` v souboru *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="36753-456">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="36753-457">V následujícím kódu je do kontejneru služby přidána třetí <xref:Microsoft.Extensions.Options.IConfigureOptions%601> služba.</span><span class="sxs-lookup"><span data-stu-id="36753-457">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="36753-458">Vytvoří vazby `MySubOptions` na oddíl `subsection` souboru *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="36753-458">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="36753-459">Metoda `GetSection` vyžaduje obor názvů <xref:Microsoft.Extensions.Configuration?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="36753-459">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="36753-460">Soubor *appSettings. JSON* ve vzorci definuje člena `subsection` s klíči pro `suboption1` a `suboption2`:</span><span class="sxs-lookup"><span data-stu-id="36753-460">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="36753-461">Třída `MySubOptions` definuje vlastnosti, `SubOption1` a `SubOption2`, aby obsahovaly hodnoty možností (*modely/MySubOptions. cs*):</span><span class="sxs-lookup"><span data-stu-id="36753-461">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="36753-462">Metoda `OnGet` modelu stránky vrací řetězec s hodnotami možností (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="36753-462">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="36753-463">Při spuštění aplikace vrátí metoda `OnGet` řetězec zobrazující hodnoty třídy dílčích možností:</span><span class="sxs-lookup"><span data-stu-id="36753-463">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="36753-464">Možnosti poskytované modelem zobrazení nebo vkládáním přímých zobrazení</span><span class="sxs-lookup"><span data-stu-id="36753-464">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="36753-465">Možnosti poskytované modelem zobrazení nebo vkládáním přímých zobrazení jsou v ukázkové aplikaci znázorněné jako příklad &num;4.</span><span class="sxs-lookup"><span data-stu-id="36753-465">Options provided by a view model or with direct view injection is demonstrated as Example &num;4 in the sample app.</span></span>

<span data-ttu-id="36753-466">Možnosti lze zadat v modelu zobrazení nebo vložením <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> přímo do zobrazení (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="36753-466">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="36753-467">Ukázková aplikace ukazuje, jak vložit `IOptionsMonitor<MyOptions>` s direktivou `@inject`:</span><span class="sxs-lookup"><span data-stu-id="36753-467">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="36753-468">Po spuštění aplikace se hodnoty možností zobrazí na vykreslené stránce:</span><span class="sxs-lookup"><span data-stu-id="36753-468">When the app is run, the options values are shown in the rendered page:</span></span>

![Hodnoty možností možnost1: value1_from_json a Možnost2:-1 jsou načteny z modelu a vložením do zobrazení.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="36753-470">Znovu načíst konfigurační data pomocí IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="36753-470">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="36753-471">Opětovné načtení konfiguračních dat pomocí <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> je v ukázkové aplikaci znázorněno v příkladu &num;5.</span><span class="sxs-lookup"><span data-stu-id="36753-471">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example &num;5 in the sample app.</span></span>

<span data-ttu-id="36753-472"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> podporuje opětovné načítání možností s minimální režií zpracování.</span><span class="sxs-lookup"><span data-stu-id="36753-472"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="36753-473">Možnosti jsou vypočítány jednou za požadavek při otevření a ukládání do mezipaměti po dobu života žádosti.</span><span class="sxs-lookup"><span data-stu-id="36753-473">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="36753-474">Následující příklad ukazuje, jak se vytvoří nový <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> po změnách *appSettings. JSON* (*pages/index. cshtml. cs*).</span><span class="sxs-lookup"><span data-stu-id="36753-474">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="36753-475">Více požadavků na server vrací konstantní hodnoty, které poskytuje soubor *appSettings. JSON* , dokud nedojde ke změně souboru a opětovnému načtení konfigurace.</span><span class="sxs-lookup"><span data-stu-id="36753-475">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="36753-476">Následující obrázek ukazuje počáteční hodnoty `option1` a `option2` načtené ze souboru *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="36753-476">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="36753-477">Změňte hodnoty v souboru *appSettings. JSON* na `value1_from_json UPDATED` a `200`.</span><span class="sxs-lookup"><span data-stu-id="36753-477">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="36753-478">Uložte soubor *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="36753-478">Save the *appsettings.json* file.</span></span> <span data-ttu-id="36753-479">Aktualizujte prohlížeč, aby se zobrazily hodnoty možností, které jsou aktualizované:</span><span class="sxs-lookup"><span data-stu-id="36753-479">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="36753-480">Podpora pojmenovaných možností s IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="36753-480">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="36753-481">Podpora pojmenovaných možností s <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> je v ukázkové aplikaci znázorněna jako příklad &num;6.</span><span class="sxs-lookup"><span data-stu-id="36753-481">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example &num;6 in the sample app.</span></span>

<span data-ttu-id="36753-482">Podpora *pojmenovaných možností* umožňuje aplikaci rozlišovat konfigurace pojmenovaných možností.</span><span class="sxs-lookup"><span data-stu-id="36753-482">*Named options* support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="36753-483">V ukázkové aplikaci jsou pojmenované možnosti deklarovány pomocí [OptionsServiceCollectionExtensions. Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), který volá [ConfigureNamedOptions >\<TOptions. Konfigurace](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) metody rozšíření:</span><span class="sxs-lookup"><span data-stu-id="36753-483">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="36753-484">Ukázková aplikace přistupuje k pojmenovaným možnostem <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="36753-484">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="36753-485">Když spustíte ukázkovou aplikaci, vrátí se pojmenované možnosti:</span><span class="sxs-lookup"><span data-stu-id="36753-485">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="36753-486">hodnoty `named_options_1` se poskytují z konfigurace, které se načítají ze souboru *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="36753-486">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="36753-487">hodnoty `named_options_2` poskytují:</span><span class="sxs-lookup"><span data-stu-id="36753-487">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="36753-488">`named_options_2` delegát v `ConfigureServices` pro `Option1`.</span><span class="sxs-lookup"><span data-stu-id="36753-488">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="36753-489">Výchozí hodnota pro `Option2` poskytovaná třídou `MyOptions`.</span><span class="sxs-lookup"><span data-stu-id="36753-489">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="36753-490">Konfigurace všech možností pomocí metody ConfigureAll</span><span class="sxs-lookup"><span data-stu-id="36753-490">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="36753-491">Nakonfigurujte všechny instance možností pomocí metody <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*>.</span><span class="sxs-lookup"><span data-stu-id="36753-491">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="36753-492">Následující kód konfiguruje `Option1` pro všechny instance konfigurace se společnou hodnotou.</span><span class="sxs-lookup"><span data-stu-id="36753-492">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="36753-493">Přidejte následující kód ručně do metody `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="36753-493">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="36753-494">Spuštění ukázkové aplikace po přidání kódu vytvoří následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="36753-494">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="36753-495">Všechny možnosti jsou pojmenované instance.</span><span class="sxs-lookup"><span data-stu-id="36753-495">All options are named instances.</span></span> <span data-ttu-id="36753-496">Existující instance <xref:Microsoft.Extensions.Options.IConfigureOptions%601> jsou považovány za cílení na instanci `Options.DefaultName`, která je `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="36753-496">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="36753-497"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> také implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="36753-497"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="36753-498">Výchozí implementace <xref:Microsoft.Extensions.Options.IOptionsFactory%601> má logiku odpovídajícím způsobem použít.</span><span class="sxs-lookup"><span data-stu-id="36753-498">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="36753-499">Pojmenovaná možnost `null` se používá k cílení na všechny pojmenované instance místo konkrétní pojmenované instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> a <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> používají tuto konvenci).</span><span class="sxs-lookup"><span data-stu-id="36753-499">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="36753-500">Rozhraní API pro OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="36753-500">OptionsBuilder API</span></span>

<span data-ttu-id="36753-501"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> se používá ke konfiguraci `TOptions` instancí.</span><span class="sxs-lookup"><span data-stu-id="36753-501"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="36753-502">`OptionsBuilder` zjednodušuje vytváření pojmenovaných možností, protože se jedná pouze o jediný parametr počátečního volání `AddOptions<TOptions>(string optionsName)` namísto zobrazení ve všech následujících voláních.</span><span class="sxs-lookup"><span data-stu-id="36753-502">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="36753-503">Ověřování možností a přetížení `ConfigureOptions`, které přijímají závislosti služby, jsou k dispozici pouze prostřednictvím `OptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="36753-503">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="36753-504">Ke konfiguraci možností použijte DI Services.</span><span class="sxs-lookup"><span data-stu-id="36753-504">Use DI services to configure options</span></span>

<span data-ttu-id="36753-505">K dalším službám můžete přistupovat pomocí injektáže závislosti při konfiguraci možností dvěma způsoby:</span><span class="sxs-lookup"><span data-stu-id="36753-505">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="36753-506">Předejte delegáta konfigurace [na](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) [OptionsBuilder\<TOptions >](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="36753-506">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="36753-507">[OptionsBuilder\<TOptions >](xref:Microsoft.Extensions.Options.OptionsBuilder`1) poskytuje přetížení [Konfigurace](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) , která umožňují použít až pět služeb ke konfiguraci možností:</span><span class="sxs-lookup"><span data-stu-id="36753-507">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="36753-508">Vytvořte vlastní typ, který implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> nebo <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> a zaregistrujte typ jako službu.</span><span class="sxs-lookup"><span data-stu-id="36753-508">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="36753-509">Doporučujeme předat delegáta [konfigurace, protože](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)vytvoření služby je složitější.</span><span class="sxs-lookup"><span data-stu-id="36753-509">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="36753-510">Vytvoření vlastního typu je ekvivalentní s tím, k čemu architektura slouží při použití možnosti [Konfigurovat](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="36753-510">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="36753-511">Volání metody [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) obsahuje přechodný obecný <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, který má konstruktor, který přijímá obecné zadané typy služeb.</span><span class="sxs-lookup"><span data-stu-id="36753-511">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-post-configuration"></a><span data-ttu-id="36753-512">Možnosti po konfiguraci</span><span class="sxs-lookup"><span data-stu-id="36753-512">Options post-configuration</span></span>

<span data-ttu-id="36753-513">Nastavte po konfiguraci <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="36753-513">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="36753-514">Po dokončení konfigurace <xref:Microsoft.Extensions.Options.IConfigureOptions%601> se spustí po konfiguraci:</span><span class="sxs-lookup"><span data-stu-id="36753-514">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="36753-515"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> je k dispozici pro nastavení po konfiguraci s názvem:</span><span class="sxs-lookup"><span data-stu-id="36753-515"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="36753-516">Po dokončení konfigurace všech instancí konfigurace použijte <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*>:</span><span class="sxs-lookup"><span data-stu-id="36753-516">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="36753-517">Přístup k možnostem při spuštění</span><span class="sxs-lookup"><span data-stu-id="36753-517">Accessing options during startup</span></span>

<span data-ttu-id="36753-518"><xref:Microsoft.Extensions.Options.IOptions%601> a <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> lze použít v `Startup.Configure`, protože služby jsou vytvořeny před spuštěním metody `Configure`.</span><span class="sxs-lookup"><span data-stu-id="36753-518"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="36753-519">V `Startup.ConfigureServices`nepoužívejte <xref:Microsoft.Extensions.Options.IOptions%601> ani <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="36753-519">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="36753-520">Stav nekonzistentních možností může existovat v důsledku řazení registrací služby.</span><span class="sxs-lookup"><span data-stu-id="36753-520">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="36753-521">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="36753-521">Additional resources</span></span>

* <xref:fundamentals/configuration/index>

---
title: Vzor možností v ASP.NET Core
author: guardrex
description: Zjistěte, jak pomocí vzoru možností znázornit skupiny souvisejících nastavení v aplikacích ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/18/2019
uid: fundamentals/configuration/options
ms.openlocfilehash: 4192bab8acef7c4f7bdf1ac481c468cd0a835420
ms.sourcegitcommit: 8157e5a351f49aeef3769f7d38b787b4386aad5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74239795"
---
# <a name="options-pattern-in-aspnet-core"></a><span data-ttu-id="69f5e-103">Vzor možností v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="69f5e-103">Options pattern in ASP.NET Core</span></span>

<span data-ttu-id="69f5e-104">Od [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="69f5e-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="69f5e-105">Vzor možností nastavení využívá třídy pro reprezentaci skupin souvisejících nastavení.</span><span class="sxs-lookup"><span data-stu-id="69f5e-105">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="69f5e-106">Když jsou [nastavení konfigurace](xref:fundamentals/configuration/index) izolována scénářem do samostatných tříd, aplikace dodržuje dva důležité principy technického řízení:</span><span class="sxs-lookup"><span data-stu-id="69f5e-106">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="69f5e-107">[Princip oddělování rozhraní (ISP) nebo zapouzdření](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; scénáře (třídy), které závisí na nastavení konfigurace, závisí jenom na nastavení konfigurace, která používají.</span><span class="sxs-lookup"><span data-stu-id="69f5e-107">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="69f5e-108">[Oddělení](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) potíží s tím, jak &ndash; nastavení různých částí aplikace nejsou závislá nebo vzájemně závislá na sobě.</span><span class="sxs-lookup"><span data-stu-id="69f5e-108">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="69f5e-109">Možnosti také poskytují mechanismus pro ověření konfiguračních dat.</span><span class="sxs-lookup"><span data-stu-id="69f5e-109">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="69f5e-110">Další informace najdete v části [ověřování možností](#options-validation) .</span><span class="sxs-lookup"><span data-stu-id="69f5e-110">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="69f5e-111">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="69f5e-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="package"></a><span data-ttu-id="69f5e-112">Balíček</span><span class="sxs-lookup"><span data-stu-id="69f5e-112">Package</span></span>

<span data-ttu-id="69f5e-113">Na balíček [Microsoft. Extensions. Options. ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) se implicitně odkazuje v aplikacích ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="69f5e-113">The [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package is implicitly referenced in ASP.NET Core apps.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="69f5e-114">Možnosti rozhraní</span><span class="sxs-lookup"><span data-stu-id="69f5e-114">Options interfaces</span></span>

<span data-ttu-id="69f5e-115"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> slouží k načtení možností a správě oznámení možností pro `TOptions` instance.</span><span class="sxs-lookup"><span data-stu-id="69f5e-115"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="69f5e-116"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> podporuje následující scénáře:</span><span class="sxs-lookup"><span data-stu-id="69f5e-116"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="69f5e-117">Oznámení změny</span><span class="sxs-lookup"><span data-stu-id="69f5e-117">Change notifications</span></span>
* [<span data-ttu-id="69f5e-118">Pojmenované možnosti</span><span class="sxs-lookup"><span data-stu-id="69f5e-118">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="69f5e-119">Znovu Načítatelné konfigurace</span><span class="sxs-lookup"><span data-stu-id="69f5e-119">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="69f5e-120">Neplatné selektivní možnosti (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="69f5e-120">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="69f5e-121">Scénáře po [konfiguraci](#options-post-configuration) umožňují nastavit nebo změnit možnosti po <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ke konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="69f5e-121">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="69f5e-122"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> zodpovídá za vytváření nových instancí možností.</span><span class="sxs-lookup"><span data-stu-id="69f5e-122"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="69f5e-123">Má jednu <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> metodu.</span><span class="sxs-lookup"><span data-stu-id="69f5e-123">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="69f5e-124">Výchozí implementace přebírá všechny registrované <xref:Microsoft.Extensions.Options.IConfigureOptions%601> a <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> a předá všechny konfigurace a potom po konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="69f5e-124">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="69f5e-125">Rozlišuje mezi <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> a <xref:Microsoft.Extensions.Options.IConfigureOptions%601> a volá pouze příslušné rozhraní.</span><span class="sxs-lookup"><span data-stu-id="69f5e-125">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="69f5e-126"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> používá <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> k ukládání instancí `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="69f5e-126"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="69f5e-127"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> zruší platnost instancí možností v monitorování tak, aby se hodnota přepočítala (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="69f5e-127">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="69f5e-128">Hodnoty lze ručně zavést pomocí <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span><span class="sxs-lookup"><span data-stu-id="69f5e-128">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="69f5e-129">Metoda <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> se používá v případě, že by se všechny pojmenované instance měly znovu vytvořit na vyžádání.</span><span class="sxs-lookup"><span data-stu-id="69f5e-129">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="69f5e-130"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> je užitečná ve scénářích, ve kterých by se měly přepočítat možnosti pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="69f5e-130"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="69f5e-131">Další informace najdete v části [opětovné načtení konfiguračních dat pomocí IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) .</span><span class="sxs-lookup"><span data-stu-id="69f5e-131">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="69f5e-132"><xref:Microsoft.Extensions.Options.IOptions%601> lze použít k podpoře možností.</span><span class="sxs-lookup"><span data-stu-id="69f5e-132"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="69f5e-133"><xref:Microsoft.Extensions.Options.IOptions%601> ale nepodporuje předchozí scénáře <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="69f5e-133">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="69f5e-134">Můžete nadále používat <xref:Microsoft.Extensions.Options.IOptions%601> ve stávajících rozhraních a knihovnách, které již používají rozhraní <xref:Microsoft.Extensions.Options.IOptions%601> a nevyžadují scénáře poskytované <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="69f5e-134">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="69f5e-135">Konfigurace obecných možností</span><span class="sxs-lookup"><span data-stu-id="69f5e-135">General options configuration</span></span>

<span data-ttu-id="69f5e-136">Konfigurace obecných možností se v ukázkové aplikaci ukazuje jako příklad &num;1.</span><span class="sxs-lookup"><span data-stu-id="69f5e-136">General options configuration is demonstrated as Example &num;1 in the sample app.</span></span>

<span data-ttu-id="69f5e-137">Třída Options musí být neabstraktní s veřejným konstruktorem bez parametrů.</span><span class="sxs-lookup"><span data-stu-id="69f5e-137">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="69f5e-138">Následující třída `MyOptions`má dvě vlastnosti, `Option1` a `Option2`.</span><span class="sxs-lookup"><span data-stu-id="69f5e-138">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="69f5e-139">Nastavení výchozích hodnot je volitelné, ale konstruktor třídy v následujícím příkladu nastaví výchozí hodnotu `Option1`.</span><span class="sxs-lookup"><span data-stu-id="69f5e-139">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="69f5e-140">`Option2` má výchozí hodnotu nastavenou pomocí přímé inicializace vlastnosti (*modely/MyOptions. cs*):</span><span class="sxs-lookup"><span data-stu-id="69f5e-140">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="69f5e-141">Třída `MyOptions` je přidána do kontejneru služby s <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> a je svázána s konfigurací:</span><span class="sxs-lookup"><span data-stu-id="69f5e-141">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="69f5e-142">Následující model stránky používá [vkládání závislostí konstruktoru](xref:mvc/controllers/dependency-injection) s <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> pro přístup k nastavení (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="69f5e-142">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="69f5e-143">Soubor *appSettings. JSON* v ukázce určuje hodnoty pro `option1` a `option2`:</span><span class="sxs-lookup"><span data-stu-id="69f5e-143">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/3.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="69f5e-144">Při spuštění aplikace vrátí `OnGet` metoda modelu stránky řetězec zobrazující hodnoty třídy možností:</span><span class="sxs-lookup"><span data-stu-id="69f5e-144">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="69f5e-145">Při načítání konfigurace možností pomocí vlastního <xref:System.Configuration.ConfigurationBuilder> v souboru nastavení potvrďte, že je správně nastavená základní cesta:</span><span class="sxs-lookup"><span data-stu-id="69f5e-145">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="69f5e-146">Při načítání konfigurace možností ze souboru nastavení prostřednictvím <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>není nutné explicitně nastavit základní cestu.</span><span class="sxs-lookup"><span data-stu-id="69f5e-146">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="69f5e-147">Konfigurace jednoduchých možností pomocí delegáta</span><span class="sxs-lookup"><span data-stu-id="69f5e-147">Configure simple options with a delegate</span></span>

<span data-ttu-id="69f5e-148">Konfigurace jednoduchých možností s delegátem je v ukázkové aplikaci znázorněna jako příklad &num;2.</span><span class="sxs-lookup"><span data-stu-id="69f5e-148">Configuring simple options with a delegate is demonstrated as Example &num;2 in the sample app.</span></span>

<span data-ttu-id="69f5e-149">K nastavení hodnot možností použijte delegáta.</span><span class="sxs-lookup"><span data-stu-id="69f5e-149">Use a delegate to set options values.</span></span> <span data-ttu-id="69f5e-150">Ukázková aplikace používá třídu `MyOptionsWithDelegateConfig` (*modely/MyOptionsWithDelegateConfig. cs*):</span><span class="sxs-lookup"><span data-stu-id="69f5e-150">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="69f5e-151">V následujícím kódu se do kontejneru služby přidá druhá <xref:Microsoft.Extensions.Options.IConfigureOptions%601> služba.</span><span class="sxs-lookup"><span data-stu-id="69f5e-151">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="69f5e-152">Pomocí delegáta konfiguruje vazbu s `MyOptionsWithDelegateConfig`:</span><span class="sxs-lookup"><span data-stu-id="69f5e-152">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="69f5e-153">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="69f5e-153">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="69f5e-154">Můžete přidat více poskytovatelů konfigurace.</span><span class="sxs-lookup"><span data-stu-id="69f5e-154">You can add multiple configuration providers.</span></span> <span data-ttu-id="69f5e-155">Poskytovatelé konfigurace jsou k dispozici z balíčků NuGet a jsou aplikováni v pořadí, v jakém jsou registrováni.</span><span class="sxs-lookup"><span data-stu-id="69f5e-155">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="69f5e-156">Další informace najdete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="69f5e-156">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="69f5e-157">Každé volání <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> přidá <xref:Microsoft.Extensions.Options.IConfigureOptions%601> službu do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="69f5e-157">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="69f5e-158">V předchozím příkladu jsou hodnoty `Option1` a `Option2` zadány v souboru *appSettings. JSON*, ale hodnoty `Option1` a `Option2` jsou přepsány nakonfigurovaným delegátem.</span><span class="sxs-lookup"><span data-stu-id="69f5e-158">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="69f5e-159">Je-li povolena více než jedna služba konfigurace, je v posledním zdroji konfigurace zadaný *Server WINS* a nastavena hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="69f5e-159">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="69f5e-160">Při spuštění aplikace vrátí `OnGet` metoda modelu stránky řetězec zobrazující hodnoty třídy možností:</span><span class="sxs-lookup"><span data-stu-id="69f5e-160">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="69f5e-161">Konfigurace podNastavení</span><span class="sxs-lookup"><span data-stu-id="69f5e-161">Suboptions configuration</span></span>

<span data-ttu-id="69f5e-162">Konfigurace dílčích možností je v ukázkové aplikaci znázorněna jako příklad &num;3.</span><span class="sxs-lookup"><span data-stu-id="69f5e-162">Suboptions configuration is demonstrated as Example &num;3 in the sample app.</span></span>

<span data-ttu-id="69f5e-163">Aplikace by měly vytvořit třídy možností, které se týkají konkrétních skupin scénářů (tříd) v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="69f5e-163">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="69f5e-164">Části aplikace, které vyžadují konfigurační hodnoty, by měly mít přístup jenom k hodnotám konfigurace, které používají.</span><span class="sxs-lookup"><span data-stu-id="69f5e-164">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="69f5e-165">Při vytváření vazby mezi možnostmi konfigurace je každá vlastnost v typu možnosti vázána na konfigurační klíč formuláře `property[:sub-property:]`.</span><span class="sxs-lookup"><span data-stu-id="69f5e-165">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="69f5e-166">Například vlastnost `MyOptions.Option1` je vázána na klíčovou `Option1`, která je čtena z vlastnosti `option1` v souboru *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="69f5e-166">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="69f5e-167">V následujícím kódu je do kontejneru služby přidána třetí <xref:Microsoft.Extensions.Options.IConfigureOptions%601> služba.</span><span class="sxs-lookup"><span data-stu-id="69f5e-167">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="69f5e-168">Vytvoří vazby `MySubOptions` k oddílu `subsection` souboru *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="69f5e-168">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="69f5e-169">Metoda `GetSection` vyžaduje obor názvů <xref:Microsoft.Extensions.Configuration?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="69f5e-169">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="69f5e-170">Soubor *appSettings. JSON* ve vzorci definuje `subsection`ho člena s klíči pro `suboption1` a `suboption2`:</span><span class="sxs-lookup"><span data-stu-id="69f5e-170">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/3.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="69f5e-171">Třída `MySubOptions` definuje vlastnosti, `SubOption1` a `SubOption2`pro uchovávání hodnot možností (*modely/MySubOptions. cs*):</span><span class="sxs-lookup"><span data-stu-id="69f5e-171">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="69f5e-172">Metoda `OnGet` modelu stránky vrací řetězec s hodnotami možností (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="69f5e-172">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="69f5e-173">Když je aplikace spuštěna, metoda `OnGet` vrátí řetězec zobrazující hodnoty třídy dílčích možností:</span><span class="sxs-lookup"><span data-stu-id="69f5e-173">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-injection"></a><span data-ttu-id="69f5e-174">Vkládání možností</span><span class="sxs-lookup"><span data-stu-id="69f5e-174">Options injection</span></span>

<span data-ttu-id="69f5e-175">Vkládání možností je znázorněno jako příklad &num;4 v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="69f5e-175">Options injection is demonstrated as Example &num;4 in the sample app.</span></span>

<span data-ttu-id="69f5e-176">Vložit <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> do:</span><span class="sxs-lookup"><span data-stu-id="69f5e-176">Inject <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into:</span></span>

* <span data-ttu-id="69f5e-177">Stránka Razor nebo zobrazení MVC s direktivou [@inject](xref:mvc/views/razor#inject) Razor</span><span class="sxs-lookup"><span data-stu-id="69f5e-177">A Razor page or MVC view with the [@inject](xref:mvc/views/razor#inject) Razor directive.</span></span>
* <span data-ttu-id="69f5e-178">Model stránky nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="69f5e-178">A page or view model.</span></span>

<span data-ttu-id="69f5e-179">Následující příklad z ukázkové aplikace vloží <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> do modelu stránky (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="69f5e-179">The following example from the sample app injects <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into a page model (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="69f5e-180">Ukázková aplikace ukazuje, jak vložit `IOptionsMonitor<MyOptions>` s direktivou `@inject`:</span><span class="sxs-lookup"><span data-stu-id="69f5e-180">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/3.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="69f5e-181">Po spuštění aplikace se hodnoty možností zobrazí na vykreslené stránce:</span><span class="sxs-lookup"><span data-stu-id="69f5e-181">When the app is run, the options values are shown in the rendered page:</span></span>

![Hodnoty možností možnost1: value1_from_json a Možnost2:-1 jsou načteny z modelu a zavstřikem do zobrazení.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="69f5e-183">Znovu načíst konfigurační data pomocí IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="69f5e-183">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="69f5e-184">Opětovné načtení konfiguračních dat pomocí <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> je v ukázkové aplikaci znázorněno v příkladu &num;5.</span><span class="sxs-lookup"><span data-stu-id="69f5e-184">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example &num;5 in the sample app.</span></span>

<span data-ttu-id="69f5e-185">Pomocí <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>jsou možnosti vypočítány jednou za požadavek při otevření a ukládání do mezipaměti po dobu života žádosti.</span><span class="sxs-lookup"><span data-stu-id="69f5e-185">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="69f5e-186">Rozdíl mezi `IOptionsMonitor` a `IOptionsSnapshot` je následující:</span><span class="sxs-lookup"><span data-stu-id="69f5e-186">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="69f5e-187">`IOptionsMonitor` je [Služba typu Singleton](xref:fundamentals/dependency-injection#singleton) , která načte aktuální hodnoty možností kdykoli, což je zvláště užitečné v závislostech singleton.</span><span class="sxs-lookup"><span data-stu-id="69f5e-187">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="69f5e-188">`IOptionsSnapshot` je [Oborová služba](xref:fundamentals/dependency-injection#scoped) a poskytuje snímek možností v době, kdy je objekt `IOptionsSnapshot<T>` vytvořen.</span><span class="sxs-lookup"><span data-stu-id="69f5e-188">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="69f5e-189">Snímky možností jsou navržené pro použití s přechodnými a vymezenými závislostmi.</span><span class="sxs-lookup"><span data-stu-id="69f5e-189">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="69f5e-190">Následující příklad ukazuje, jak se vytvoří nový <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> po změnách *appSettings. JSON* (*pages/index. cshtml. cs*).</span><span class="sxs-lookup"><span data-stu-id="69f5e-190">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="69f5e-191">Více požadavků na server vrací konstantní hodnoty, které poskytuje soubor *appSettings. JSON* , dokud nedojde ke změně souboru a opětovnému načtení konfigurace.</span><span class="sxs-lookup"><span data-stu-id="69f5e-191">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="69f5e-192">Následující obrázek ukazuje počáteční `option1` a hodnoty `option2` načtené ze souboru *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="69f5e-192">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="69f5e-193">Změňte hodnoty v souboru *appSettings. JSON* na `value1_from_json UPDATED` a `200`.</span><span class="sxs-lookup"><span data-stu-id="69f5e-193">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="69f5e-194">Uložte soubor *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="69f5e-194">Save the *appsettings.json* file.</span></span> <span data-ttu-id="69f5e-195">Aktualizujte prohlížeč, aby se zobrazily hodnoty možností, které jsou aktualizované:</span><span class="sxs-lookup"><span data-stu-id="69f5e-195">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="69f5e-196">Podpora pojmenovaných možností s IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="69f5e-196">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="69f5e-197">Podpora pojmenovaných možností s <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> je v ukázkové aplikaci znázorněna jako příklad &num;6.</span><span class="sxs-lookup"><span data-stu-id="69f5e-197">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example &num;6 in the sample app.</span></span>

<span data-ttu-id="69f5e-198">Podpora *pojmenovaných možností* umožňuje aplikaci rozlišovat konfigurace pojmenovaných možností.</span><span class="sxs-lookup"><span data-stu-id="69f5e-198">*Named options* support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="69f5e-199">V ukázkové aplikaci jsou pojmenované možnosti deklarovány pomocí [OptionsServiceCollectionExtensions. Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), který volá [ConfigureNamedOptions >\<TOptions. Konfigurace](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) metody rozšíření:</span><span class="sxs-lookup"><span data-stu-id="69f5e-199">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="69f5e-200">Ukázková aplikace přistupuje k pojmenovaným možnostem pomocí <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="69f5e-200">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="69f5e-201">Když spustíte ukázkovou aplikaci, vrátí se pojmenované možnosti:</span><span class="sxs-lookup"><span data-stu-id="69f5e-201">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="69f5e-202">hodnoty `named_options_1` se poskytují z konfigurace, které se načítají ze souboru *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="69f5e-202">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="69f5e-203">hodnoty `named_options_2` poskytují:</span><span class="sxs-lookup"><span data-stu-id="69f5e-203">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="69f5e-204">`named_options_2` delegát v `ConfigureServices` pro `Option1`.</span><span class="sxs-lookup"><span data-stu-id="69f5e-204">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="69f5e-205">Výchozí hodnota pro `Option2` poskytovaná `MyOptions`ou třídou.</span><span class="sxs-lookup"><span data-stu-id="69f5e-205">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="69f5e-206">Konfigurace všech možností pomocí metody ConfigureAll</span><span class="sxs-lookup"><span data-stu-id="69f5e-206">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="69f5e-207">Nakonfigurujte všechny instance možností pomocí metody <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*>.</span><span class="sxs-lookup"><span data-stu-id="69f5e-207">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="69f5e-208">Následující kód konfiguruje `Option1` pro všechny instance konfigurace se společnou hodnotou.</span><span class="sxs-lookup"><span data-stu-id="69f5e-208">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="69f5e-209">Do metody `Startup.ConfigureServices` přidejte ručně následující kód:</span><span class="sxs-lookup"><span data-stu-id="69f5e-209">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="69f5e-210">Spuštění ukázkové aplikace po přidání kódu vytvoří následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="69f5e-210">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="69f5e-211">Všechny možnosti jsou pojmenované instance.</span><span class="sxs-lookup"><span data-stu-id="69f5e-211">All options are named instances.</span></span> <span data-ttu-id="69f5e-212">Existující instance <xref:Microsoft.Extensions.Options.IConfigureOptions%601> jsou považovány za cílení na instanci `Options.DefaultName`, která je `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="69f5e-212">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="69f5e-213"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> také implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="69f5e-213"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="69f5e-214">Výchozí implementace <xref:Microsoft.Extensions.Options.IOptionsFactory%601> má logiku odpovídajícím způsobem použít.</span><span class="sxs-lookup"><span data-stu-id="69f5e-214">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="69f5e-215">Možnost `null` s názvem slouží k cílení na všechny pojmenované instance místo konkrétní pojmenované instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> a <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> použít tuto konvenci).</span><span class="sxs-lookup"><span data-stu-id="69f5e-215">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="69f5e-216">Rozhraní API pro OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="69f5e-216">OptionsBuilder API</span></span>

<span data-ttu-id="69f5e-217"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> se používá ke konfiguraci `TOptions` instancí.</span><span class="sxs-lookup"><span data-stu-id="69f5e-217"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="69f5e-218">`OptionsBuilder` zjednodušuje vytváření pojmenovaných možností, protože je pouze jedním parametrem počátečního `AddOptions<TOptions>(string optionsName)`ho volání namísto zobrazení ve všech následujících voláních.</span><span class="sxs-lookup"><span data-stu-id="69f5e-218">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="69f5e-219">Ověřování možností a `ConfigureOptions` přetížení, která přijímají závislosti služby, jsou k dispozici pouze prostřednictvím `OptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="69f5e-219">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="69f5e-220">Ke konfiguraci možností použijte DI Services.</span><span class="sxs-lookup"><span data-stu-id="69f5e-220">Use DI services to configure options</span></span>

<span data-ttu-id="69f5e-221">K dalším službám můžete přistupovat pomocí injektáže závislosti při konfiguraci možností dvěma způsoby:</span><span class="sxs-lookup"><span data-stu-id="69f5e-221">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="69f5e-222">Předejte delegáta konfigurace [na](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) [OptionsBuilder\<TOptions >](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="69f5e-222">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="69f5e-223">[OptionsBuilder\<TOptions >](xref:Microsoft.Extensions.Options.OptionsBuilder`1) poskytuje přetížení [Konfigurace](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) , která umožňují použít až pět služeb ke konfiguraci možností:</span><span class="sxs-lookup"><span data-stu-id="69f5e-223">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="69f5e-224">Vytvořte vlastní typ, který implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> nebo <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> a zaregistrujte typ jako službu.</span><span class="sxs-lookup"><span data-stu-id="69f5e-224">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="69f5e-225">Doporučujeme předat delegáta [konfigurace, protože](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)vytvoření služby je složitější.</span><span class="sxs-lookup"><span data-stu-id="69f5e-225">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="69f5e-226">Vytvoření vlastního typu je ekvivalentní s tím, k čemu architektura slouží při použití možnosti [Konfigurovat](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="69f5e-226">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="69f5e-227">Volání metody [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) obsahuje přechodný obecný <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, který má konstruktor, který přijímá obecné zadané typy služeb.</span><span class="sxs-lookup"><span data-stu-id="69f5e-227">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="69f5e-228">Ověřování možností</span><span class="sxs-lookup"><span data-stu-id="69f5e-228">Options validation</span></span>

<span data-ttu-id="69f5e-229">Ověřování možností umožňuje ověřit možnosti při konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="69f5e-229">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="69f5e-230">Zavolejte `Validate` s metodou ověřování, která vrátí `true`, pokud jsou možnosti platné a `false`, pokud nejsou platné:</span><span class="sxs-lookup"><span data-stu-id="69f5e-230">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

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

<span data-ttu-id="69f5e-231">Předchozí příklad nastaví instanci pojmenované možnosti na `optionalOptionsName`.</span><span class="sxs-lookup"><span data-stu-id="69f5e-231">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="69f5e-232">Instance výchozích možností je `Options.DefaultName`.</span><span class="sxs-lookup"><span data-stu-id="69f5e-232">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="69f5e-233">Ověřování se spustí při vytvoření instance Options.</span><span class="sxs-lookup"><span data-stu-id="69f5e-233">Validation runs when the options instance is created.</span></span> <span data-ttu-id="69f5e-234">Při prvním použití vaší instance možností je zaručeno předání ověření.</span><span class="sxs-lookup"><span data-stu-id="69f5e-234">Your options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="69f5e-235">Ověřování možností nechrání proti změnám možností po počáteční konfiguraci a ověření možností.</span><span class="sxs-lookup"><span data-stu-id="69f5e-235">Options validation doesn't guard against options modifications after the options are initially configured and validated.</span></span>

<span data-ttu-id="69f5e-236">Metoda `Validate` přijímá `Func<TOptions, bool>`.</span><span class="sxs-lookup"><span data-stu-id="69f5e-236">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="69f5e-237">K úplnému přizpůsobení ověřování implementujte `IValidateOptions<TOptions>`, která umožňuje:</span><span class="sxs-lookup"><span data-stu-id="69f5e-237">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="69f5e-238">Ověřování více typů možností: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="69f5e-238">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="69f5e-239">Ověření, které závisí na jiném typu možnosti: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="69f5e-239">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="69f5e-240">`IValidateOptions` ověří:</span><span class="sxs-lookup"><span data-stu-id="69f5e-240">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="69f5e-241">Konkrétní instance pojmenované možnosti.</span><span class="sxs-lookup"><span data-stu-id="69f5e-241">A specific named options instance.</span></span>
* <span data-ttu-id="69f5e-242">Všechny možnosti, pokud je `name` `null`.</span><span class="sxs-lookup"><span data-stu-id="69f5e-242">All options when `name` is `null`.</span></span>

<span data-ttu-id="69f5e-243">Vrácení `ValidateOptionsResult` z implementace rozhraní:</span><span class="sxs-lookup"><span data-stu-id="69f5e-243">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="69f5e-244">Ověřování založené na datových anotacích je k dispozici v balíčku [Microsoft. Extensions. Options. DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) voláním metody <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> v `OptionsBuilder<TOptions>`.</span><span class="sxs-lookup"><span data-stu-id="69f5e-244">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="69f5e-245">na `Microsoft.Extensions.Options.DataAnnotations` se implicitně odkazuje v ASP.NET Corech aplikacích.</span><span class="sxs-lookup"><span data-stu-id="69f5e-245">`Microsoft.Extensions.Options.DataAnnotations` is implicitly referenced in ASP.NET Core apps.</span></span>

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

<span data-ttu-id="69f5e-246">Ověření Eager (při spuštění selže) je v budoucí verzi zváženo.</span><span class="sxs-lookup"><span data-stu-id="69f5e-246">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="69f5e-247">Možnosti po konfiguraci</span><span class="sxs-lookup"><span data-stu-id="69f5e-247">Options post-configuration</span></span>

<span data-ttu-id="69f5e-248">Nastavte po konfiguraci <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="69f5e-248">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="69f5e-249">Po dokončení konfigurace <xref:Microsoft.Extensions.Options.IConfigureOptions%601> se spustí po konfiguraci:</span><span class="sxs-lookup"><span data-stu-id="69f5e-249">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="69f5e-250"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> je k dispozici pro nastavení po konfiguraci s názvem:</span><span class="sxs-lookup"><span data-stu-id="69f5e-250"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="69f5e-251">Pomocí <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> vystavte všechny instance konfigurace:</span><span class="sxs-lookup"><span data-stu-id="69f5e-251">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="69f5e-252">Přístup k možnostem při spuštění</span><span class="sxs-lookup"><span data-stu-id="69f5e-252">Accessing options during startup</span></span>

<span data-ttu-id="69f5e-253"><xref:Microsoft.Extensions.Options.IOptions%601> a <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> lze použít v `Startup.Configure`, protože služby jsou vytvořeny před spuštěním metody `Configure`.</span><span class="sxs-lookup"><span data-stu-id="69f5e-253"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, 
    IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="69f5e-254">V `Startup.ConfigureServices`nepoužívejte <xref:Microsoft.Extensions.Options.IOptions%601> ani <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="69f5e-254">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="69f5e-255">Stav nekonzistentních možností může existovat v důsledku řazení registrací služby.</span><span class="sxs-lookup"><span data-stu-id="69f5e-255">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="69f5e-256">Vzor možností nastavení využívá třídy pro reprezentaci skupin souvisejících nastavení.</span><span class="sxs-lookup"><span data-stu-id="69f5e-256">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="69f5e-257">Když jsou [nastavení konfigurace](xref:fundamentals/configuration/index) izolována scénářem do samostatných tříd, aplikace dodržuje dva důležité principy technického řízení:</span><span class="sxs-lookup"><span data-stu-id="69f5e-257">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="69f5e-258">[Princip oddělování rozhraní (ISP) nebo zapouzdření](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; scénáře (třídy), které závisí na nastavení konfigurace, závisí jenom na nastavení konfigurace, která používají.</span><span class="sxs-lookup"><span data-stu-id="69f5e-258">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="69f5e-259">[Oddělení](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) potíží s tím, jak &ndash; nastavení různých částí aplikace nejsou závislá nebo vzájemně závislá na sobě.</span><span class="sxs-lookup"><span data-stu-id="69f5e-259">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="69f5e-260">Možnosti také poskytují mechanismus pro ověření konfiguračních dat.</span><span class="sxs-lookup"><span data-stu-id="69f5e-260">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="69f5e-261">Další informace najdete v části [ověřování možností](#options-validation) .</span><span class="sxs-lookup"><span data-stu-id="69f5e-261">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="69f5e-262">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="69f5e-262">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="69f5e-263">Požadavky</span><span class="sxs-lookup"><span data-stu-id="69f5e-263">Prerequisites</span></span>

<span data-ttu-id="69f5e-264">Odkaz na balíček [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) nebo přidejte odkaz na balíček do balíčku [Microsoft. Extensions. Options. ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) .</span><span class="sxs-lookup"><span data-stu-id="69f5e-264">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="69f5e-265">Možnosti rozhraní</span><span class="sxs-lookup"><span data-stu-id="69f5e-265">Options interfaces</span></span>

<span data-ttu-id="69f5e-266"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> slouží k načtení možností a správě oznámení možností pro `TOptions` instance.</span><span class="sxs-lookup"><span data-stu-id="69f5e-266"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="69f5e-267"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> podporuje následující scénáře:</span><span class="sxs-lookup"><span data-stu-id="69f5e-267"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="69f5e-268">Oznámení změny</span><span class="sxs-lookup"><span data-stu-id="69f5e-268">Change notifications</span></span>
* [<span data-ttu-id="69f5e-269">Pojmenované možnosti</span><span class="sxs-lookup"><span data-stu-id="69f5e-269">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="69f5e-270">Znovu Načítatelné konfigurace</span><span class="sxs-lookup"><span data-stu-id="69f5e-270">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="69f5e-271">Neplatné selektivní možnosti (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="69f5e-271">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="69f5e-272">Scénáře po [konfiguraci](#options-post-configuration) umožňují nastavit nebo změnit možnosti po <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ke konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="69f5e-272">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="69f5e-273"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> zodpovídá za vytváření nových instancí možností.</span><span class="sxs-lookup"><span data-stu-id="69f5e-273"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="69f5e-274">Má jednu <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> metodu.</span><span class="sxs-lookup"><span data-stu-id="69f5e-274">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="69f5e-275">Výchozí implementace přebírá všechny registrované <xref:Microsoft.Extensions.Options.IConfigureOptions%601> a <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> a předá všechny konfigurace a potom po konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="69f5e-275">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="69f5e-276">Rozlišuje mezi <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> a <xref:Microsoft.Extensions.Options.IConfigureOptions%601> a volá pouze příslušné rozhraní.</span><span class="sxs-lookup"><span data-stu-id="69f5e-276">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="69f5e-277"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> používá <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> k ukládání instancí `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="69f5e-277"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="69f5e-278"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> zruší platnost instancí možností v monitorování tak, aby se hodnota přepočítala (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="69f5e-278">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="69f5e-279">Hodnoty lze ručně zavést pomocí <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span><span class="sxs-lookup"><span data-stu-id="69f5e-279">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="69f5e-280">Metoda <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> se používá v případě, že by se všechny pojmenované instance měly znovu vytvořit na vyžádání.</span><span class="sxs-lookup"><span data-stu-id="69f5e-280">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="69f5e-281"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> je užitečná ve scénářích, ve kterých by se měly přepočítat možnosti pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="69f5e-281"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="69f5e-282">Další informace najdete v části [opětovné načtení konfiguračních dat pomocí IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) .</span><span class="sxs-lookup"><span data-stu-id="69f5e-282">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="69f5e-283"><xref:Microsoft.Extensions.Options.IOptions%601> lze použít k podpoře možností.</span><span class="sxs-lookup"><span data-stu-id="69f5e-283"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="69f5e-284"><xref:Microsoft.Extensions.Options.IOptions%601> ale nepodporuje předchozí scénáře <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="69f5e-284">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="69f5e-285">Můžete nadále používat <xref:Microsoft.Extensions.Options.IOptions%601> ve stávajících rozhraních a knihovnách, které již používají rozhraní <xref:Microsoft.Extensions.Options.IOptions%601> a nevyžadují scénáře poskytované <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="69f5e-285">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="69f5e-286">Konfigurace obecných možností</span><span class="sxs-lookup"><span data-stu-id="69f5e-286">General options configuration</span></span>

<span data-ttu-id="69f5e-287">Konfigurace obecných možností se v ukázkové aplikaci ukazuje jako příklad &num;1.</span><span class="sxs-lookup"><span data-stu-id="69f5e-287">General options configuration is demonstrated as Example &num;1 in the sample app.</span></span>

<span data-ttu-id="69f5e-288">Třída Options musí být neabstraktní s veřejným konstruktorem bez parametrů.</span><span class="sxs-lookup"><span data-stu-id="69f5e-288">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="69f5e-289">Následující třída `MyOptions`má dvě vlastnosti, `Option1` a `Option2`.</span><span class="sxs-lookup"><span data-stu-id="69f5e-289">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="69f5e-290">Nastavení výchozích hodnot je volitelné, ale konstruktor třídy v následujícím příkladu nastaví výchozí hodnotu `Option1`.</span><span class="sxs-lookup"><span data-stu-id="69f5e-290">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="69f5e-291">`Option2` má výchozí hodnotu nastavenou pomocí přímé inicializace vlastnosti (*modely/MyOptions. cs*):</span><span class="sxs-lookup"><span data-stu-id="69f5e-291">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="69f5e-292">Třída `MyOptions` je přidána do kontejneru služby s <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> a je svázána s konfigurací:</span><span class="sxs-lookup"><span data-stu-id="69f5e-292">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="69f5e-293">Následující model stránky používá [vkládání závislostí konstruktoru](xref:mvc/controllers/dependency-injection) s <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> pro přístup k nastavení (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="69f5e-293">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="69f5e-294">Soubor *appSettings. JSON* v ukázce určuje hodnoty pro `option1` a `option2`:</span><span class="sxs-lookup"><span data-stu-id="69f5e-294">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="69f5e-295">Při spuštění aplikace vrátí `OnGet` metoda modelu stránky řetězec zobrazující hodnoty třídy možností:</span><span class="sxs-lookup"><span data-stu-id="69f5e-295">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="69f5e-296">Při načítání konfigurace možností pomocí vlastního <xref:System.Configuration.ConfigurationBuilder> v souboru nastavení potvrďte, že je správně nastavená základní cesta:</span><span class="sxs-lookup"><span data-stu-id="69f5e-296">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="69f5e-297">Při načítání konfigurace možností ze souboru nastavení prostřednictvím <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>není nutné explicitně nastavit základní cestu.</span><span class="sxs-lookup"><span data-stu-id="69f5e-297">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="69f5e-298">Konfigurace jednoduchých možností pomocí delegáta</span><span class="sxs-lookup"><span data-stu-id="69f5e-298">Configure simple options with a delegate</span></span>

<span data-ttu-id="69f5e-299">Konfigurace jednoduchých možností s delegátem je v ukázkové aplikaci znázorněna jako příklad &num;2.</span><span class="sxs-lookup"><span data-stu-id="69f5e-299">Configuring simple options with a delegate is demonstrated as Example &num;2 in the sample app.</span></span>

<span data-ttu-id="69f5e-300">K nastavení hodnot možností použijte delegáta.</span><span class="sxs-lookup"><span data-stu-id="69f5e-300">Use a delegate to set options values.</span></span> <span data-ttu-id="69f5e-301">Ukázková aplikace používá třídu `MyOptionsWithDelegateConfig` (*modely/MyOptionsWithDelegateConfig. cs*):</span><span class="sxs-lookup"><span data-stu-id="69f5e-301">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="69f5e-302">V následujícím kódu se do kontejneru služby přidá druhá <xref:Microsoft.Extensions.Options.IConfigureOptions%601> služba.</span><span class="sxs-lookup"><span data-stu-id="69f5e-302">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="69f5e-303">Pomocí delegáta konfiguruje vazbu s `MyOptionsWithDelegateConfig`:</span><span class="sxs-lookup"><span data-stu-id="69f5e-303">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="69f5e-304">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="69f5e-304">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="69f5e-305">Můžete přidat více poskytovatelů konfigurace.</span><span class="sxs-lookup"><span data-stu-id="69f5e-305">You can add multiple configuration providers.</span></span> <span data-ttu-id="69f5e-306">Poskytovatelé konfigurace jsou k dispozici z balíčků NuGet a jsou aplikováni v pořadí, v jakém jsou registrováni.</span><span class="sxs-lookup"><span data-stu-id="69f5e-306">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="69f5e-307">Další informace najdete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="69f5e-307">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="69f5e-308">Každé volání <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> přidá <xref:Microsoft.Extensions.Options.IConfigureOptions%601> službu do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="69f5e-308">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="69f5e-309">V předchozím příkladu jsou hodnoty `Option1` a `Option2` zadány v souboru *appSettings. JSON*, ale hodnoty `Option1` a `Option2` jsou přepsány nakonfigurovaným delegátem.</span><span class="sxs-lookup"><span data-stu-id="69f5e-309">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="69f5e-310">Je-li povolena více než jedna služba konfigurace, je v posledním zdroji konfigurace zadaný *Server WINS* a nastavena hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="69f5e-310">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="69f5e-311">Při spuštění aplikace vrátí `OnGet` metoda modelu stránky řetězec zobrazující hodnoty třídy možností:</span><span class="sxs-lookup"><span data-stu-id="69f5e-311">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="69f5e-312">Konfigurace podNastavení</span><span class="sxs-lookup"><span data-stu-id="69f5e-312">Suboptions configuration</span></span>

<span data-ttu-id="69f5e-313">Konfigurace dílčích možností je v ukázkové aplikaci znázorněna jako příklad &num;3.</span><span class="sxs-lookup"><span data-stu-id="69f5e-313">Suboptions configuration is demonstrated as Example &num;3 in the sample app.</span></span>

<span data-ttu-id="69f5e-314">Aplikace by měly vytvořit třídy možností, které se týkají konkrétních skupin scénářů (tříd) v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="69f5e-314">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="69f5e-315">Části aplikace, které vyžadují konfigurační hodnoty, by měly mít přístup jenom k hodnotám konfigurace, které používají.</span><span class="sxs-lookup"><span data-stu-id="69f5e-315">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="69f5e-316">Při vytváření vazby mezi možnostmi konfigurace je každá vlastnost v typu možnosti vázána na konfigurační klíč formuláře `property[:sub-property:]`.</span><span class="sxs-lookup"><span data-stu-id="69f5e-316">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="69f5e-317">Například vlastnost `MyOptions.Option1` je vázána na klíčovou `Option1`, která je čtena z vlastnosti `option1` v souboru *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="69f5e-317">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="69f5e-318">V následujícím kódu je do kontejneru služby přidána třetí <xref:Microsoft.Extensions.Options.IConfigureOptions%601> služba.</span><span class="sxs-lookup"><span data-stu-id="69f5e-318">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="69f5e-319">Vytvoří vazby `MySubOptions` k oddílu `subsection` souboru *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="69f5e-319">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="69f5e-320">Metoda `GetSection` vyžaduje obor názvů <xref:Microsoft.Extensions.Configuration?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="69f5e-320">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="69f5e-321">Soubor *appSettings. JSON* ve vzorci definuje `subsection`ho člena s klíči pro `suboption1` a `suboption2`:</span><span class="sxs-lookup"><span data-stu-id="69f5e-321">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="69f5e-322">Třída `MySubOptions` definuje vlastnosti, `SubOption1` a `SubOption2`pro uchovávání hodnot možností (*modely/MySubOptions. cs*):</span><span class="sxs-lookup"><span data-stu-id="69f5e-322">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="69f5e-323">Metoda `OnGet` modelu stránky vrací řetězec s hodnotami možností (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="69f5e-323">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="69f5e-324">Když je aplikace spuštěna, metoda `OnGet` vrátí řetězec zobrazující hodnoty třídy dílčích možností:</span><span class="sxs-lookup"><span data-stu-id="69f5e-324">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-injection"></a><span data-ttu-id="69f5e-325">Vkládání možností</span><span class="sxs-lookup"><span data-stu-id="69f5e-325">Options injection</span></span>

<span data-ttu-id="69f5e-326">Vkládání možností je znázorněno jako příklad &num;4 v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="69f5e-326">Options injection is demonstrated as Example &num;4 in the sample app.</span></span>

<span data-ttu-id="69f5e-327">Vložit <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> do:</span><span class="sxs-lookup"><span data-stu-id="69f5e-327">Inject <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into:</span></span>

* <span data-ttu-id="69f5e-328">Stránka Razor nebo zobrazení MVC s direktivou [@inject](xref:mvc/views/razor#inject) Razor</span><span class="sxs-lookup"><span data-stu-id="69f5e-328">A Razor page or MVC view with the [@inject](xref:mvc/views/razor#inject) Razor directive.</span></span>
* <span data-ttu-id="69f5e-329">Model stránky nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="69f5e-329">A page or view model.</span></span>

<span data-ttu-id="69f5e-330">Následující příklad z ukázkové aplikace vloží <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> do modelu stránky (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="69f5e-330">The following example from the sample app injects <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into a page model (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="69f5e-331">Ukázková aplikace ukazuje, jak vložit `IOptionsMonitor<MyOptions>` s direktivou `@inject`:</span><span class="sxs-lookup"><span data-stu-id="69f5e-331">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="69f5e-332">Po spuštění aplikace se hodnoty možností zobrazí na vykreslené stránce:</span><span class="sxs-lookup"><span data-stu-id="69f5e-332">When the app is run, the options values are shown in the rendered page:</span></span>

![Hodnoty možností možnost1: value1_from_json a Možnost2:-1 jsou načteny z modelu a zavstřikem do zobrazení.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="69f5e-334">Znovu načíst konfigurační data pomocí IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="69f5e-334">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="69f5e-335">Opětovné načtení konfiguračních dat pomocí <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> je v ukázkové aplikaci znázorněno v příkladu &num;5.</span><span class="sxs-lookup"><span data-stu-id="69f5e-335">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example &num;5 in the sample app.</span></span>

<span data-ttu-id="69f5e-336">Pomocí <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>jsou možnosti vypočítány jednou za požadavek při otevření a ukládání do mezipaměti po dobu života žádosti.</span><span class="sxs-lookup"><span data-stu-id="69f5e-336">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="69f5e-337">Rozdíl mezi `IOptionsMonitor` a `IOptionsSnapshot` je následující:</span><span class="sxs-lookup"><span data-stu-id="69f5e-337">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="69f5e-338">`IOptionsMonitor` je [Služba typu Singleton](xref:fundamentals/dependency-injection#singleton) , která načte aktuální hodnoty možností kdykoli, což je zvláště užitečné v závislostech singleton.</span><span class="sxs-lookup"><span data-stu-id="69f5e-338">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="69f5e-339">`IOptionsSnapshot` je [Oborová služba](xref:fundamentals/dependency-injection#scoped) a poskytuje snímek možností v době, kdy je objekt `IOptionsSnapshot<T>` vytvořen.</span><span class="sxs-lookup"><span data-stu-id="69f5e-339">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="69f5e-340">Snímky možností jsou navržené pro použití s přechodnými a vymezenými závislostmi.</span><span class="sxs-lookup"><span data-stu-id="69f5e-340">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="69f5e-341">Následující příklad ukazuje, jak se vytvoří nový <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> po změnách *appSettings. JSON* (*pages/index. cshtml. cs*).</span><span class="sxs-lookup"><span data-stu-id="69f5e-341">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="69f5e-342">Více požadavků na server vrací konstantní hodnoty, které poskytuje soubor *appSettings. JSON* , dokud nedojde ke změně souboru a opětovnému načtení konfigurace.</span><span class="sxs-lookup"><span data-stu-id="69f5e-342">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="69f5e-343">Následující obrázek ukazuje počáteční `option1` a hodnoty `option2` načtené ze souboru *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="69f5e-343">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="69f5e-344">Změňte hodnoty v souboru *appSettings. JSON* na `value1_from_json UPDATED` a `200`.</span><span class="sxs-lookup"><span data-stu-id="69f5e-344">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="69f5e-345">Uložte soubor *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="69f5e-345">Save the *appsettings.json* file.</span></span> <span data-ttu-id="69f5e-346">Aktualizujte prohlížeč, aby se zobrazily hodnoty možností, které jsou aktualizované:</span><span class="sxs-lookup"><span data-stu-id="69f5e-346">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="69f5e-347">Podpora pojmenovaných možností s IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="69f5e-347">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="69f5e-348">Podpora pojmenovaných možností s <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> je v ukázkové aplikaci znázorněna jako příklad &num;6.</span><span class="sxs-lookup"><span data-stu-id="69f5e-348">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example &num;6 in the sample app.</span></span>

<span data-ttu-id="69f5e-349">Podpora *pojmenovaných možností* umožňuje aplikaci rozlišovat konfigurace pojmenovaných možností.</span><span class="sxs-lookup"><span data-stu-id="69f5e-349">*Named options* support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="69f5e-350">V ukázkové aplikaci jsou pojmenované možnosti deklarovány pomocí [OptionsServiceCollectionExtensions. Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), který volá [ConfigureNamedOptions >\<TOptions. Konfigurace](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) metody rozšíření:</span><span class="sxs-lookup"><span data-stu-id="69f5e-350">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="69f5e-351">Ukázková aplikace přistupuje k pojmenovaným možnostem pomocí <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="69f5e-351">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="69f5e-352">Když spustíte ukázkovou aplikaci, vrátí se pojmenované možnosti:</span><span class="sxs-lookup"><span data-stu-id="69f5e-352">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="69f5e-353">hodnoty `named_options_1` se poskytují z konfigurace, které se načítají ze souboru *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="69f5e-353">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="69f5e-354">hodnoty `named_options_2` poskytují:</span><span class="sxs-lookup"><span data-stu-id="69f5e-354">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="69f5e-355">`named_options_2` delegát v `ConfigureServices` pro `Option1`.</span><span class="sxs-lookup"><span data-stu-id="69f5e-355">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="69f5e-356">Výchozí hodnota pro `Option2` poskytovaná `MyOptions`ou třídou.</span><span class="sxs-lookup"><span data-stu-id="69f5e-356">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="69f5e-357">Konfigurace všech možností pomocí metody ConfigureAll</span><span class="sxs-lookup"><span data-stu-id="69f5e-357">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="69f5e-358">Nakonfigurujte všechny instance možností pomocí metody <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*>.</span><span class="sxs-lookup"><span data-stu-id="69f5e-358">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="69f5e-359">Následující kód konfiguruje `Option1` pro všechny instance konfigurace se společnou hodnotou.</span><span class="sxs-lookup"><span data-stu-id="69f5e-359">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="69f5e-360">Do metody `Startup.ConfigureServices` přidejte ručně následující kód:</span><span class="sxs-lookup"><span data-stu-id="69f5e-360">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="69f5e-361">Spuštění ukázkové aplikace po přidání kódu vytvoří následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="69f5e-361">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="69f5e-362">Všechny možnosti jsou pojmenované instance.</span><span class="sxs-lookup"><span data-stu-id="69f5e-362">All options are named instances.</span></span> <span data-ttu-id="69f5e-363">Existující instance <xref:Microsoft.Extensions.Options.IConfigureOptions%601> jsou považovány za cílení na instanci `Options.DefaultName`, která je `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="69f5e-363">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="69f5e-364"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> také implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="69f5e-364"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="69f5e-365">Výchozí implementace <xref:Microsoft.Extensions.Options.IOptionsFactory%601> má logiku odpovídajícím způsobem použít.</span><span class="sxs-lookup"><span data-stu-id="69f5e-365">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="69f5e-366">Možnost `null` s názvem slouží k cílení na všechny pojmenované instance místo konkrétní pojmenované instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> a <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> použít tuto konvenci).</span><span class="sxs-lookup"><span data-stu-id="69f5e-366">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="69f5e-367">Rozhraní API pro OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="69f5e-367">OptionsBuilder API</span></span>

<span data-ttu-id="69f5e-368"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> se používá ke konfiguraci `TOptions` instancí.</span><span class="sxs-lookup"><span data-stu-id="69f5e-368"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="69f5e-369">`OptionsBuilder` zjednodušuje vytváření pojmenovaných možností, protože je pouze jedním parametrem počátečního `AddOptions<TOptions>(string optionsName)`ho volání namísto zobrazení ve všech následujících voláních.</span><span class="sxs-lookup"><span data-stu-id="69f5e-369">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="69f5e-370">Ověřování možností a `ConfigureOptions` přetížení, která přijímají závislosti služby, jsou k dispozici pouze prostřednictvím `OptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="69f5e-370">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="69f5e-371">Ke konfiguraci možností použijte DI Services.</span><span class="sxs-lookup"><span data-stu-id="69f5e-371">Use DI services to configure options</span></span>

<span data-ttu-id="69f5e-372">K dalším službám můžete přistupovat pomocí injektáže závislosti při konfiguraci možností dvěma způsoby:</span><span class="sxs-lookup"><span data-stu-id="69f5e-372">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="69f5e-373">Předejte delegáta konfigurace [na](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) [OptionsBuilder\<TOptions >](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="69f5e-373">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="69f5e-374">[OptionsBuilder\<TOptions >](xref:Microsoft.Extensions.Options.OptionsBuilder`1) poskytuje přetížení [Konfigurace](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) , která umožňují použít až pět služeb ke konfiguraci možností:</span><span class="sxs-lookup"><span data-stu-id="69f5e-374">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="69f5e-375">Vytvořte vlastní typ, který implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> nebo <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> a zaregistrujte typ jako službu.</span><span class="sxs-lookup"><span data-stu-id="69f5e-375">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="69f5e-376">Doporučujeme předat delegáta [konfigurace, protože](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)vytvoření služby je složitější.</span><span class="sxs-lookup"><span data-stu-id="69f5e-376">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="69f5e-377">Vytvoření vlastního typu je ekvivalentní s tím, k čemu architektura slouží při použití možnosti [Konfigurovat](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="69f5e-377">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="69f5e-378">Volání metody [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) obsahuje přechodný obecný <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, který má konstruktor, který přijímá obecné zadané typy služeb.</span><span class="sxs-lookup"><span data-stu-id="69f5e-378">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="69f5e-379">Ověřování možností</span><span class="sxs-lookup"><span data-stu-id="69f5e-379">Options validation</span></span>

<span data-ttu-id="69f5e-380">Ověřování možností umožňuje ověřit možnosti při konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="69f5e-380">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="69f5e-381">Zavolejte `Validate` s metodou ověřování, která vrátí `true`, pokud jsou možnosti platné a `false`, pokud nejsou platné:</span><span class="sxs-lookup"><span data-stu-id="69f5e-381">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

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

<span data-ttu-id="69f5e-382">Předchozí příklad nastaví instanci pojmenované možnosti na `optionalOptionsName`.</span><span class="sxs-lookup"><span data-stu-id="69f5e-382">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="69f5e-383">Instance výchozích možností je `Options.DefaultName`.</span><span class="sxs-lookup"><span data-stu-id="69f5e-383">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="69f5e-384">Ověřování se spustí při vytvoření instance Options.</span><span class="sxs-lookup"><span data-stu-id="69f5e-384">Validation runs when the options instance is created.</span></span> <span data-ttu-id="69f5e-385">Při prvním použití vaší instance možností je zaručeno předání ověření.</span><span class="sxs-lookup"><span data-stu-id="69f5e-385">Your options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="69f5e-386">Ověřování možností nechrání proti změnám možností po počáteční konfiguraci a ověření možností.</span><span class="sxs-lookup"><span data-stu-id="69f5e-386">Options validation doesn't guard against options modifications after the options are initially configured and validated.</span></span>

<span data-ttu-id="69f5e-387">Metoda `Validate` přijímá `Func<TOptions, bool>`.</span><span class="sxs-lookup"><span data-stu-id="69f5e-387">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="69f5e-388">K úplnému přizpůsobení ověřování implementujte `IValidateOptions<TOptions>`, která umožňuje:</span><span class="sxs-lookup"><span data-stu-id="69f5e-388">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="69f5e-389">Ověřování více typů možností: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="69f5e-389">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="69f5e-390">Ověření, které závisí na jiném typu možnosti: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="69f5e-390">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="69f5e-391">`IValidateOptions` ověří:</span><span class="sxs-lookup"><span data-stu-id="69f5e-391">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="69f5e-392">Konkrétní instance pojmenované možnosti.</span><span class="sxs-lookup"><span data-stu-id="69f5e-392">A specific named options instance.</span></span>
* <span data-ttu-id="69f5e-393">Všechny možnosti, pokud je `name` `null`.</span><span class="sxs-lookup"><span data-stu-id="69f5e-393">All options when `name` is `null`.</span></span>

<span data-ttu-id="69f5e-394">Vrácení `ValidateOptionsResult` z implementace rozhraní:</span><span class="sxs-lookup"><span data-stu-id="69f5e-394">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="69f5e-395">Ověřování založené na datových anotacích je k dispozici v balíčku [Microsoft. Extensions. Options. DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) voláním metody <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> v `OptionsBuilder<TOptions>`.</span><span class="sxs-lookup"><span data-stu-id="69f5e-395">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="69f5e-396">`Microsoft.Extensions.Options.DataAnnotations` je součástí [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="69f5e-396">`Microsoft.Extensions.Options.DataAnnotations` is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

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

<span data-ttu-id="69f5e-397">Ověření Eager (při spuštění selže) je v budoucí verzi zváženo.</span><span class="sxs-lookup"><span data-stu-id="69f5e-397">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="69f5e-398">Možnosti po konfiguraci</span><span class="sxs-lookup"><span data-stu-id="69f5e-398">Options post-configuration</span></span>

<span data-ttu-id="69f5e-399">Nastavte po konfiguraci <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="69f5e-399">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="69f5e-400">Po dokončení konfigurace <xref:Microsoft.Extensions.Options.IConfigureOptions%601> se spustí po konfiguraci:</span><span class="sxs-lookup"><span data-stu-id="69f5e-400">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="69f5e-401"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> je k dispozici pro nastavení po konfiguraci s názvem:</span><span class="sxs-lookup"><span data-stu-id="69f5e-401"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="69f5e-402">Pomocí <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> vystavte všechny instance konfigurace:</span><span class="sxs-lookup"><span data-stu-id="69f5e-402">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="69f5e-403">Přístup k možnostem při spuštění</span><span class="sxs-lookup"><span data-stu-id="69f5e-403">Accessing options during startup</span></span>

<span data-ttu-id="69f5e-404"><xref:Microsoft.Extensions.Options.IOptions%601> a <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> lze použít v `Startup.Configure`, protože služby jsou vytvořeny před spuštěním metody `Configure`.</span><span class="sxs-lookup"><span data-stu-id="69f5e-404"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="69f5e-405">V `Startup.ConfigureServices`nepoužívejte <xref:Microsoft.Extensions.Options.IOptions%601> ani <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="69f5e-405">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="69f5e-406">Stav nekonzistentních možností může existovat v důsledku řazení registrací služby.</span><span class="sxs-lookup"><span data-stu-id="69f5e-406">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="69f5e-407">Vzor možností nastavení využívá třídy pro reprezentaci skupin souvisejících nastavení.</span><span class="sxs-lookup"><span data-stu-id="69f5e-407">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="69f5e-408">Když jsou [nastavení konfigurace](xref:fundamentals/configuration/index) izolována scénářem do samostatných tříd, aplikace dodržuje dva důležité principy technického řízení:</span><span class="sxs-lookup"><span data-stu-id="69f5e-408">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="69f5e-409">[Princip oddělování rozhraní (ISP) nebo zapouzdření](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; scénáře (třídy), které závisí na nastavení konfigurace, závisí jenom na nastavení konfigurace, která používají.</span><span class="sxs-lookup"><span data-stu-id="69f5e-409">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="69f5e-410">[Oddělení](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) potíží s tím, jak &ndash; nastavení různých částí aplikace nejsou závislá nebo vzájemně závislá na sobě.</span><span class="sxs-lookup"><span data-stu-id="69f5e-410">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="69f5e-411">Možnosti také poskytují mechanismus pro ověření konfiguračních dat.</span><span class="sxs-lookup"><span data-stu-id="69f5e-411">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="69f5e-412">Další informace najdete v části [ověřování možností](#options-validation) .</span><span class="sxs-lookup"><span data-stu-id="69f5e-412">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="69f5e-413">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="69f5e-413">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="69f5e-414">Požadavky</span><span class="sxs-lookup"><span data-stu-id="69f5e-414">Prerequisites</span></span>

<span data-ttu-id="69f5e-415">Odkaz na balíček [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) nebo přidejte odkaz na balíček do balíčku [Microsoft. Extensions. Options. ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) .</span><span class="sxs-lookup"><span data-stu-id="69f5e-415">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="69f5e-416">Možnosti rozhraní</span><span class="sxs-lookup"><span data-stu-id="69f5e-416">Options interfaces</span></span>

<span data-ttu-id="69f5e-417"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> slouží k načtení možností a správě oznámení možností pro `TOptions` instance.</span><span class="sxs-lookup"><span data-stu-id="69f5e-417"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="69f5e-418"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> podporuje následující scénáře:</span><span class="sxs-lookup"><span data-stu-id="69f5e-418"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="69f5e-419">Oznámení změny</span><span class="sxs-lookup"><span data-stu-id="69f5e-419">Change notifications</span></span>
* [<span data-ttu-id="69f5e-420">Pojmenované možnosti</span><span class="sxs-lookup"><span data-stu-id="69f5e-420">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="69f5e-421">Znovu Načítatelné konfigurace</span><span class="sxs-lookup"><span data-stu-id="69f5e-421">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="69f5e-422">Neplatné selektivní možnosti (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="69f5e-422">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="69f5e-423">Scénáře po [konfiguraci](#options-post-configuration) umožňují nastavit nebo změnit možnosti po <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ke konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="69f5e-423">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="69f5e-424"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> zodpovídá za vytváření nových instancí možností.</span><span class="sxs-lookup"><span data-stu-id="69f5e-424"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="69f5e-425">Má jednu <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> metodu.</span><span class="sxs-lookup"><span data-stu-id="69f5e-425">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="69f5e-426">Výchozí implementace přebírá všechny registrované <xref:Microsoft.Extensions.Options.IConfigureOptions%601> a <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> a předá všechny konfigurace a potom po konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="69f5e-426">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="69f5e-427">Rozlišuje mezi <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> a <xref:Microsoft.Extensions.Options.IConfigureOptions%601> a volá pouze příslušné rozhraní.</span><span class="sxs-lookup"><span data-stu-id="69f5e-427">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="69f5e-428"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> používá <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> k ukládání instancí `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="69f5e-428"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="69f5e-429"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> zruší platnost instancí možností v monitorování tak, aby se hodnota přepočítala (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="69f5e-429">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="69f5e-430">Hodnoty lze ručně zavést pomocí <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span><span class="sxs-lookup"><span data-stu-id="69f5e-430">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="69f5e-431">Metoda <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> se používá v případě, že by se všechny pojmenované instance měly znovu vytvořit na vyžádání.</span><span class="sxs-lookup"><span data-stu-id="69f5e-431">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="69f5e-432"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> je užitečná ve scénářích, ve kterých by se měly přepočítat možnosti pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="69f5e-432"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="69f5e-433">Další informace najdete v části [opětovné načtení konfiguračních dat pomocí IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) .</span><span class="sxs-lookup"><span data-stu-id="69f5e-433">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="69f5e-434"><xref:Microsoft.Extensions.Options.IOptions%601> lze použít k podpoře možností.</span><span class="sxs-lookup"><span data-stu-id="69f5e-434"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="69f5e-435"><xref:Microsoft.Extensions.Options.IOptions%601> ale nepodporuje předchozí scénáře <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="69f5e-435">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="69f5e-436">Můžete nadále používat <xref:Microsoft.Extensions.Options.IOptions%601> ve stávajících rozhraních a knihovnách, které již používají rozhraní <xref:Microsoft.Extensions.Options.IOptions%601> a nevyžadují scénáře poskytované <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="69f5e-436">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="69f5e-437">Konfigurace obecných možností</span><span class="sxs-lookup"><span data-stu-id="69f5e-437">General options configuration</span></span>

<span data-ttu-id="69f5e-438">Konfigurace obecných možností se v ukázkové aplikaci ukazuje jako příklad &num;1.</span><span class="sxs-lookup"><span data-stu-id="69f5e-438">General options configuration is demonstrated as Example &num;1 in the sample app.</span></span>

<span data-ttu-id="69f5e-439">Třída Options musí být neabstraktní s veřejným konstruktorem bez parametrů.</span><span class="sxs-lookup"><span data-stu-id="69f5e-439">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="69f5e-440">Následující třída `MyOptions`má dvě vlastnosti, `Option1` a `Option2`.</span><span class="sxs-lookup"><span data-stu-id="69f5e-440">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="69f5e-441">Nastavení výchozích hodnot je volitelné, ale konstruktor třídy v následujícím příkladu nastaví výchozí hodnotu `Option1`.</span><span class="sxs-lookup"><span data-stu-id="69f5e-441">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="69f5e-442">`Option2` má výchozí hodnotu nastavenou pomocí přímé inicializace vlastnosti (*modely/MyOptions. cs*):</span><span class="sxs-lookup"><span data-stu-id="69f5e-442">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="69f5e-443">Třída `MyOptions` je přidána do kontejneru služby s <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> a je svázána s konfigurací:</span><span class="sxs-lookup"><span data-stu-id="69f5e-443">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="69f5e-444">Následující model stránky používá [vkládání závislostí konstruktoru](xref:mvc/controllers/dependency-injection) s <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> pro přístup k nastavení (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="69f5e-444">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="69f5e-445">Soubor *appSettings. JSON* v ukázce určuje hodnoty pro `option1` a `option2`:</span><span class="sxs-lookup"><span data-stu-id="69f5e-445">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="69f5e-446">Při spuštění aplikace vrátí `OnGet` metoda modelu stránky řetězec zobrazující hodnoty třídy možností:</span><span class="sxs-lookup"><span data-stu-id="69f5e-446">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="69f5e-447">Při načítání konfigurace možností pomocí vlastního <xref:System.Configuration.ConfigurationBuilder> v souboru nastavení potvrďte, že je správně nastavená základní cesta:</span><span class="sxs-lookup"><span data-stu-id="69f5e-447">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="69f5e-448">Při načítání konfigurace možností ze souboru nastavení prostřednictvím <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>není nutné explicitně nastavit základní cestu.</span><span class="sxs-lookup"><span data-stu-id="69f5e-448">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="69f5e-449">Konfigurace jednoduchých možností pomocí delegáta</span><span class="sxs-lookup"><span data-stu-id="69f5e-449">Configure simple options with a delegate</span></span>

<span data-ttu-id="69f5e-450">Konfigurace jednoduchých možností s delegátem je v ukázkové aplikaci znázorněna jako příklad &num;2.</span><span class="sxs-lookup"><span data-stu-id="69f5e-450">Configuring simple options with a delegate is demonstrated as Example &num;2 in the sample app.</span></span>

<span data-ttu-id="69f5e-451">K nastavení hodnot možností použijte delegáta.</span><span class="sxs-lookup"><span data-stu-id="69f5e-451">Use a delegate to set options values.</span></span> <span data-ttu-id="69f5e-452">Ukázková aplikace používá třídu `MyOptionsWithDelegateConfig` (*modely/MyOptionsWithDelegateConfig. cs*):</span><span class="sxs-lookup"><span data-stu-id="69f5e-452">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="69f5e-453">V následujícím kódu se do kontejneru služby přidá druhá <xref:Microsoft.Extensions.Options.IConfigureOptions%601> služba.</span><span class="sxs-lookup"><span data-stu-id="69f5e-453">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="69f5e-454">Pomocí delegáta konfiguruje vazbu s `MyOptionsWithDelegateConfig`:</span><span class="sxs-lookup"><span data-stu-id="69f5e-454">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="69f5e-455">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="69f5e-455">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="69f5e-456">Můžete přidat více poskytovatelů konfigurace.</span><span class="sxs-lookup"><span data-stu-id="69f5e-456">You can add multiple configuration providers.</span></span> <span data-ttu-id="69f5e-457">Poskytovatelé konfigurace jsou k dispozici z balíčků NuGet a jsou aplikováni v pořadí, v jakém jsou registrováni.</span><span class="sxs-lookup"><span data-stu-id="69f5e-457">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="69f5e-458">Další informace najdete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="69f5e-458">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="69f5e-459">Každé volání <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> přidá <xref:Microsoft.Extensions.Options.IConfigureOptions%601> službu do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="69f5e-459">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="69f5e-460">V předchozím příkladu jsou hodnoty `Option1` a `Option2` zadány v souboru *appSettings. JSON*, ale hodnoty `Option1` a `Option2` jsou přepsány nakonfigurovaným delegátem.</span><span class="sxs-lookup"><span data-stu-id="69f5e-460">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="69f5e-461">Je-li povolena více než jedna služba konfigurace, je v posledním zdroji konfigurace zadaný *Server WINS* a nastavena hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="69f5e-461">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="69f5e-462">Při spuštění aplikace vrátí `OnGet` metoda modelu stránky řetězec zobrazující hodnoty třídy možností:</span><span class="sxs-lookup"><span data-stu-id="69f5e-462">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="69f5e-463">Konfigurace podNastavení</span><span class="sxs-lookup"><span data-stu-id="69f5e-463">Suboptions configuration</span></span>

<span data-ttu-id="69f5e-464">Konfigurace dílčích možností je v ukázkové aplikaci znázorněna jako příklad &num;3.</span><span class="sxs-lookup"><span data-stu-id="69f5e-464">Suboptions configuration is demonstrated as Example &num;3 in the sample app.</span></span>

<span data-ttu-id="69f5e-465">Aplikace by měly vytvořit třídy možností, které se týkají konkrétních skupin scénářů (tříd) v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="69f5e-465">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="69f5e-466">Části aplikace, které vyžadují konfigurační hodnoty, by měly mít přístup jenom k hodnotám konfigurace, které používají.</span><span class="sxs-lookup"><span data-stu-id="69f5e-466">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="69f5e-467">Při vytváření vazby mezi možnostmi konfigurace je každá vlastnost v typu možnosti vázána na konfigurační klíč formuláře `property[:sub-property:]`.</span><span class="sxs-lookup"><span data-stu-id="69f5e-467">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="69f5e-468">Například vlastnost `MyOptions.Option1` je vázána na klíčovou `Option1`, která je čtena z vlastnosti `option1` v souboru *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="69f5e-468">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="69f5e-469">V následujícím kódu je do kontejneru služby přidána třetí <xref:Microsoft.Extensions.Options.IConfigureOptions%601> služba.</span><span class="sxs-lookup"><span data-stu-id="69f5e-469">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="69f5e-470">Vytvoří vazby `MySubOptions` k oddílu `subsection` souboru *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="69f5e-470">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="69f5e-471">Metoda `GetSection` vyžaduje obor názvů <xref:Microsoft.Extensions.Configuration?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="69f5e-471">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="69f5e-472">Soubor *appSettings. JSON* ve vzorci definuje `subsection`ho člena s klíči pro `suboption1` a `suboption2`:</span><span class="sxs-lookup"><span data-stu-id="69f5e-472">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="69f5e-473">Třída `MySubOptions` definuje vlastnosti, `SubOption1` a `SubOption2`pro uchovávání hodnot možností (*modely/MySubOptions. cs*):</span><span class="sxs-lookup"><span data-stu-id="69f5e-473">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="69f5e-474">Metoda `OnGet` modelu stránky vrací řetězec s hodnotami možností (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="69f5e-474">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="69f5e-475">Když je aplikace spuštěna, metoda `OnGet` vrátí řetězec zobrazující hodnoty třídy dílčích možností:</span><span class="sxs-lookup"><span data-stu-id="69f5e-475">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="69f5e-476">Možnosti poskytované modelem zobrazení nebo vkládáním přímých zobrazení</span><span class="sxs-lookup"><span data-stu-id="69f5e-476">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="69f5e-477">Možnosti poskytované modelem zobrazení nebo vkládáním přímých zobrazení jsou v ukázkové aplikaci znázorněné jako příklad &num;4.</span><span class="sxs-lookup"><span data-stu-id="69f5e-477">Options provided by a view model or with direct view injection is demonstrated as Example &num;4 in the sample app.</span></span>

<span data-ttu-id="69f5e-478">Možnosti lze zadat v modelu zobrazení nebo vložením <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> přímo do zobrazení (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="69f5e-478">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="69f5e-479">Ukázková aplikace ukazuje, jak vložit `IOptionsMonitor<MyOptions>` s direktivou `@inject`:</span><span class="sxs-lookup"><span data-stu-id="69f5e-479">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="69f5e-480">Po spuštění aplikace se hodnoty možností zobrazí na vykreslené stránce:</span><span class="sxs-lookup"><span data-stu-id="69f5e-480">When the app is run, the options values are shown in the rendered page:</span></span>

![Hodnoty možností možnost1: value1_from_json a Možnost2:-1 jsou načteny z modelu a zavstřikem do zobrazení.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="69f5e-482">Znovu načíst konfigurační data pomocí IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="69f5e-482">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="69f5e-483">Opětovné načtení konfiguračních dat pomocí <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> je v ukázkové aplikaci znázorněno v příkladu &num;5.</span><span class="sxs-lookup"><span data-stu-id="69f5e-483">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example &num;5 in the sample app.</span></span>

<span data-ttu-id="69f5e-484"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> podporuje opětovné načítání možností s minimální režií zpracování.</span><span class="sxs-lookup"><span data-stu-id="69f5e-484"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="69f5e-485">Možnosti jsou vypočítány jednou za požadavek při otevření a ukládání do mezipaměti po dobu života žádosti.</span><span class="sxs-lookup"><span data-stu-id="69f5e-485">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="69f5e-486">Následující příklad ukazuje, jak se vytvoří nový <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> po změnách *appSettings. JSON* (*pages/index. cshtml. cs*).</span><span class="sxs-lookup"><span data-stu-id="69f5e-486">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="69f5e-487">Více požadavků na server vrací konstantní hodnoty, které poskytuje soubor *appSettings. JSON* , dokud nedojde ke změně souboru a opětovnému načtení konfigurace.</span><span class="sxs-lookup"><span data-stu-id="69f5e-487">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="69f5e-488">Následující obrázek ukazuje počáteční `option1` a hodnoty `option2` načtené ze souboru *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="69f5e-488">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="69f5e-489">Změňte hodnoty v souboru *appSettings. JSON* na `value1_from_json UPDATED` a `200`.</span><span class="sxs-lookup"><span data-stu-id="69f5e-489">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="69f5e-490">Uložte soubor *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="69f5e-490">Save the *appsettings.json* file.</span></span> <span data-ttu-id="69f5e-491">Aktualizujte prohlížeč, aby se zobrazily hodnoty možností, které jsou aktualizované:</span><span class="sxs-lookup"><span data-stu-id="69f5e-491">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="69f5e-492">Podpora pojmenovaných možností s IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="69f5e-492">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="69f5e-493">Podpora pojmenovaných možností s <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> je v ukázkové aplikaci znázorněna jako příklad &num;6.</span><span class="sxs-lookup"><span data-stu-id="69f5e-493">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example &num;6 in the sample app.</span></span>

<span data-ttu-id="69f5e-494">Podpora *pojmenovaných možností* umožňuje aplikaci rozlišovat konfigurace pojmenovaných možností.</span><span class="sxs-lookup"><span data-stu-id="69f5e-494">*Named options* support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="69f5e-495">V ukázkové aplikaci jsou pojmenované možnosti deklarovány pomocí [OptionsServiceCollectionExtensions. Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), který volá [ConfigureNamedOptions >\<TOptions. Konfigurace](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) metody rozšíření:</span><span class="sxs-lookup"><span data-stu-id="69f5e-495">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="69f5e-496">Ukázková aplikace přistupuje k pojmenovaným možnostem pomocí <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="69f5e-496">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="69f5e-497">Když spustíte ukázkovou aplikaci, vrátí se pojmenované možnosti:</span><span class="sxs-lookup"><span data-stu-id="69f5e-497">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="69f5e-498">hodnoty `named_options_1` se poskytují z konfigurace, které se načítají ze souboru *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="69f5e-498">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="69f5e-499">hodnoty `named_options_2` poskytují:</span><span class="sxs-lookup"><span data-stu-id="69f5e-499">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="69f5e-500">`named_options_2` delegát v `ConfigureServices` pro `Option1`.</span><span class="sxs-lookup"><span data-stu-id="69f5e-500">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="69f5e-501">Výchozí hodnota pro `Option2` poskytovaná `MyOptions`ou třídou.</span><span class="sxs-lookup"><span data-stu-id="69f5e-501">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="69f5e-502">Konfigurace všech možností pomocí metody ConfigureAll</span><span class="sxs-lookup"><span data-stu-id="69f5e-502">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="69f5e-503">Nakonfigurujte všechny instance možností pomocí metody <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*>.</span><span class="sxs-lookup"><span data-stu-id="69f5e-503">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="69f5e-504">Následující kód konfiguruje `Option1` pro všechny instance konfigurace se společnou hodnotou.</span><span class="sxs-lookup"><span data-stu-id="69f5e-504">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="69f5e-505">Do metody `Startup.ConfigureServices` přidejte ručně následující kód:</span><span class="sxs-lookup"><span data-stu-id="69f5e-505">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="69f5e-506">Spuštění ukázkové aplikace po přidání kódu vytvoří následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="69f5e-506">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="69f5e-507">Všechny možnosti jsou pojmenované instance.</span><span class="sxs-lookup"><span data-stu-id="69f5e-507">All options are named instances.</span></span> <span data-ttu-id="69f5e-508">Existující instance <xref:Microsoft.Extensions.Options.IConfigureOptions%601> jsou považovány za cílení na instanci `Options.DefaultName`, která je `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="69f5e-508">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="69f5e-509"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> také implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="69f5e-509"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="69f5e-510">Výchozí implementace <xref:Microsoft.Extensions.Options.IOptionsFactory%601> má logiku odpovídajícím způsobem použít.</span><span class="sxs-lookup"><span data-stu-id="69f5e-510">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="69f5e-511">Možnost `null` s názvem slouží k cílení na všechny pojmenované instance místo konkrétní pojmenované instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> a <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> použít tuto konvenci).</span><span class="sxs-lookup"><span data-stu-id="69f5e-511">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="69f5e-512">Rozhraní API pro OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="69f5e-512">OptionsBuilder API</span></span>

<span data-ttu-id="69f5e-513"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> se používá ke konfiguraci `TOptions` instancí.</span><span class="sxs-lookup"><span data-stu-id="69f5e-513"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="69f5e-514">`OptionsBuilder` zjednodušuje vytváření pojmenovaných možností, protože je pouze jedním parametrem počátečního `AddOptions<TOptions>(string optionsName)`ho volání namísto zobrazení ve všech následujících voláních.</span><span class="sxs-lookup"><span data-stu-id="69f5e-514">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="69f5e-515">Ověřování možností a `ConfigureOptions` přetížení, která přijímají závislosti služby, jsou k dispozici pouze prostřednictvím `OptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="69f5e-515">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="69f5e-516">Ke konfiguraci možností použijte DI Services.</span><span class="sxs-lookup"><span data-stu-id="69f5e-516">Use DI services to configure options</span></span>

<span data-ttu-id="69f5e-517">K dalším službám můžete přistupovat pomocí injektáže závislosti při konfiguraci možností dvěma způsoby:</span><span class="sxs-lookup"><span data-stu-id="69f5e-517">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="69f5e-518">Předejte delegáta konfigurace [na](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) [OptionsBuilder\<TOptions >](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="69f5e-518">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="69f5e-519">[OptionsBuilder\<TOptions >](xref:Microsoft.Extensions.Options.OptionsBuilder`1) poskytuje přetížení [Konfigurace](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) , která umožňují použít až pět služeb ke konfiguraci možností:</span><span class="sxs-lookup"><span data-stu-id="69f5e-519">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="69f5e-520">Vytvořte vlastní typ, který implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> nebo <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> a zaregistrujte typ jako službu.</span><span class="sxs-lookup"><span data-stu-id="69f5e-520">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="69f5e-521">Doporučujeme předat delegáta [konfigurace, protože](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)vytvoření služby je složitější.</span><span class="sxs-lookup"><span data-stu-id="69f5e-521">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="69f5e-522">Vytvoření vlastního typu je ekvivalentní s tím, k čemu architektura slouží při použití možnosti [Konfigurovat](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="69f5e-522">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="69f5e-523">Volání metody [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) obsahuje přechodný obecný <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, který má konstruktor, který přijímá obecné zadané typy služeb.</span><span class="sxs-lookup"><span data-stu-id="69f5e-523">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-post-configuration"></a><span data-ttu-id="69f5e-524">Možnosti po konfiguraci</span><span class="sxs-lookup"><span data-stu-id="69f5e-524">Options post-configuration</span></span>

<span data-ttu-id="69f5e-525">Nastavte po konfiguraci <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="69f5e-525">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="69f5e-526">Po dokončení konfigurace <xref:Microsoft.Extensions.Options.IConfigureOptions%601> se spustí po konfiguraci:</span><span class="sxs-lookup"><span data-stu-id="69f5e-526">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="69f5e-527"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> je k dispozici pro nastavení po konfiguraci s názvem:</span><span class="sxs-lookup"><span data-stu-id="69f5e-527"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="69f5e-528">Pomocí <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> vystavte všechny instance konfigurace:</span><span class="sxs-lookup"><span data-stu-id="69f5e-528">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="69f5e-529">Přístup k možnostem při spuštění</span><span class="sxs-lookup"><span data-stu-id="69f5e-529">Accessing options during startup</span></span>

<span data-ttu-id="69f5e-530"><xref:Microsoft.Extensions.Options.IOptions%601> a <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> lze použít v `Startup.Configure`, protože služby jsou vytvořeny před spuštěním metody `Configure`.</span><span class="sxs-lookup"><span data-stu-id="69f5e-530"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="69f5e-531">V `Startup.ConfigureServices`nepoužívejte <xref:Microsoft.Extensions.Options.IOptions%601> ani <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="69f5e-531">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="69f5e-532">Stav nekonzistentních možností může existovat v důsledku řazení registrací služby.</span><span class="sxs-lookup"><span data-stu-id="69f5e-532">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="69f5e-533">Další zdroje informací:</span><span class="sxs-lookup"><span data-stu-id="69f5e-533">Additional resources</span></span>

* <xref:fundamentals/configuration/index>

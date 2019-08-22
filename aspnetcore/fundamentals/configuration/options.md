---
title: Vzor možností v ASP.NET Core
author: guardrex
description: Zjistěte, jak pomocí vzoru možností znázornit skupiny souvisejících nastavení v aplikacích ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/19/2019
uid: fundamentals/configuration/options
ms.openlocfilehash: 22dde4b05ea20fedb696c6a4b144755a957e8c0d
ms.sourcegitcommit: 41f2c1a6b316e6e368a4fd27a8b18d157cef91e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2019
ms.locfileid: "69886378"
---
# <a name="options-pattern-in-aspnet-core"></a><span data-ttu-id="1877e-103">Vzor možností v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1877e-103">Options pattern in ASP.NET Core</span></span>

<span data-ttu-id="1877e-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1877e-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1877e-105">Vzor možností nastavení využívá třídy pro reprezentaci skupin souvisejících nastavení.</span><span class="sxs-lookup"><span data-stu-id="1877e-105">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="1877e-106">Když jsou [nastavení konfigurace](xref:fundamentals/configuration/index) izolována scénářem do samostatných tříd, aplikace dodržuje dva důležité principy technického řízení:</span><span class="sxs-lookup"><span data-stu-id="1877e-106">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="1877e-107">[Princip oddělení (ISP) nebo](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; scénářů zapouzdření (třídy) závislých na nastavení konfigurace závisí jenom na nastavení konfigurace, která používají.</span><span class="sxs-lookup"><span data-stu-id="1877e-107">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="1877e-108">[Oddělení obav](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Nastavení pro různé části aplikace nejsou závislá ani vzájemně nezávislá.</span><span class="sxs-lookup"><span data-stu-id="1877e-108">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="1877e-109">Možnosti také poskytují mechanismus pro ověření konfiguračních dat.</span><span class="sxs-lookup"><span data-stu-id="1877e-109">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="1877e-110">Další informace najdete v části [ověřování možností](#options-validation) .</span><span class="sxs-lookup"><span data-stu-id="1877e-110">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="1877e-111">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1877e-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1877e-112">Požadavky</span><span class="sxs-lookup"><span data-stu-id="1877e-112">Prerequisites</span></span>

<span data-ttu-id="1877e-113">Odkaz na balíček [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) nebo přidejte odkaz na balíček do balíčku [Microsoft. Extensions. Options. ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) .</span><span class="sxs-lookup"><span data-stu-id="1877e-113">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="1877e-114">Možnosti rozhraní</span><span class="sxs-lookup"><span data-stu-id="1877e-114">Options interfaces</span></span>

<span data-ttu-id="1877e-115"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>slouží k načtení možností a správě oznámení možností pro `TOptions` instance.</span><span class="sxs-lookup"><span data-stu-id="1877e-115"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="1877e-116"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>podporuje následující scénáře:</span><span class="sxs-lookup"><span data-stu-id="1877e-116"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="1877e-117">Oznámení změny</span><span class="sxs-lookup"><span data-stu-id="1877e-117">Change notifications</span></span>
* [<span data-ttu-id="1877e-118">Pojmenované možnosti</span><span class="sxs-lookup"><span data-stu-id="1877e-118">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="1877e-119">Znovu Načítatelné konfigurace</span><span class="sxs-lookup"><span data-stu-id="1877e-119">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="1877e-120">Neplatných selektivních možností<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>()</span><span class="sxs-lookup"><span data-stu-id="1877e-120">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="1877e-121">Scénáře po [konfiguraci](#options-post-configuration) umožňují nastavit nebo změnit možnosti po výskytu všech <xref:Microsoft.Extensions.Options.IConfigureOptions%601> konfigurací.</span><span class="sxs-lookup"><span data-stu-id="1877e-121">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="1877e-122"><xref:Microsoft.Extensions.Options.IOptionsFactory%601>zodpovídá za vytváření nových instancí možností.</span><span class="sxs-lookup"><span data-stu-id="1877e-122"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="1877e-123">Má jedinou <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> metodu.</span><span class="sxs-lookup"><span data-stu-id="1877e-123">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="1877e-124">Výchozí implementace přebírá všechny <xref:Microsoft.Extensions.Options.IConfigureOptions%601> konfigurace a <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> a spustí nejprve všechny konfigurace, následované po konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="1877e-124">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="1877e-125">Rozlišuje mezi <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> a <xref:Microsoft.Extensions.Options.IConfigureOptions%601> a volá pouze příslušné rozhraní.</span><span class="sxs-lookup"><span data-stu-id="1877e-125">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="1877e-126"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>je používána <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> pro ukládání instancí `TOptions` do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="1877e-126"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="1877e-127">Zruší platnost instancí možností v monitorování tak, aby se hodnota přepočítala (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>). <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601></span><span class="sxs-lookup"><span data-stu-id="1877e-127">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="1877e-128">Hodnoty lze ručně zavést pomocí <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span><span class="sxs-lookup"><span data-stu-id="1877e-128">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="1877e-129">Metoda <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> se používá v případě, že by měly být všechny pojmenované instance znovu vytvořeny na vyžádání.</span><span class="sxs-lookup"><span data-stu-id="1877e-129">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="1877e-130"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>je užitečná ve scénářích, kde by se měly přepočítat možnosti pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="1877e-130"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="1877e-131">Další informace najdete v části [opětovné načtení konfiguračních dat pomocí IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) .</span><span class="sxs-lookup"><span data-stu-id="1877e-131">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="1877e-132"><xref:Microsoft.Extensions.Options.IOptions%601>dá se použít k podpoře možností.</span><span class="sxs-lookup"><span data-stu-id="1877e-132"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="1877e-133">Nicméně nepodporuje předchozí <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>scénáře. <xref:Microsoft.Extensions.Options.IOptions%601></span><span class="sxs-lookup"><span data-stu-id="1877e-133">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="1877e-134">Můžete pokračovat v používání <xref:Microsoft.Extensions.Options.IOptions%601> existujících architektur a knihoven, které již <xref:Microsoft.Extensions.Options.IOptions%601> rozhraní používají, a <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>nevyžadují scénáře, které poskytuje.</span><span class="sxs-lookup"><span data-stu-id="1877e-134">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="1877e-135">Konfigurace obecných možností</span><span class="sxs-lookup"><span data-stu-id="1877e-135">General options configuration</span></span>

<span data-ttu-id="1877e-136">Konfigurace obecných možností se v ukázkové aplikaci &num;ukazuje jako příklad 1.</span><span class="sxs-lookup"><span data-stu-id="1877e-136">General options configuration is demonstrated as Example &num;1 in the sample app.</span></span>

<span data-ttu-id="1877e-137">Třída Options musí být neabstraktní s veřejným konstruktorem bez parametrů.</span><span class="sxs-lookup"><span data-stu-id="1877e-137">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="1877e-138">Následující třída `MyOptions`má dvě vlastnosti, `Option1` a `Option2`.</span><span class="sxs-lookup"><span data-stu-id="1877e-138">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="1877e-139">Nastavení výchozích hodnot je volitelné, ale konstruktor třídy v následujícím příkladu nastaví výchozí hodnotu `Option1`.</span><span class="sxs-lookup"><span data-stu-id="1877e-139">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="1877e-140">`Option2`má výchozí hodnotu nastavenou pomocí přímé inicializace vlastnosti (*modely/MyOptions. cs*):</span><span class="sxs-lookup"><span data-stu-id="1877e-140">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="1877e-141">Třída je přidána do kontejneru služby s <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> konfigurací a je vázána na konfiguraci: `MyOptions`</span><span class="sxs-lookup"><span data-stu-id="1877e-141">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="1877e-142">Následující model stránky používá [vkládání závislostí konstruktoru](xref:mvc/controllers/dependency-injection) s <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> pro přístup k nastavení (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="1877e-142">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="1877e-143">Soubor *appSettings. JSON* v ukázce určuje hodnoty pro `option1` a: `option2`</span><span class="sxs-lookup"><span data-stu-id="1877e-143">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/3.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="1877e-144">Při spuštění aplikace vrátí `OnGet` metoda modelu stránky řetězec zobrazující hodnoty třídy možností:</span><span class="sxs-lookup"><span data-stu-id="1877e-144">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="1877e-145">Když použijete vlastní <xref:System.Configuration.ConfigurationBuilder> nastavení pro načtení konfigurace možností z konfiguračního souboru, zkontrolujte, jestli je správně nastavená základní cesta:</span><span class="sxs-lookup"><span data-stu-id="1877e-145">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="1877e-146">Při načítání konfigurace možností ze souboru s nastavením prostřednictvím <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>je explicitně nutné nastavit základní cestu.</span><span class="sxs-lookup"><span data-stu-id="1877e-146">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="1877e-147">Konfigurace jednoduchých možností pomocí delegáta</span><span class="sxs-lookup"><span data-stu-id="1877e-147">Configure simple options with a delegate</span></span>

<span data-ttu-id="1877e-148">Konfigurace jednoduchých možností s delegátem je znázorněna jako &num;příklad 2 v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="1877e-148">Configuring simple options with a delegate is demonstrated as Example &num;2 in the sample app.</span></span>

<span data-ttu-id="1877e-149">K nastavení hodnot možností použijte delegáta.</span><span class="sxs-lookup"><span data-stu-id="1877e-149">Use a delegate to set options values.</span></span> <span data-ttu-id="1877e-150">Ukázková aplikace používá `MyOptionsWithDelegateConfig` třídu (*modely/MyOptionsWithDelegateConfig. cs*):</span><span class="sxs-lookup"><span data-stu-id="1877e-150">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="1877e-151">V následujícím kódu je do kontejneru služby <xref:Microsoft.Extensions.Options.IConfigureOptions%601> přidána druhá služba.</span><span class="sxs-lookup"><span data-stu-id="1877e-151">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="1877e-152">Pomocí delegáta konfiguruje vazbu s `MyOptionsWithDelegateConfig`:</span><span class="sxs-lookup"><span data-stu-id="1877e-152">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="1877e-153">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="1877e-153">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="1877e-154">Můžete přidat více poskytovatelů konfigurace.</span><span class="sxs-lookup"><span data-stu-id="1877e-154">You can add multiple configuration providers.</span></span> <span data-ttu-id="1877e-155">Poskytovatelé konfigurace jsou k dispozici z balíčků NuGet a jsou aplikováni v pořadí, v jakém jsou registrováni.</span><span class="sxs-lookup"><span data-stu-id="1877e-155">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="1877e-156">Další informace naleznete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="1877e-156">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="1877e-157">Každé volání <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> <xref:Microsoft.Extensions.Options.IConfigureOptions%601> přidá službu do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="1877e-157">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="1877e-158">V předchozím příkladu jsou `Option1` hodnoty a `Option2` zadány v souboru `Option1` *appSettings. JSON*, ale hodnoty a `Option2` jsou přepsány nakonfigurovaným delegátem.</span><span class="sxs-lookup"><span data-stu-id="1877e-158">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="1877e-159">Je-li povolena více než jedna služba konfigurace, je v posledním zdroji konfigurace zadaný *Server WINS* a nastavena hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="1877e-159">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="1877e-160">Při spuštění aplikace vrátí `OnGet` metoda modelu stránky řetězec zobrazující hodnoty třídy možností:</span><span class="sxs-lookup"><span data-stu-id="1877e-160">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="1877e-161">Konfigurace podNastavení</span><span class="sxs-lookup"><span data-stu-id="1877e-161">Suboptions configuration</span></span>

<span data-ttu-id="1877e-162">Konfigurace dílčích možností je v ukázkové &num;aplikaci znázorněna jako příklad 3.</span><span class="sxs-lookup"><span data-stu-id="1877e-162">Suboptions configuration is demonstrated as Example &num;3 in the sample app.</span></span>

<span data-ttu-id="1877e-163">Aplikace by měly vytvořit třídy možností, které se týkají konkrétních skupin scénářů (tříd) v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="1877e-163">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="1877e-164">Části aplikace, které vyžadují konfigurační hodnoty, by měly mít přístup jenom k hodnotám konfigurace, které používají.</span><span class="sxs-lookup"><span data-stu-id="1877e-164">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="1877e-165">Při vytváření vazby mezi možnostmi konfigurace jsou jednotlivé vlastnosti v typu možnosti vázány na konfigurační klíč formuláře `property[:sub-property:]`.</span><span class="sxs-lookup"><span data-stu-id="1877e-165">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="1877e-166">Například `MyOptions.Option1` vlastnost je svázána s klíčem `Option1`, `option1` který je načten z vlastnosti v souboru *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="1877e-166">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="1877e-167">V následujícím kódu je do kontejneru služby <xref:Microsoft.Extensions.Options.IConfigureOptions%601> přidána třetí služba.</span><span class="sxs-lookup"><span data-stu-id="1877e-167">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="1877e-168">Váže `MySubOptions` se k části `subsection` souboru *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="1877e-168">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="1877e-169">Metoda rozšíření vyžaduje balíček [Microsoft. Extensions. Options. ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) NuGet. `GetSection`</span><span class="sxs-lookup"><span data-stu-id="1877e-169">The `GetSection` extension method requires the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) NuGet package.</span></span> <span data-ttu-id="1877e-170">Pokud aplikace používá [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 nebo novější), balíček je automaticky zahrnutý.</span><span class="sxs-lookup"><span data-stu-id="1877e-170">If the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later), the package is automatically included.</span></span>

<span data-ttu-id="1877e-171">Soubor *appSettings. JSON* ukázky definuje `subsection` člena s klíči pro `suboption1` a `suboption2`:</span><span class="sxs-lookup"><span data-stu-id="1877e-171">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/3.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="1877e-172">Třída definuje `SubOption1` vlastnosti a`SubOption2`, aby obsahovala hodnoty možností (*modely/MySubOptions. cs*): `MySubOptions`</span><span class="sxs-lookup"><span data-stu-id="1877e-172">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="1877e-173">`OnGet` Metoda modelu stránky vrátí řetězec s hodnotami možností (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="1877e-173">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="1877e-174">Při spuštění `OnGet` aplikace vrátí metoda řetězec zobrazující hodnoty třídy dílčích možností:</span><span class="sxs-lookup"><span data-stu-id="1877e-174">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="1877e-175">Možnosti poskytované modelem zobrazení nebo vkládáním přímých zobrazení</span><span class="sxs-lookup"><span data-stu-id="1877e-175">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="1877e-176">Možnosti poskytované modelem zobrazení nebo vkládáním přímých zobrazení jsou v ukázkové aplikaci znázorněné jako příklad &num;4.</span><span class="sxs-lookup"><span data-stu-id="1877e-176">Options provided by a view model or with direct view injection is demonstrated as Example &num;4 in the sample app.</span></span>

<span data-ttu-id="1877e-177">Možnosti lze zadat v modelu zobrazení nebo <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> přímým vložením do zobrazení (Pages */index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="1877e-177">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="1877e-178">Ukázková aplikace ukazuje, jak vložit `IOptionsMonitor<MyOptions>` `@inject` pomocí direktivy:</span><span class="sxs-lookup"><span data-stu-id="1877e-178">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/3.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="1877e-179">Po spuštění aplikace se hodnoty možností zobrazí na vykreslené stránce:</span><span class="sxs-lookup"><span data-stu-id="1877e-179">When the app is run, the options values are shown in the rendered page:</span></span>

![Hodnoty možností možnost1: value1_from_json a Možnost2:-1 jsou načteny z modelu a vložením do zobrazení.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="1877e-181">Znovu načíst konfigurační data pomocí IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="1877e-181">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="1877e-182">Opětovné načtení konfiguračních dat pomocí <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> je v ukázkové aplikaci znázorněno v příkladu &num;5.</span><span class="sxs-lookup"><span data-stu-id="1877e-182">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example &num;5 in the sample app.</span></span>

<span data-ttu-id="1877e-183"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>podporuje opětovné načítání možností s minimální režií zpracování.</span><span class="sxs-lookup"><span data-stu-id="1877e-183"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="1877e-184">Možnosti jsou vypočítány jednou za požadavek při otevření a ukládání do mezipaměti po dobu života žádosti.</span><span class="sxs-lookup"><span data-stu-id="1877e-184">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="1877e-185">Následující příklad ukazuje, jak se vytvoří <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> nový po změnách *appSettings. JSON* (*pages/index. cshtml. cs*).</span><span class="sxs-lookup"><span data-stu-id="1877e-185">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="1877e-186">Více požadavků na server vrací konstantní hodnoty, které poskytuje soubor *appSettings. JSON* , dokud nedojde ke změně souboru a opětovnému načtení konfigurace.</span><span class="sxs-lookup"><span data-stu-id="1877e-186">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="1877e-187">Následující obrázek ukazuje počáteční `option1` hodnoty a `option2` hodnoty načtené ze souboru *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="1877e-187">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="1877e-188">Změňte hodnoty v souboru *appSettings. JSON* na `value1_from_json UPDATED` a `200`.</span><span class="sxs-lookup"><span data-stu-id="1877e-188">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="1877e-189">Uložte soubor *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="1877e-189">Save the *appsettings.json* file.</span></span> <span data-ttu-id="1877e-190">Aktualizujte prohlížeč, aby se zobrazily hodnoty možností, které jsou aktualizované:</span><span class="sxs-lookup"><span data-stu-id="1877e-190">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="1877e-191">Podpora pojmenovaných možností s IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="1877e-191">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="1877e-192">Pojmenovaná podpora <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> s názvem je znázorněna jako příklad &num;6 v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="1877e-192">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example &num;6 in the sample app.</span></span>

<span data-ttu-id="1877e-193">Podpora pojmenovaných *možností* umožňuje aplikaci rozlišovat konfigurace pojmenovaných možností.</span><span class="sxs-lookup"><span data-stu-id="1877e-193">*Named options* support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="1877e-194">V ukázkové aplikaci jsou pojmenované možnosti deklarovány pomocí [OptionsServiceCollectionExtensions. Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), který volá [ConfigureNamedOptions\<TOptions >. Konfigurace](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) metody rozšíření:</span><span class="sxs-lookup"><span data-stu-id="1877e-194">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="1877e-195">Ukázková aplikace přistupuje k pojmenovaným možnostem <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="1877e-195">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="1877e-196">Když spustíte ukázkovou aplikaci, vrátí se pojmenované možnosti:</span><span class="sxs-lookup"><span data-stu-id="1877e-196">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="1877e-197">`named_options_1`hodnoty se poskytují z konfigurace, které se načítají ze souboru *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="1877e-197">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="1877e-198">`named_options_2`hodnoty poskytuje:</span><span class="sxs-lookup"><span data-stu-id="1877e-198">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="1877e-199">Delegát v `ConfigureServices` pro`Option1`. `named_options_2`</span><span class="sxs-lookup"><span data-stu-id="1877e-199">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="1877e-200">Výchozí hodnota `Option2` poskytnutá `MyOptions` třídou</span><span class="sxs-lookup"><span data-stu-id="1877e-200">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="1877e-201">Konfigurace všech možností pomocí metody ConfigureAll</span><span class="sxs-lookup"><span data-stu-id="1877e-201">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="1877e-202">Nakonfigurujte všechny instance možností pomocí <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> metody.</span><span class="sxs-lookup"><span data-stu-id="1877e-202">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="1877e-203">Následující kód konfiguruje `Option1` pro všechny instance konfigurace se společnou hodnotou.</span><span class="sxs-lookup"><span data-stu-id="1877e-203">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="1877e-204">Do `Startup.ConfigureServices` metody přidejte následující kód ručně:</span><span class="sxs-lookup"><span data-stu-id="1877e-204">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="1877e-205">Spuštění ukázkové aplikace po přidání kódu vytvoří následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="1877e-205">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="1877e-206">Všechny možnosti jsou pojmenované instance.</span><span class="sxs-lookup"><span data-stu-id="1877e-206">All options are named instances.</span></span> <span data-ttu-id="1877e-207">Existující <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instance jsou považovány za `Options.DefaultName` cílení instance, což `string.Empty`je.</span><span class="sxs-lookup"><span data-stu-id="1877e-207">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="1877e-208"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>také implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="1877e-208"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="1877e-209">Výchozí implementace <xref:Microsoft.Extensions.Options.IOptionsFactory%601> pro má logiku odpovídajícím způsobem použít.</span><span class="sxs-lookup"><span data-stu-id="1877e-209">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="1877e-210">Pojmenovaná možnost se používá pro cílení na všechny pojmenované instance místo konkrétní pojmenované instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> a <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> použití této konvence). `null`</span><span class="sxs-lookup"><span data-stu-id="1877e-210">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="1877e-211">Rozhraní API pro OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="1877e-211">OptionsBuilder API</span></span>

<span data-ttu-id="1877e-212"><xref:Microsoft.Extensions.Options.OptionsBuilder%601>se používá ke konfiguraci `TOptions` instancí.</span><span class="sxs-lookup"><span data-stu-id="1877e-212"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="1877e-213">`OptionsBuilder`zjednodušuje vytváření pojmenovaných možností, protože je jenom jedním parametrem počátečního `AddOptions<TOptions>(string optionsName)` volání, takže se neobjeví ve všech dalších voláních.</span><span class="sxs-lookup"><span data-stu-id="1877e-213">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="1877e-214">Ověřování možností a `ConfigureOptions` přetížení, která přijímají závislosti služby, jsou k dispozici `OptionsBuilder`pouze prostřednictvím.</span><span class="sxs-lookup"><span data-stu-id="1877e-214">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="1877e-215">Ke konfiguraci možností použijte DI Services.</span><span class="sxs-lookup"><span data-stu-id="1877e-215">Use DI services to configure options</span></span>

<span data-ttu-id="1877e-216">K dalším službám můžete přistupovat pomocí injektáže závislosti při konfiguraci možností dvěma způsoby:</span><span class="sxs-lookup"><span data-stu-id="1877e-216">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="1877e-217">Předejte delegáta konfigurace [](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) na [\<OptionsBuilder TOptions >](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="1877e-217">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="1877e-218">[OptionsBuilder\<TOptions >](xref:Microsoft.Extensions.Options.OptionsBuilder`1) poskytuje přetížení [Konfigurace](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) , která umožňují použít až pět služeb ke konfiguraci možností:</span><span class="sxs-lookup"><span data-stu-id="1877e-218">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="1877e-219">Vytvořte vlastní typ, který implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> nebo <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> a zaregistruje typ jako službu.</span><span class="sxs-lookup"><span data-stu-id="1877e-219">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="1877e-220">Doporučujeme předat delegáta konfigurace, protože [](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)vytvoření služby je složitější.</span><span class="sxs-lookup"><span data-stu-id="1877e-220">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="1877e-221">Vytvoření vlastního typu je ekvivalentní s tím, k čemu architektura slouží při použití možnosti [Konfigurovat](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="1877e-221">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="1877e-222">Volání [](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) metody Configure provede přechodný <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>obecný, který má konstruktor, který přijímá obecné zadané typy služeb.</span><span class="sxs-lookup"><span data-stu-id="1877e-222">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="1877e-223">Ověřování možností</span><span class="sxs-lookup"><span data-stu-id="1877e-223">Options validation</span></span>

<span data-ttu-id="1877e-224">Ověřování možností umožňuje ověřit možnosti při konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="1877e-224">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="1877e-225">Zavolejte `Validate` na metodu ověřování, která vrátí `true` , pokud jsou možnosti platné `false` , a pokud nejsou platné:</span><span class="sxs-lookup"><span data-stu-id="1877e-225">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

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

<span data-ttu-id="1877e-226">Předchozí příklad nastaví instanci pojmenované možnosti na `optionalOptionsName`.</span><span class="sxs-lookup"><span data-stu-id="1877e-226">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="1877e-227">Instance výchozích možností je `Options.DefaultName`.</span><span class="sxs-lookup"><span data-stu-id="1877e-227">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="1877e-228">Ověřování se spustí při vytvoření instance Options.</span><span class="sxs-lookup"><span data-stu-id="1877e-228">Validation runs when the options instance is created.</span></span> <span data-ttu-id="1877e-229">Při prvním použití vaší instance možností je zaručeno předání ověření.</span><span class="sxs-lookup"><span data-stu-id="1877e-229">Your options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="1877e-230">Ověřování možností nechrání proti změnám možností po počáteční konfiguraci a ověření možností.</span><span class="sxs-lookup"><span data-stu-id="1877e-230">Options validation doesn't guard against options modifications after the options are initially configured and validated.</span></span>

<span data-ttu-id="1877e-231">`Validate` Metoda`Func<TOptions, bool>`přijímá.</span><span class="sxs-lookup"><span data-stu-id="1877e-231">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="1877e-232">Pro úplné přizpůsobení ověřování, implementujte `IValidateOptions<TOptions>`, což umožňuje:</span><span class="sxs-lookup"><span data-stu-id="1877e-232">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="1877e-233">Ověřování více typů možností:`class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="1877e-233">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="1877e-234">Ověření, které závisí na jiném typu možnosti:`public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="1877e-234">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="1877e-235">`IValidateOptions`ověří</span><span class="sxs-lookup"><span data-stu-id="1877e-235">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="1877e-236">Konkrétní instance pojmenované možnosti.</span><span class="sxs-lookup"><span data-stu-id="1877e-236">A specific named options instance.</span></span>
* <span data-ttu-id="1877e-237">Všechny možnosti, `name` Pokud `null`je.</span><span class="sxs-lookup"><span data-stu-id="1877e-237">All options when `name` is `null`.</span></span>

<span data-ttu-id="1877e-238">Vraťte se `ValidateOptionsResult` z vaší implementace rozhraní:</span><span class="sxs-lookup"><span data-stu-id="1877e-238">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="1877e-239">Ověřování na základě datových poznámek je k dispozici v balíčku [Microsoft. Extensions. Options.](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) DataAnnotations pomocí <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> volání metody `OptionsBuilder<TOptions>`on.</span><span class="sxs-lookup"><span data-stu-id="1877e-239">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="1877e-240">`Microsoft.Extensions.Options.DataAnnotations`je součástí [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2,2 nebo novější).</span><span class="sxs-lookup"><span data-stu-id="1877e-240">`Microsoft.Extensions.Options.DataAnnotations` is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.2 or later).</span></span>

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
        sp.GetRequiredService<IOptionsMonitor<AnnotatedOptions>>().Value);
    ValidateFailure<AnnotatedOptions>(error, Options.DefaultName, 1,
        "DataAnnotation validation failed for members Required " +
            "with the error 'The Required field is required.'.",
        "DataAnnotation validation failed for members StringLength " +
            "with the error 'Too long.'.",
        "DataAnnotation validation failed for members IntRange " +
            "with the error 'Out of range.'.");
}
```

<span data-ttu-id="1877e-241">Ověření Eager (při spuštění selže) je v budoucí verzi zváženo.</span><span class="sxs-lookup"><span data-stu-id="1877e-241">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="1877e-242">Možnosti po konfiguraci</span><span class="sxs-lookup"><span data-stu-id="1877e-242">Options post-configuration</span></span>

<span data-ttu-id="1877e-243">Nastavení po konfiguraci <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>nastavte na.</span><span class="sxs-lookup"><span data-stu-id="1877e-243">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="1877e-244">Po dokončení všech <xref:Microsoft.Extensions.Options.IConfigureOptions%601> konfigurací se spustí po konfiguraci:</span><span class="sxs-lookup"><span data-stu-id="1877e-244">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="1877e-245"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>je k dispozici pro nastavení po konfiguraci s názvem:</span><span class="sxs-lookup"><span data-stu-id="1877e-245"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="1877e-246">Použijte <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> k následné konfiguraci všech instancí konfigurace:</span><span class="sxs-lookup"><span data-stu-id="1877e-246">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="1877e-247">Přístup k možnostem při spuštění</span><span class="sxs-lookup"><span data-stu-id="1877e-247">Accessing options during startup</span></span>

<span data-ttu-id="1877e-248"><xref:Microsoft.Extensions.Options.IOptions%601>a <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> lze jej použít v `Startup.Configure`, protože `Configure` služby jsou vytvořeny před spuštěním metody.</span><span class="sxs-lookup"><span data-stu-id="1877e-248"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="1877e-249">Nepoužívejte <xref:Microsoft.Extensions.Options.IOptions%601> nebo <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="1877e-249">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="1877e-250">Stav nekonzistentních možností může existovat v důsledku řazení registrací služby.</span><span class="sxs-lookup"><span data-stu-id="1877e-250">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="1877e-251">Vzor možností nastavení využívá třídy pro reprezentaci skupin souvisejících nastavení.</span><span class="sxs-lookup"><span data-stu-id="1877e-251">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="1877e-252">Když jsou [nastavení konfigurace](xref:fundamentals/configuration/index) izolována scénářem do samostatných tříd, aplikace dodržuje dva důležité principy technického řízení:</span><span class="sxs-lookup"><span data-stu-id="1877e-252">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="1877e-253">[Princip oddělení (ISP) nebo](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; scénářů zapouzdření (třídy) závislých na nastavení konfigurace závisí jenom na nastavení konfigurace, která používají.</span><span class="sxs-lookup"><span data-stu-id="1877e-253">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="1877e-254">[Oddělení obav](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Nastavení pro různé části aplikace nejsou závislá ani vzájemně nezávislá.</span><span class="sxs-lookup"><span data-stu-id="1877e-254">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="1877e-255">Možnosti také poskytují mechanismus pro ověření konfiguračních dat.</span><span class="sxs-lookup"><span data-stu-id="1877e-255">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="1877e-256">Další informace najdete v části [ověřování možností](#options-validation) .</span><span class="sxs-lookup"><span data-stu-id="1877e-256">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="1877e-257">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1877e-257">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1877e-258">Požadavky</span><span class="sxs-lookup"><span data-stu-id="1877e-258">Prerequisites</span></span>

<span data-ttu-id="1877e-259">Odkaz na balíček [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) nebo přidejte odkaz na balíček do balíčku [Microsoft. Extensions. Options. ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) .</span><span class="sxs-lookup"><span data-stu-id="1877e-259">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="1877e-260">Možnosti rozhraní</span><span class="sxs-lookup"><span data-stu-id="1877e-260">Options interfaces</span></span>

<span data-ttu-id="1877e-261"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>slouží k načtení možností a správě oznámení možností pro `TOptions` instance.</span><span class="sxs-lookup"><span data-stu-id="1877e-261"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="1877e-262"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>podporuje následující scénáře:</span><span class="sxs-lookup"><span data-stu-id="1877e-262"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="1877e-263">Oznámení změny</span><span class="sxs-lookup"><span data-stu-id="1877e-263">Change notifications</span></span>
* [<span data-ttu-id="1877e-264">Pojmenované možnosti</span><span class="sxs-lookup"><span data-stu-id="1877e-264">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="1877e-265">Znovu Načítatelné konfigurace</span><span class="sxs-lookup"><span data-stu-id="1877e-265">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="1877e-266">Neplatných selektivních možností<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>()</span><span class="sxs-lookup"><span data-stu-id="1877e-266">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="1877e-267">Scénáře po [konfiguraci](#options-post-configuration) umožňují nastavit nebo změnit možnosti po výskytu všech <xref:Microsoft.Extensions.Options.IConfigureOptions%601> konfigurací.</span><span class="sxs-lookup"><span data-stu-id="1877e-267">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="1877e-268"><xref:Microsoft.Extensions.Options.IOptionsFactory%601>zodpovídá za vytváření nových instancí možností.</span><span class="sxs-lookup"><span data-stu-id="1877e-268"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="1877e-269">Má jedinou <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> metodu.</span><span class="sxs-lookup"><span data-stu-id="1877e-269">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="1877e-270">Výchozí implementace přebírá všechny <xref:Microsoft.Extensions.Options.IConfigureOptions%601> konfigurace a <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> a spustí nejprve všechny konfigurace, následované po konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="1877e-270">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="1877e-271">Rozlišuje mezi <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> a <xref:Microsoft.Extensions.Options.IConfigureOptions%601> a volá pouze příslušné rozhraní.</span><span class="sxs-lookup"><span data-stu-id="1877e-271">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="1877e-272"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>je používána <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> pro ukládání instancí `TOptions` do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="1877e-272"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="1877e-273">Zruší platnost instancí možností v monitorování tak, aby se hodnota přepočítala (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>). <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601></span><span class="sxs-lookup"><span data-stu-id="1877e-273">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="1877e-274">Hodnoty lze ručně zavést pomocí <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span><span class="sxs-lookup"><span data-stu-id="1877e-274">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="1877e-275">Metoda <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> se používá v případě, že by měly být všechny pojmenované instance znovu vytvořeny na vyžádání.</span><span class="sxs-lookup"><span data-stu-id="1877e-275">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="1877e-276"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>je užitečná ve scénářích, kde by se měly přepočítat možnosti pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="1877e-276"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="1877e-277">Další informace najdete v části [opětovné načtení konfiguračních dat pomocí IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) .</span><span class="sxs-lookup"><span data-stu-id="1877e-277">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="1877e-278"><xref:Microsoft.Extensions.Options.IOptions%601>dá se použít k podpoře možností.</span><span class="sxs-lookup"><span data-stu-id="1877e-278"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="1877e-279">Nicméně nepodporuje předchozí <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>scénáře. <xref:Microsoft.Extensions.Options.IOptions%601></span><span class="sxs-lookup"><span data-stu-id="1877e-279">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="1877e-280">Můžete pokračovat v používání <xref:Microsoft.Extensions.Options.IOptions%601> existujících architektur a knihoven, které již <xref:Microsoft.Extensions.Options.IOptions%601> rozhraní používají, a <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>nevyžadují scénáře, které poskytuje.</span><span class="sxs-lookup"><span data-stu-id="1877e-280">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="1877e-281">Konfigurace obecných možností</span><span class="sxs-lookup"><span data-stu-id="1877e-281">General options configuration</span></span>

<span data-ttu-id="1877e-282">Konfigurace obecných možností se v ukázkové aplikaci &num;ukazuje jako příklad 1.</span><span class="sxs-lookup"><span data-stu-id="1877e-282">General options configuration is demonstrated as Example &num;1 in the sample app.</span></span>

<span data-ttu-id="1877e-283">Třída Options musí být neabstraktní s veřejným konstruktorem bez parametrů.</span><span class="sxs-lookup"><span data-stu-id="1877e-283">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="1877e-284">Následující třída `MyOptions`má dvě vlastnosti, `Option1` a `Option2`.</span><span class="sxs-lookup"><span data-stu-id="1877e-284">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="1877e-285">Nastavení výchozích hodnot je volitelné, ale konstruktor třídy v následujícím příkladu nastaví výchozí hodnotu `Option1`.</span><span class="sxs-lookup"><span data-stu-id="1877e-285">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="1877e-286">`Option2`má výchozí hodnotu nastavenou pomocí přímé inicializace vlastnosti (*modely/MyOptions. cs*):</span><span class="sxs-lookup"><span data-stu-id="1877e-286">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="1877e-287">Třída je přidána do kontejneru služby s <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> konfigurací a je vázána na konfiguraci: `MyOptions`</span><span class="sxs-lookup"><span data-stu-id="1877e-287">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="1877e-288">Následující model stránky používá [vkládání závislostí konstruktoru](xref:mvc/controllers/dependency-injection) s <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> pro přístup k nastavení (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="1877e-288">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="1877e-289">Soubor *appSettings. JSON* v ukázce určuje hodnoty pro `option1` a: `option2`</span><span class="sxs-lookup"><span data-stu-id="1877e-289">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="1877e-290">Při spuštění aplikace vrátí `OnGet` metoda modelu stránky řetězec zobrazující hodnoty třídy možností:</span><span class="sxs-lookup"><span data-stu-id="1877e-290">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="1877e-291">Když použijete vlastní <xref:System.Configuration.ConfigurationBuilder> nastavení pro načtení konfigurace možností z konfiguračního souboru, zkontrolujte, jestli je správně nastavená základní cesta:</span><span class="sxs-lookup"><span data-stu-id="1877e-291">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="1877e-292">Při načítání konfigurace možností ze souboru s nastavením prostřednictvím <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>je explicitně nutné nastavit základní cestu.</span><span class="sxs-lookup"><span data-stu-id="1877e-292">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="1877e-293">Konfigurace jednoduchých možností pomocí delegáta</span><span class="sxs-lookup"><span data-stu-id="1877e-293">Configure simple options with a delegate</span></span>

<span data-ttu-id="1877e-294">Konfigurace jednoduchých možností s delegátem je znázorněna jako &num;příklad 2 v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="1877e-294">Configuring simple options with a delegate is demonstrated as Example &num;2 in the sample app.</span></span>

<span data-ttu-id="1877e-295">K nastavení hodnot možností použijte delegáta.</span><span class="sxs-lookup"><span data-stu-id="1877e-295">Use a delegate to set options values.</span></span> <span data-ttu-id="1877e-296">Ukázková aplikace používá `MyOptionsWithDelegateConfig` třídu (*modely/MyOptionsWithDelegateConfig. cs*):</span><span class="sxs-lookup"><span data-stu-id="1877e-296">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="1877e-297">V následujícím kódu je do kontejneru služby <xref:Microsoft.Extensions.Options.IConfigureOptions%601> přidána druhá služba.</span><span class="sxs-lookup"><span data-stu-id="1877e-297">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="1877e-298">Pomocí delegáta konfiguruje vazbu s `MyOptionsWithDelegateConfig`:</span><span class="sxs-lookup"><span data-stu-id="1877e-298">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="1877e-299">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="1877e-299">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="1877e-300">Můžete přidat více poskytovatelů konfigurace.</span><span class="sxs-lookup"><span data-stu-id="1877e-300">You can add multiple configuration providers.</span></span> <span data-ttu-id="1877e-301">Poskytovatelé konfigurace jsou k dispozici z balíčků NuGet a jsou aplikováni v pořadí, v jakém jsou registrováni.</span><span class="sxs-lookup"><span data-stu-id="1877e-301">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="1877e-302">Další informace naleznete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="1877e-302">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="1877e-303">Každé volání <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> <xref:Microsoft.Extensions.Options.IConfigureOptions%601> přidá službu do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="1877e-303">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="1877e-304">V předchozím příkladu jsou `Option1` hodnoty a `Option2` zadány v souboru `Option1` *appSettings. JSON*, ale hodnoty a `Option2` jsou přepsány nakonfigurovaným delegátem.</span><span class="sxs-lookup"><span data-stu-id="1877e-304">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="1877e-305">Je-li povolena více než jedna služba konfigurace, je v posledním zdroji konfigurace zadaný *Server WINS* a nastavena hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="1877e-305">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="1877e-306">Při spuštění aplikace vrátí `OnGet` metoda modelu stránky řetězec zobrazující hodnoty třídy možností:</span><span class="sxs-lookup"><span data-stu-id="1877e-306">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="1877e-307">Konfigurace podNastavení</span><span class="sxs-lookup"><span data-stu-id="1877e-307">Suboptions configuration</span></span>

<span data-ttu-id="1877e-308">Konfigurace dílčích možností je v ukázkové &num;aplikaci znázorněna jako příklad 3.</span><span class="sxs-lookup"><span data-stu-id="1877e-308">Suboptions configuration is demonstrated as Example &num;3 in the sample app.</span></span>

<span data-ttu-id="1877e-309">Aplikace by měly vytvořit třídy možností, které se týkají konkrétních skupin scénářů (tříd) v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="1877e-309">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="1877e-310">Části aplikace, které vyžadují konfigurační hodnoty, by měly mít přístup jenom k hodnotám konfigurace, které používají.</span><span class="sxs-lookup"><span data-stu-id="1877e-310">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="1877e-311">Při vytváření vazby mezi možnostmi konfigurace jsou jednotlivé vlastnosti v typu možnosti vázány na konfigurační klíč formuláře `property[:sub-property:]`.</span><span class="sxs-lookup"><span data-stu-id="1877e-311">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="1877e-312">Například `MyOptions.Option1` vlastnost je svázána s klíčem `Option1`, `option1` který je načten z vlastnosti v souboru *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="1877e-312">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="1877e-313">V následujícím kódu je do kontejneru služby <xref:Microsoft.Extensions.Options.IConfigureOptions%601> přidána třetí služba.</span><span class="sxs-lookup"><span data-stu-id="1877e-313">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="1877e-314">Váže `MySubOptions` se k části `subsection` souboru *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="1877e-314">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="1877e-315">Metoda rozšíření vyžaduje balíček [Microsoft. Extensions. Options. ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) NuGet. `GetSection`</span><span class="sxs-lookup"><span data-stu-id="1877e-315">The `GetSection` extension method requires the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) NuGet package.</span></span> <span data-ttu-id="1877e-316">Pokud aplikace používá [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 nebo novější), balíček je automaticky zahrnutý.</span><span class="sxs-lookup"><span data-stu-id="1877e-316">If the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later), the package is automatically included.</span></span>

<span data-ttu-id="1877e-317">Soubor *appSettings. JSON* ukázky definuje `subsection` člena s klíči pro `suboption1` a `suboption2`:</span><span class="sxs-lookup"><span data-stu-id="1877e-317">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="1877e-318">Třída definuje `SubOption1` vlastnosti a`SubOption2`, aby obsahovala hodnoty možností (*modely/MySubOptions. cs*): `MySubOptions`</span><span class="sxs-lookup"><span data-stu-id="1877e-318">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="1877e-319">`OnGet` Metoda modelu stránky vrátí řetězec s hodnotami možností (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="1877e-319">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="1877e-320">Při spuštění `OnGet` aplikace vrátí metoda řetězec zobrazující hodnoty třídy dílčích možností:</span><span class="sxs-lookup"><span data-stu-id="1877e-320">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="1877e-321">Možnosti poskytované modelem zobrazení nebo vkládáním přímých zobrazení</span><span class="sxs-lookup"><span data-stu-id="1877e-321">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="1877e-322">Možnosti poskytované modelem zobrazení nebo vkládáním přímých zobrazení jsou v ukázkové aplikaci znázorněné jako příklad &num;4.</span><span class="sxs-lookup"><span data-stu-id="1877e-322">Options provided by a view model or with direct view injection is demonstrated as Example &num;4 in the sample app.</span></span>

<span data-ttu-id="1877e-323">Možnosti lze zadat v modelu zobrazení nebo <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> přímým vložením do zobrazení (Pages */index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="1877e-323">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="1877e-324">Ukázková aplikace ukazuje, jak vložit `IOptionsMonitor<MyOptions>` `@inject` pomocí direktivy:</span><span class="sxs-lookup"><span data-stu-id="1877e-324">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="1877e-325">Po spuštění aplikace se hodnoty možností zobrazí na vykreslené stránce:</span><span class="sxs-lookup"><span data-stu-id="1877e-325">When the app is run, the options values are shown in the rendered page:</span></span>

![Hodnoty možností možnost1: value1_from_json a Možnost2:-1 jsou načteny z modelu a vložením do zobrazení.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="1877e-327">Znovu načíst konfigurační data pomocí IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="1877e-327">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="1877e-328">Opětovné načtení konfiguračních dat pomocí <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> je v ukázkové aplikaci znázorněno v příkladu &num;5.</span><span class="sxs-lookup"><span data-stu-id="1877e-328">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example &num;5 in the sample app.</span></span>

<span data-ttu-id="1877e-329"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>podporuje opětovné načítání možností s minimální režií zpracování.</span><span class="sxs-lookup"><span data-stu-id="1877e-329"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="1877e-330">Možnosti jsou vypočítány jednou za požadavek při otevření a ukládání do mezipaměti po dobu života žádosti.</span><span class="sxs-lookup"><span data-stu-id="1877e-330">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="1877e-331">Následující příklad ukazuje, jak se vytvoří <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> nový po změnách *appSettings. JSON* (*pages/index. cshtml. cs*).</span><span class="sxs-lookup"><span data-stu-id="1877e-331">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="1877e-332">Více požadavků na server vrací konstantní hodnoty, které poskytuje soubor *appSettings. JSON* , dokud nedojde ke změně souboru a opětovnému načtení konfigurace.</span><span class="sxs-lookup"><span data-stu-id="1877e-332">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="1877e-333">Následující obrázek ukazuje počáteční `option1` hodnoty a `option2` hodnoty načtené ze souboru *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="1877e-333">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="1877e-334">Změňte hodnoty v souboru *appSettings. JSON* na `value1_from_json UPDATED` a `200`.</span><span class="sxs-lookup"><span data-stu-id="1877e-334">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="1877e-335">Uložte soubor *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="1877e-335">Save the *appsettings.json* file.</span></span> <span data-ttu-id="1877e-336">Aktualizujte prohlížeč, aby se zobrazily hodnoty možností, které jsou aktualizované:</span><span class="sxs-lookup"><span data-stu-id="1877e-336">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="1877e-337">Podpora pojmenovaných možností s IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="1877e-337">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="1877e-338">Pojmenovaná podpora <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> s názvem je znázorněna jako příklad &num;6 v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="1877e-338">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example &num;6 in the sample app.</span></span>

<span data-ttu-id="1877e-339">Podpora pojmenovaných *možností* umožňuje aplikaci rozlišovat konfigurace pojmenovaných možností.</span><span class="sxs-lookup"><span data-stu-id="1877e-339">*Named options* support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="1877e-340">V ukázkové aplikaci jsou pojmenované možnosti deklarovány pomocí [OptionsServiceCollectionExtensions. Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), který volá [ConfigureNamedOptions\<TOptions >. Konfigurace](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) metody rozšíření:</span><span class="sxs-lookup"><span data-stu-id="1877e-340">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="1877e-341">Ukázková aplikace přistupuje k pojmenovaným možnostem <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="1877e-341">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="1877e-342">Když spustíte ukázkovou aplikaci, vrátí se pojmenované možnosti:</span><span class="sxs-lookup"><span data-stu-id="1877e-342">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="1877e-343">`named_options_1`hodnoty se poskytují z konfigurace, které se načítají ze souboru *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="1877e-343">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="1877e-344">`named_options_2`hodnoty poskytuje:</span><span class="sxs-lookup"><span data-stu-id="1877e-344">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="1877e-345">Delegát v `ConfigureServices` pro`Option1`. `named_options_2`</span><span class="sxs-lookup"><span data-stu-id="1877e-345">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="1877e-346">Výchozí hodnota `Option2` poskytnutá `MyOptions` třídou</span><span class="sxs-lookup"><span data-stu-id="1877e-346">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="1877e-347">Konfigurace všech možností pomocí metody ConfigureAll</span><span class="sxs-lookup"><span data-stu-id="1877e-347">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="1877e-348">Nakonfigurujte všechny instance možností pomocí <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> metody.</span><span class="sxs-lookup"><span data-stu-id="1877e-348">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="1877e-349">Následující kód konfiguruje `Option1` pro všechny instance konfigurace se společnou hodnotou.</span><span class="sxs-lookup"><span data-stu-id="1877e-349">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="1877e-350">Do `Startup.ConfigureServices` metody přidejte následující kód ručně:</span><span class="sxs-lookup"><span data-stu-id="1877e-350">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="1877e-351">Spuštění ukázkové aplikace po přidání kódu vytvoří následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="1877e-351">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="1877e-352">Všechny možnosti jsou pojmenované instance.</span><span class="sxs-lookup"><span data-stu-id="1877e-352">All options are named instances.</span></span> <span data-ttu-id="1877e-353">Existující <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instance jsou považovány za `Options.DefaultName` cílení instance, což `string.Empty`je.</span><span class="sxs-lookup"><span data-stu-id="1877e-353">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="1877e-354"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>také implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="1877e-354"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="1877e-355">Výchozí implementace <xref:Microsoft.Extensions.Options.IOptionsFactory%601> pro má logiku odpovídajícím způsobem použít.</span><span class="sxs-lookup"><span data-stu-id="1877e-355">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="1877e-356">Pojmenovaná možnost se používá pro cílení na všechny pojmenované instance místo konkrétní pojmenované instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> a <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> použití této konvence). `null`</span><span class="sxs-lookup"><span data-stu-id="1877e-356">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="1877e-357">Rozhraní API pro OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="1877e-357">OptionsBuilder API</span></span>

<span data-ttu-id="1877e-358"><xref:Microsoft.Extensions.Options.OptionsBuilder%601>se používá ke konfiguraci `TOptions` instancí.</span><span class="sxs-lookup"><span data-stu-id="1877e-358"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="1877e-359">`OptionsBuilder`zjednodušuje vytváření pojmenovaných možností, protože je jenom jedním parametrem počátečního `AddOptions<TOptions>(string optionsName)` volání, takže se neobjeví ve všech dalších voláních.</span><span class="sxs-lookup"><span data-stu-id="1877e-359">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="1877e-360">Ověřování možností a `ConfigureOptions` přetížení, která přijímají závislosti služby, jsou k dispozici `OptionsBuilder`pouze prostřednictvím.</span><span class="sxs-lookup"><span data-stu-id="1877e-360">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="1877e-361">Ke konfiguraci možností použijte DI Services.</span><span class="sxs-lookup"><span data-stu-id="1877e-361">Use DI services to configure options</span></span>

<span data-ttu-id="1877e-362">K dalším službám můžete přistupovat pomocí injektáže závislosti při konfiguraci možností dvěma způsoby:</span><span class="sxs-lookup"><span data-stu-id="1877e-362">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="1877e-363">Předejte delegáta konfigurace [](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) na [\<OptionsBuilder TOptions >](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="1877e-363">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="1877e-364">[OptionsBuilder\<TOptions >](xref:Microsoft.Extensions.Options.OptionsBuilder`1) poskytuje přetížení [Konfigurace](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) , která umožňují použít až pět služeb ke konfiguraci možností:</span><span class="sxs-lookup"><span data-stu-id="1877e-364">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="1877e-365">Vytvořte vlastní typ, který implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> nebo <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> a zaregistruje typ jako službu.</span><span class="sxs-lookup"><span data-stu-id="1877e-365">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="1877e-366">Doporučujeme předat delegáta konfigurace, protože [](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)vytvoření služby je složitější.</span><span class="sxs-lookup"><span data-stu-id="1877e-366">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="1877e-367">Vytvoření vlastního typu je ekvivalentní s tím, k čemu architektura slouží při použití možnosti [Konfigurovat](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="1877e-367">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="1877e-368">Volání [](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) metody Configure provede přechodný <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>obecný, který má konstruktor, který přijímá obecné zadané typy služeb.</span><span class="sxs-lookup"><span data-stu-id="1877e-368">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="1877e-369">Ověřování možností</span><span class="sxs-lookup"><span data-stu-id="1877e-369">Options validation</span></span>

<span data-ttu-id="1877e-370">Ověřování možností umožňuje ověřit možnosti při konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="1877e-370">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="1877e-371">Zavolejte `Validate` na metodu ověřování, která vrátí `true` , pokud jsou možnosti platné `false` , a pokud nejsou platné:</span><span class="sxs-lookup"><span data-stu-id="1877e-371">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

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

<span data-ttu-id="1877e-372">Předchozí příklad nastaví instanci pojmenované možnosti na `optionalOptionsName`.</span><span class="sxs-lookup"><span data-stu-id="1877e-372">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="1877e-373">Instance výchozích možností je `Options.DefaultName`.</span><span class="sxs-lookup"><span data-stu-id="1877e-373">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="1877e-374">Ověřování se spustí při vytvoření instance Options.</span><span class="sxs-lookup"><span data-stu-id="1877e-374">Validation runs when the options instance is created.</span></span> <span data-ttu-id="1877e-375">Při prvním použití vaší instance možností je zaručeno předání ověření.</span><span class="sxs-lookup"><span data-stu-id="1877e-375">Your options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="1877e-376">Ověřování možností nechrání proti změnám možností po počáteční konfiguraci a ověření možností.</span><span class="sxs-lookup"><span data-stu-id="1877e-376">Options validation doesn't guard against options modifications after the options are initially configured and validated.</span></span>

<span data-ttu-id="1877e-377">`Validate` Metoda`Func<TOptions, bool>`přijímá.</span><span class="sxs-lookup"><span data-stu-id="1877e-377">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="1877e-378">Pro úplné přizpůsobení ověřování, implementujte `IValidateOptions<TOptions>`, což umožňuje:</span><span class="sxs-lookup"><span data-stu-id="1877e-378">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="1877e-379">Ověřování více typů možností:`class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="1877e-379">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="1877e-380">Ověření, které závisí na jiném typu možnosti:`public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="1877e-380">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="1877e-381">`IValidateOptions`ověří</span><span class="sxs-lookup"><span data-stu-id="1877e-381">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="1877e-382">Konkrétní instance pojmenované možnosti.</span><span class="sxs-lookup"><span data-stu-id="1877e-382">A specific named options instance.</span></span>
* <span data-ttu-id="1877e-383">Všechny možnosti, `name` Pokud `null`je.</span><span class="sxs-lookup"><span data-stu-id="1877e-383">All options when `name` is `null`.</span></span>

<span data-ttu-id="1877e-384">Vraťte se `ValidateOptionsResult` z vaší implementace rozhraní:</span><span class="sxs-lookup"><span data-stu-id="1877e-384">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="1877e-385">Ověřování na základě datových poznámek je k dispozici v balíčku [Microsoft. Extensions. Options.](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) DataAnnotations pomocí <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> volání metody `OptionsBuilder<TOptions>`on.</span><span class="sxs-lookup"><span data-stu-id="1877e-385">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="1877e-386">`Microsoft.Extensions.Options.DataAnnotations`je součástí [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2,2 nebo novější).</span><span class="sxs-lookup"><span data-stu-id="1877e-386">`Microsoft.Extensions.Options.DataAnnotations` is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.2 or later).</span></span>

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
        sp.GetRequiredService<IOptionsMonitor<AnnotatedOptions>>().Value);
    ValidateFailure<AnnotatedOptions>(error, Options.DefaultName, 1,
        "DataAnnotation validation failed for members Required " +
            "with the error 'The Required field is required.'.",
        "DataAnnotation validation failed for members StringLength " +
            "with the error 'Too long.'.",
        "DataAnnotation validation failed for members IntRange " +
            "with the error 'Out of range.'.");
}
```

<span data-ttu-id="1877e-387">Ověření Eager (při spuštění selže) je v budoucí verzi zváženo.</span><span class="sxs-lookup"><span data-stu-id="1877e-387">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="1877e-388">Možnosti po konfiguraci</span><span class="sxs-lookup"><span data-stu-id="1877e-388">Options post-configuration</span></span>

<span data-ttu-id="1877e-389">Nastavení po konfiguraci <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>nastavte na.</span><span class="sxs-lookup"><span data-stu-id="1877e-389">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="1877e-390">Po dokončení všech <xref:Microsoft.Extensions.Options.IConfigureOptions%601> konfigurací se spustí po konfiguraci:</span><span class="sxs-lookup"><span data-stu-id="1877e-390">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="1877e-391"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>je k dispozici pro nastavení po konfiguraci s názvem:</span><span class="sxs-lookup"><span data-stu-id="1877e-391"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="1877e-392">Použijte <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> k následné konfiguraci všech instancí konfigurace:</span><span class="sxs-lookup"><span data-stu-id="1877e-392">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="1877e-393">Přístup k možnostem při spuštění</span><span class="sxs-lookup"><span data-stu-id="1877e-393">Accessing options during startup</span></span>

<span data-ttu-id="1877e-394"><xref:Microsoft.Extensions.Options.IOptions%601>a <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> lze jej použít v `Startup.Configure`, protože `Configure` služby jsou vytvořeny před spuštěním metody.</span><span class="sxs-lookup"><span data-stu-id="1877e-394"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="1877e-395">Nepoužívejte <xref:Microsoft.Extensions.Options.IOptions%601> nebo <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="1877e-395">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="1877e-396">Stav nekonzistentních možností může existovat v důsledku řazení registrací služby.</span><span class="sxs-lookup"><span data-stu-id="1877e-396">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="1877e-397">Vzor možností nastavení využívá třídy pro reprezentaci skupin souvisejících nastavení.</span><span class="sxs-lookup"><span data-stu-id="1877e-397">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="1877e-398">Když jsou [nastavení konfigurace](xref:fundamentals/configuration/index) izolována scénářem do samostatných tříd, aplikace dodržuje dva důležité principy technického řízení:</span><span class="sxs-lookup"><span data-stu-id="1877e-398">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="1877e-399">[Princip oddělení (ISP) nebo](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; scénářů zapouzdření (třídy) závislých na nastavení konfigurace závisí jenom na nastavení konfigurace, která používají.</span><span class="sxs-lookup"><span data-stu-id="1877e-399">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="1877e-400">[Oddělení obav](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Nastavení pro různé části aplikace nejsou závislá ani vzájemně nezávislá.</span><span class="sxs-lookup"><span data-stu-id="1877e-400">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="1877e-401">Možnosti také poskytují mechanismus pro ověření konfiguračních dat.</span><span class="sxs-lookup"><span data-stu-id="1877e-401">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="1877e-402">Další informace najdete v části [ověřování možností](#options-validation) .</span><span class="sxs-lookup"><span data-stu-id="1877e-402">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="1877e-403">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1877e-403">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1877e-404">Požadavky</span><span class="sxs-lookup"><span data-stu-id="1877e-404">Prerequisites</span></span>

<span data-ttu-id="1877e-405">Odkaz na balíček [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) nebo přidejte odkaz na balíček do balíčku [Microsoft. Extensions. Options. ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) .</span><span class="sxs-lookup"><span data-stu-id="1877e-405">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="1877e-406">Možnosti rozhraní</span><span class="sxs-lookup"><span data-stu-id="1877e-406">Options interfaces</span></span>

<span data-ttu-id="1877e-407"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>slouží k načtení možností a správě oznámení možností pro `TOptions` instance.</span><span class="sxs-lookup"><span data-stu-id="1877e-407"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="1877e-408"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>podporuje následující scénáře:</span><span class="sxs-lookup"><span data-stu-id="1877e-408"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="1877e-409">Oznámení změny</span><span class="sxs-lookup"><span data-stu-id="1877e-409">Change notifications</span></span>
* [<span data-ttu-id="1877e-410">Pojmenované možnosti</span><span class="sxs-lookup"><span data-stu-id="1877e-410">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="1877e-411">Znovu Načítatelné konfigurace</span><span class="sxs-lookup"><span data-stu-id="1877e-411">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="1877e-412">Neplatných selektivních možností<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>()</span><span class="sxs-lookup"><span data-stu-id="1877e-412">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="1877e-413">Scénáře po [konfiguraci](#options-post-configuration) umožňují nastavit nebo změnit možnosti po výskytu všech <xref:Microsoft.Extensions.Options.IConfigureOptions%601> konfigurací.</span><span class="sxs-lookup"><span data-stu-id="1877e-413">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="1877e-414"><xref:Microsoft.Extensions.Options.IOptionsFactory%601>zodpovídá za vytváření nových instancí možností.</span><span class="sxs-lookup"><span data-stu-id="1877e-414"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="1877e-415">Má jedinou <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> metodu.</span><span class="sxs-lookup"><span data-stu-id="1877e-415">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="1877e-416">Výchozí implementace přebírá všechny <xref:Microsoft.Extensions.Options.IConfigureOptions%601> konfigurace a <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> a spustí nejprve všechny konfigurace, následované po konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="1877e-416">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="1877e-417">Rozlišuje mezi <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> a <xref:Microsoft.Extensions.Options.IConfigureOptions%601> a volá pouze příslušné rozhraní.</span><span class="sxs-lookup"><span data-stu-id="1877e-417">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="1877e-418"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>je používána <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> pro ukládání instancí `TOptions` do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="1877e-418"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="1877e-419">Zruší platnost instancí možností v monitorování tak, aby se hodnota přepočítala (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>). <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601></span><span class="sxs-lookup"><span data-stu-id="1877e-419">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="1877e-420">Hodnoty lze ručně zavést pomocí <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span><span class="sxs-lookup"><span data-stu-id="1877e-420">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="1877e-421">Metoda <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> se používá v případě, že by měly být všechny pojmenované instance znovu vytvořeny na vyžádání.</span><span class="sxs-lookup"><span data-stu-id="1877e-421">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="1877e-422"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>je užitečná ve scénářích, kde by se měly přepočítat možnosti pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="1877e-422"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="1877e-423">Další informace najdete v části [opětovné načtení konfiguračních dat pomocí IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) .</span><span class="sxs-lookup"><span data-stu-id="1877e-423">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="1877e-424"><xref:Microsoft.Extensions.Options.IOptions%601>dá se použít k podpoře možností.</span><span class="sxs-lookup"><span data-stu-id="1877e-424"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="1877e-425">Nicméně nepodporuje předchozí <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>scénáře. <xref:Microsoft.Extensions.Options.IOptions%601></span><span class="sxs-lookup"><span data-stu-id="1877e-425">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="1877e-426">Můžete pokračovat v používání <xref:Microsoft.Extensions.Options.IOptions%601> existujících architektur a knihoven, které již <xref:Microsoft.Extensions.Options.IOptions%601> rozhraní používají, a <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>nevyžadují scénáře, které poskytuje.</span><span class="sxs-lookup"><span data-stu-id="1877e-426">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="1877e-427">Konfigurace obecných možností</span><span class="sxs-lookup"><span data-stu-id="1877e-427">General options configuration</span></span>

<span data-ttu-id="1877e-428">Konfigurace obecných možností se v ukázkové aplikaci &num;ukazuje jako příklad 1.</span><span class="sxs-lookup"><span data-stu-id="1877e-428">General options configuration is demonstrated as Example &num;1 in the sample app.</span></span>

<span data-ttu-id="1877e-429">Třída Options musí být neabstraktní s veřejným konstruktorem bez parametrů.</span><span class="sxs-lookup"><span data-stu-id="1877e-429">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="1877e-430">Následující třída `MyOptions`má dvě vlastnosti, `Option1` a `Option2`.</span><span class="sxs-lookup"><span data-stu-id="1877e-430">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="1877e-431">Nastavení výchozích hodnot je volitelné, ale konstruktor třídy v následujícím příkladu nastaví výchozí hodnotu `Option1`.</span><span class="sxs-lookup"><span data-stu-id="1877e-431">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="1877e-432">`Option2`má výchozí hodnotu nastavenou pomocí přímé inicializace vlastnosti (*modely/MyOptions. cs*):</span><span class="sxs-lookup"><span data-stu-id="1877e-432">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="1877e-433">Třída je přidána do kontejneru služby s <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> konfigurací a je vázána na konfiguraci: `MyOptions`</span><span class="sxs-lookup"><span data-stu-id="1877e-433">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="1877e-434">Následující model stránky používá [vkládání závislostí konstruktoru](xref:mvc/controllers/dependency-injection) s <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> pro přístup k nastavení (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="1877e-434">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="1877e-435">Soubor *appSettings. JSON* v ukázce určuje hodnoty pro `option1` a: `option2`</span><span class="sxs-lookup"><span data-stu-id="1877e-435">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="1877e-436">Při spuštění aplikace vrátí `OnGet` metoda modelu stránky řetězec zobrazující hodnoty třídy možností:</span><span class="sxs-lookup"><span data-stu-id="1877e-436">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="1877e-437">Když použijete vlastní <xref:System.Configuration.ConfigurationBuilder> nastavení pro načtení konfigurace možností z konfiguračního souboru, zkontrolujte, jestli je správně nastavená základní cesta:</span><span class="sxs-lookup"><span data-stu-id="1877e-437">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="1877e-438">Při načítání konfigurace možností ze souboru s nastavením prostřednictvím <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>je explicitně nutné nastavit základní cestu.</span><span class="sxs-lookup"><span data-stu-id="1877e-438">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="1877e-439">Konfigurace jednoduchých možností pomocí delegáta</span><span class="sxs-lookup"><span data-stu-id="1877e-439">Configure simple options with a delegate</span></span>

<span data-ttu-id="1877e-440">Konfigurace jednoduchých možností s delegátem je znázorněna jako &num;příklad 2 v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="1877e-440">Configuring simple options with a delegate is demonstrated as Example &num;2 in the sample app.</span></span>

<span data-ttu-id="1877e-441">K nastavení hodnot možností použijte delegáta.</span><span class="sxs-lookup"><span data-stu-id="1877e-441">Use a delegate to set options values.</span></span> <span data-ttu-id="1877e-442">Ukázková aplikace používá `MyOptionsWithDelegateConfig` třídu (*modely/MyOptionsWithDelegateConfig. cs*):</span><span class="sxs-lookup"><span data-stu-id="1877e-442">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="1877e-443">V následujícím kódu je do kontejneru služby <xref:Microsoft.Extensions.Options.IConfigureOptions%601> přidána druhá služba.</span><span class="sxs-lookup"><span data-stu-id="1877e-443">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="1877e-444">Pomocí delegáta konfiguruje vazbu s `MyOptionsWithDelegateConfig`:</span><span class="sxs-lookup"><span data-stu-id="1877e-444">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="1877e-445">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="1877e-445">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="1877e-446">Můžete přidat více poskytovatelů konfigurace.</span><span class="sxs-lookup"><span data-stu-id="1877e-446">You can add multiple configuration providers.</span></span> <span data-ttu-id="1877e-447">Poskytovatelé konfigurace jsou k dispozici z balíčků NuGet a jsou aplikováni v pořadí, v jakém jsou registrováni.</span><span class="sxs-lookup"><span data-stu-id="1877e-447">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="1877e-448">Další informace naleznete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="1877e-448">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="1877e-449">Každé volání <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> <xref:Microsoft.Extensions.Options.IConfigureOptions%601> přidá službu do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="1877e-449">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="1877e-450">V předchozím příkladu jsou `Option1` hodnoty a `Option2` zadány v souboru `Option1` *appSettings. JSON*, ale hodnoty a `Option2` jsou přepsány nakonfigurovaným delegátem.</span><span class="sxs-lookup"><span data-stu-id="1877e-450">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="1877e-451">Je-li povolena více než jedna služba konfigurace, je v posledním zdroji konfigurace zadaný *Server WINS* a nastavena hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="1877e-451">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="1877e-452">Při spuštění aplikace vrátí `OnGet` metoda modelu stránky řetězec zobrazující hodnoty třídy možností:</span><span class="sxs-lookup"><span data-stu-id="1877e-452">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="1877e-453">Konfigurace podNastavení</span><span class="sxs-lookup"><span data-stu-id="1877e-453">Suboptions configuration</span></span>

<span data-ttu-id="1877e-454">Konfigurace dílčích možností je v ukázkové &num;aplikaci znázorněna jako příklad 3.</span><span class="sxs-lookup"><span data-stu-id="1877e-454">Suboptions configuration is demonstrated as Example &num;3 in the sample app.</span></span>

<span data-ttu-id="1877e-455">Aplikace by měly vytvořit třídy možností, které se týkají konkrétních skupin scénářů (tříd) v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="1877e-455">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="1877e-456">Části aplikace, které vyžadují konfigurační hodnoty, by měly mít přístup jenom k hodnotám konfigurace, které používají.</span><span class="sxs-lookup"><span data-stu-id="1877e-456">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="1877e-457">Při vytváření vazby mezi možnostmi konfigurace jsou jednotlivé vlastnosti v typu možnosti vázány na konfigurační klíč formuláře `property[:sub-property:]`.</span><span class="sxs-lookup"><span data-stu-id="1877e-457">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="1877e-458">Například `MyOptions.Option1` vlastnost je svázána s klíčem `Option1`, `option1` který je načten z vlastnosti v souboru *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="1877e-458">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="1877e-459">V následujícím kódu je do kontejneru služby <xref:Microsoft.Extensions.Options.IConfigureOptions%601> přidána třetí služba.</span><span class="sxs-lookup"><span data-stu-id="1877e-459">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="1877e-460">Váže `MySubOptions` se k části `subsection` souboru *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="1877e-460">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="1877e-461">Metoda rozšíření vyžaduje balíček [Microsoft. Extensions. Options. ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) NuGet. `GetSection`</span><span class="sxs-lookup"><span data-stu-id="1877e-461">The `GetSection` extension method requires the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) NuGet package.</span></span> <span data-ttu-id="1877e-462">Pokud aplikace používá [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 nebo novější), balíček je automaticky zahrnutý.</span><span class="sxs-lookup"><span data-stu-id="1877e-462">If the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later), the package is automatically included.</span></span>

<span data-ttu-id="1877e-463">Soubor *appSettings. JSON* ukázky definuje `subsection` člena s klíči pro `suboption1` a `suboption2`:</span><span class="sxs-lookup"><span data-stu-id="1877e-463">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="1877e-464">Třída definuje `SubOption1` vlastnosti a`SubOption2`, aby obsahovala hodnoty možností (*modely/MySubOptions. cs*): `MySubOptions`</span><span class="sxs-lookup"><span data-stu-id="1877e-464">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="1877e-465">`OnGet` Metoda modelu stránky vrátí řetězec s hodnotami možností (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="1877e-465">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="1877e-466">Při spuštění `OnGet` aplikace vrátí metoda řetězec zobrazující hodnoty třídy dílčích možností:</span><span class="sxs-lookup"><span data-stu-id="1877e-466">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="1877e-467">Možnosti poskytované modelem zobrazení nebo vkládáním přímých zobrazení</span><span class="sxs-lookup"><span data-stu-id="1877e-467">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="1877e-468">Možnosti poskytované modelem zobrazení nebo vkládáním přímých zobrazení jsou v ukázkové aplikaci znázorněné jako příklad &num;4.</span><span class="sxs-lookup"><span data-stu-id="1877e-468">Options provided by a view model or with direct view injection is demonstrated as Example &num;4 in the sample app.</span></span>

<span data-ttu-id="1877e-469">Možnosti lze zadat v modelu zobrazení nebo <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> přímým vložením do zobrazení (Pages */index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="1877e-469">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="1877e-470">Ukázková aplikace ukazuje, jak vložit `IOptionsMonitor<MyOptions>` `@inject` pomocí direktivy:</span><span class="sxs-lookup"><span data-stu-id="1877e-470">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="1877e-471">Po spuštění aplikace se hodnoty možností zobrazí na vykreslené stránce:</span><span class="sxs-lookup"><span data-stu-id="1877e-471">When the app is run, the options values are shown in the rendered page:</span></span>

![Hodnoty možností možnost1: value1_from_json a Možnost2:-1 jsou načteny z modelu a vložením do zobrazení.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="1877e-473">Znovu načíst konfigurační data pomocí IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="1877e-473">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="1877e-474">Opětovné načtení konfiguračních dat pomocí <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> je v ukázkové aplikaci znázorněno v příkladu &num;5.</span><span class="sxs-lookup"><span data-stu-id="1877e-474">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example &num;5 in the sample app.</span></span>

<span data-ttu-id="1877e-475"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>podporuje opětovné načítání možností s minimální režií zpracování.</span><span class="sxs-lookup"><span data-stu-id="1877e-475"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="1877e-476">Možnosti jsou vypočítány jednou za požadavek při otevření a ukládání do mezipaměti po dobu života žádosti.</span><span class="sxs-lookup"><span data-stu-id="1877e-476">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="1877e-477">Následující příklad ukazuje, jak se vytvoří <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> nový po změnách *appSettings. JSON* (*pages/index. cshtml. cs*).</span><span class="sxs-lookup"><span data-stu-id="1877e-477">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="1877e-478">Více požadavků na server vrací konstantní hodnoty, které poskytuje soubor *appSettings. JSON* , dokud nedojde ke změně souboru a opětovnému načtení konfigurace.</span><span class="sxs-lookup"><span data-stu-id="1877e-478">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="1877e-479">Následující obrázek ukazuje počáteční `option1` hodnoty a `option2` hodnoty načtené ze souboru *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="1877e-479">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="1877e-480">Změňte hodnoty v souboru *appSettings. JSON* na `value1_from_json UPDATED` a `200`.</span><span class="sxs-lookup"><span data-stu-id="1877e-480">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="1877e-481">Uložte soubor *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="1877e-481">Save the *appsettings.json* file.</span></span> <span data-ttu-id="1877e-482">Aktualizujte prohlížeč, aby se zobrazily hodnoty možností, které jsou aktualizované:</span><span class="sxs-lookup"><span data-stu-id="1877e-482">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="1877e-483">Podpora pojmenovaných možností s IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="1877e-483">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="1877e-484">Pojmenovaná podpora <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> s názvem je znázorněna jako příklad &num;6 v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="1877e-484">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example &num;6 in the sample app.</span></span>

<span data-ttu-id="1877e-485">Podpora pojmenovaných *možností* umožňuje aplikaci rozlišovat konfigurace pojmenovaných možností.</span><span class="sxs-lookup"><span data-stu-id="1877e-485">*Named options* support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="1877e-486">V ukázkové aplikaci jsou pojmenované možnosti deklarovány pomocí [OptionsServiceCollectionExtensions. Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), který volá [ConfigureNamedOptions\<TOptions >. Konfigurace](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) metody rozšíření:</span><span class="sxs-lookup"><span data-stu-id="1877e-486">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="1877e-487">Ukázková aplikace přistupuje k pojmenovaným možnostem <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="1877e-487">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="1877e-488">Když spustíte ukázkovou aplikaci, vrátí se pojmenované možnosti:</span><span class="sxs-lookup"><span data-stu-id="1877e-488">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="1877e-489">`named_options_1`hodnoty se poskytují z konfigurace, které se načítají ze souboru *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="1877e-489">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="1877e-490">`named_options_2`hodnoty poskytuje:</span><span class="sxs-lookup"><span data-stu-id="1877e-490">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="1877e-491">Delegát v `ConfigureServices` pro`Option1`. `named_options_2`</span><span class="sxs-lookup"><span data-stu-id="1877e-491">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="1877e-492">Výchozí hodnota `Option2` poskytnutá `MyOptions` třídou</span><span class="sxs-lookup"><span data-stu-id="1877e-492">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="1877e-493">Konfigurace všech možností pomocí metody ConfigureAll</span><span class="sxs-lookup"><span data-stu-id="1877e-493">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="1877e-494">Nakonfigurujte všechny instance možností pomocí <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> metody.</span><span class="sxs-lookup"><span data-stu-id="1877e-494">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="1877e-495">Následující kód konfiguruje `Option1` pro všechny instance konfigurace se společnou hodnotou.</span><span class="sxs-lookup"><span data-stu-id="1877e-495">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="1877e-496">Do `Startup.ConfigureServices` metody přidejte následující kód ručně:</span><span class="sxs-lookup"><span data-stu-id="1877e-496">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="1877e-497">Spuštění ukázkové aplikace po přidání kódu vytvoří následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="1877e-497">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="1877e-498">Všechny možnosti jsou pojmenované instance.</span><span class="sxs-lookup"><span data-stu-id="1877e-498">All options are named instances.</span></span> <span data-ttu-id="1877e-499">Existující <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instance jsou považovány za `Options.DefaultName` cílení instance, což `string.Empty`je.</span><span class="sxs-lookup"><span data-stu-id="1877e-499">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="1877e-500"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>také implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="1877e-500"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="1877e-501">Výchozí implementace <xref:Microsoft.Extensions.Options.IOptionsFactory%601> pro má logiku odpovídajícím způsobem použít.</span><span class="sxs-lookup"><span data-stu-id="1877e-501">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="1877e-502">Pojmenovaná možnost se používá pro cílení na všechny pojmenované instance místo konkrétní pojmenované instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> a <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> použití této konvence). `null`</span><span class="sxs-lookup"><span data-stu-id="1877e-502">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="1877e-503">Rozhraní API pro OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="1877e-503">OptionsBuilder API</span></span>

<span data-ttu-id="1877e-504"><xref:Microsoft.Extensions.Options.OptionsBuilder%601>se používá ke konfiguraci `TOptions` instancí.</span><span class="sxs-lookup"><span data-stu-id="1877e-504"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="1877e-505">`OptionsBuilder`zjednodušuje vytváření pojmenovaných možností, protože je jenom jedním parametrem počátečního `AddOptions<TOptions>(string optionsName)` volání, takže se neobjeví ve všech dalších voláních.</span><span class="sxs-lookup"><span data-stu-id="1877e-505">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="1877e-506">Ověřování možností a `ConfigureOptions` přetížení, která přijímají závislosti služby, jsou k dispozici `OptionsBuilder`pouze prostřednictvím.</span><span class="sxs-lookup"><span data-stu-id="1877e-506">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="1877e-507">Ke konfiguraci možností použijte DI Services.</span><span class="sxs-lookup"><span data-stu-id="1877e-507">Use DI services to configure options</span></span>

<span data-ttu-id="1877e-508">K dalším službám můžete přistupovat pomocí injektáže závislosti při konfiguraci možností dvěma způsoby:</span><span class="sxs-lookup"><span data-stu-id="1877e-508">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="1877e-509">Předejte delegáta konfigurace [](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) na [\<OptionsBuilder TOptions >](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="1877e-509">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="1877e-510">[OptionsBuilder\<TOptions >](xref:Microsoft.Extensions.Options.OptionsBuilder`1) poskytuje přetížení [Konfigurace](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) , která umožňují použít až pět služeb ke konfiguraci možností:</span><span class="sxs-lookup"><span data-stu-id="1877e-510">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="1877e-511">Vytvořte vlastní typ, který implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> nebo <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> a zaregistruje typ jako službu.</span><span class="sxs-lookup"><span data-stu-id="1877e-511">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="1877e-512">Doporučujeme předat delegáta konfigurace, protože [](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)vytvoření služby je složitější.</span><span class="sxs-lookup"><span data-stu-id="1877e-512">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="1877e-513">Vytvoření vlastního typu je ekvivalentní s tím, k čemu architektura slouží při použití možnosti [Konfigurovat](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="1877e-513">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="1877e-514">Volání [](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) metody Configure provede přechodný <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>obecný, který má konstruktor, který přijímá obecné zadané typy služeb.</span><span class="sxs-lookup"><span data-stu-id="1877e-514">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-post-configuration"></a><span data-ttu-id="1877e-515">Možnosti po konfiguraci</span><span class="sxs-lookup"><span data-stu-id="1877e-515">Options post-configuration</span></span>

<span data-ttu-id="1877e-516">Nastavení po konfiguraci <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>nastavte na.</span><span class="sxs-lookup"><span data-stu-id="1877e-516">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="1877e-517">Po dokončení všech <xref:Microsoft.Extensions.Options.IConfigureOptions%601> konfigurací se spustí po konfiguraci:</span><span class="sxs-lookup"><span data-stu-id="1877e-517">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="1877e-518"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>je k dispozici pro nastavení po konfiguraci s názvem:</span><span class="sxs-lookup"><span data-stu-id="1877e-518"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="1877e-519">Použijte <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> k následné konfiguraci všech instancí konfigurace:</span><span class="sxs-lookup"><span data-stu-id="1877e-519">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="1877e-520">Přístup k možnostem při spuštění</span><span class="sxs-lookup"><span data-stu-id="1877e-520">Accessing options during startup</span></span>

<span data-ttu-id="1877e-521"><xref:Microsoft.Extensions.Options.IOptions%601>a <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> lze jej použít v `Startup.Configure`, protože `Configure` služby jsou vytvořeny před spuštěním metody.</span><span class="sxs-lookup"><span data-stu-id="1877e-521"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="1877e-522">Nepoužívejte <xref:Microsoft.Extensions.Options.IOptions%601> nebo <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="1877e-522">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="1877e-523">Stav nekonzistentních možností může existovat v důsledku řazení registrací služby.</span><span class="sxs-lookup"><span data-stu-id="1877e-523">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="1877e-524">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="1877e-524">Additional resources</span></span>

* <xref:fundamentals/configuration/index>

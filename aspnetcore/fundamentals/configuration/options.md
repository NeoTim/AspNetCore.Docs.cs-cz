---
title: Vzor možností v ASP.NET Core
author: guardrex
description: Zjistěte, jak pomocí vzoru možností znázornit skupiny souvisejících nastavení v aplikacích ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/19/2019
uid: fundamentals/configuration/options
ms.openlocfilehash: 753afb9def90fd35122182f260f1dd0ce8d2830a
ms.sourcegitcommit: 020c3760492efed71b19e476f25392dda5dd7388
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/12/2019
ms.locfileid: "72288954"
---
# <a name="options-pattern-in-aspnet-core"></a><span data-ttu-id="63157-103">Vzor možností v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="63157-103">Options pattern in ASP.NET Core</span></span>

<span data-ttu-id="63157-104">Od [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="63157-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="63157-105">Vzor možností používá třídy, které reprezentují skupiny souvisejících nastavení.</span><span class="sxs-lookup"><span data-stu-id="63157-105">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="63157-106">Když jsou [nastavení konfigurace](xref:fundamentals/configuration/index) izolována scénářem do samostatných tříd, aplikace dodržuje dva důležité principy technického řízení:</span><span class="sxs-lookup"><span data-stu-id="63157-106">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="63157-107">[Princip oddělování rozhraní (ISP) nebo zapouzdření](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; scénáře (třídy), které závisí na nastavení konfigurace, závisí jenom na nastavení konfigurace, která používají.</span><span class="sxs-lookup"><span data-stu-id="63157-107">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="63157-108">[Oddělení](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) potíží, které se týkají &ndash; pro různé části aplikace, není závislé nebo vzájemně nezávisle na sobě.</span><span class="sxs-lookup"><span data-stu-id="63157-108">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="63157-109">Možnosti také poskytují mechanismus pro ověření konfiguračních dat.</span><span class="sxs-lookup"><span data-stu-id="63157-109">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="63157-110">Další informace najdete v části [ověřování možností](#options-validation) .</span><span class="sxs-lookup"><span data-stu-id="63157-110">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="63157-111">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="63157-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="63157-112">Požadavky</span><span class="sxs-lookup"><span data-stu-id="63157-112">Prerequisites</span></span>

<span data-ttu-id="63157-113">Odkaz na balíček [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) nebo přidejte odkaz na balíček do balíčku [Microsoft. Extensions. Options. ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) .</span><span class="sxs-lookup"><span data-stu-id="63157-113">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="63157-114">Možnosti rozhraní</span><span class="sxs-lookup"><span data-stu-id="63157-114">Options interfaces</span></span>

<span data-ttu-id="63157-115"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> slouží k načtení možností a správě oznámení možností pro instance `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="63157-115"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="63157-116"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> podporuje následující scénáře:</span><span class="sxs-lookup"><span data-stu-id="63157-116"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="63157-117">Oznámení změny</span><span class="sxs-lookup"><span data-stu-id="63157-117">Change notifications</span></span>
* [<span data-ttu-id="63157-118">Pojmenované možnosti</span><span class="sxs-lookup"><span data-stu-id="63157-118">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="63157-119">Znovu Načítatelné konfigurace</span><span class="sxs-lookup"><span data-stu-id="63157-119">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="63157-120">Neplatné selektivní možnosti (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="63157-120">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="63157-121">Scénáře po [konfiguraci](#options-post-configuration) umožňují nastavit nebo změnit možnosti po všech konfiguracích <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="63157-121">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="63157-122"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> zodpovídá za vytváření nových instancí možností.</span><span class="sxs-lookup"><span data-stu-id="63157-122"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="63157-123">Má jednu metodu <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*>.</span><span class="sxs-lookup"><span data-stu-id="63157-123">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="63157-124">Výchozí implementace přebírá všechny zaregistrované <xref:Microsoft.Extensions.Options.IConfigureOptions%601> a <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> a nejprve spustí všechny konfigurace a poté po konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="63157-124">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="63157-125">Rozlišuje mezi <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> a <xref:Microsoft.Extensions.Options.IConfigureOptions%601> a volá pouze příslušné rozhraní.</span><span class="sxs-lookup"><span data-stu-id="63157-125">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="63157-126"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> používá <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> k ukládání instancí `TOptions` do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="63157-126"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="63157-127">@No__t-0 zruší platnost instancí možností v monitorování tak, aby se hodnota přepočítala (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="63157-127">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="63157-128">Hodnoty lze ručně zavést pomocí <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span><span class="sxs-lookup"><span data-stu-id="63157-128">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="63157-129">Metoda <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> se používá v případě, že by se všechny pojmenované instance měly znovu vytvořit na vyžádání.</span><span class="sxs-lookup"><span data-stu-id="63157-129">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="63157-130"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> je užitečné v situacích, kdy by se měly možnosti Přepočítat při každém požadavku.</span><span class="sxs-lookup"><span data-stu-id="63157-130"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="63157-131">Další informace najdete v části [opětovné načtení konfiguračních dat pomocí IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) .</span><span class="sxs-lookup"><span data-stu-id="63157-131">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="63157-132"><xref:Microsoft.Extensions.Options.IOptions%601> se dá použít k podpoře možností.</span><span class="sxs-lookup"><span data-stu-id="63157-132"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="63157-133">@No__t-0 však nepodporuje předchozí scénáře <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="63157-133">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="63157-134">@No__t-0 můžete dál používat ve stávajících architekturách a knihovnách, které už používají rozhraní <xref:Microsoft.Extensions.Options.IOptions%601> a nevyžadují scénáře, které poskytuje <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="63157-134">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="63157-135">Konfigurace obecných možností</span><span class="sxs-lookup"><span data-stu-id="63157-135">General options configuration</span></span>

<span data-ttu-id="63157-136">Konfigurace obecných možností se v ukázkové aplikaci ukazuje jako příklad &num;1.</span><span class="sxs-lookup"><span data-stu-id="63157-136">General options configuration is demonstrated as Example &num;1 in the sample app.</span></span>

<span data-ttu-id="63157-137">Třída Options musí být neabstraktní s veřejným konstruktorem bez parametrů.</span><span class="sxs-lookup"><span data-stu-id="63157-137">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="63157-138">Následující třída `MyOptions` má dvě vlastnosti `Option1` a `Option2`.</span><span class="sxs-lookup"><span data-stu-id="63157-138">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="63157-139">Nastavení výchozích hodnot je volitelné, ale konstruktor třídy v následujícím příkladu nastaví výchozí hodnotu `Option1`.</span><span class="sxs-lookup"><span data-stu-id="63157-139">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="63157-140">`Option2` má výchozí hodnotu nastavenou tak, že vlastnost inicializuje přímo (*modely/MyOptions. cs*):</span><span class="sxs-lookup"><span data-stu-id="63157-140">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="63157-141">Třída `MyOptions` je přidána do kontejneru služby s <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> a je svázána s konfigurací:</span><span class="sxs-lookup"><span data-stu-id="63157-141">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="63157-142">Následující model stránky používá [vkládání závislostí konstruktoru](xref:mvc/controllers/dependency-injection) s <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> pro přístup k nastavení (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="63157-142">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="63157-143">Soubor *appSettings. JSON* v ukázce určuje hodnoty pro `option1` a `option2`:</span><span class="sxs-lookup"><span data-stu-id="63157-143">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/3.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="63157-144">Když je aplikace spuštěna, metoda `OnGet` modelu stránky vrátí řetězec zobrazující hodnoty třídy možností:</span><span class="sxs-lookup"><span data-stu-id="63157-144">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="63157-145">Když pomocí vlastního <xref:System.Configuration.ConfigurationBuilder> načtěte konfiguraci možností ze souboru nastavení, zkontrolujte, jestli je správně nastavená základní cesta:</span><span class="sxs-lookup"><span data-stu-id="63157-145">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="63157-146">Při načítání konfigurace možností ze souboru nastavení prostřednictvím <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> není nutné explicitně nastavit základní cestu.</span><span class="sxs-lookup"><span data-stu-id="63157-146">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="63157-147">Konfigurace jednoduchých možností pomocí delegáta</span><span class="sxs-lookup"><span data-stu-id="63157-147">Configure simple options with a delegate</span></span>

<span data-ttu-id="63157-148">Konfigurace jednoduchých možností s delegátem je znázorněna jako příklad &num;2 v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="63157-148">Configuring simple options with a delegate is demonstrated as Example &num;2 in the sample app.</span></span>

<span data-ttu-id="63157-149">K nastavení hodnot možností použijte delegáta.</span><span class="sxs-lookup"><span data-stu-id="63157-149">Use a delegate to set options values.</span></span> <span data-ttu-id="63157-150">Ukázková aplikace používá třídu `MyOptionsWithDelegateConfig` (*modely/MyOptionsWithDelegateConfig. cs*):</span><span class="sxs-lookup"><span data-stu-id="63157-150">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="63157-151">V následujícím kódu je do kontejneru služby přidána druhá služba <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="63157-151">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="63157-152">Pomocí delegáta konfiguruje vazbu s `MyOptionsWithDelegateConfig`:</span><span class="sxs-lookup"><span data-stu-id="63157-152">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="63157-153">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="63157-153">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="63157-154">Můžete přidat více poskytovatelů konfigurace.</span><span class="sxs-lookup"><span data-stu-id="63157-154">You can add multiple configuration providers.</span></span> <span data-ttu-id="63157-155">Poskytovatelé konfigurace jsou k dispozici z balíčků NuGet a jsou aplikováni v pořadí, v jakém jsou registrováni.</span><span class="sxs-lookup"><span data-stu-id="63157-155">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="63157-156">Další informace najdete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="63157-156">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="63157-157">Každé volání <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> přidá do kontejneru služby službu <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="63157-157">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="63157-158">V předchozím příkladu jsou hodnoty `Option1` a `Option2` zadány v souboru *appSettings. JSON*, ale hodnoty `Option1` a `Option2` jsou přepsány nakonfigurovaným delegátem.</span><span class="sxs-lookup"><span data-stu-id="63157-158">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="63157-159">Je-li povolena více než jedna služba konfigurace, je v posledním zdroji konfigurace zadaný *Server WINS* a nastavena hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="63157-159">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="63157-160">Když je aplikace spuštěna, metoda `OnGet` modelu stránky vrátí řetězec zobrazující hodnoty třídy možností:</span><span class="sxs-lookup"><span data-stu-id="63157-160">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="63157-161">Konfigurace podNastavení</span><span class="sxs-lookup"><span data-stu-id="63157-161">Suboptions configuration</span></span>

<span data-ttu-id="63157-162">Konfigurace dílčích možností se v ukázkové aplikaci ukazuje jako příklad &num;3.</span><span class="sxs-lookup"><span data-stu-id="63157-162">Suboptions configuration is demonstrated as Example &num;3 in the sample app.</span></span>

<span data-ttu-id="63157-163">Aplikace by měly vytvořit třídy možností, které se týkají konkrétních skupin scénářů (tříd) v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="63157-163">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="63157-164">Části aplikace, které vyžadují konfigurační hodnoty, by měly mít přístup jenom k hodnotám konfigurace, které používají.</span><span class="sxs-lookup"><span data-stu-id="63157-164">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="63157-165">Při vytváření vazby mezi možnostmi konfigurace jsou jednotlivé vlastnosti v typu možnosti vázány na konfigurační klíč formuláře `property[:sub-property:]`.</span><span class="sxs-lookup"><span data-stu-id="63157-165">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="63157-166">Například vlastnost `MyOptions.Option1` je vázána na klíč `Option1`, který je načten z vlastnosti `option1` v souboru *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="63157-166">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="63157-167">V následujícím kódu je třetí služba <xref:Microsoft.Extensions.Options.IConfigureOptions%601> přidána do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="63157-167">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="63157-168">Vytvoří vazby `MySubOptions` na oddíl `subsection` souboru *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="63157-168">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="63157-169">Metoda rozšíření `GetSection` vyžaduje balíček [Microsoft. Extensions. Options. ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="63157-169">The `GetSection` extension method requires the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) NuGet package.</span></span> <span data-ttu-id="63157-170">Pokud aplikace používá [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 nebo novější), balíček je automaticky zahrnutý.</span><span class="sxs-lookup"><span data-stu-id="63157-170">If the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later), the package is automatically included.</span></span>

<span data-ttu-id="63157-171">Soubor *appSettings. JSON* ve vzorci definuje člena `subsection` s klíči pro `suboption1` a `suboption2`:</span><span class="sxs-lookup"><span data-stu-id="63157-171">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/3.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="63157-172">Třída `MySubOptions` definuje vlastnosti, `SubOption1` a `SubOption2`, aby obsahovaly hodnoty možností (*modely/MySubOptions. cs*):</span><span class="sxs-lookup"><span data-stu-id="63157-172">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="63157-173">Metoda `OnGet` modelu stránky vrací řetězec s hodnotami možností (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="63157-173">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="63157-174">Při spuštění aplikace vrátí metoda `OnGet` řetězec zobrazující hodnoty třídy dílčích možností:</span><span class="sxs-lookup"><span data-stu-id="63157-174">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="63157-175">Možnosti poskytované modelem zobrazení nebo vkládáním přímých zobrazení</span><span class="sxs-lookup"><span data-stu-id="63157-175">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="63157-176">Možnosti poskytované modelem zobrazení nebo vkládáním přímých zobrazení jsou v ukázkové aplikaci znázorněné jako příklad &num;4.</span><span class="sxs-lookup"><span data-stu-id="63157-176">Options provided by a view model or with direct view injection is demonstrated as Example &num;4 in the sample app.</span></span>

<span data-ttu-id="63157-177">Možnosti lze zadat v modelu zobrazení nebo vložením <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> přímo do zobrazení (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="63157-177">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="63157-178">Ukázková aplikace ukazuje, jak vložit `IOptionsMonitor<MyOptions>` s direktivou `@inject`:</span><span class="sxs-lookup"><span data-stu-id="63157-178">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/3.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="63157-179">Po spuštění aplikace se hodnoty možností zobrazí na vykreslené stránce:</span><span class="sxs-lookup"><span data-stu-id="63157-179">When the app is run, the options values are shown in the rendered page:</span></span>

![Hodnoty možností možnost1: value1_from_json a Možnost2:-1 jsou načteny z modelu a vložením do zobrazení.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="63157-181">Znovu načíst konfigurační data pomocí IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="63157-181">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="63157-182">Opětovné načtení konfiguračních dat pomocí <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> je v ukázkové aplikaci znázorněno v příkladu &num;5.</span><span class="sxs-lookup"><span data-stu-id="63157-182">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example &num;5 in the sample app.</span></span>

<span data-ttu-id="63157-183"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> podporuje opětovné načítání možností s minimální režií zpracování.</span><span class="sxs-lookup"><span data-stu-id="63157-183"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="63157-184">Možnosti jsou vypočítány jednou za požadavek při otevření a ukládání do mezipaměti po dobu života žádosti.</span><span class="sxs-lookup"><span data-stu-id="63157-184">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="63157-185">Následující příklad ukazuje, jak se vytvoří nový <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> po změnách *appSettings. JSON* (*pages/index. cshtml. cs*).</span><span class="sxs-lookup"><span data-stu-id="63157-185">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="63157-186">Více požadavků na server vrací konstantní hodnoty, které poskytuje soubor *appSettings. JSON* , dokud nedojde ke změně souboru a opětovnému načtení konfigurace.</span><span class="sxs-lookup"><span data-stu-id="63157-186">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="63157-187">Následující obrázek ukazuje počáteční hodnoty `option1` a `option2` načtené ze souboru *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="63157-187">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="63157-188">Změňte hodnoty v souboru *appSettings. JSON* na `value1_from_json UPDATED` a `200`.</span><span class="sxs-lookup"><span data-stu-id="63157-188">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="63157-189">Uložte soubor *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="63157-189">Save the *appsettings.json* file.</span></span> <span data-ttu-id="63157-190">Aktualizujte prohlížeč, aby se zobrazily hodnoty možností, které jsou aktualizované:</span><span class="sxs-lookup"><span data-stu-id="63157-190">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="63157-191">Podpora pojmenovaných možností s IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="63157-191">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="63157-192">Pojmenovaná podpora s možností <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> je v ukázkové aplikaci znázorněna jako příklad &num;6.</span><span class="sxs-lookup"><span data-stu-id="63157-192">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example &num;6 in the sample app.</span></span>

<span data-ttu-id="63157-193">Podpora *pojmenovaných možností* umožňuje aplikaci rozlišovat konfigurace pojmenovaných možností.</span><span class="sxs-lookup"><span data-stu-id="63157-193">*Named options* support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="63157-194">V ukázkové aplikaci jsou pojmenované možnosti deklarovány pomocí [OptionsServiceCollectionExtensions. Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), který volá [ConfigureNamedOptions @ no__t-2TOptions >. Konfigurace](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) metody rozšíření:</span><span class="sxs-lookup"><span data-stu-id="63157-194">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="63157-195">Ukázková aplikace přistupuje k pojmenovaným možnostem <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="63157-195">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="63157-196">Když spustíte ukázkovou aplikaci, vrátí se pojmenované možnosti:</span><span class="sxs-lookup"><span data-stu-id="63157-196">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="63157-197">hodnoty `named_options_1` se poskytují z konfigurace, které se načítají ze souboru *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="63157-197">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="63157-198">hodnoty `named_options_2` poskytuje:</span><span class="sxs-lookup"><span data-stu-id="63157-198">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="63157-199">Delegát `named_options_2` v `ConfigureServices` pro `Option1`.</span><span class="sxs-lookup"><span data-stu-id="63157-199">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="63157-200">Výchozí hodnota pro `Option2` poskytovaná třídou `MyOptions`.</span><span class="sxs-lookup"><span data-stu-id="63157-200">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="63157-201">Konfigurace všech možností pomocí metody ConfigureAll</span><span class="sxs-lookup"><span data-stu-id="63157-201">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="63157-202">Nakonfigurujte všechny instance možností pomocí metody <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*>.</span><span class="sxs-lookup"><span data-stu-id="63157-202">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="63157-203">Následující kód konfiguruje `Option1` pro všechny instance konfigurace se společnou hodnotou.</span><span class="sxs-lookup"><span data-stu-id="63157-203">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="63157-204">Přidejte následující kód ručně do metody `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="63157-204">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="63157-205">Spuštění ukázkové aplikace po přidání kódu vytvoří následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="63157-205">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="63157-206">Všechny možnosti jsou pojmenované instance.</span><span class="sxs-lookup"><span data-stu-id="63157-206">All options are named instances.</span></span> <span data-ttu-id="63157-207">Existující instance <xref:Microsoft.Extensions.Options.IConfigureOptions%601> se považují za cílení na instanci `Options.DefaultName`, která je `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="63157-207">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="63157-208"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> také implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="63157-208"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="63157-209">Výchozí implementace <xref:Microsoft.Extensions.Options.IOptionsFactory%601> má logiku odpovídajícím způsobem použít.</span><span class="sxs-lookup"><span data-stu-id="63157-209">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="63157-210">Pojmenovaná možnost `null` se používá k cílení na všechny pojmenované instance místo konkrétní pojmenované instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> a <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> používají tuto konvenci).</span><span class="sxs-lookup"><span data-stu-id="63157-210">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="63157-211">Rozhraní API pro OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="63157-211">OptionsBuilder API</span></span>

<span data-ttu-id="63157-212"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> se používá ke konfiguraci instancí `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="63157-212"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="63157-213">`OptionsBuilder` zjednodušuje vytváření pojmenovaných možností, protože se jedná pouze o jediný parametr počátečního volání `AddOptions<TOptions>(string optionsName)` namísto zobrazení ve všech následujících voláních.</span><span class="sxs-lookup"><span data-stu-id="63157-213">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="63157-214">Ověřování možností a přetížení `ConfigureOptions`, které přijímají závislosti služby, jsou k dispozici pouze prostřednictvím `OptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="63157-214">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="63157-215">Ke konfiguraci možností použijte DI Services.</span><span class="sxs-lookup"><span data-stu-id="63157-215">Use DI services to configure options</span></span>

<span data-ttu-id="63157-216">K dalším službám můžete přistupovat pomocí injektáže závislosti při konfiguraci možností dvěma způsoby:</span><span class="sxs-lookup"><span data-stu-id="63157-216">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="63157-217">[Nakonfigurujte](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) delegáta konfigurace na [OptionsBuilder @ no__t-2TOptions >](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="63157-217">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="63157-218">[OptionsBuilder @ no__t-1TOptions >](xref:Microsoft.Extensions.Options.OptionsBuilder`1) poskytuje přetížení [Konfigurace](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) , která umožňují použít až pět služeb ke konfiguraci možností:</span><span class="sxs-lookup"><span data-stu-id="63157-218">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="63157-219">Vytvořte vlastní typ, který implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> nebo <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> a zaregistrujte typ jako službu.</span><span class="sxs-lookup"><span data-stu-id="63157-219">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="63157-220">Doporučujeme předat delegáta [konfigurace, protože](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)vytvoření služby je složitější.</span><span class="sxs-lookup"><span data-stu-id="63157-220">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="63157-221">Vytvoření vlastního typu je ekvivalentní s tím, k čemu architektura slouží při použití možnosti [Konfigurovat](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="63157-221">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="63157-222">Volání metody [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) obsahuje přechodný obecný <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, který má konstruktor, který přijímá obecné zadané typy služeb.</span><span class="sxs-lookup"><span data-stu-id="63157-222">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="63157-223">Ověřování možností</span><span class="sxs-lookup"><span data-stu-id="63157-223">Options validation</span></span>

<span data-ttu-id="63157-224">Ověřování možností umožňuje ověřit možnosti při konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="63157-224">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="63157-225">Volejte `Validate` s metodou ověřování, která vrátí `true`, pokud jsou možnosti platné a `false`, pokud nejsou platné:</span><span class="sxs-lookup"><span data-stu-id="63157-225">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

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

<span data-ttu-id="63157-226">Předchozí příklad nastaví instanci pojmenovaných možností na `optionalOptionsName`.</span><span class="sxs-lookup"><span data-stu-id="63157-226">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="63157-227">Instance výchozích možností je `Options.DefaultName`.</span><span class="sxs-lookup"><span data-stu-id="63157-227">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="63157-228">Ověřování se spustí při vytvoření instance Options.</span><span class="sxs-lookup"><span data-stu-id="63157-228">Validation runs when the options instance is created.</span></span> <span data-ttu-id="63157-229">Při prvním použití vaší instance možností je zaručeno předání ověření.</span><span class="sxs-lookup"><span data-stu-id="63157-229">Your options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="63157-230">Ověřování možností nechrání proti změnám možností po počáteční konfiguraci a ověření možností.</span><span class="sxs-lookup"><span data-stu-id="63157-230">Options validation doesn't guard against options modifications after the options are initially configured and validated.</span></span>

<span data-ttu-id="63157-231">Metoda `Validate` přijímá hodnotu `Func<TOptions, bool>`.</span><span class="sxs-lookup"><span data-stu-id="63157-231">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="63157-232">Chcete-li plně přizpůsobit ověřování, implementujte `IValidateOptions<TOptions>`, což umožňuje:</span><span class="sxs-lookup"><span data-stu-id="63157-232">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="63157-233">Ověřování více typů možností: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="63157-233">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="63157-234">Ověření, které závisí na jiném typu možnosti: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="63157-234">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="63157-235">`IValidateOptions` ověří:</span><span class="sxs-lookup"><span data-stu-id="63157-235">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="63157-236">Konkrétní instance pojmenované možnosti.</span><span class="sxs-lookup"><span data-stu-id="63157-236">A specific named options instance.</span></span>
* <span data-ttu-id="63157-237">Všechny možnosti, pokud `name` je `null`.</span><span class="sxs-lookup"><span data-stu-id="63157-237">All options when `name` is `null`.</span></span>

<span data-ttu-id="63157-238">Vrácení `ValidateOptionsResult` z vaší implementace rozhraní:</span><span class="sxs-lookup"><span data-stu-id="63157-238">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="63157-239">Ověřování na základě datových poznámek je k dispozici v balíčku [Microsoft. Extensions. Options. DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) pomocí volání metody <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> v `OptionsBuilder<TOptions>`.</span><span class="sxs-lookup"><span data-stu-id="63157-239">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="63157-240">`Microsoft.Extensions.Options.DataAnnotations` je součástí [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2,2 nebo novější).</span><span class="sxs-lookup"><span data-stu-id="63157-240">`Microsoft.Extensions.Options.DataAnnotations` is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.2 or later).</span></span>

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

<span data-ttu-id="63157-241">Ověření Eager (při spuštění selže) je v budoucí verzi zváženo.</span><span class="sxs-lookup"><span data-stu-id="63157-241">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="63157-242">Možnosti po konfiguraci</span><span class="sxs-lookup"><span data-stu-id="63157-242">Options post-configuration</span></span>

<span data-ttu-id="63157-243">Nastavte po konfiguraci <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="63157-243">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="63157-244">Po spuštění všech konfigurací <xref:Microsoft.Extensions.Options.IConfigureOptions%601> se spustí po konfiguraci:</span><span class="sxs-lookup"><span data-stu-id="63157-244">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="63157-245"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> je k dispozici pro nastavení po konfiguraci s názvem:</span><span class="sxs-lookup"><span data-stu-id="63157-245"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="63157-246">Po dokončení konfigurace všech instancí konfigurace použijte <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*>:</span><span class="sxs-lookup"><span data-stu-id="63157-246">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="63157-247">Přístup k možnostem při spuštění</span><span class="sxs-lookup"><span data-stu-id="63157-247">Accessing options during startup</span></span>

<span data-ttu-id="63157-248"><xref:Microsoft.Extensions.Options.IOptions%601> a <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> lze použít v `Startup.Configure`, protože služby jsou vytvořeny před spuštěním metody `Configure`.</span><span class="sxs-lookup"><span data-stu-id="63157-248"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="63157-249">V `Startup.ConfigureServices` Nepoužívejte <xref:Microsoft.Extensions.Options.IOptions%601> ani <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="63157-249">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="63157-250">Stav nekonzistentních možností může existovat v důsledku řazení registrací služby.</span><span class="sxs-lookup"><span data-stu-id="63157-250">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="63157-251">Vzor možností používá třídy, které reprezentují skupiny souvisejících nastavení.</span><span class="sxs-lookup"><span data-stu-id="63157-251">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="63157-252">Když jsou [nastavení konfigurace](xref:fundamentals/configuration/index) izolována scénářem do samostatných tříd, aplikace dodržuje dva důležité principy technického řízení:</span><span class="sxs-lookup"><span data-stu-id="63157-252">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="63157-253">[Princip oddělování rozhraní (ISP) nebo zapouzdření](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; scénáře (třídy), které závisí na nastavení konfigurace, závisí jenom na nastavení konfigurace, která používají.</span><span class="sxs-lookup"><span data-stu-id="63157-253">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="63157-254">[Oddělení](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) potíží, které se týkají &ndash; pro různé části aplikace, není závislé nebo vzájemně nezávisle na sobě.</span><span class="sxs-lookup"><span data-stu-id="63157-254">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="63157-255">Možnosti také poskytují mechanismus pro ověření konfiguračních dat.</span><span class="sxs-lookup"><span data-stu-id="63157-255">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="63157-256">Další informace najdete v části [ověřování možností](#options-validation) .</span><span class="sxs-lookup"><span data-stu-id="63157-256">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="63157-257">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="63157-257">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="63157-258">Požadavky</span><span class="sxs-lookup"><span data-stu-id="63157-258">Prerequisites</span></span>

<span data-ttu-id="63157-259">Odkaz na balíček [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) nebo přidejte odkaz na balíček do balíčku [Microsoft. Extensions. Options. ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) .</span><span class="sxs-lookup"><span data-stu-id="63157-259">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="63157-260">Možnosti rozhraní</span><span class="sxs-lookup"><span data-stu-id="63157-260">Options interfaces</span></span>

<span data-ttu-id="63157-261"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> slouží k načtení možností a správě oznámení možností pro instance `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="63157-261"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="63157-262"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> podporuje následující scénáře:</span><span class="sxs-lookup"><span data-stu-id="63157-262"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="63157-263">Oznámení změny</span><span class="sxs-lookup"><span data-stu-id="63157-263">Change notifications</span></span>
* [<span data-ttu-id="63157-264">Pojmenované možnosti</span><span class="sxs-lookup"><span data-stu-id="63157-264">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="63157-265">Znovu Načítatelné konfigurace</span><span class="sxs-lookup"><span data-stu-id="63157-265">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="63157-266">Neplatné selektivní možnosti (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="63157-266">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="63157-267">Scénáře po [konfiguraci](#options-post-configuration) umožňují nastavit nebo změnit možnosti po všech konfiguracích <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="63157-267">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="63157-268"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> zodpovídá za vytváření nových instancí možností.</span><span class="sxs-lookup"><span data-stu-id="63157-268"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="63157-269">Má jednu metodu <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*>.</span><span class="sxs-lookup"><span data-stu-id="63157-269">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="63157-270">Výchozí implementace přebírá všechny zaregistrované <xref:Microsoft.Extensions.Options.IConfigureOptions%601> a <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> a nejprve spustí všechny konfigurace a poté po konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="63157-270">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="63157-271">Rozlišuje mezi <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> a <xref:Microsoft.Extensions.Options.IConfigureOptions%601> a volá pouze příslušné rozhraní.</span><span class="sxs-lookup"><span data-stu-id="63157-271">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="63157-272"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> používá <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> k ukládání instancí `TOptions` do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="63157-272"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="63157-273">@No__t-0 zruší platnost instancí možností v monitorování tak, aby se hodnota přepočítala (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="63157-273">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="63157-274">Hodnoty lze ručně zavést pomocí <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span><span class="sxs-lookup"><span data-stu-id="63157-274">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="63157-275">Metoda <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> se používá v případě, že by se všechny pojmenované instance měly znovu vytvořit na vyžádání.</span><span class="sxs-lookup"><span data-stu-id="63157-275">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="63157-276"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> je užitečné v situacích, kdy by se měly možnosti Přepočítat při každém požadavku.</span><span class="sxs-lookup"><span data-stu-id="63157-276"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="63157-277">Další informace najdete v části [opětovné načtení konfiguračních dat pomocí IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) .</span><span class="sxs-lookup"><span data-stu-id="63157-277">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="63157-278"><xref:Microsoft.Extensions.Options.IOptions%601> se dá použít k podpoře možností.</span><span class="sxs-lookup"><span data-stu-id="63157-278"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="63157-279">@No__t-0 však nepodporuje předchozí scénáře <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="63157-279">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="63157-280">@No__t-0 můžete dál používat ve stávajících architekturách a knihovnách, které už používají rozhraní <xref:Microsoft.Extensions.Options.IOptions%601> a nevyžadují scénáře, které poskytuje <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="63157-280">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="63157-281">Konfigurace obecných možností</span><span class="sxs-lookup"><span data-stu-id="63157-281">General options configuration</span></span>

<span data-ttu-id="63157-282">Konfigurace obecných možností se v ukázkové aplikaci ukazuje jako příklad &num;1.</span><span class="sxs-lookup"><span data-stu-id="63157-282">General options configuration is demonstrated as Example &num;1 in the sample app.</span></span>

<span data-ttu-id="63157-283">Třída Options musí být neabstraktní s veřejným konstruktorem bez parametrů.</span><span class="sxs-lookup"><span data-stu-id="63157-283">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="63157-284">Následující třída `MyOptions` má dvě vlastnosti `Option1` a `Option2`.</span><span class="sxs-lookup"><span data-stu-id="63157-284">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="63157-285">Nastavení výchozích hodnot je volitelné, ale konstruktor třídy v následujícím příkladu nastaví výchozí hodnotu `Option1`.</span><span class="sxs-lookup"><span data-stu-id="63157-285">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="63157-286">`Option2` má výchozí hodnotu nastavenou tak, že vlastnost inicializuje přímo (*modely/MyOptions. cs*):</span><span class="sxs-lookup"><span data-stu-id="63157-286">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="63157-287">Třída `MyOptions` je přidána do kontejneru služby s <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> a je svázána s konfigurací:</span><span class="sxs-lookup"><span data-stu-id="63157-287">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="63157-288">Následující model stránky používá [vkládání závislostí konstruktoru](xref:mvc/controllers/dependency-injection) s <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> pro přístup k nastavení (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="63157-288">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="63157-289">Soubor *appSettings. JSON* v ukázce určuje hodnoty pro `option1` a `option2`:</span><span class="sxs-lookup"><span data-stu-id="63157-289">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="63157-290">Když je aplikace spuštěna, metoda `OnGet` modelu stránky vrátí řetězec zobrazující hodnoty třídy možností:</span><span class="sxs-lookup"><span data-stu-id="63157-290">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="63157-291">Když pomocí vlastního <xref:System.Configuration.ConfigurationBuilder> načtěte konfiguraci možností ze souboru nastavení, zkontrolujte, jestli je správně nastavená základní cesta:</span><span class="sxs-lookup"><span data-stu-id="63157-291">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="63157-292">Při načítání konfigurace možností ze souboru nastavení prostřednictvím <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> není nutné explicitně nastavit základní cestu.</span><span class="sxs-lookup"><span data-stu-id="63157-292">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="63157-293">Konfigurace jednoduchých možností pomocí delegáta</span><span class="sxs-lookup"><span data-stu-id="63157-293">Configure simple options with a delegate</span></span>

<span data-ttu-id="63157-294">Konfigurace jednoduchých možností s delegátem je znázorněna jako příklad &num;2 v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="63157-294">Configuring simple options with a delegate is demonstrated as Example &num;2 in the sample app.</span></span>

<span data-ttu-id="63157-295">K nastavení hodnot možností použijte delegáta.</span><span class="sxs-lookup"><span data-stu-id="63157-295">Use a delegate to set options values.</span></span> <span data-ttu-id="63157-296">Ukázková aplikace používá třídu `MyOptionsWithDelegateConfig` (*modely/MyOptionsWithDelegateConfig. cs*):</span><span class="sxs-lookup"><span data-stu-id="63157-296">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="63157-297">V následujícím kódu je do kontejneru služby přidána druhá služba <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="63157-297">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="63157-298">Pomocí delegáta konfiguruje vazbu s `MyOptionsWithDelegateConfig`:</span><span class="sxs-lookup"><span data-stu-id="63157-298">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="63157-299">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="63157-299">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="63157-300">Můžete přidat více poskytovatelů konfigurace.</span><span class="sxs-lookup"><span data-stu-id="63157-300">You can add multiple configuration providers.</span></span> <span data-ttu-id="63157-301">Poskytovatelé konfigurace jsou k dispozici z balíčků NuGet a jsou aplikováni v pořadí, v jakém jsou registrováni.</span><span class="sxs-lookup"><span data-stu-id="63157-301">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="63157-302">Další informace najdete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="63157-302">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="63157-303">Každé volání <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> přidá do kontejneru služby službu <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="63157-303">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="63157-304">V předchozím příkladu jsou hodnoty `Option1` a `Option2` zadány v souboru *appSettings. JSON*, ale hodnoty `Option1` a `Option2` jsou přepsány nakonfigurovaným delegátem.</span><span class="sxs-lookup"><span data-stu-id="63157-304">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="63157-305">Je-li povolena více než jedna služba konfigurace, je v posledním zdroji konfigurace zadaný *Server WINS* a nastavena hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="63157-305">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="63157-306">Když je aplikace spuštěna, metoda `OnGet` modelu stránky vrátí řetězec zobrazující hodnoty třídy možností:</span><span class="sxs-lookup"><span data-stu-id="63157-306">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="63157-307">Konfigurace podNastavení</span><span class="sxs-lookup"><span data-stu-id="63157-307">Suboptions configuration</span></span>

<span data-ttu-id="63157-308">Konfigurace dílčích možností se v ukázkové aplikaci ukazuje jako příklad &num;3.</span><span class="sxs-lookup"><span data-stu-id="63157-308">Suboptions configuration is demonstrated as Example &num;3 in the sample app.</span></span>

<span data-ttu-id="63157-309">Aplikace by měly vytvořit třídy možností, které se týkají konkrétních skupin scénářů (tříd) v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="63157-309">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="63157-310">Části aplikace, které vyžadují konfigurační hodnoty, by měly mít přístup jenom k hodnotám konfigurace, které používají.</span><span class="sxs-lookup"><span data-stu-id="63157-310">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="63157-311">Při vytváření vazby mezi možnostmi konfigurace jsou jednotlivé vlastnosti v typu možnosti vázány na konfigurační klíč formuláře `property[:sub-property:]`.</span><span class="sxs-lookup"><span data-stu-id="63157-311">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="63157-312">Například vlastnost `MyOptions.Option1` je vázána na klíč `Option1`, který je načten z vlastnosti `option1` v souboru *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="63157-312">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="63157-313">V následujícím kódu je třetí služba <xref:Microsoft.Extensions.Options.IConfigureOptions%601> přidána do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="63157-313">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="63157-314">Vytvoří vazby `MySubOptions` na oddíl `subsection` souboru *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="63157-314">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="63157-315">Metoda rozšíření `GetSection` vyžaduje balíček [Microsoft. Extensions. Options. ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="63157-315">The `GetSection` extension method requires the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) NuGet package.</span></span> <span data-ttu-id="63157-316">Pokud aplikace používá [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 nebo novější), balíček je automaticky zahrnutý.</span><span class="sxs-lookup"><span data-stu-id="63157-316">If the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later), the package is automatically included.</span></span>

<span data-ttu-id="63157-317">Soubor *appSettings. JSON* ve vzorci definuje člena `subsection` s klíči pro `suboption1` a `suboption2`:</span><span class="sxs-lookup"><span data-stu-id="63157-317">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="63157-318">Třída `MySubOptions` definuje vlastnosti, `SubOption1` a `SubOption2`, aby obsahovaly hodnoty možností (*modely/MySubOptions. cs*):</span><span class="sxs-lookup"><span data-stu-id="63157-318">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="63157-319">Metoda `OnGet` modelu stránky vrací řetězec s hodnotami možností (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="63157-319">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="63157-320">Při spuštění aplikace vrátí metoda `OnGet` řetězec zobrazující hodnoty třídy dílčích možností:</span><span class="sxs-lookup"><span data-stu-id="63157-320">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="63157-321">Možnosti poskytované modelem zobrazení nebo vkládáním přímých zobrazení</span><span class="sxs-lookup"><span data-stu-id="63157-321">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="63157-322">Možnosti poskytované modelem zobrazení nebo vkládáním přímých zobrazení jsou v ukázkové aplikaci znázorněné jako příklad &num;4.</span><span class="sxs-lookup"><span data-stu-id="63157-322">Options provided by a view model or with direct view injection is demonstrated as Example &num;4 in the sample app.</span></span>

<span data-ttu-id="63157-323">Možnosti lze zadat v modelu zobrazení nebo vložením <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> přímo do zobrazení (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="63157-323">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="63157-324">Ukázková aplikace ukazuje, jak vložit `IOptionsMonitor<MyOptions>` s direktivou `@inject`:</span><span class="sxs-lookup"><span data-stu-id="63157-324">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="63157-325">Po spuštění aplikace se hodnoty možností zobrazí na vykreslené stránce:</span><span class="sxs-lookup"><span data-stu-id="63157-325">When the app is run, the options values are shown in the rendered page:</span></span>

![Hodnoty možností možnost1: value1_from_json a Možnost2:-1 jsou načteny z modelu a vložením do zobrazení.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="63157-327">Znovu načíst konfigurační data pomocí IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="63157-327">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="63157-328">Opětovné načtení konfiguračních dat pomocí <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> je v ukázkové aplikaci znázorněno v příkladu &num;5.</span><span class="sxs-lookup"><span data-stu-id="63157-328">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example &num;5 in the sample app.</span></span>

<span data-ttu-id="63157-329"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> podporuje opětovné načítání možností s minimální režií zpracování.</span><span class="sxs-lookup"><span data-stu-id="63157-329"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="63157-330">Možnosti jsou vypočítány jednou za požadavek při otevření a ukládání do mezipaměti po dobu života žádosti.</span><span class="sxs-lookup"><span data-stu-id="63157-330">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="63157-331">Následující příklad ukazuje, jak se vytvoří nový <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> po změnách *appSettings. JSON* (*pages/index. cshtml. cs*).</span><span class="sxs-lookup"><span data-stu-id="63157-331">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="63157-332">Více požadavků na server vrací konstantní hodnoty, které poskytuje soubor *appSettings. JSON* , dokud nedojde ke změně souboru a opětovnému načtení konfigurace.</span><span class="sxs-lookup"><span data-stu-id="63157-332">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="63157-333">Následující obrázek ukazuje počáteční hodnoty `option1` a `option2` načtené ze souboru *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="63157-333">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="63157-334">Změňte hodnoty v souboru *appSettings. JSON* na `value1_from_json UPDATED` a `200`.</span><span class="sxs-lookup"><span data-stu-id="63157-334">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="63157-335">Uložte soubor *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="63157-335">Save the *appsettings.json* file.</span></span> <span data-ttu-id="63157-336">Aktualizujte prohlížeč, aby se zobrazily hodnoty možností, které jsou aktualizované:</span><span class="sxs-lookup"><span data-stu-id="63157-336">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="63157-337">Podpora pojmenovaných možností s IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="63157-337">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="63157-338">Pojmenovaná podpora s možností <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> je v ukázkové aplikaci znázorněna jako příklad &num;6.</span><span class="sxs-lookup"><span data-stu-id="63157-338">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example &num;6 in the sample app.</span></span>

<span data-ttu-id="63157-339">Podpora *pojmenovaných možností* umožňuje aplikaci rozlišovat konfigurace pojmenovaných možností.</span><span class="sxs-lookup"><span data-stu-id="63157-339">*Named options* support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="63157-340">V ukázkové aplikaci jsou pojmenované možnosti deklarovány pomocí [OptionsServiceCollectionExtensions. Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), který volá [ConfigureNamedOptions @ no__t-2TOptions >. Konfigurace](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) metody rozšíření:</span><span class="sxs-lookup"><span data-stu-id="63157-340">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="63157-341">Ukázková aplikace přistupuje k pojmenovaným možnostem <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="63157-341">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="63157-342">Když spustíte ukázkovou aplikaci, vrátí se pojmenované možnosti:</span><span class="sxs-lookup"><span data-stu-id="63157-342">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="63157-343">hodnoty `named_options_1` se poskytují z konfigurace, které se načítají ze souboru *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="63157-343">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="63157-344">hodnoty `named_options_2` poskytuje:</span><span class="sxs-lookup"><span data-stu-id="63157-344">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="63157-345">Delegát `named_options_2` v `ConfigureServices` pro `Option1`.</span><span class="sxs-lookup"><span data-stu-id="63157-345">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="63157-346">Výchozí hodnota pro `Option2` poskytovaná třídou `MyOptions`.</span><span class="sxs-lookup"><span data-stu-id="63157-346">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="63157-347">Konfigurace všech možností pomocí metody ConfigureAll</span><span class="sxs-lookup"><span data-stu-id="63157-347">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="63157-348">Nakonfigurujte všechny instance možností pomocí metody <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*>.</span><span class="sxs-lookup"><span data-stu-id="63157-348">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="63157-349">Následující kód konfiguruje `Option1` pro všechny instance konfigurace se společnou hodnotou.</span><span class="sxs-lookup"><span data-stu-id="63157-349">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="63157-350">Přidejte následující kód ručně do metody `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="63157-350">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="63157-351">Spuštění ukázkové aplikace po přidání kódu vytvoří následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="63157-351">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="63157-352">Všechny možnosti jsou pojmenované instance.</span><span class="sxs-lookup"><span data-stu-id="63157-352">All options are named instances.</span></span> <span data-ttu-id="63157-353">Existující instance <xref:Microsoft.Extensions.Options.IConfigureOptions%601> se považují za cílení na instanci `Options.DefaultName`, která je `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="63157-353">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="63157-354"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> také implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="63157-354"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="63157-355">Výchozí implementace <xref:Microsoft.Extensions.Options.IOptionsFactory%601> má logiku odpovídajícím způsobem použít.</span><span class="sxs-lookup"><span data-stu-id="63157-355">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="63157-356">Pojmenovaná možnost `null` se používá k cílení na všechny pojmenované instance místo konkrétní pojmenované instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> a <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> používají tuto konvenci).</span><span class="sxs-lookup"><span data-stu-id="63157-356">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="63157-357">Rozhraní API pro OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="63157-357">OptionsBuilder API</span></span>

<span data-ttu-id="63157-358"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> se používá ke konfiguraci instancí `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="63157-358"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="63157-359">`OptionsBuilder` zjednodušuje vytváření pojmenovaných možností, protože se jedná pouze o jediný parametr počátečního volání `AddOptions<TOptions>(string optionsName)` namísto zobrazení ve všech následujících voláních.</span><span class="sxs-lookup"><span data-stu-id="63157-359">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="63157-360">Ověřování možností a přetížení `ConfigureOptions`, které přijímají závislosti služby, jsou k dispozici pouze prostřednictvím `OptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="63157-360">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="63157-361">Ke konfiguraci možností použijte DI Services.</span><span class="sxs-lookup"><span data-stu-id="63157-361">Use DI services to configure options</span></span>

<span data-ttu-id="63157-362">K dalším službám můžete přistupovat pomocí injektáže závislosti při konfiguraci možností dvěma způsoby:</span><span class="sxs-lookup"><span data-stu-id="63157-362">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="63157-363">[Nakonfigurujte](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) delegáta konfigurace na [OptionsBuilder @ no__t-2TOptions >](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="63157-363">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="63157-364">[OptionsBuilder @ no__t-1TOptions >](xref:Microsoft.Extensions.Options.OptionsBuilder`1) poskytuje přetížení [Konfigurace](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) , která umožňují použít až pět služeb ke konfiguraci možností:</span><span class="sxs-lookup"><span data-stu-id="63157-364">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="63157-365">Vytvořte vlastní typ, který implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> nebo <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> a zaregistrujte typ jako službu.</span><span class="sxs-lookup"><span data-stu-id="63157-365">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="63157-366">Doporučujeme předat delegáta [konfigurace, protože](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)vytvoření služby je složitější.</span><span class="sxs-lookup"><span data-stu-id="63157-366">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="63157-367">Vytvoření vlastního typu je ekvivalentní s tím, k čemu architektura slouží při použití možnosti [Konfigurovat](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="63157-367">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="63157-368">Volání metody [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) obsahuje přechodný obecný <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, který má konstruktor, který přijímá obecné zadané typy služeb.</span><span class="sxs-lookup"><span data-stu-id="63157-368">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="63157-369">Ověřování možností</span><span class="sxs-lookup"><span data-stu-id="63157-369">Options validation</span></span>

<span data-ttu-id="63157-370">Ověřování možností umožňuje ověřit možnosti při konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="63157-370">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="63157-371">Volejte `Validate` s metodou ověřování, která vrátí `true`, pokud jsou možnosti platné a `false`, pokud nejsou platné:</span><span class="sxs-lookup"><span data-stu-id="63157-371">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

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

<span data-ttu-id="63157-372">Předchozí příklad nastaví instanci pojmenovaných možností na `optionalOptionsName`.</span><span class="sxs-lookup"><span data-stu-id="63157-372">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="63157-373">Instance výchozích možností je `Options.DefaultName`.</span><span class="sxs-lookup"><span data-stu-id="63157-373">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="63157-374">Ověřování se spustí při vytvoření instance Options.</span><span class="sxs-lookup"><span data-stu-id="63157-374">Validation runs when the options instance is created.</span></span> <span data-ttu-id="63157-375">Při prvním použití vaší instance možností je zaručeno předání ověření.</span><span class="sxs-lookup"><span data-stu-id="63157-375">Your options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="63157-376">Ověřování možností nechrání proti změnám možností po počáteční konfiguraci a ověření možností.</span><span class="sxs-lookup"><span data-stu-id="63157-376">Options validation doesn't guard against options modifications after the options are initially configured and validated.</span></span>

<span data-ttu-id="63157-377">Metoda `Validate` přijímá hodnotu `Func<TOptions, bool>`.</span><span class="sxs-lookup"><span data-stu-id="63157-377">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="63157-378">Chcete-li plně přizpůsobit ověřování, implementujte `IValidateOptions<TOptions>`, což umožňuje:</span><span class="sxs-lookup"><span data-stu-id="63157-378">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="63157-379">Ověřování více typů možností: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="63157-379">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="63157-380">Ověření, které závisí na jiném typu možnosti: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="63157-380">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="63157-381">`IValidateOptions` ověří:</span><span class="sxs-lookup"><span data-stu-id="63157-381">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="63157-382">Konkrétní instance pojmenované možnosti.</span><span class="sxs-lookup"><span data-stu-id="63157-382">A specific named options instance.</span></span>
* <span data-ttu-id="63157-383">Všechny možnosti, pokud `name` je `null`.</span><span class="sxs-lookup"><span data-stu-id="63157-383">All options when `name` is `null`.</span></span>

<span data-ttu-id="63157-384">Vrácení `ValidateOptionsResult` z vaší implementace rozhraní:</span><span class="sxs-lookup"><span data-stu-id="63157-384">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="63157-385">Ověřování na základě datových poznámek je k dispozici v balíčku [Microsoft. Extensions. Options. DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) pomocí volání metody <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> v `OptionsBuilder<TOptions>`.</span><span class="sxs-lookup"><span data-stu-id="63157-385">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="63157-386">`Microsoft.Extensions.Options.DataAnnotations` je součástí [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2,2 nebo novější).</span><span class="sxs-lookup"><span data-stu-id="63157-386">`Microsoft.Extensions.Options.DataAnnotations` is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.2 or later).</span></span>

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

<span data-ttu-id="63157-387">Ověření Eager (při spuštění selže) je v budoucí verzi zváženo.</span><span class="sxs-lookup"><span data-stu-id="63157-387">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="63157-388">Možnosti po konfiguraci</span><span class="sxs-lookup"><span data-stu-id="63157-388">Options post-configuration</span></span>

<span data-ttu-id="63157-389">Nastavte po konfiguraci <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="63157-389">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="63157-390">Po spuštění všech konfigurací <xref:Microsoft.Extensions.Options.IConfigureOptions%601> se spustí po konfiguraci:</span><span class="sxs-lookup"><span data-stu-id="63157-390">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="63157-391"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> je k dispozici pro nastavení po konfiguraci s názvem:</span><span class="sxs-lookup"><span data-stu-id="63157-391"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="63157-392">Po dokončení konfigurace všech instancí konfigurace použijte <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*>:</span><span class="sxs-lookup"><span data-stu-id="63157-392">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="63157-393">Přístup k možnostem při spuštění</span><span class="sxs-lookup"><span data-stu-id="63157-393">Accessing options during startup</span></span>

<span data-ttu-id="63157-394"><xref:Microsoft.Extensions.Options.IOptions%601> a <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> lze použít v `Startup.Configure`, protože služby jsou vytvořeny před spuštěním metody `Configure`.</span><span class="sxs-lookup"><span data-stu-id="63157-394"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="63157-395">V `Startup.ConfigureServices` Nepoužívejte <xref:Microsoft.Extensions.Options.IOptions%601> ani <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="63157-395">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="63157-396">Stav nekonzistentních možností může existovat v důsledku řazení registrací služby.</span><span class="sxs-lookup"><span data-stu-id="63157-396">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="63157-397">Vzor možností používá třídy, které reprezentují skupiny souvisejících nastavení.</span><span class="sxs-lookup"><span data-stu-id="63157-397">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="63157-398">Když jsou [nastavení konfigurace](xref:fundamentals/configuration/index) izolována scénářem do samostatných tříd, aplikace dodržuje dva důležité principy technického řízení:</span><span class="sxs-lookup"><span data-stu-id="63157-398">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="63157-399">[Princip oddělování rozhraní (ISP) nebo zapouzdření](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; scénáře (třídy), které závisí na nastavení konfigurace, závisí jenom na nastavení konfigurace, která používají.</span><span class="sxs-lookup"><span data-stu-id="63157-399">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="63157-400">[Oddělení](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) potíží, které se týkají &ndash; pro různé části aplikace, není závislé nebo vzájemně nezávisle na sobě.</span><span class="sxs-lookup"><span data-stu-id="63157-400">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="63157-401">Možnosti také poskytují mechanismus pro ověření konfiguračních dat.</span><span class="sxs-lookup"><span data-stu-id="63157-401">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="63157-402">Další informace najdete v části [ověřování možností](#options-validation) .</span><span class="sxs-lookup"><span data-stu-id="63157-402">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="63157-403">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="63157-403">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="63157-404">Požadavky</span><span class="sxs-lookup"><span data-stu-id="63157-404">Prerequisites</span></span>

<span data-ttu-id="63157-405">Odkaz na balíček [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) nebo přidejte odkaz na balíček do balíčku [Microsoft. Extensions. Options. ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) .</span><span class="sxs-lookup"><span data-stu-id="63157-405">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="63157-406">Možnosti rozhraní</span><span class="sxs-lookup"><span data-stu-id="63157-406">Options interfaces</span></span>

<span data-ttu-id="63157-407"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> slouží k načtení možností a správě oznámení možností pro instance `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="63157-407"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="63157-408"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> podporuje následující scénáře:</span><span class="sxs-lookup"><span data-stu-id="63157-408"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="63157-409">Oznámení změny</span><span class="sxs-lookup"><span data-stu-id="63157-409">Change notifications</span></span>
* [<span data-ttu-id="63157-410">Pojmenované možnosti</span><span class="sxs-lookup"><span data-stu-id="63157-410">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="63157-411">Znovu Načítatelné konfigurace</span><span class="sxs-lookup"><span data-stu-id="63157-411">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="63157-412">Neplatné selektivní možnosti (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="63157-412">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="63157-413">Scénáře po [konfiguraci](#options-post-configuration) umožňují nastavit nebo změnit možnosti po všech konfiguracích <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="63157-413">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="63157-414"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> zodpovídá za vytváření nových instancí možností.</span><span class="sxs-lookup"><span data-stu-id="63157-414"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="63157-415">Má jednu metodu <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*>.</span><span class="sxs-lookup"><span data-stu-id="63157-415">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="63157-416">Výchozí implementace přebírá všechny zaregistrované <xref:Microsoft.Extensions.Options.IConfigureOptions%601> a <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> a nejprve spustí všechny konfigurace a poté po konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="63157-416">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="63157-417">Rozlišuje mezi <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> a <xref:Microsoft.Extensions.Options.IConfigureOptions%601> a volá pouze příslušné rozhraní.</span><span class="sxs-lookup"><span data-stu-id="63157-417">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="63157-418"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> používá <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> k ukládání instancí `TOptions` do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="63157-418"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="63157-419">@No__t-0 zruší platnost instancí možností v monitorování tak, aby se hodnota přepočítala (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="63157-419">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="63157-420">Hodnoty lze ručně zavést pomocí <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span><span class="sxs-lookup"><span data-stu-id="63157-420">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="63157-421">Metoda <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> se používá v případě, že by se všechny pojmenované instance měly znovu vytvořit na vyžádání.</span><span class="sxs-lookup"><span data-stu-id="63157-421">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="63157-422"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> je užitečné v situacích, kdy by se měly možnosti Přepočítat při každém požadavku.</span><span class="sxs-lookup"><span data-stu-id="63157-422"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="63157-423">Další informace najdete v části [opětovné načtení konfiguračních dat pomocí IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) .</span><span class="sxs-lookup"><span data-stu-id="63157-423">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="63157-424"><xref:Microsoft.Extensions.Options.IOptions%601> se dá použít k podpoře možností.</span><span class="sxs-lookup"><span data-stu-id="63157-424"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="63157-425">@No__t-0 však nepodporuje předchozí scénáře <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="63157-425">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="63157-426">@No__t-0 můžete dál používat ve stávajících architekturách a knihovnách, které už používají rozhraní <xref:Microsoft.Extensions.Options.IOptions%601> a nevyžadují scénáře, které poskytuje <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="63157-426">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="63157-427">Konfigurace obecných možností</span><span class="sxs-lookup"><span data-stu-id="63157-427">General options configuration</span></span>

<span data-ttu-id="63157-428">Konfigurace obecných možností se v ukázkové aplikaci ukazuje jako příklad &num;1.</span><span class="sxs-lookup"><span data-stu-id="63157-428">General options configuration is demonstrated as Example &num;1 in the sample app.</span></span>

<span data-ttu-id="63157-429">Třída Options musí být neabstraktní s veřejným konstruktorem bez parametrů.</span><span class="sxs-lookup"><span data-stu-id="63157-429">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="63157-430">Následující třída `MyOptions` má dvě vlastnosti `Option1` a `Option2`.</span><span class="sxs-lookup"><span data-stu-id="63157-430">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="63157-431">Nastavení výchozích hodnot je volitelné, ale konstruktor třídy v následujícím příkladu nastaví výchozí hodnotu `Option1`.</span><span class="sxs-lookup"><span data-stu-id="63157-431">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="63157-432">`Option2` má výchozí hodnotu nastavenou tak, že vlastnost inicializuje přímo (*modely/MyOptions. cs*):</span><span class="sxs-lookup"><span data-stu-id="63157-432">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="63157-433">Třída `MyOptions` je přidána do kontejneru služby s <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> a je svázána s konfigurací:</span><span class="sxs-lookup"><span data-stu-id="63157-433">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="63157-434">Následující model stránky používá [vkládání závislostí konstruktoru](xref:mvc/controllers/dependency-injection) s <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> pro přístup k nastavení (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="63157-434">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="63157-435">Soubor *appSettings. JSON* v ukázce určuje hodnoty pro `option1` a `option2`:</span><span class="sxs-lookup"><span data-stu-id="63157-435">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="63157-436">Když je aplikace spuštěna, metoda `OnGet` modelu stránky vrátí řetězec zobrazující hodnoty třídy možností:</span><span class="sxs-lookup"><span data-stu-id="63157-436">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="63157-437">Když pomocí vlastního <xref:System.Configuration.ConfigurationBuilder> načtěte konfiguraci možností ze souboru nastavení, zkontrolujte, jestli je správně nastavená základní cesta:</span><span class="sxs-lookup"><span data-stu-id="63157-437">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="63157-438">Při načítání konfigurace možností ze souboru nastavení prostřednictvím <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> není nutné explicitně nastavit základní cestu.</span><span class="sxs-lookup"><span data-stu-id="63157-438">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="63157-439">Konfigurace jednoduchých možností pomocí delegáta</span><span class="sxs-lookup"><span data-stu-id="63157-439">Configure simple options with a delegate</span></span>

<span data-ttu-id="63157-440">Konfigurace jednoduchých možností s delegátem je znázorněna jako příklad &num;2 v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="63157-440">Configuring simple options with a delegate is demonstrated as Example &num;2 in the sample app.</span></span>

<span data-ttu-id="63157-441">K nastavení hodnot možností použijte delegáta.</span><span class="sxs-lookup"><span data-stu-id="63157-441">Use a delegate to set options values.</span></span> <span data-ttu-id="63157-442">Ukázková aplikace používá třídu `MyOptionsWithDelegateConfig` (*modely/MyOptionsWithDelegateConfig. cs*):</span><span class="sxs-lookup"><span data-stu-id="63157-442">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="63157-443">V následujícím kódu je do kontejneru služby přidána druhá služba <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="63157-443">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="63157-444">Pomocí delegáta konfiguruje vazbu s `MyOptionsWithDelegateConfig`:</span><span class="sxs-lookup"><span data-stu-id="63157-444">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="63157-445">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="63157-445">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="63157-446">Můžete přidat více poskytovatelů konfigurace.</span><span class="sxs-lookup"><span data-stu-id="63157-446">You can add multiple configuration providers.</span></span> <span data-ttu-id="63157-447">Poskytovatelé konfigurace jsou k dispozici z balíčků NuGet a jsou aplikováni v pořadí, v jakém jsou registrováni.</span><span class="sxs-lookup"><span data-stu-id="63157-447">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="63157-448">Další informace najdete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="63157-448">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="63157-449">Každé volání <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> přidá do kontejneru služby službu <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="63157-449">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="63157-450">V předchozím příkladu jsou hodnoty `Option1` a `Option2` zadány v souboru *appSettings. JSON*, ale hodnoty `Option1` a `Option2` jsou přepsány nakonfigurovaným delegátem.</span><span class="sxs-lookup"><span data-stu-id="63157-450">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="63157-451">Je-li povolena více než jedna služba konfigurace, je v posledním zdroji konfigurace zadaný *Server WINS* a nastavena hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="63157-451">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="63157-452">Když je aplikace spuštěna, metoda `OnGet` modelu stránky vrátí řetězec zobrazující hodnoty třídy možností:</span><span class="sxs-lookup"><span data-stu-id="63157-452">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="63157-453">Konfigurace podNastavení</span><span class="sxs-lookup"><span data-stu-id="63157-453">Suboptions configuration</span></span>

<span data-ttu-id="63157-454">Konfigurace dílčích možností se v ukázkové aplikaci ukazuje jako příklad &num;3.</span><span class="sxs-lookup"><span data-stu-id="63157-454">Suboptions configuration is demonstrated as Example &num;3 in the sample app.</span></span>

<span data-ttu-id="63157-455">Aplikace by měly vytvořit třídy možností, které se týkají konkrétních skupin scénářů (tříd) v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="63157-455">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="63157-456">Části aplikace, které vyžadují konfigurační hodnoty, by měly mít přístup jenom k hodnotám konfigurace, které používají.</span><span class="sxs-lookup"><span data-stu-id="63157-456">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="63157-457">Při vytváření vazby mezi možnostmi konfigurace jsou jednotlivé vlastnosti v typu možnosti vázány na konfigurační klíč formuláře `property[:sub-property:]`.</span><span class="sxs-lookup"><span data-stu-id="63157-457">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="63157-458">Například vlastnost `MyOptions.Option1` je vázána na klíč `Option1`, který je načten z vlastnosti `option1` v souboru *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="63157-458">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="63157-459">V následujícím kódu je třetí služba <xref:Microsoft.Extensions.Options.IConfigureOptions%601> přidána do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="63157-459">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="63157-460">Vytvoří vazby `MySubOptions` na oddíl `subsection` souboru *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="63157-460">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="63157-461">Metoda rozšíření `GetSection` vyžaduje balíček [Microsoft. Extensions. Options. ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="63157-461">The `GetSection` extension method requires the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) NuGet package.</span></span> <span data-ttu-id="63157-462">Pokud aplikace používá [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 nebo novější), balíček je automaticky zahrnutý.</span><span class="sxs-lookup"><span data-stu-id="63157-462">If the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later), the package is automatically included.</span></span>

<span data-ttu-id="63157-463">Soubor *appSettings. JSON* ve vzorci definuje člena `subsection` s klíči pro `suboption1` a `suboption2`:</span><span class="sxs-lookup"><span data-stu-id="63157-463">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="63157-464">Třída `MySubOptions` definuje vlastnosti, `SubOption1` a `SubOption2`, aby obsahovaly hodnoty možností (*modely/MySubOptions. cs*):</span><span class="sxs-lookup"><span data-stu-id="63157-464">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="63157-465">Metoda `OnGet` modelu stránky vrací řetězec s hodnotami možností (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="63157-465">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="63157-466">Při spuštění aplikace vrátí metoda `OnGet` řetězec zobrazující hodnoty třídy dílčích možností:</span><span class="sxs-lookup"><span data-stu-id="63157-466">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="63157-467">Možnosti poskytované modelem zobrazení nebo vkládáním přímých zobrazení</span><span class="sxs-lookup"><span data-stu-id="63157-467">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="63157-468">Možnosti poskytované modelem zobrazení nebo vkládáním přímých zobrazení jsou v ukázkové aplikaci znázorněné jako příklad &num;4.</span><span class="sxs-lookup"><span data-stu-id="63157-468">Options provided by a view model or with direct view injection is demonstrated as Example &num;4 in the sample app.</span></span>

<span data-ttu-id="63157-469">Možnosti lze zadat v modelu zobrazení nebo vložením <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> přímo do zobrazení (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="63157-469">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="63157-470">Ukázková aplikace ukazuje, jak vložit `IOptionsMonitor<MyOptions>` s direktivou `@inject`:</span><span class="sxs-lookup"><span data-stu-id="63157-470">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="63157-471">Po spuštění aplikace se hodnoty možností zobrazí na vykreslené stránce:</span><span class="sxs-lookup"><span data-stu-id="63157-471">When the app is run, the options values are shown in the rendered page:</span></span>

![Hodnoty možností možnost1: value1_from_json a Možnost2:-1 jsou načteny z modelu a vložením do zobrazení.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="63157-473">Znovu načíst konfigurační data pomocí IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="63157-473">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="63157-474">Opětovné načtení konfiguračních dat pomocí <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> je v ukázkové aplikaci znázorněno v příkladu &num;5.</span><span class="sxs-lookup"><span data-stu-id="63157-474">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example &num;5 in the sample app.</span></span>

<span data-ttu-id="63157-475"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> podporuje opětovné načítání možností s minimální režií zpracování.</span><span class="sxs-lookup"><span data-stu-id="63157-475"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="63157-476">Možnosti jsou vypočítány jednou za požadavek při otevření a ukládání do mezipaměti po dobu života žádosti.</span><span class="sxs-lookup"><span data-stu-id="63157-476">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="63157-477">Následující příklad ukazuje, jak se vytvoří nový <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> po změnách *appSettings. JSON* (*pages/index. cshtml. cs*).</span><span class="sxs-lookup"><span data-stu-id="63157-477">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="63157-478">Více požadavků na server vrací konstantní hodnoty, které poskytuje soubor *appSettings. JSON* , dokud nedojde ke změně souboru a opětovnému načtení konfigurace.</span><span class="sxs-lookup"><span data-stu-id="63157-478">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="63157-479">Následující obrázek ukazuje počáteční hodnoty `option1` a `option2` načtené ze souboru *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="63157-479">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="63157-480">Změňte hodnoty v souboru *appSettings. JSON* na `value1_from_json UPDATED` a `200`.</span><span class="sxs-lookup"><span data-stu-id="63157-480">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="63157-481">Uložte soubor *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="63157-481">Save the *appsettings.json* file.</span></span> <span data-ttu-id="63157-482">Aktualizujte prohlížeč, aby se zobrazily hodnoty možností, které jsou aktualizované:</span><span class="sxs-lookup"><span data-stu-id="63157-482">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="63157-483">Podpora pojmenovaných možností s IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="63157-483">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="63157-484">Pojmenovaná podpora s možností <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> je v ukázkové aplikaci znázorněna jako příklad &num;6.</span><span class="sxs-lookup"><span data-stu-id="63157-484">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example &num;6 in the sample app.</span></span>

<span data-ttu-id="63157-485">Podpora *pojmenovaných možností* umožňuje aplikaci rozlišovat konfigurace pojmenovaných možností.</span><span class="sxs-lookup"><span data-stu-id="63157-485">*Named options* support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="63157-486">V ukázkové aplikaci jsou pojmenované možnosti deklarovány pomocí [OptionsServiceCollectionExtensions. Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), který volá [ConfigureNamedOptions @ no__t-2TOptions >. Konfigurace](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) metody rozšíření:</span><span class="sxs-lookup"><span data-stu-id="63157-486">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="63157-487">Ukázková aplikace přistupuje k pojmenovaným možnostem <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="63157-487">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="63157-488">Když spustíte ukázkovou aplikaci, vrátí se pojmenované možnosti:</span><span class="sxs-lookup"><span data-stu-id="63157-488">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="63157-489">hodnoty `named_options_1` se poskytují z konfigurace, které se načítají ze souboru *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="63157-489">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="63157-490">hodnoty `named_options_2` poskytuje:</span><span class="sxs-lookup"><span data-stu-id="63157-490">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="63157-491">Delegát `named_options_2` v `ConfigureServices` pro `Option1`.</span><span class="sxs-lookup"><span data-stu-id="63157-491">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="63157-492">Výchozí hodnota pro `Option2` poskytovaná třídou `MyOptions`.</span><span class="sxs-lookup"><span data-stu-id="63157-492">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="63157-493">Konfigurace všech možností pomocí metody ConfigureAll</span><span class="sxs-lookup"><span data-stu-id="63157-493">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="63157-494">Nakonfigurujte všechny instance možností pomocí metody <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*>.</span><span class="sxs-lookup"><span data-stu-id="63157-494">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="63157-495">Následující kód konfiguruje `Option1` pro všechny instance konfigurace se společnou hodnotou.</span><span class="sxs-lookup"><span data-stu-id="63157-495">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="63157-496">Přidejte následující kód ručně do metody `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="63157-496">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="63157-497">Spuštění ukázkové aplikace po přidání kódu vytvoří následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="63157-497">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="63157-498">Všechny možnosti jsou pojmenované instance.</span><span class="sxs-lookup"><span data-stu-id="63157-498">All options are named instances.</span></span> <span data-ttu-id="63157-499">Existující instance <xref:Microsoft.Extensions.Options.IConfigureOptions%601> se považují za cílení na instanci `Options.DefaultName`, která je `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="63157-499">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="63157-500"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> také implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="63157-500"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="63157-501">Výchozí implementace <xref:Microsoft.Extensions.Options.IOptionsFactory%601> má logiku odpovídajícím způsobem použít.</span><span class="sxs-lookup"><span data-stu-id="63157-501">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="63157-502">Pojmenovaná možnost `null` se používá k cílení na všechny pojmenované instance místo konkrétní pojmenované instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> a <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> používají tuto konvenci).</span><span class="sxs-lookup"><span data-stu-id="63157-502">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="63157-503">Rozhraní API pro OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="63157-503">OptionsBuilder API</span></span>

<span data-ttu-id="63157-504"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> se používá ke konfiguraci instancí `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="63157-504"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="63157-505">`OptionsBuilder` zjednodušuje vytváření pojmenovaných možností, protože se jedná pouze o jediný parametr počátečního volání `AddOptions<TOptions>(string optionsName)` namísto zobrazení ve všech následujících voláních.</span><span class="sxs-lookup"><span data-stu-id="63157-505">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="63157-506">Ověřování možností a přetížení `ConfigureOptions`, které přijímají závislosti služby, jsou k dispozici pouze prostřednictvím `OptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="63157-506">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="63157-507">Ke konfiguraci možností použijte DI Services.</span><span class="sxs-lookup"><span data-stu-id="63157-507">Use DI services to configure options</span></span>

<span data-ttu-id="63157-508">K dalším službám můžete přistupovat pomocí injektáže závislosti při konfiguraci možností dvěma způsoby:</span><span class="sxs-lookup"><span data-stu-id="63157-508">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="63157-509">[Nakonfigurujte](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) delegáta konfigurace na [OptionsBuilder @ no__t-2TOptions >](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="63157-509">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="63157-510">[OptionsBuilder @ no__t-1TOptions >](xref:Microsoft.Extensions.Options.OptionsBuilder`1) poskytuje přetížení [Konfigurace](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) , která umožňují použít až pět služeb ke konfiguraci možností:</span><span class="sxs-lookup"><span data-stu-id="63157-510">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="63157-511">Vytvořte vlastní typ, který implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> nebo <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> a zaregistrujte typ jako službu.</span><span class="sxs-lookup"><span data-stu-id="63157-511">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="63157-512">Doporučujeme předat delegáta [konfigurace, protože](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)vytvoření služby je složitější.</span><span class="sxs-lookup"><span data-stu-id="63157-512">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="63157-513">Vytvoření vlastního typu je ekvivalentní s tím, k čemu architektura slouží při použití možnosti [Konfigurovat](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="63157-513">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="63157-514">Volání metody [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) obsahuje přechodný obecný <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, který má konstruktor, který přijímá obecné zadané typy služeb.</span><span class="sxs-lookup"><span data-stu-id="63157-514">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-post-configuration"></a><span data-ttu-id="63157-515">Možnosti po konfiguraci</span><span class="sxs-lookup"><span data-stu-id="63157-515">Options post-configuration</span></span>

<span data-ttu-id="63157-516">Nastavte po konfiguraci <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="63157-516">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="63157-517">Po spuštění všech konfigurací <xref:Microsoft.Extensions.Options.IConfigureOptions%601> se spustí po konfiguraci:</span><span class="sxs-lookup"><span data-stu-id="63157-517">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="63157-518"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> je k dispozici pro nastavení po konfiguraci s názvem:</span><span class="sxs-lookup"><span data-stu-id="63157-518"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="63157-519">Po dokončení konfigurace všech instancí konfigurace použijte <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*>:</span><span class="sxs-lookup"><span data-stu-id="63157-519">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="63157-520">Přístup k možnostem při spuštění</span><span class="sxs-lookup"><span data-stu-id="63157-520">Accessing options during startup</span></span>

<span data-ttu-id="63157-521"><xref:Microsoft.Extensions.Options.IOptions%601> a <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> lze použít v `Startup.Configure`, protože služby jsou vytvořeny před spuštěním metody `Configure`.</span><span class="sxs-lookup"><span data-stu-id="63157-521"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="63157-522">V `Startup.ConfigureServices` Nepoužívejte <xref:Microsoft.Extensions.Options.IOptions%601> ani <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="63157-522">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="63157-523">Stav nekonzistentních možností může existovat v důsledku řazení registrací služby.</span><span class="sxs-lookup"><span data-stu-id="63157-523">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="63157-524">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="63157-524">Additional resources</span></span>

* <xref:fundamentals/configuration/index>

---
title: Vzor možností v ASP.NET jádru
author: rick-anderson
description: Zjistěte, jak pomocí vzoru možností reprezentovat skupiny souvisejících nastavení v ASP.NET základních aplikacích.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
uid: fundamentals/configuration/options
ms.openlocfilehash: 756d3d57122642ab10ab671c9accb75975c3799d
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78665457"
---
# <a name="options-pattern-in-aspnet-core"></a><span data-ttu-id="f8d76-103">Vzor možností v ASP.NET jádru</span><span class="sxs-lookup"><span data-stu-id="f8d76-103">Options pattern in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f8d76-104">Vzor voleb používá třídy k reprezentaci skupin souvisejících nastavení.</span><span class="sxs-lookup"><span data-stu-id="f8d76-104">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="f8d76-105">Když jsou [nastavení konfigurace](xref:fundamentals/configuration/index) izolována scénářem do samostatných tříd, aplikace dodržuje dva důležité principy softwarového inženýrství:</span><span class="sxs-lookup"><span data-stu-id="f8d76-105">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="f8d76-106">[Princip segregace rozhraní (ISP) nebo](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; scénáře zapouzdření (třídy), které závisí na nastavení konfigurace, závisí pouze na nastavení konfigurace, které používají.</span><span class="sxs-lookup"><span data-stu-id="f8d76-106">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="f8d76-107">[Nastavení oddělení obav](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; pro různé části aplikace není závislé nebo vzájemně se nespřahouje.</span><span class="sxs-lookup"><span data-stu-id="f8d76-107">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="f8d76-108">Možnosti také poskytují mechanismus pro ověření konfiguračních dat.</span><span class="sxs-lookup"><span data-stu-id="f8d76-108">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="f8d76-109">Další informace naleznete v části [Možnosti ověření.](#options-validation)</span><span class="sxs-lookup"><span data-stu-id="f8d76-109">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="f8d76-110">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="f8d76-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="package"></a><span data-ttu-id="f8d76-111">Balíček</span><span class="sxs-lookup"><span data-stu-id="f8d76-111">Package</span></span>

<span data-ttu-id="f8d76-112">Balíček [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) je implicitně odkazován v aplikacích ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f8d76-112">The [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package is implicitly referenced in ASP.NET Core apps.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="f8d76-113">Rozhraní možností</span><span class="sxs-lookup"><span data-stu-id="f8d76-113">Options interfaces</span></span>

<span data-ttu-id="f8d76-114"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>slouží k načtení možností a `TOptions` správě oznámení o možnostech pro instance.</span><span class="sxs-lookup"><span data-stu-id="f8d76-114"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="f8d76-115"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>podporuje následující scénáře:</span><span class="sxs-lookup"><span data-stu-id="f8d76-115"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="f8d76-116">Změnit oznámení</span><span class="sxs-lookup"><span data-stu-id="f8d76-116">Change notifications</span></span>
* [<span data-ttu-id="f8d76-117">Pojmenované možnosti</span><span class="sxs-lookup"><span data-stu-id="f8d76-117">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="f8d76-118">Znovu načítatelná konfigurace</span><span class="sxs-lookup"><span data-stu-id="f8d76-118">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="f8d76-119">Selektivní možnosti<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>zneplatnění ( )</span><span class="sxs-lookup"><span data-stu-id="f8d76-119">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="f8d76-120">[Scénáře po konfiguraci](#options-post-configuration) umožňují nastavit nebo <xref:Microsoft.Extensions.Options.IConfigureOptions%601> změnit možnosti po všech konfiguracích.</span><span class="sxs-lookup"><span data-stu-id="f8d76-120">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="f8d76-121"><xref:Microsoft.Extensions.Options.IOptionsFactory%601>je zodpovědný za vytváření nových instancí možností.</span><span class="sxs-lookup"><span data-stu-id="f8d76-121"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="f8d76-122">Má jedinou <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> metodu.</span><span class="sxs-lookup"><span data-stu-id="f8d76-122">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="f8d76-123">Výchozí implementace trvá <xref:Microsoft.Extensions.Options.IConfigureOptions%601> všechny <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> registrované a spustí všechny konfigurace jako první, následuje post-konfigurace.</span><span class="sxs-lookup"><span data-stu-id="f8d76-123">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="f8d76-124">Rozlišuje mezi <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> a <xref:Microsoft.Extensions.Options.IConfigureOptions%601> pouze volá příslušné rozhraní.</span><span class="sxs-lookup"><span data-stu-id="f8d76-124">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="f8d76-125"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>slouží <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> k ukládání `TOptions` instancí do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="f8d76-125"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="f8d76-126">Zruší <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> platnost instance možností v monitoru tak, aby hodnota<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>byla přepočítána ( ).</span><span class="sxs-lookup"><span data-stu-id="f8d76-126">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="f8d76-127">Hodnoty lze ručně zavést <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>pomocí .</span><span class="sxs-lookup"><span data-stu-id="f8d76-127">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="f8d76-128">Metoda <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> se používá, když všechny pojmenované instance by měly být znovu vytvořeny na vyžádání.</span><span class="sxs-lookup"><span data-stu-id="f8d76-128">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="f8d76-129"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>je užitečné ve scénářích, kde by měly být přepočítány možnosti na každém požadavku.</span><span class="sxs-lookup"><span data-stu-id="f8d76-129"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="f8d76-130">Další informace naleznete v části [Znovu načíst konfigurační data pomocí iOptionsSnapshot.](#reload-configuration-data-with-ioptionssnapshot)</span><span class="sxs-lookup"><span data-stu-id="f8d76-130">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="f8d76-131"><xref:Microsoft.Extensions.Options.IOptions%601>lze použít k podpoře možností.</span><span class="sxs-lookup"><span data-stu-id="f8d76-131"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="f8d76-132">Však <xref:Microsoft.Extensions.Options.IOptions%601> nepodporuje předchozí scénáře <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="f8d76-132">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="f8d76-133">Můžete pokračovat v <xref:Microsoft.Extensions.Options.IOptions%601> používání v existujících architekturách <xref:Microsoft.Extensions.Options.IOptions%601> a knihovnách, které již používají <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>rozhraní a nevyžadují scénáře poskytované aplikací .</span><span class="sxs-lookup"><span data-stu-id="f8d76-133">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="f8d76-134">Konfigurace obecných možností</span><span class="sxs-lookup"><span data-stu-id="f8d76-134">General options configuration</span></span>

<span data-ttu-id="f8d76-135">Obecná konfigurace možností je v ukázkové aplikaci demonstrována jako Příklad 1.</span><span class="sxs-lookup"><span data-stu-id="f8d76-135">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="f8d76-136">Třída options musí být neabstraktní s veřejným konstruktorem bez parametrů.</span><span class="sxs-lookup"><span data-stu-id="f8d76-136">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="f8d76-137">Následující třída `MyOptions`, má dvě `Option1` `Option2`vlastnosti a .</span><span class="sxs-lookup"><span data-stu-id="f8d76-137">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="f8d76-138">Nastavení výchozích hodnot je volitelné, ale konstruktor třídy `Option1`v následujícím příkladu nastaví výchozí hodnotu .</span><span class="sxs-lookup"><span data-stu-id="f8d76-138">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="f8d76-139">`Option2`má výchozí hodnotu nastavenou přímou inicializací vlastnosti *(Models/MyOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="f8d76-139">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="f8d76-140">Třída `MyOptions` je přidána do <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> kontejneru služby s konfigurací a vázána na konfiguraci:</span><span class="sxs-lookup"><span data-stu-id="f8d76-140">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="f8d76-141">Následující model stránky používá [vkládání závislostí konstruktoru](xref:mvc/controllers/dependency-injection) s <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> přístupem k nastavení *(Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="f8d76-141">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="f8d76-142">Ukázkový soubor *appsettings.json* určuje hodnoty `option1` `option2`pro a :</span><span class="sxs-lookup"><span data-stu-id="f8d76-142">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/3.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="f8d76-143">Při spuštění aplikace `OnGet` metoda modelu stránky vrátí řetězec zobrazující hodnoty třídy možností:</span><span class="sxs-lookup"><span data-stu-id="f8d76-143">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="f8d76-144">Při použití <xref:System.Configuration.ConfigurationBuilder> vlastní konfigurace možností z souboru nastavení zkontrolujte, zda je základní cesta nastavena správně:</span><span class="sxs-lookup"><span data-stu-id="f8d76-144">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="f8d76-145">Explicitní nastavení základní cesty není nutné při načítání konfigurace <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>možností z souboru nastavení prostřednictvím aplikace .</span><span class="sxs-lookup"><span data-stu-id="f8d76-145">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="f8d76-146">Konfigurace jednoduchých možností s delegátem</span><span class="sxs-lookup"><span data-stu-id="f8d76-146">Configure simple options with a delegate</span></span>

<span data-ttu-id="f8d76-147">Konfigurace jednoduchých možností s delegátem je v ukázkové aplikaci demonstrována jako příklad 2.</span><span class="sxs-lookup"><span data-stu-id="f8d76-147">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="f8d76-148">Pomocí delegáta nastavte hodnoty možností.</span><span class="sxs-lookup"><span data-stu-id="f8d76-148">Use a delegate to set options values.</span></span> <span data-ttu-id="f8d76-149">Ukázková aplikace `MyOptionsWithDelegateConfig` používá třídu *(Models/MyOptionsWithDelegateConfig.cs*):</span><span class="sxs-lookup"><span data-stu-id="f8d76-149">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="f8d76-150">V následujícím kódu je <xref:Microsoft.Extensions.Options.IConfigureOptions%601> do kontejneru služby přidána druhá služba.</span><span class="sxs-lookup"><span data-stu-id="f8d76-150">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="f8d76-151">Používá delegáta ke konfiguraci `MyOptionsWithDelegateConfig`vazby pomocí aplikace :</span><span class="sxs-lookup"><span data-stu-id="f8d76-151">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="f8d76-152">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="f8d76-152">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="f8d76-153">Můžete přidat více poskytovatelů konfigurace.</span><span class="sxs-lookup"><span data-stu-id="f8d76-153">You can add multiple configuration providers.</span></span> <span data-ttu-id="f8d76-154">Zprostředkovatelé konfigurace jsou k dispozici z balíčků NuGet a jsou použity v pořadí, ve kterém jsou registrovány.</span><span class="sxs-lookup"><span data-stu-id="f8d76-154">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="f8d76-155">Další informace naleznete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="f8d76-155">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="f8d76-156">Každé volání <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> přidá <xref:Microsoft.Extensions.Options.IConfigureOptions%601> službu do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="f8d76-156">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="f8d76-157">V předchozím příkladu jsou `Option1` hodnoty `Option2` a jsou zadány v *souboru appsettings.json*, ale hodnoty `Option1` a `Option2` jsou přepsány nakonfigurovaným delegátem.</span><span class="sxs-lookup"><span data-stu-id="f8d76-157">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="f8d76-158">Pokud je povolena více než jedna konfigurační služba, *vyhrává* poslední zadaný zdroj konfigurace a nastavuje hodnotu konfigurace.</span><span class="sxs-lookup"><span data-stu-id="f8d76-158">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="f8d76-159">Při spuštění aplikace `OnGet` metoda modelu stránky vrátí řetězec zobrazující hodnoty třídy možností:</span><span class="sxs-lookup"><span data-stu-id="f8d76-159">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="f8d76-160">Konfigurace dílčích možností</span><span class="sxs-lookup"><span data-stu-id="f8d76-160">Suboptions configuration</span></span>

<span data-ttu-id="f8d76-161">Konfigurace dílčích možností je v ukázkové aplikaci demonstrována jako Příklad 3.</span><span class="sxs-lookup"><span data-stu-id="f8d76-161">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="f8d76-162">Aplikace by měly vytvářet třídy možností, které se v aplikaci připojují ke konkrétním skupinám scénářů (třídám).</span><span class="sxs-lookup"><span data-stu-id="f8d76-162">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="f8d76-163">Části aplikace, které vyžadují hodnoty konfigurace by měl mít přístup pouze k hodnotám konfigurace, které používají.</span><span class="sxs-lookup"><span data-stu-id="f8d76-163">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="f8d76-164">Při vazbě možnosti konfigurace, každá vlastnost v typu možnosti `property[:sub-property:]`je vázánna na konfigurační klíč formuláře .</span><span class="sxs-lookup"><span data-stu-id="f8d76-164">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="f8d76-165">Například `MyOptions.Option1` vlastnost je vázána `Option1`na klíč , `option1` který je číst z vlastnosti v *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="f8d76-165">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="f8d76-166">V následujícím kódu je <xref:Microsoft.Extensions.Options.IConfigureOptions%601> do kontejneru služby přidána třetí služba.</span><span class="sxs-lookup"><span data-stu-id="f8d76-166">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="f8d76-167">Váže `MySubOptions` se na `subsection` část souboru *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="f8d76-167">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="f8d76-168">Metoda `GetSection` vyžaduje <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> obor názvů.</span><span class="sxs-lookup"><span data-stu-id="f8d76-168">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="f8d76-169">Ukázkový soubor *appsettings.json* definuje `subsection` člen s `suboption1` klíči pro a `suboption2`:</span><span class="sxs-lookup"><span data-stu-id="f8d76-169">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/3.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="f8d76-170">Třída `MySubOptions` definuje vlastnosti `SubOption1` `SubOption2`a , chcete-li podržet hodnoty možností *(Models/MySubOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="f8d76-170">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="f8d76-171">`OnGet` Metoda modelu stránky vrátí řetězec s hodnotami voleb *(Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="f8d76-171">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="f8d76-172">Při spuštění aplikace `OnGet` metoda vrátí řetězec zobrazující hodnoty třídy dílčí možnosti:</span><span class="sxs-lookup"><span data-stu-id="f8d76-172">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-injection"></a><span data-ttu-id="f8d76-173">Možnosti vstřikování</span><span class="sxs-lookup"><span data-stu-id="f8d76-173">Options injection</span></span>

<span data-ttu-id="f8d76-174">Možnosti vkládání je demonstrována jako příklad 4 v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f8d76-174">Options injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="f8d76-175">Vstříkněte <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> do:</span><span class="sxs-lookup"><span data-stu-id="f8d76-175">Inject <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into:</span></span>

* <span data-ttu-id="f8d76-176">Razor stránky nebo MVC [`@inject`](xref:mvc/views/razor#inject) zobrazení se směrnicí Razor.</span><span class="sxs-lookup"><span data-stu-id="f8d76-176">A Razor page or MVC view with the [`@inject`](xref:mvc/views/razor#inject) Razor directive.</span></span>
* <span data-ttu-id="f8d76-177">Model stránky nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="f8d76-177">A page or view model.</span></span>

<span data-ttu-id="f8d76-178">Následující příklad z ukázkové <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> aplikace vloží do modelu stránky *(Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="f8d76-178">The following example from the sample app injects <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into a page model (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="f8d76-179">Ukázková aplikace ukazuje, `IOptionsMonitor<MyOptions>` jak `@inject` vložit direktivu:</span><span class="sxs-lookup"><span data-stu-id="f8d76-179">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/3.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="f8d76-180">Při spuštění aplikace se hodnoty možností zobrazí na vykreslené stránce:</span><span class="sxs-lookup"><span data-stu-id="f8d76-180">When the app is run, the options values are shown in the rendered page:</span></span>

![Hodnoty možností Option1: value1_from_json a Option2: -1 jsou načteny z modelu a vstřikováním do pohledu.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="f8d76-182">Opětovné načtení konfiguračních dat pomocí aplikace IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="f8d76-182">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="f8d76-183">Opětovné načtení <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> konfiguračních dat s je znázorněno v příkladu 5 v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f8d76-183">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="f8d76-184">Pomocí <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, možnosti jsou vypočítány jednou na požadavek při přístupu a ukládání do mezipaměti po dobu platnosti požadavku.</span><span class="sxs-lookup"><span data-stu-id="f8d76-184">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="f8d76-185">Rozdíl mezi `IOptionsMonitor` `IOptionsSnapshot` a je, že:</span><span class="sxs-lookup"><span data-stu-id="f8d76-185">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="f8d76-186">`IOptionsMonitor`je [služba singleton,](xref:fundamentals/dependency-injection#singleton) která kdykoli načte aktuální hodnoty možností, což je užitečné zejména v závislostech singleton.</span><span class="sxs-lookup"><span data-stu-id="f8d76-186">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="f8d76-187">`IOptionsSnapshot`je [služba s vymezeným oborem](xref:fundamentals/dependency-injection#scoped) a poskytuje `IOptionsSnapshot<T>` snímek možností v době, kdy je objekt vytvořen.</span><span class="sxs-lookup"><span data-stu-id="f8d76-187">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="f8d76-188">Možnosti snímky jsou určeny pro použití s přechodnými závislostmi a závislostmi s vymezeným oborem.</span><span class="sxs-lookup"><span data-stu-id="f8d76-188">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="f8d76-189">Následující příklad ukazuje, jak <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> je vytvořen nový po *appsettings.json* změny (*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="f8d76-189">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="f8d76-190">Více požadavků na server vrátí konstantní hodnoty poskytované souborem *appsettings.json,* dokud se soubor nezmění a konfigurace se znovu nenačte.</span><span class="sxs-lookup"><span data-stu-id="f8d76-190">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="f8d76-191">Následující obrázek znázorňuje počáteční `option1` hodnoty načtené `option2` ze souboru *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="f8d76-191">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="f8d76-192">Změňte hodnoty v souboru *appsettings.json* na `value1_from_json UPDATED` a `200`.</span><span class="sxs-lookup"><span data-stu-id="f8d76-192">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="f8d76-193">Uložte soubor *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="f8d76-193">Save the *appsettings.json* file.</span></span> <span data-ttu-id="f8d76-194">Aktualizujte prohlížeč, abyste viděli, že jsou aktualizovány hodnoty možností:</span><span class="sxs-lookup"><span data-stu-id="f8d76-194">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="f8d76-195">Podpora pojmenovaných možností pomocí nástroje IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="f8d76-195">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="f8d76-196">Pojmenované možnosti <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> podpora s je demonstrován jako příklad 6 v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f8d76-196">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="f8d76-197">Podpora pojmenovaných možností umožňuje aplikaci rozlišovat mezi konfiguracemi pojmenovaných možností.</span><span class="sxs-lookup"><span data-stu-id="f8d76-197">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="f8d76-198">V ukázkové aplikaci jsou pojmenované možnosti deklarovány pomocí [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), který volá [> ConfigureNamedOptions\<TOptions. Nakonfigurujte](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) metodu rozšíření.</span><span class="sxs-lookup"><span data-stu-id="f8d76-198">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="f8d76-199">Pojmenované možnosti rozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="f8d76-199">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="f8d76-200">Ukázková aplikace přistupuje k pojmenovaným možnostem <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> pomocí *(Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="f8d76-200">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="f8d76-201">Spuštění ukázkové aplikace, pojmenované možnosti jsou vráceny:</span><span class="sxs-lookup"><span data-stu-id="f8d76-201">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="f8d76-202">`named_options_1`hodnoty jsou k dispozici z konfigurace, které jsou načteny ze souboru *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="f8d76-202">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="f8d76-203">`named_options_2`hodnoty jsou poskytovány:</span><span class="sxs-lookup"><span data-stu-id="f8d76-203">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="f8d76-204">Delegát `named_options_2` v `ConfigureServices` `Option1`pro .</span><span class="sxs-lookup"><span data-stu-id="f8d76-204">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="f8d76-205">Výchozí hodnota `Option2` pro poskytované `MyOptions` třídy.</span><span class="sxs-lookup"><span data-stu-id="f8d76-205">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="f8d76-206">Konfigurace všech možností pomocí metody ConfigureAll</span><span class="sxs-lookup"><span data-stu-id="f8d76-206">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="f8d76-207">Nakonfigurujte všechny <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> instance možností pomocí metody.</span><span class="sxs-lookup"><span data-stu-id="f8d76-207">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="f8d76-208">Následující kód konfiguruje `Option1` pro všechny instance konfigurace se společnou hodnotou.</span><span class="sxs-lookup"><span data-stu-id="f8d76-208">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="f8d76-209">Přidejte do `Startup.ConfigureServices` metody ručně následující kód:</span><span class="sxs-lookup"><span data-stu-id="f8d76-209">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="f8d76-210">Spuštění ukázkové aplikace po přidání kódu vytvoří následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="f8d76-210">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="f8d76-211">Všechny možnosti jsou pojmenované instance.</span><span class="sxs-lookup"><span data-stu-id="f8d76-211">All options are named instances.</span></span> <span data-ttu-id="f8d76-212">Existující <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instance jsou považovány za `Options.DefaultName` cílení instance, která je `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="f8d76-212">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="f8d76-213"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>také provádí <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="f8d76-213"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="f8d76-214">Výchozí implementace má <xref:Microsoft.Extensions.Options.IOptionsFactory%601> logiku použít každý vhodně.</span><span class="sxs-lookup"><span data-stu-id="f8d76-214">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="f8d76-215">Pojmenovaná `null` možnost se používá k cílení na všechny pojmenované<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> instance <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> namísto konkrétní pojmenované instance (a použít tuto konvenci).</span><span class="sxs-lookup"><span data-stu-id="f8d76-215">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="f8d76-216">Rozhraní API pro stavitele možností</span><span class="sxs-lookup"><span data-stu-id="f8d76-216">OptionsBuilder API</span></span>

<span data-ttu-id="f8d76-217"><xref:Microsoft.Extensions.Options.OptionsBuilder%601>slouží ke `TOptions` konfiguraci instancí.</span><span class="sxs-lookup"><span data-stu-id="f8d76-217"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="f8d76-218">`OptionsBuilder`zjednodušuje vytváření pojmenovaných možností, protože se `AddOptions<TOptions>(string optionsName)` jedná pouze o jeden parametr počátečního volání namísto zobrazení ve všech následných voláních.</span><span class="sxs-lookup"><span data-stu-id="f8d76-218">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="f8d76-219">Možnosti ověření `ConfigureOptions` a přetížení, které přijímají závislosti `OptionsBuilder`služby jsou k dispozici pouze prostřednictvím .</span><span class="sxs-lookup"><span data-stu-id="f8d76-219">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="f8d76-220">Konfigurace možností pomocí služeb DI</span><span class="sxs-lookup"><span data-stu-id="f8d76-220">Use DI services to configure options</span></span>

<span data-ttu-id="f8d76-221">Můžete přistupovat k dalším službám z vkládání závislostí při konfiguraci možností dvěma způsoby:</span><span class="sxs-lookup"><span data-stu-id="f8d76-221">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="f8d76-222">Předejte delegáta konfigurace [konfigurovat na](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="f8d76-222">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="f8d76-223">`OptionsBuilder<TOptions>`poskytuje přetížení [Konfigurovat,](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) které umožňují použití až pět služeb pro konfiguraci možností:</span><span class="sxs-lookup"><span data-stu-id="f8d76-223">`OptionsBuilder<TOptions>` provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="f8d76-224">Vytvořte vlastní typ, <xref:Microsoft.Extensions.Options.IConfigureOptions%601> <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> který implementuje nebo a zaregistrujte typ jako službu.</span><span class="sxs-lookup"><span data-stu-id="f8d76-224">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="f8d76-225">Doporučujeme předat delegáta konfigurace [konfigurovat](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), protože vytvoření služby je složitější.</span><span class="sxs-lookup"><span data-stu-id="f8d76-225">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="f8d76-226">Vytvoření vlastního typu je ekvivalentní tomu, co pro vás framework dělá při použití [konfigurovat](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="f8d76-226">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="f8d76-227">Volání [Konfigurovat](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registruje přechodný <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>obecný , který má konstruktor, který přijímá zadané obecné typy služeb.</span><span class="sxs-lookup"><span data-stu-id="f8d76-227">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="f8d76-228">Ověření možností</span><span class="sxs-lookup"><span data-stu-id="f8d76-228">Options validation</span></span>

<span data-ttu-id="f8d76-229">Ověření možností umožňuje ověřit možnosti při konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="f8d76-229">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="f8d76-230">Volání `Validate` s metodou ověření, která vrátí, `true` pokud jsou možnosti platné a `false` pokud nejsou platné:</span><span class="sxs-lookup"><span data-stu-id="f8d76-230">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

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

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="f8d76-231">Předchozí příklad nastaví instanci `optionalOptionsName`pojmenovaných možností na .</span><span class="sxs-lookup"><span data-stu-id="f8d76-231">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="f8d76-232">Výchozí instance možností `Options.DefaultName`je .</span><span class="sxs-lookup"><span data-stu-id="f8d76-232">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="f8d76-233">Ověření se spustí při vytvoření instance možností.</span><span class="sxs-lookup"><span data-stu-id="f8d76-233">Validation runs when the options instance is created.</span></span> <span data-ttu-id="f8d76-234">Instance možností je zaručeno, že projít ověření při prvním přístupu.</span><span class="sxs-lookup"><span data-stu-id="f8d76-234">An options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="f8d76-235">Ověření možností nechrání před úpravami možností po vytvoření instance možností.</span><span class="sxs-lookup"><span data-stu-id="f8d76-235">Options validation doesn't guard against options modifications after the options instance is created.</span></span> <span data-ttu-id="f8d76-236">Například `IOptionsSnapshot` možnosti jsou vytvořeny a ověřeny jednou na požadavek při prvním přístupu k možnostem.</span><span class="sxs-lookup"><span data-stu-id="f8d76-236">For example, `IOptionsSnapshot` options are created and validated once per request when the options are first accessed.</span></span> <span data-ttu-id="f8d76-237">Možnosti `IOptionsSnapshot` nejsou znovu ověřeny při následných pokusech o přístup *pro stejný požadavek*.</span><span class="sxs-lookup"><span data-stu-id="f8d76-237">The `IOptionsSnapshot` options aren't validated again on subsequent access attempts *for the same request*.</span></span>

<span data-ttu-id="f8d76-238">Metoda `Validate` přijímá `Func<TOptions, bool>`.</span><span class="sxs-lookup"><span data-stu-id="f8d76-238">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="f8d76-239">Chcete-li plně `IValidateOptions<TOptions>`přizpůsobit ověření, implementujte , který umožňuje:</span><span class="sxs-lookup"><span data-stu-id="f8d76-239">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="f8d76-240">Ověření více typů možností:`class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="f8d76-240">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="f8d76-241">Ověření, které závisí na jiném typu možnosti:`public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="f8d76-241">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="f8d76-242">`IValidateOptions`Ověřuje:</span><span class="sxs-lookup"><span data-stu-id="f8d76-242">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="f8d76-243">Konkrétní instance pojmenovaných možností.</span><span class="sxs-lookup"><span data-stu-id="f8d76-243">A specific named options instance.</span></span>
* <span data-ttu-id="f8d76-244">Všechny možnosti, kdy `name` je `null`.</span><span class="sxs-lookup"><span data-stu-id="f8d76-244">All options when `name` is `null`.</span></span>

<span data-ttu-id="f8d76-245">Vraťte `ValidateOptionsResult` a z implementace rozhraní:</span><span class="sxs-lookup"><span data-stu-id="f8d76-245">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="f8d76-246">Ověření založené na datových poznámkách je k dispozici v balíčku [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) voláním <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> metody na . `OptionsBuilder<TOptions>`</span><span class="sxs-lookup"><span data-stu-id="f8d76-246">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="f8d76-247">`Microsoft.Extensions.Options.DataAnnotations`je implicitně odkazováno v aplikacích ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f8d76-247">`Microsoft.Extensions.Options.DataAnnotations` is implicitly referenced in ASP.NET Core apps.</span></span>

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

<span data-ttu-id="f8d76-248">Eager ověření (selhání rychle při spuštění) je v úvahu pro budoucí verzi.</span><span class="sxs-lookup"><span data-stu-id="f8d76-248">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="f8d76-249">Možnosti po konfiguraci</span><span class="sxs-lookup"><span data-stu-id="f8d76-249">Options post-configuration</span></span>

<span data-ttu-id="f8d76-250">Nastavte post-konfiguraci s <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="f8d76-250">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="f8d76-251">Po konfiguraci se <xref:Microsoft.Extensions.Options.IConfigureOptions%601> spustí po všech konfiguracích:</span><span class="sxs-lookup"><span data-stu-id="f8d76-251">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="f8d76-252"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>je k dispozici pro následné konfigurace pojmenovaných možností:</span><span class="sxs-lookup"><span data-stu-id="f8d76-252"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="f8d76-253">Slouží <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> k následné konfiguraci všech instancí konfigurace:</span><span class="sxs-lookup"><span data-stu-id="f8d76-253">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="f8d76-254">Přístup k možnostem při spuštění</span><span class="sxs-lookup"><span data-stu-id="f8d76-254">Accessing options during startup</span></span>

<span data-ttu-id="f8d76-255"><xref:Microsoft.Extensions.Options.IOptions%601>a <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> lze použít `Startup.Configure`v , protože `Configure` služby jsou sestaveny před spuštěním metody.</span><span class="sxs-lookup"><span data-stu-id="f8d76-255"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, 
    IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="f8d76-256">Nepoužívejte <xref:Microsoft.Extensions.Options.IOptions%601> ani <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="f8d76-256">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f8d76-257">Nekonzistentní možnosti stavu může existovat z důvodu řazení registrace služby.</span><span class="sxs-lookup"><span data-stu-id="f8d76-257">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="f8d76-258">Vzor voleb používá třídy k reprezentaci skupin souvisejících nastavení.</span><span class="sxs-lookup"><span data-stu-id="f8d76-258">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="f8d76-259">Když jsou [nastavení konfigurace](xref:fundamentals/configuration/index) izolována scénářem do samostatných tříd, aplikace dodržuje dva důležité principy softwarového inženýrství:</span><span class="sxs-lookup"><span data-stu-id="f8d76-259">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="f8d76-260">[Princip segregace rozhraní (ISP) nebo](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; scénáře zapouzdření (třídy), které závisí na nastavení konfigurace, závisí pouze na nastavení konfigurace, které používají.</span><span class="sxs-lookup"><span data-stu-id="f8d76-260">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="f8d76-261">[Nastavení oddělení obav](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; pro různé části aplikace není závislé nebo vzájemně se nespřahouje.</span><span class="sxs-lookup"><span data-stu-id="f8d76-261">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="f8d76-262">Možnosti také poskytují mechanismus pro ověření konfiguračních dat.</span><span class="sxs-lookup"><span data-stu-id="f8d76-262">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="f8d76-263">Další informace naleznete v části [Možnosti ověření.](#options-validation)</span><span class="sxs-lookup"><span data-stu-id="f8d76-263">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="f8d76-264">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="f8d76-264">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f8d76-265">Požadavky</span><span class="sxs-lookup"><span data-stu-id="f8d76-265">Prerequisites</span></span>

<span data-ttu-id="f8d76-266">Odkazna [metabalíček Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) nebo přidejte odkaz na balíček [microsoft.Extensions.Options.ConfigurationExtensions.](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/)</span><span class="sxs-lookup"><span data-stu-id="f8d76-266">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="f8d76-267">Rozhraní možností</span><span class="sxs-lookup"><span data-stu-id="f8d76-267">Options interfaces</span></span>

<span data-ttu-id="f8d76-268"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>slouží k načtení možností a `TOptions` správě oznámení o možnostech pro instance.</span><span class="sxs-lookup"><span data-stu-id="f8d76-268"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="f8d76-269"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>podporuje následující scénáře:</span><span class="sxs-lookup"><span data-stu-id="f8d76-269"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="f8d76-270">Změnit oznámení</span><span class="sxs-lookup"><span data-stu-id="f8d76-270">Change notifications</span></span>
* [<span data-ttu-id="f8d76-271">Pojmenované možnosti</span><span class="sxs-lookup"><span data-stu-id="f8d76-271">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="f8d76-272">Znovu načítatelná konfigurace</span><span class="sxs-lookup"><span data-stu-id="f8d76-272">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="f8d76-273">Selektivní možnosti<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>zneplatnění ( )</span><span class="sxs-lookup"><span data-stu-id="f8d76-273">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="f8d76-274">[Scénáře po konfiguraci](#options-post-configuration) umožňují nastavit nebo <xref:Microsoft.Extensions.Options.IConfigureOptions%601> změnit možnosti po všech konfiguracích.</span><span class="sxs-lookup"><span data-stu-id="f8d76-274">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="f8d76-275"><xref:Microsoft.Extensions.Options.IOptionsFactory%601>je zodpovědný za vytváření nových instancí možností.</span><span class="sxs-lookup"><span data-stu-id="f8d76-275"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="f8d76-276">Má jedinou <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> metodu.</span><span class="sxs-lookup"><span data-stu-id="f8d76-276">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="f8d76-277">Výchozí implementace trvá <xref:Microsoft.Extensions.Options.IConfigureOptions%601> všechny <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> registrované a spustí všechny konfigurace jako první, následuje post-konfigurace.</span><span class="sxs-lookup"><span data-stu-id="f8d76-277">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="f8d76-278">Rozlišuje mezi <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> a <xref:Microsoft.Extensions.Options.IConfigureOptions%601> pouze volá příslušné rozhraní.</span><span class="sxs-lookup"><span data-stu-id="f8d76-278">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="f8d76-279"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>slouží <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> k ukládání `TOptions` instancí do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="f8d76-279"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="f8d76-280">Zruší <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> platnost instance možností v monitoru tak, aby hodnota<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>byla přepočítána ( ).</span><span class="sxs-lookup"><span data-stu-id="f8d76-280">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="f8d76-281">Hodnoty lze ručně zavést <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>pomocí .</span><span class="sxs-lookup"><span data-stu-id="f8d76-281">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="f8d76-282">Metoda <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> se používá, když všechny pojmenované instance by měly být znovu vytvořeny na vyžádání.</span><span class="sxs-lookup"><span data-stu-id="f8d76-282">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="f8d76-283"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>je užitečné ve scénářích, kde by měly být přepočítány možnosti na každém požadavku.</span><span class="sxs-lookup"><span data-stu-id="f8d76-283"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="f8d76-284">Další informace naleznete v části [Znovu načíst konfigurační data pomocí iOptionsSnapshot.](#reload-configuration-data-with-ioptionssnapshot)</span><span class="sxs-lookup"><span data-stu-id="f8d76-284">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="f8d76-285"><xref:Microsoft.Extensions.Options.IOptions%601>lze použít k podpoře možností.</span><span class="sxs-lookup"><span data-stu-id="f8d76-285"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="f8d76-286">Však <xref:Microsoft.Extensions.Options.IOptions%601> nepodporuje předchozí scénáře <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="f8d76-286">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="f8d76-287">Můžete pokračovat v <xref:Microsoft.Extensions.Options.IOptions%601> používání v existujících architekturách <xref:Microsoft.Extensions.Options.IOptions%601> a knihovnách, které již používají <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>rozhraní a nevyžadují scénáře poskytované aplikací .</span><span class="sxs-lookup"><span data-stu-id="f8d76-287">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="f8d76-288">Konfigurace obecných možností</span><span class="sxs-lookup"><span data-stu-id="f8d76-288">General options configuration</span></span>

<span data-ttu-id="f8d76-289">Obecná konfigurace možností je v ukázkové aplikaci demonstrována jako Příklad 1.</span><span class="sxs-lookup"><span data-stu-id="f8d76-289">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="f8d76-290">Třída options musí být neabstraktní s veřejným konstruktorem bez parametrů.</span><span class="sxs-lookup"><span data-stu-id="f8d76-290">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="f8d76-291">Následující třída `MyOptions`, má dvě `Option1` `Option2`vlastnosti a .</span><span class="sxs-lookup"><span data-stu-id="f8d76-291">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="f8d76-292">Nastavení výchozích hodnot je volitelné, ale konstruktor třídy `Option1`v následujícím příkladu nastaví výchozí hodnotu .</span><span class="sxs-lookup"><span data-stu-id="f8d76-292">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="f8d76-293">`Option2`má výchozí hodnotu nastavenou přímou inicializací vlastnosti *(Models/MyOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="f8d76-293">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="f8d76-294">Třída `MyOptions` je přidána do <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> kontejneru služby s konfigurací a vázána na konfiguraci:</span><span class="sxs-lookup"><span data-stu-id="f8d76-294">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="f8d76-295">Následující model stránky používá [vkládání závislostí konstruktoru](xref:mvc/controllers/dependency-injection) s <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> přístupem k nastavení *(Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="f8d76-295">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="f8d76-296">Ukázkový soubor *appsettings.json* určuje hodnoty `option1` `option2`pro a :</span><span class="sxs-lookup"><span data-stu-id="f8d76-296">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="f8d76-297">Při spuštění aplikace `OnGet` metoda modelu stránky vrátí řetězec zobrazující hodnoty třídy možností:</span><span class="sxs-lookup"><span data-stu-id="f8d76-297">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="f8d76-298">Při použití <xref:System.Configuration.ConfigurationBuilder> vlastní konfigurace možností z souboru nastavení zkontrolujte, zda je základní cesta nastavena správně:</span><span class="sxs-lookup"><span data-stu-id="f8d76-298">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="f8d76-299">Explicitní nastavení základní cesty není nutné při načítání konfigurace <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>možností z souboru nastavení prostřednictvím aplikace .</span><span class="sxs-lookup"><span data-stu-id="f8d76-299">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="f8d76-300">Konfigurace jednoduchých možností s delegátem</span><span class="sxs-lookup"><span data-stu-id="f8d76-300">Configure simple options with a delegate</span></span>

<span data-ttu-id="f8d76-301">Konfigurace jednoduchých možností s delegátem je v ukázkové aplikaci demonstrována jako příklad 2.</span><span class="sxs-lookup"><span data-stu-id="f8d76-301">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="f8d76-302">Pomocí delegáta nastavte hodnoty možností.</span><span class="sxs-lookup"><span data-stu-id="f8d76-302">Use a delegate to set options values.</span></span> <span data-ttu-id="f8d76-303">Ukázková aplikace `MyOptionsWithDelegateConfig` používá třídu *(Models/MyOptionsWithDelegateConfig.cs*):</span><span class="sxs-lookup"><span data-stu-id="f8d76-303">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="f8d76-304">V následujícím kódu je <xref:Microsoft.Extensions.Options.IConfigureOptions%601> do kontejneru služby přidána druhá služba.</span><span class="sxs-lookup"><span data-stu-id="f8d76-304">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="f8d76-305">Používá delegáta ke konfiguraci `MyOptionsWithDelegateConfig`vazby pomocí aplikace :</span><span class="sxs-lookup"><span data-stu-id="f8d76-305">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="f8d76-306">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="f8d76-306">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="f8d76-307">Můžete přidat více poskytovatelů konfigurace.</span><span class="sxs-lookup"><span data-stu-id="f8d76-307">You can add multiple configuration providers.</span></span> <span data-ttu-id="f8d76-308">Zprostředkovatelé konfigurace jsou k dispozici z balíčků NuGet a jsou použity v pořadí, ve kterém jsou registrovány.</span><span class="sxs-lookup"><span data-stu-id="f8d76-308">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="f8d76-309">Další informace naleznete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="f8d76-309">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="f8d76-310">Každé volání <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> přidá <xref:Microsoft.Extensions.Options.IConfigureOptions%601> službu do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="f8d76-310">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="f8d76-311">V předchozím příkladu jsou `Option1` hodnoty `Option2` a jsou zadány v *souboru appsettings.json*, ale hodnoty `Option1` a `Option2` jsou přepsány nakonfigurovaným delegátem.</span><span class="sxs-lookup"><span data-stu-id="f8d76-311">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="f8d76-312">Pokud je povolena více než jedna konfigurační služba, *vyhrává* poslední zadaný zdroj konfigurace a nastavuje hodnotu konfigurace.</span><span class="sxs-lookup"><span data-stu-id="f8d76-312">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="f8d76-313">Při spuštění aplikace `OnGet` metoda modelu stránky vrátí řetězec zobrazující hodnoty třídy možností:</span><span class="sxs-lookup"><span data-stu-id="f8d76-313">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="f8d76-314">Konfigurace dílčích možností</span><span class="sxs-lookup"><span data-stu-id="f8d76-314">Suboptions configuration</span></span>

<span data-ttu-id="f8d76-315">Konfigurace dílčích možností je v ukázkové aplikaci demonstrována jako Příklad 3.</span><span class="sxs-lookup"><span data-stu-id="f8d76-315">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="f8d76-316">Aplikace by měly vytvářet třídy možností, které se v aplikaci připojují ke konkrétním skupinám scénářů (třídám).</span><span class="sxs-lookup"><span data-stu-id="f8d76-316">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="f8d76-317">Části aplikace, které vyžadují hodnoty konfigurace by měl mít přístup pouze k hodnotám konfigurace, které používají.</span><span class="sxs-lookup"><span data-stu-id="f8d76-317">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="f8d76-318">Při vazbě možnosti konfigurace, každá vlastnost v typu možnosti `property[:sub-property:]`je vázánna na konfigurační klíč formuláře .</span><span class="sxs-lookup"><span data-stu-id="f8d76-318">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="f8d76-319">Například `MyOptions.Option1` vlastnost je vázána `Option1`na klíč , `option1` který je číst z vlastnosti v *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="f8d76-319">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="f8d76-320">V následujícím kódu je <xref:Microsoft.Extensions.Options.IConfigureOptions%601> do kontejneru služby přidána třetí služba.</span><span class="sxs-lookup"><span data-stu-id="f8d76-320">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="f8d76-321">Váže `MySubOptions` se na `subsection` část souboru *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="f8d76-321">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="f8d76-322">Metoda `GetSection` vyžaduje <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> obor názvů.</span><span class="sxs-lookup"><span data-stu-id="f8d76-322">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="f8d76-323">Ukázkový soubor *appsettings.json* definuje `subsection` člen s `suboption1` klíči pro a `suboption2`:</span><span class="sxs-lookup"><span data-stu-id="f8d76-323">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="f8d76-324">Třída `MySubOptions` definuje vlastnosti `SubOption1` `SubOption2`a , chcete-li podržet hodnoty možností *(Models/MySubOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="f8d76-324">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="f8d76-325">`OnGet` Metoda modelu stránky vrátí řetězec s hodnotami voleb *(Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="f8d76-325">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="f8d76-326">Při spuštění aplikace `OnGet` metoda vrátí řetězec zobrazující hodnoty třídy dílčí možnosti:</span><span class="sxs-lookup"><span data-stu-id="f8d76-326">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-injection"></a><span data-ttu-id="f8d76-327">Možnosti vstřikování</span><span class="sxs-lookup"><span data-stu-id="f8d76-327">Options injection</span></span>

<span data-ttu-id="f8d76-328">Možnosti vkládání je demonstrována jako příklad 4 v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f8d76-328">Options injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="f8d76-329">Vstříkněte <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> do:</span><span class="sxs-lookup"><span data-stu-id="f8d76-329">Inject <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into:</span></span>

* <span data-ttu-id="f8d76-330">Razor stránky nebo MVC [`@inject`](xref:mvc/views/razor#inject) zobrazení se směrnicí Razor.</span><span class="sxs-lookup"><span data-stu-id="f8d76-330">A Razor page or MVC view with the [`@inject`](xref:mvc/views/razor#inject) Razor directive.</span></span>
* <span data-ttu-id="f8d76-331">Model stránky nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="f8d76-331">A page or view model.</span></span>

<span data-ttu-id="f8d76-332">Následující příklad z ukázkové <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> aplikace vloží do modelu stránky *(Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="f8d76-332">The following example from the sample app injects <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into a page model (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="f8d76-333">Ukázková aplikace ukazuje, `IOptionsMonitor<MyOptions>` jak `@inject` vložit direktivu:</span><span class="sxs-lookup"><span data-stu-id="f8d76-333">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="f8d76-334">Při spuštění aplikace se hodnoty možností zobrazí na vykreslené stránce:</span><span class="sxs-lookup"><span data-stu-id="f8d76-334">When the app is run, the options values are shown in the rendered page:</span></span>

![Hodnoty možností Option1: value1_from_json a Option2: -1 jsou načteny z modelu a vstřikováním do pohledu.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="f8d76-336">Opětovné načtení konfiguračních dat pomocí aplikace IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="f8d76-336">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="f8d76-337">Opětovné načtení <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> konfiguračních dat s je znázorněno v příkladu 5 v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f8d76-337">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="f8d76-338">Pomocí <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, možnosti jsou vypočítány jednou na požadavek při přístupu a ukládání do mezipaměti po dobu platnosti požadavku.</span><span class="sxs-lookup"><span data-stu-id="f8d76-338">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="f8d76-339">Rozdíl mezi `IOptionsMonitor` `IOptionsSnapshot` a je, že:</span><span class="sxs-lookup"><span data-stu-id="f8d76-339">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="f8d76-340">`IOptionsMonitor`je [služba singleton,](xref:fundamentals/dependency-injection#singleton) která kdykoli načte aktuální hodnoty možností, což je užitečné zejména v závislostech singleton.</span><span class="sxs-lookup"><span data-stu-id="f8d76-340">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="f8d76-341">`IOptionsSnapshot`je [služba s vymezeným oborem](xref:fundamentals/dependency-injection#scoped) a poskytuje `IOptionsSnapshot<T>` snímek možností v době, kdy je objekt vytvořen.</span><span class="sxs-lookup"><span data-stu-id="f8d76-341">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="f8d76-342">Možnosti snímky jsou určeny pro použití s přechodnými závislostmi a závislostmi s vymezeným oborem.</span><span class="sxs-lookup"><span data-stu-id="f8d76-342">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="f8d76-343">Následující příklad ukazuje, jak <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> je vytvořen nový po *appsettings.json* změny (*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="f8d76-343">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="f8d76-344">Více požadavků na server vrátí konstantní hodnoty poskytované souborem *appsettings.json,* dokud se soubor nezmění a konfigurace se znovu nenačte.</span><span class="sxs-lookup"><span data-stu-id="f8d76-344">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="f8d76-345">Následující obrázek znázorňuje počáteční `option1` hodnoty načtené `option2` ze souboru *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="f8d76-345">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="f8d76-346">Změňte hodnoty v souboru *appsettings.json* na `value1_from_json UPDATED` a `200`.</span><span class="sxs-lookup"><span data-stu-id="f8d76-346">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="f8d76-347">Uložte soubor *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="f8d76-347">Save the *appsettings.json* file.</span></span> <span data-ttu-id="f8d76-348">Aktualizujte prohlížeč, abyste viděli, že jsou aktualizovány hodnoty možností:</span><span class="sxs-lookup"><span data-stu-id="f8d76-348">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="f8d76-349">Podpora pojmenovaných možností pomocí nástroje IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="f8d76-349">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="f8d76-350">Pojmenované možnosti <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> podpora s je demonstrován jako příklad 6 v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f8d76-350">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="f8d76-351">Podpora pojmenovaných možností umožňuje aplikaci rozlišovat mezi konfiguracemi pojmenovaných možností.</span><span class="sxs-lookup"><span data-stu-id="f8d76-351">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="f8d76-352">V ukázkové aplikaci jsou pojmenované možnosti deklarovány pomocí [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), který volá [> ConfigureNamedOptions\<TOptions. Nakonfigurujte](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) metodu rozšíření.</span><span class="sxs-lookup"><span data-stu-id="f8d76-352">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="f8d76-353">Pojmenované možnosti rozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="f8d76-353">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="f8d76-354">Ukázková aplikace přistupuje k pojmenovaným možnostem <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> pomocí *(Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="f8d76-354">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="f8d76-355">Spuštění ukázkové aplikace, pojmenované možnosti jsou vráceny:</span><span class="sxs-lookup"><span data-stu-id="f8d76-355">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="f8d76-356">`named_options_1`hodnoty jsou k dispozici z konfigurace, které jsou načteny ze souboru *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="f8d76-356">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="f8d76-357">`named_options_2`hodnoty jsou poskytovány:</span><span class="sxs-lookup"><span data-stu-id="f8d76-357">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="f8d76-358">Delegát `named_options_2` v `ConfigureServices` `Option1`pro .</span><span class="sxs-lookup"><span data-stu-id="f8d76-358">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="f8d76-359">Výchozí hodnota `Option2` pro poskytované `MyOptions` třídy.</span><span class="sxs-lookup"><span data-stu-id="f8d76-359">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="f8d76-360">Konfigurace všech možností pomocí metody ConfigureAll</span><span class="sxs-lookup"><span data-stu-id="f8d76-360">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="f8d76-361">Nakonfigurujte všechny <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> instance možností pomocí metody.</span><span class="sxs-lookup"><span data-stu-id="f8d76-361">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="f8d76-362">Následující kód konfiguruje `Option1` pro všechny instance konfigurace se společnou hodnotou.</span><span class="sxs-lookup"><span data-stu-id="f8d76-362">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="f8d76-363">Přidejte do `Startup.ConfigureServices` metody ručně následující kód:</span><span class="sxs-lookup"><span data-stu-id="f8d76-363">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="f8d76-364">Spuštění ukázkové aplikace po přidání kódu vytvoří následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="f8d76-364">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="f8d76-365">Všechny možnosti jsou pojmenované instance.</span><span class="sxs-lookup"><span data-stu-id="f8d76-365">All options are named instances.</span></span> <span data-ttu-id="f8d76-366">Existující <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instance jsou považovány za `Options.DefaultName` cílení instance, která je `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="f8d76-366">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="f8d76-367"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>také provádí <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="f8d76-367"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="f8d76-368">Výchozí implementace má <xref:Microsoft.Extensions.Options.IOptionsFactory%601> logiku použít každý vhodně.</span><span class="sxs-lookup"><span data-stu-id="f8d76-368">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="f8d76-369">Pojmenovaná `null` možnost se používá k cílení na všechny pojmenované<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> instance <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> namísto konkrétní pojmenované instance (a použít tuto konvenci).</span><span class="sxs-lookup"><span data-stu-id="f8d76-369">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="f8d76-370">Rozhraní API pro stavitele možností</span><span class="sxs-lookup"><span data-stu-id="f8d76-370">OptionsBuilder API</span></span>

<span data-ttu-id="f8d76-371"><xref:Microsoft.Extensions.Options.OptionsBuilder%601>slouží ke `TOptions` konfiguraci instancí.</span><span class="sxs-lookup"><span data-stu-id="f8d76-371"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="f8d76-372">`OptionsBuilder`zjednodušuje vytváření pojmenovaných možností, protože se `AddOptions<TOptions>(string optionsName)` jedná pouze o jeden parametr počátečního volání namísto zobrazení ve všech následných voláních.</span><span class="sxs-lookup"><span data-stu-id="f8d76-372">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="f8d76-373">Možnosti ověření `ConfigureOptions` a přetížení, které přijímají závislosti `OptionsBuilder`služby jsou k dispozici pouze prostřednictvím .</span><span class="sxs-lookup"><span data-stu-id="f8d76-373">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="f8d76-374">Konfigurace možností pomocí služeb DI</span><span class="sxs-lookup"><span data-stu-id="f8d76-374">Use DI services to configure options</span></span>

<span data-ttu-id="f8d76-375">Můžete přistupovat k dalším službám z vkládání závislostí při konfiguraci možností dvěma způsoby:</span><span class="sxs-lookup"><span data-stu-id="f8d76-375">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="f8d76-376">Předejte delegáta konfigurace [konfigurovat na](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="f8d76-376">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="f8d76-377">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) poskytuje přetížení [Konfigurovat,](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) které umožňují používat až pět služeb ke konfiguraci možností:</span><span class="sxs-lookup"><span data-stu-id="f8d76-377">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="f8d76-378">Vytvořte vlastní typ, <xref:Microsoft.Extensions.Options.IConfigureOptions%601> <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> který implementuje nebo a zaregistrujte typ jako službu.</span><span class="sxs-lookup"><span data-stu-id="f8d76-378">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="f8d76-379">Doporučujeme předat delegáta konfigurace [konfigurovat](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), protože vytvoření služby je složitější.</span><span class="sxs-lookup"><span data-stu-id="f8d76-379">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="f8d76-380">Vytvoření vlastního typu je ekvivalentní tomu, co pro vás framework dělá při použití [konfigurovat](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="f8d76-380">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="f8d76-381">Volání [Konfigurovat](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registruje přechodný <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>obecný , který má konstruktor, který přijímá zadané obecné typy služeb.</span><span class="sxs-lookup"><span data-stu-id="f8d76-381">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="f8d76-382">Ověření možností</span><span class="sxs-lookup"><span data-stu-id="f8d76-382">Options validation</span></span>

<span data-ttu-id="f8d76-383">Ověření možností umožňuje ověřit možnosti při konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="f8d76-383">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="f8d76-384">Volání `Validate` s metodou ověření, která vrátí, `true` pokud jsou možnosti platné a `false` pokud nejsou platné:</span><span class="sxs-lookup"><span data-stu-id="f8d76-384">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

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

<span data-ttu-id="f8d76-385">Předchozí příklad nastaví instanci `optionalOptionsName`pojmenovaných možností na .</span><span class="sxs-lookup"><span data-stu-id="f8d76-385">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="f8d76-386">Výchozí instance možností `Options.DefaultName`je .</span><span class="sxs-lookup"><span data-stu-id="f8d76-386">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="f8d76-387">Ověření se spustí při vytvoření instance možností.</span><span class="sxs-lookup"><span data-stu-id="f8d76-387">Validation runs when the options instance is created.</span></span> <span data-ttu-id="f8d76-388">Instance možností je zaručeno, že projít ověření při prvním přístupu.</span><span class="sxs-lookup"><span data-stu-id="f8d76-388">An options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="f8d76-389">Ověření možností nechrání před úpravami možností po vytvoření instance možností.</span><span class="sxs-lookup"><span data-stu-id="f8d76-389">Options validation doesn't guard against options modifications after the options instance is created.</span></span> <span data-ttu-id="f8d76-390">Například `IOptionsSnapshot` možnosti jsou vytvořeny a ověřeny jednou na požadavek při prvním přístupu k možnostem.</span><span class="sxs-lookup"><span data-stu-id="f8d76-390">For example, `IOptionsSnapshot` options are created and validated once per request when the options are first accessed.</span></span> <span data-ttu-id="f8d76-391">Možnosti `IOptionsSnapshot` nejsou znovu ověřeny při následných pokusech o přístup *pro stejný požadavek*.</span><span class="sxs-lookup"><span data-stu-id="f8d76-391">The `IOptionsSnapshot` options aren't validated again on subsequent access attempts *for the same request*.</span></span>

<span data-ttu-id="f8d76-392">Metoda `Validate` přijímá `Func<TOptions, bool>`.</span><span class="sxs-lookup"><span data-stu-id="f8d76-392">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="f8d76-393">Chcete-li plně `IValidateOptions<TOptions>`přizpůsobit ověření, implementujte , který umožňuje:</span><span class="sxs-lookup"><span data-stu-id="f8d76-393">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="f8d76-394">Ověření více typů možností:`class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="f8d76-394">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="f8d76-395">Ověření, které závisí na jiném typu možnosti:`public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="f8d76-395">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="f8d76-396">`IValidateOptions`Ověřuje:</span><span class="sxs-lookup"><span data-stu-id="f8d76-396">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="f8d76-397">Konkrétní instance pojmenovaných možností.</span><span class="sxs-lookup"><span data-stu-id="f8d76-397">A specific named options instance.</span></span>
* <span data-ttu-id="f8d76-398">Všechny možnosti, kdy `name` je `null`.</span><span class="sxs-lookup"><span data-stu-id="f8d76-398">All options when `name` is `null`.</span></span>

<span data-ttu-id="f8d76-399">Vraťte `ValidateOptionsResult` a z implementace rozhraní:</span><span class="sxs-lookup"><span data-stu-id="f8d76-399">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="f8d76-400">Ověření založené na datových poznámkách je k dispozici v balíčku [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) voláním <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> metody na . `OptionsBuilder<TOptions>`</span><span class="sxs-lookup"><span data-stu-id="f8d76-400">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="f8d76-401">`Microsoft.Extensions.Options.DataAnnotations`je součástí [metabalíčku Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="f8d76-401">`Microsoft.Extensions.Options.DataAnnotations` is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

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

<span data-ttu-id="f8d76-402">Eager ověření (selhání rychle při spuštění) je v úvahu pro budoucí verzi.</span><span class="sxs-lookup"><span data-stu-id="f8d76-402">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="f8d76-403">Možnosti po konfiguraci</span><span class="sxs-lookup"><span data-stu-id="f8d76-403">Options post-configuration</span></span>

<span data-ttu-id="f8d76-404">Nastavte post-konfiguraci s <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="f8d76-404">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="f8d76-405">Po konfiguraci se <xref:Microsoft.Extensions.Options.IConfigureOptions%601> spustí po všech konfiguracích:</span><span class="sxs-lookup"><span data-stu-id="f8d76-405">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="f8d76-406"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>je k dispozici pro následné konfigurace pojmenovaných možností:</span><span class="sxs-lookup"><span data-stu-id="f8d76-406"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="f8d76-407">Slouží <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> k následné konfiguraci všech instancí konfigurace:</span><span class="sxs-lookup"><span data-stu-id="f8d76-407">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="f8d76-408">Přístup k možnostem při spuštění</span><span class="sxs-lookup"><span data-stu-id="f8d76-408">Accessing options during startup</span></span>

<span data-ttu-id="f8d76-409"><xref:Microsoft.Extensions.Options.IOptions%601>a <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> lze použít `Startup.Configure`v , protože `Configure` služby jsou sestaveny před spuštěním metody.</span><span class="sxs-lookup"><span data-stu-id="f8d76-409"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="f8d76-410">Nepoužívejte <xref:Microsoft.Extensions.Options.IOptions%601> ani <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="f8d76-410">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f8d76-411">Nekonzistentní možnosti stavu může existovat z důvodu řazení registrace služby.</span><span class="sxs-lookup"><span data-stu-id="f8d76-411">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="f8d76-412">Vzor voleb používá třídy k reprezentaci skupin souvisejících nastavení.</span><span class="sxs-lookup"><span data-stu-id="f8d76-412">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="f8d76-413">Když jsou [nastavení konfigurace](xref:fundamentals/configuration/index) izolována scénářem do samostatných tříd, aplikace dodržuje dva důležité principy softwarového inženýrství:</span><span class="sxs-lookup"><span data-stu-id="f8d76-413">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="f8d76-414">[Princip segregace rozhraní (ISP) nebo](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; scénáře zapouzdření (třídy), které závisí na nastavení konfigurace, závisí pouze na nastavení konfigurace, které používají.</span><span class="sxs-lookup"><span data-stu-id="f8d76-414">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="f8d76-415">[Nastavení oddělení obav](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; pro různé části aplikace není závislé nebo vzájemně se nespřahouje.</span><span class="sxs-lookup"><span data-stu-id="f8d76-415">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="f8d76-416">Možnosti také poskytují mechanismus pro ověření konfiguračních dat.</span><span class="sxs-lookup"><span data-stu-id="f8d76-416">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="f8d76-417">Další informace naleznete v části [Možnosti ověření.](#options-validation)</span><span class="sxs-lookup"><span data-stu-id="f8d76-417">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="f8d76-418">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="f8d76-418">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f8d76-419">Požadavky</span><span class="sxs-lookup"><span data-stu-id="f8d76-419">Prerequisites</span></span>

<span data-ttu-id="f8d76-420">Odkazna [metabalíček Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) nebo přidejte odkaz na balíček [microsoft.Extensions.Options.ConfigurationExtensions.](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/)</span><span class="sxs-lookup"><span data-stu-id="f8d76-420">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="f8d76-421">Rozhraní možností</span><span class="sxs-lookup"><span data-stu-id="f8d76-421">Options interfaces</span></span>

<span data-ttu-id="f8d76-422"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>slouží k načtení možností a `TOptions` správě oznámení o možnostech pro instance.</span><span class="sxs-lookup"><span data-stu-id="f8d76-422"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="f8d76-423"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>podporuje následující scénáře:</span><span class="sxs-lookup"><span data-stu-id="f8d76-423"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="f8d76-424">Změnit oznámení</span><span class="sxs-lookup"><span data-stu-id="f8d76-424">Change notifications</span></span>
* [<span data-ttu-id="f8d76-425">Pojmenované možnosti</span><span class="sxs-lookup"><span data-stu-id="f8d76-425">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="f8d76-426">Znovu načítatelná konfigurace</span><span class="sxs-lookup"><span data-stu-id="f8d76-426">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="f8d76-427">Selektivní možnosti<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>zneplatnění ( )</span><span class="sxs-lookup"><span data-stu-id="f8d76-427">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="f8d76-428">[Scénáře po konfiguraci](#options-post-configuration) umožňují nastavit nebo <xref:Microsoft.Extensions.Options.IConfigureOptions%601> změnit možnosti po všech konfiguracích.</span><span class="sxs-lookup"><span data-stu-id="f8d76-428">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="f8d76-429"><xref:Microsoft.Extensions.Options.IOptionsFactory%601>je zodpovědný za vytváření nových instancí možností.</span><span class="sxs-lookup"><span data-stu-id="f8d76-429"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="f8d76-430">Má jedinou <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> metodu.</span><span class="sxs-lookup"><span data-stu-id="f8d76-430">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="f8d76-431">Výchozí implementace trvá <xref:Microsoft.Extensions.Options.IConfigureOptions%601> všechny <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> registrované a spustí všechny konfigurace jako první, následuje post-konfigurace.</span><span class="sxs-lookup"><span data-stu-id="f8d76-431">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="f8d76-432">Rozlišuje mezi <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> a <xref:Microsoft.Extensions.Options.IConfigureOptions%601> pouze volá příslušné rozhraní.</span><span class="sxs-lookup"><span data-stu-id="f8d76-432">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="f8d76-433"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>slouží <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> k ukládání `TOptions` instancí do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="f8d76-433"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="f8d76-434">Zruší <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> platnost instance možností v monitoru tak, aby hodnota<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>byla přepočítána ( ).</span><span class="sxs-lookup"><span data-stu-id="f8d76-434">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="f8d76-435">Hodnoty lze ručně zavést <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>pomocí .</span><span class="sxs-lookup"><span data-stu-id="f8d76-435">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="f8d76-436">Metoda <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> se používá, když všechny pojmenované instance by měly být znovu vytvořeny na vyžádání.</span><span class="sxs-lookup"><span data-stu-id="f8d76-436">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="f8d76-437"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>je užitečné ve scénářích, kde by měly být přepočítány možnosti na každém požadavku.</span><span class="sxs-lookup"><span data-stu-id="f8d76-437"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="f8d76-438">Další informace naleznete v části [Znovu načíst konfigurační data pomocí iOptionsSnapshot.](#reload-configuration-data-with-ioptionssnapshot)</span><span class="sxs-lookup"><span data-stu-id="f8d76-438">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="f8d76-439"><xref:Microsoft.Extensions.Options.IOptions%601>lze použít k podpoře možností.</span><span class="sxs-lookup"><span data-stu-id="f8d76-439"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="f8d76-440">Však <xref:Microsoft.Extensions.Options.IOptions%601> nepodporuje předchozí scénáře <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="f8d76-440">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="f8d76-441">Můžete pokračovat v <xref:Microsoft.Extensions.Options.IOptions%601> používání v existujících architekturách <xref:Microsoft.Extensions.Options.IOptions%601> a knihovnách, které již používají <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>rozhraní a nevyžadují scénáře poskytované aplikací .</span><span class="sxs-lookup"><span data-stu-id="f8d76-441">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="f8d76-442">Konfigurace obecných možností</span><span class="sxs-lookup"><span data-stu-id="f8d76-442">General options configuration</span></span>

<span data-ttu-id="f8d76-443">Obecná konfigurace možností je v ukázkové aplikaci demonstrována jako Příklad 1.</span><span class="sxs-lookup"><span data-stu-id="f8d76-443">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="f8d76-444">Třída options musí být neabstraktní s veřejným konstruktorem bez parametrů.</span><span class="sxs-lookup"><span data-stu-id="f8d76-444">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="f8d76-445">Následující třída `MyOptions`, má dvě `Option1` `Option2`vlastnosti a .</span><span class="sxs-lookup"><span data-stu-id="f8d76-445">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="f8d76-446">Nastavení výchozích hodnot je volitelné, ale konstruktor třídy `Option1`v následujícím příkladu nastaví výchozí hodnotu .</span><span class="sxs-lookup"><span data-stu-id="f8d76-446">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="f8d76-447">`Option2`má výchozí hodnotu nastavenou přímou inicializací vlastnosti *(Models/MyOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="f8d76-447">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="f8d76-448">Třída `MyOptions` je přidána do <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> kontejneru služby s konfigurací a vázána na konfiguraci:</span><span class="sxs-lookup"><span data-stu-id="f8d76-448">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="f8d76-449">Následující model stránky používá [vkládání závislostí konstruktoru](xref:mvc/controllers/dependency-injection) s <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> přístupem k nastavení *(Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="f8d76-449">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="f8d76-450">Ukázkový soubor *appsettings.json* určuje hodnoty `option1` `option2`pro a :</span><span class="sxs-lookup"><span data-stu-id="f8d76-450">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="f8d76-451">Při spuštění aplikace `OnGet` metoda modelu stránky vrátí řetězec zobrazující hodnoty třídy možností:</span><span class="sxs-lookup"><span data-stu-id="f8d76-451">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="f8d76-452">Při použití <xref:System.Configuration.ConfigurationBuilder> vlastní konfigurace možností z souboru nastavení zkontrolujte, zda je základní cesta nastavena správně:</span><span class="sxs-lookup"><span data-stu-id="f8d76-452">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="f8d76-453">Explicitní nastavení základní cesty není nutné při načítání konfigurace <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>možností z souboru nastavení prostřednictvím aplikace .</span><span class="sxs-lookup"><span data-stu-id="f8d76-453">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="f8d76-454">Konfigurace jednoduchých možností s delegátem</span><span class="sxs-lookup"><span data-stu-id="f8d76-454">Configure simple options with a delegate</span></span>

<span data-ttu-id="f8d76-455">Konfigurace jednoduchých možností s delegátem je v ukázkové aplikaci demonstrována jako příklad 2.</span><span class="sxs-lookup"><span data-stu-id="f8d76-455">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="f8d76-456">Pomocí delegáta nastavte hodnoty možností.</span><span class="sxs-lookup"><span data-stu-id="f8d76-456">Use a delegate to set options values.</span></span> <span data-ttu-id="f8d76-457">Ukázková aplikace `MyOptionsWithDelegateConfig` používá třídu *(Models/MyOptionsWithDelegateConfig.cs*):</span><span class="sxs-lookup"><span data-stu-id="f8d76-457">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="f8d76-458">V následujícím kódu je <xref:Microsoft.Extensions.Options.IConfigureOptions%601> do kontejneru služby přidána druhá služba.</span><span class="sxs-lookup"><span data-stu-id="f8d76-458">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="f8d76-459">Používá delegáta ke konfiguraci `MyOptionsWithDelegateConfig`vazby pomocí aplikace :</span><span class="sxs-lookup"><span data-stu-id="f8d76-459">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="f8d76-460">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="f8d76-460">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="f8d76-461">Můžete přidat více poskytovatelů konfigurace.</span><span class="sxs-lookup"><span data-stu-id="f8d76-461">You can add multiple configuration providers.</span></span> <span data-ttu-id="f8d76-462">Zprostředkovatelé konfigurace jsou k dispozici z balíčků NuGet a jsou použity v pořadí, ve kterém jsou registrovány.</span><span class="sxs-lookup"><span data-stu-id="f8d76-462">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="f8d76-463">Další informace naleznete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="f8d76-463">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="f8d76-464">Každé volání <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> přidá <xref:Microsoft.Extensions.Options.IConfigureOptions%601> službu do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="f8d76-464">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="f8d76-465">V předchozím příkladu jsou `Option1` hodnoty `Option2` a jsou zadány v *souboru appsettings.json*, ale hodnoty `Option1` a `Option2` jsou přepsány nakonfigurovaným delegátem.</span><span class="sxs-lookup"><span data-stu-id="f8d76-465">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="f8d76-466">Pokud je povolena více než jedna konfigurační služba, *vyhrává* poslední zadaný zdroj konfigurace a nastavuje hodnotu konfigurace.</span><span class="sxs-lookup"><span data-stu-id="f8d76-466">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="f8d76-467">Při spuštění aplikace `OnGet` metoda modelu stránky vrátí řetězec zobrazující hodnoty třídy možností:</span><span class="sxs-lookup"><span data-stu-id="f8d76-467">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="f8d76-468">Konfigurace dílčích možností</span><span class="sxs-lookup"><span data-stu-id="f8d76-468">Suboptions configuration</span></span>

<span data-ttu-id="f8d76-469">Konfigurace dílčích možností je v ukázkové aplikaci demonstrována jako Příklad 3.</span><span class="sxs-lookup"><span data-stu-id="f8d76-469">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="f8d76-470">Aplikace by měly vytvářet třídy možností, které se v aplikaci připojují ke konkrétním skupinám scénářů (třídám).</span><span class="sxs-lookup"><span data-stu-id="f8d76-470">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="f8d76-471">Části aplikace, které vyžadují hodnoty konfigurace by měl mít přístup pouze k hodnotám konfigurace, které používají.</span><span class="sxs-lookup"><span data-stu-id="f8d76-471">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="f8d76-472">Při vazbě možnosti konfigurace, každá vlastnost v typu možnosti `property[:sub-property:]`je vázánna na konfigurační klíč formuláře .</span><span class="sxs-lookup"><span data-stu-id="f8d76-472">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="f8d76-473">Například `MyOptions.Option1` vlastnost je vázána `Option1`na klíč , `option1` který je číst z vlastnosti v *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="f8d76-473">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="f8d76-474">V následujícím kódu je <xref:Microsoft.Extensions.Options.IConfigureOptions%601> do kontejneru služby přidána třetí služba.</span><span class="sxs-lookup"><span data-stu-id="f8d76-474">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="f8d76-475">Váže `MySubOptions` se na `subsection` část souboru *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="f8d76-475">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="f8d76-476">Metoda `GetSection` vyžaduje <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> obor názvů.</span><span class="sxs-lookup"><span data-stu-id="f8d76-476">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="f8d76-477">Ukázkový soubor *appsettings.json* definuje `subsection` člen s `suboption1` klíči pro a `suboption2`:</span><span class="sxs-lookup"><span data-stu-id="f8d76-477">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="f8d76-478">Třída `MySubOptions` definuje vlastnosti `SubOption1` `SubOption2`a , chcete-li podržet hodnoty možností *(Models/MySubOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="f8d76-478">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="f8d76-479">`OnGet` Metoda modelu stránky vrátí řetězec s hodnotami voleb *(Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="f8d76-479">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="f8d76-480">Při spuštění aplikace `OnGet` metoda vrátí řetězec zobrazující hodnoty třídy dílčí možnosti:</span><span class="sxs-lookup"><span data-stu-id="f8d76-480">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="f8d76-481">Možnosti poskytované modelem pohledu nebo přímým vkládáním pohledu</span><span class="sxs-lookup"><span data-stu-id="f8d76-481">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="f8d76-482">Možnosti poskytované model zobrazení nebo přímé zobrazení vkládání je demonstrován jako příklad 4 v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f8d76-482">Options provided by a view model or with direct view injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="f8d76-483">Možnosti lze dodat v modelu zobrazení <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> nebo vložením přímo do zobrazení *(Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="f8d76-483">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="f8d76-484">Ukázková aplikace ukazuje, `IOptionsMonitor<MyOptions>` jak `@inject` vložit direktivu:</span><span class="sxs-lookup"><span data-stu-id="f8d76-484">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="f8d76-485">Při spuštění aplikace se hodnoty možností zobrazí na vykreslené stránce:</span><span class="sxs-lookup"><span data-stu-id="f8d76-485">When the app is run, the options values are shown in the rendered page:</span></span>

![Hodnoty možností Option1: value1_from_json a Option2: -1 jsou načteny z modelu a vstřikováním do pohledu.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="f8d76-487">Opětovné načtení konfiguračních dat pomocí aplikace IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="f8d76-487">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="f8d76-488">Opětovné načtení <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> konfiguračních dat s je znázorněno v příkladu 5 v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f8d76-488">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="f8d76-489"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>podporuje možnosti opětovného načtení s minimální režií zpracování.</span><span class="sxs-lookup"><span data-stu-id="f8d76-489"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="f8d76-490">Možnosti jsou vypočítány jednou na požadavek při přístupu a ukládání do mezipaměti po dobu životnosti požadavku.</span><span class="sxs-lookup"><span data-stu-id="f8d76-490">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="f8d76-491">Následující příklad ukazuje, jak <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> je vytvořen nový po *appsettings.json* změny (*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="f8d76-491">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="f8d76-492">Více požadavků na server vrátí konstantní hodnoty poskytované souborem *appsettings.json,* dokud se soubor nezmění a konfigurace se znovu nenačte.</span><span class="sxs-lookup"><span data-stu-id="f8d76-492">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="f8d76-493">Následující obrázek znázorňuje počáteční `option1` hodnoty načtené `option2` ze souboru *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="f8d76-493">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="f8d76-494">Změňte hodnoty v souboru *appsettings.json* na `value1_from_json UPDATED` a `200`.</span><span class="sxs-lookup"><span data-stu-id="f8d76-494">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="f8d76-495">Uložte soubor *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="f8d76-495">Save the *appsettings.json* file.</span></span> <span data-ttu-id="f8d76-496">Aktualizujte prohlížeč, abyste viděli, že jsou aktualizovány hodnoty možností:</span><span class="sxs-lookup"><span data-stu-id="f8d76-496">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="f8d76-497">Podpora pojmenovaných možností pomocí nástroje IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="f8d76-497">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="f8d76-498">Pojmenované možnosti <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> podpora s je demonstrován jako příklad 6 v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f8d76-498">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="f8d76-499">Podpora pojmenovaných možností umožňuje aplikaci rozlišovat mezi konfiguracemi pojmenovaných možností.</span><span class="sxs-lookup"><span data-stu-id="f8d76-499">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="f8d76-500">V ukázkové aplikaci jsou pojmenované možnosti deklarovány pomocí [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), který volá [> ConfigureNamedOptions\<TOptions. Nakonfigurujte](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) metodu rozšíření.</span><span class="sxs-lookup"><span data-stu-id="f8d76-500">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="f8d76-501">Pojmenované možnosti rozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="f8d76-501">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="f8d76-502">Ukázková aplikace přistupuje k pojmenovaným možnostem <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> pomocí *(Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="f8d76-502">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="f8d76-503">Spuštění ukázkové aplikace, pojmenované možnosti jsou vráceny:</span><span class="sxs-lookup"><span data-stu-id="f8d76-503">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="f8d76-504">`named_options_1`hodnoty jsou k dispozici z konfigurace, které jsou načteny ze souboru *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="f8d76-504">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="f8d76-505">`named_options_2`hodnoty jsou poskytovány:</span><span class="sxs-lookup"><span data-stu-id="f8d76-505">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="f8d76-506">Delegát `named_options_2` v `ConfigureServices` `Option1`pro .</span><span class="sxs-lookup"><span data-stu-id="f8d76-506">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="f8d76-507">Výchozí hodnota `Option2` pro poskytované `MyOptions` třídy.</span><span class="sxs-lookup"><span data-stu-id="f8d76-507">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="f8d76-508">Konfigurace všech možností pomocí metody ConfigureAll</span><span class="sxs-lookup"><span data-stu-id="f8d76-508">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="f8d76-509">Nakonfigurujte všechny <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> instance možností pomocí metody.</span><span class="sxs-lookup"><span data-stu-id="f8d76-509">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="f8d76-510">Následující kód konfiguruje `Option1` pro všechny instance konfigurace se společnou hodnotou.</span><span class="sxs-lookup"><span data-stu-id="f8d76-510">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="f8d76-511">Přidejte do `Startup.ConfigureServices` metody ručně následující kód:</span><span class="sxs-lookup"><span data-stu-id="f8d76-511">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="f8d76-512">Spuštění ukázkové aplikace po přidání kódu vytvoří následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="f8d76-512">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="f8d76-513">Všechny možnosti jsou pojmenované instance.</span><span class="sxs-lookup"><span data-stu-id="f8d76-513">All options are named instances.</span></span> <span data-ttu-id="f8d76-514">Existující <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instance jsou považovány za `Options.DefaultName` cílení instance, která je `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="f8d76-514">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="f8d76-515"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>také provádí <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="f8d76-515"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="f8d76-516">Výchozí implementace má <xref:Microsoft.Extensions.Options.IOptionsFactory%601> logiku použít každý vhodně.</span><span class="sxs-lookup"><span data-stu-id="f8d76-516">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="f8d76-517">Pojmenovaná `null` možnost se používá k cílení na všechny pojmenované<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> instance <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> namísto konkrétní pojmenované instance (a použít tuto konvenci).</span><span class="sxs-lookup"><span data-stu-id="f8d76-517">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="f8d76-518">Rozhraní API pro stavitele možností</span><span class="sxs-lookup"><span data-stu-id="f8d76-518">OptionsBuilder API</span></span>

<span data-ttu-id="f8d76-519"><xref:Microsoft.Extensions.Options.OptionsBuilder%601>slouží ke `TOptions` konfiguraci instancí.</span><span class="sxs-lookup"><span data-stu-id="f8d76-519"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="f8d76-520">`OptionsBuilder`zjednodušuje vytváření pojmenovaných možností, protože se `AddOptions<TOptions>(string optionsName)` jedná pouze o jeden parametr počátečního volání namísto zobrazení ve všech následných voláních.</span><span class="sxs-lookup"><span data-stu-id="f8d76-520">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="f8d76-521">Možnosti ověření `ConfigureOptions` a přetížení, které přijímají závislosti `OptionsBuilder`služby jsou k dispozici pouze prostřednictvím .</span><span class="sxs-lookup"><span data-stu-id="f8d76-521">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="f8d76-522">Konfigurace možností pomocí služeb DI</span><span class="sxs-lookup"><span data-stu-id="f8d76-522">Use DI services to configure options</span></span>

<span data-ttu-id="f8d76-523">Můžete přistupovat k dalším službám z vkládání závislostí při konfiguraci možností dvěma způsoby:</span><span class="sxs-lookup"><span data-stu-id="f8d76-523">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="f8d76-524">Předejte delegáta konfigurace [konfigurovat na](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="f8d76-524">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="f8d76-525">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) poskytuje přetížení [Konfigurovat,](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) které umožňují používat až pět služeb ke konfiguraci možností:</span><span class="sxs-lookup"><span data-stu-id="f8d76-525">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="f8d76-526">Vytvořte vlastní typ, <xref:Microsoft.Extensions.Options.IConfigureOptions%601> <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> který implementuje nebo a zaregistrujte typ jako službu.</span><span class="sxs-lookup"><span data-stu-id="f8d76-526">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="f8d76-527">Doporučujeme předat delegáta konfigurace [konfigurovat](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), protože vytvoření služby je složitější.</span><span class="sxs-lookup"><span data-stu-id="f8d76-527">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="f8d76-528">Vytvoření vlastního typu je ekvivalentní tomu, co pro vás framework dělá při použití [konfigurovat](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="f8d76-528">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="f8d76-529">Volání [Konfigurovat](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registruje přechodný <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>obecný , který má konstruktor, který přijímá zadané obecné typy služeb.</span><span class="sxs-lookup"><span data-stu-id="f8d76-529">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-post-configuration"></a><span data-ttu-id="f8d76-530">Možnosti po konfiguraci</span><span class="sxs-lookup"><span data-stu-id="f8d76-530">Options post-configuration</span></span>

<span data-ttu-id="f8d76-531">Nastavte post-konfiguraci s <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="f8d76-531">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="f8d76-532">Po konfiguraci se <xref:Microsoft.Extensions.Options.IConfigureOptions%601> spustí po všech konfiguracích:</span><span class="sxs-lookup"><span data-stu-id="f8d76-532">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="f8d76-533"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>je k dispozici pro následné konfigurace pojmenovaných možností:</span><span class="sxs-lookup"><span data-stu-id="f8d76-533"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="f8d76-534">Slouží <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> k následné konfiguraci všech instancí konfigurace:</span><span class="sxs-lookup"><span data-stu-id="f8d76-534">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="f8d76-535">Přístup k možnostem při spuštění</span><span class="sxs-lookup"><span data-stu-id="f8d76-535">Accessing options during startup</span></span>

<span data-ttu-id="f8d76-536"><xref:Microsoft.Extensions.Options.IOptions%601>a <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> lze použít `Startup.Configure`v , protože `Configure` služby jsou sestaveny před spuštěním metody.</span><span class="sxs-lookup"><span data-stu-id="f8d76-536"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="f8d76-537">Nepoužívejte <xref:Microsoft.Extensions.Options.IOptions%601> ani <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="f8d76-537">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f8d76-538">Nekonzistentní možnosti stavu může existovat z důvodu řazení registrace služby.</span><span class="sxs-lookup"><span data-stu-id="f8d76-538">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="f8d76-539">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="f8d76-539">Additional resources</span></span>

* <xref:fundamentals/configuration/index>

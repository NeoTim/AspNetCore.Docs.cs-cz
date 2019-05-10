---
title: Vzor možnosti v ASP.NET Core
author: guardrex
description: Objevte, jak použít model možnosti k reprezentování skupiny související nastavení v aplikacích ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 02/26/2019
uid: fundamentals/configuration/options
ms.openlocfilehash: 5cf84cd601fb3559da559d2bd633391841fe8a19
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64901419"
---
# <a name="options-pattern-in-aspnet-core"></a><span data-ttu-id="825d8-103">Vzor možnosti v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="825d8-103">Options pattern in ASP.NET Core</span></span>

<span data-ttu-id="825d8-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="825d8-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="825d8-105">1.1 verzi tohoto tématu, stáhněte si [vzor možnosti v ASP.NET Core (verze 1.1, PDF)](https://webpifeed.blob.core.windows.net/webpifeed/Partners/Options_1.1.pdf).</span><span class="sxs-lookup"><span data-stu-id="825d8-105">For the 1.1 version of this topic, download [Options pattern in ASP.NET Core (version 1.1, PDF)](https://webpifeed.blob.core.windows.net/webpifeed/Partners/Options_1.1.pdf).</span></span>

::: moniker-end

<span data-ttu-id="825d8-106">Možnosti vzor používá k reprezentování skupiny související nastavení třídy.</span><span class="sxs-lookup"><span data-stu-id="825d8-106">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="825d8-107">Když [nastavení konfigurace](xref:fundamentals/configuration/index) jsou izolované aplikace dodržuje podle scénáře do samostatné třídy, dvě důležité softwarového inženýrství zásady:</span><span class="sxs-lookup"><span data-stu-id="825d8-107">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="825d8-108">[Princip oddělení rozhraní (ISP) nebo zapouzdření](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; scénáře (třídy), které závisí na nastavení konfigurace záviset pouze na nastavení konfigurace, které používají.</span><span class="sxs-lookup"><span data-stu-id="825d8-108">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="825d8-109">[Oddělení oblastí zájmu](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; nastavení pro různé části aplikace nejsou závislé nebo propojených mezi sebou.</span><span class="sxs-lookup"><span data-stu-id="825d8-109">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="825d8-110">Možnosti taky mělo poskytovat mechanismus pro ověření konfigurační data.</span><span class="sxs-lookup"><span data-stu-id="825d8-110">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="825d8-111">Další informace najdete v tématu [možnosti ověření](#options-validation) oddílu.</span><span class="sxs-lookup"><span data-stu-id="825d8-111">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="825d8-112">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="825d8-112">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="825d8-113">Požadavky</span><span class="sxs-lookup"><span data-stu-id="825d8-113">Prerequisites</span></span>

<span data-ttu-id="825d8-114">Odkaz [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app) nebo přidat odkaz na balíček [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) balíčku.</span><span class="sxs-lookup"><span data-stu-id="825d8-114">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="825d8-115">Možnosti rozhraní</span><span class="sxs-lookup"><span data-stu-id="825d8-115">Options interfaces</span></span>

<span data-ttu-id="825d8-116"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> slouží k načtení možností a spravovat oznámení možnosti pro `TOptions` instancí.</span><span class="sxs-lookup"><span data-stu-id="825d8-116"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="825d8-117"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> podporuje následující scénáře:</span><span class="sxs-lookup"><span data-stu-id="825d8-117"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="825d8-118">Oznámení o změnách</span><span class="sxs-lookup"><span data-stu-id="825d8-118">Change notifications</span></span>
* [<span data-ttu-id="825d8-119">Pojmenované možnosti</span><span class="sxs-lookup"><span data-stu-id="825d8-119">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="825d8-120">Konfigurace možností</span><span class="sxs-lookup"><span data-stu-id="825d8-120">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="825d8-121">Selektivní možnosti zneplatnění (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="825d8-121">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="825d8-122">[Po konfiguraci](#options-post-configuration) scénářů umožňují nastavit nebo změnit možnosti koneckonců <xref:Microsoft.Extensions.Options.IConfigureOptions%601> konfiguraci dochází.</span><span class="sxs-lookup"><span data-stu-id="825d8-122">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="825d8-123"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> zodpovídá za vytvoření nové instance možností.</span><span class="sxs-lookup"><span data-stu-id="825d8-123"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="825d8-124">Má jediný <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> metody.</span><span class="sxs-lookup"><span data-stu-id="825d8-124">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="825d8-125">Výchozí implementace používá všech registrovaných <xref:Microsoft.Extensions.Options.IConfigureOptions%601> a <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> a spustí všechny konfigurace nejprve, za nímž následuje po konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="825d8-125">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="825d8-126">Rozlišuje mezi <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> a <xref:Microsoft.Extensions.Options.IConfigureOptions%601> a jen volá odpovídající rozhraní.</span><span class="sxs-lookup"><span data-stu-id="825d8-126">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="825d8-127"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> používá <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> do mezipaměti `TOptions` instancí.</span><span class="sxs-lookup"><span data-stu-id="825d8-127"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="825d8-128"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> Zruší platnost instancí možnosti v monitorování, tak, aby přepočítány hodnoty (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="825d8-128">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="825d8-129">Hodnotami může být ručně zavedeno s <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span><span class="sxs-lookup"><span data-stu-id="825d8-129">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="825d8-130"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> Metoda se používá, když všechny pojmenované instance by měl být znovu vytvořit na vyžádání.</span><span class="sxs-lookup"><span data-stu-id="825d8-130">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="825d8-131"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> je užitečné v situacích, kdy by měla být možnosti přepočítány u každého požadavku.</span><span class="sxs-lookup"><span data-stu-id="825d8-131"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="825d8-132">Další informace najdete v tématu [znovu načíst konfigurační data s IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) oddílu.</span><span class="sxs-lookup"><span data-stu-id="825d8-132">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="825d8-133"><xref:Microsoft.Extensions.Options.IOptions%601> slouží k možnosti podpory.</span><span class="sxs-lookup"><span data-stu-id="825d8-133"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="825d8-134">Ale <xref:Microsoft.Extensions.Options.IOptions%601> nepodporuje předchozího scénáře <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="825d8-134">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="825d8-135">Můžete dál používat <xref:Microsoft.Extensions.Options.IOptions%601> stávajících architektur a knihoven, které už používají <xref:Microsoft.Extensions.Options.IOptions%601> rozhraní a nevyžadují scénáře poskytované <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="825d8-135">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="825d8-136">Konfigurace obecných možností</span><span class="sxs-lookup"><span data-stu-id="825d8-136">General options configuration</span></span>

<span data-ttu-id="825d8-137">Konfigurace obecných možností je znázorněn příklad &num;1 v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="825d8-137">General options configuration is demonstrated as Example &num;1 in the sample app.</span></span>

<span data-ttu-id="825d8-138">Třída možností musí být neabstraktní s veřejným konstruktorem bez parametrů.</span><span class="sxs-lookup"><span data-stu-id="825d8-138">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="825d8-139">Následující třídy `MyOptions`, má dvě vlastnosti `Option1` a `Option2`.</span><span class="sxs-lookup"><span data-stu-id="825d8-139">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="825d8-140">Nastavení výchozích hodnot je nepovinný, ale konstruktoru třídy v následujícím příkladu nastaví na výchozí hodnotu `Option1`.</span><span class="sxs-lookup"><span data-stu-id="825d8-140">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="825d8-141">`Option2` má výchozí hodnotu nastavenou vlastnost inicializace přímo (*Models/MyOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="825d8-141">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="825d8-142">`MyOptions` Třídy se přidá do kontejneru služby s <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> a vázán ke konfiguraci:</span><span class="sxs-lookup"><span data-stu-id="825d8-142">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="825d8-143">Na následující stránce používá model [injektáž závislostí konstruktor](xref:mvc/controllers/dependency-injection) s <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> pro přístup k nastavení (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="825d8-143">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="825d8-144">Ukázkové *appsettings.json* souboru určuje hodnoty pro `option1` a `option2`:</span><span class="sxs-lookup"><span data-stu-id="825d8-144">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="825d8-145">Při spuštění aplikace, model stránky `OnGet` metoda vrátí řetězec zobrazující možnost hodnoty třídy:</span><span class="sxs-lookup"><span data-stu-id="825d8-145">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="825d8-146">Při použití vlastního <xref:System.Configuration.ConfigurationBuilder> Pokud chcete načíst ze souboru nastavení konfigurace možností, potvrďte, že je správně nastavena základní cesta:</span><span class="sxs-lookup"><span data-stu-id="825d8-146">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="825d8-147">Explicitním nastavením základní cesta není nutné při načítání konfigurace možností z nastavení souboru prostřednictvím <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="825d8-147">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="825d8-148">Konfigurace jednoduchých možností s delegáta</span><span class="sxs-lookup"><span data-stu-id="825d8-148">Configure simple options with a delegate</span></span>

<span data-ttu-id="825d8-149">Konfigurace jednoduchých možností s delegáta je znázorněn příklad &num;2 v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="825d8-149">Configuring simple options with a delegate is demonstrated as Example &num;2 in the sample app.</span></span>

<span data-ttu-id="825d8-150">Použití delegáta k nastavení hodnot možností.</span><span class="sxs-lookup"><span data-stu-id="825d8-150">Use a delegate to set options values.</span></span> <span data-ttu-id="825d8-151">Tato ukázková aplikace používá `MyOptionsWithDelegateConfig` třídy (*Models/MyOptionsWithDelegateConfig.cs*):</span><span class="sxs-lookup"><span data-stu-id="825d8-151">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="825d8-152">V následujícím kódu druhý <xref:Microsoft.Extensions.Options.IConfigureOptions%601> služby se přidá do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="825d8-152">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="825d8-153">Delegát používá ke konfiguraci vazby s `MyOptionsWithDelegateConfig`:</span><span class="sxs-lookup"><span data-stu-id="825d8-153">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="825d8-154">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="825d8-154">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="825d8-155">Můžete přidat několik poskytovatelů konfigurace.</span><span class="sxs-lookup"><span data-stu-id="825d8-155">You can add multiple configuration providers.</span></span> <span data-ttu-id="825d8-156">Poskytovatelé konfigurace jsou k dispozici z balíčků NuGet a jsou použita popořadě, že jejich registrace.</span><span class="sxs-lookup"><span data-stu-id="825d8-156">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="825d8-157">Další informace naleznete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="825d8-157">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="825d8-158">Každé volání <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> přidá <xref:Microsoft.Extensions.Options.IConfigureOptions%601> služby service container.</span><span class="sxs-lookup"><span data-stu-id="825d8-158">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="825d8-159">V předchozím příkladu hodnoty `Option1` a `Option2` jsou určené v *appsettings.json*, ale hodnoty `Option1` a `Option2` jsou přepsány nakonfigurované delegáta.</span><span class="sxs-lookup"><span data-stu-id="825d8-159">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="825d8-160">Pokud je povoleno více než jedna služba konfigurace, poslední zdroj konfigurace zadaná *wins* a nastaví hodnotu konfigurace.</span><span class="sxs-lookup"><span data-stu-id="825d8-160">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="825d8-161">Při spuštění aplikace, model stránky `OnGet` metoda vrátí řetězec zobrazující možnost hodnoty třídy:</span><span class="sxs-lookup"><span data-stu-id="825d8-161">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="825d8-162">Konfigurace suboptions</span><span class="sxs-lookup"><span data-stu-id="825d8-162">Suboptions configuration</span></span>

<span data-ttu-id="825d8-163">Konfigurace suboptions je znázorněn příklad &num;3 v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="825d8-163">Suboptions configuration is demonstrated as Example &num;3 in the sample app.</span></span>

<span data-ttu-id="825d8-164">Aplikace by měl vytvořit třídy možnosti, které se vztahují na konkrétní scénář skupiny (třídy) v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="825d8-164">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="825d8-165">Části aplikace, které vyžadují konfigurační hodnoty by měl mít přístup pouze na hodnoty konfigurace, které používají.</span><span class="sxs-lookup"><span data-stu-id="825d8-165">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="825d8-166">Při vytváření vazby možnosti konfigurace, každou vlastnost v typu možnosti je vázán na konfigurační klíč ve formátu `property[:sub-property:]`.</span><span class="sxs-lookup"><span data-stu-id="825d8-166">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="825d8-167">Například `MyOptions.Option1` vlastnost je vázána na klíč `Option1`, který je pro čtení z `option1` vlastnost v *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="825d8-167">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="825d8-168">V následujícím kódu, třetí <xref:Microsoft.Extensions.Options.IConfigureOptions%601> služby se přidá do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="825d8-168">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="825d8-169">Vytvoří vazbu mezi `MySubOptions` do části `subsection` z *appsettings.json* souboru:</span><span class="sxs-lookup"><span data-stu-id="825d8-169">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="825d8-170">`GetSection` – Metoda rozšíření vyžaduje [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="825d8-170">The `GetSection` extension method requires the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) NuGet package.</span></span> <span data-ttu-id="825d8-171">Pokud aplikace využívá [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 nebo novější), balíček je automaticky přidána.</span><span class="sxs-lookup"><span data-stu-id="825d8-171">If the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later), the package is automatically included.</span></span>

<span data-ttu-id="825d8-172">Ukázkové *appsettings.json* soubor definuje `subsection` člena s klíči pro `suboption1` a `suboption2`:</span><span class="sxs-lookup"><span data-stu-id="825d8-172">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="825d8-173">`MySubOptions` Třídy definuje vlastnosti, `SubOption1` a `SubOption2`, pro uchování hodnoty možnosti (*Models/MySubOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="825d8-173">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="825d8-174">Model stránky `OnGet` metoda vrátí řetězec s hodnotami možnosti (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="825d8-174">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="825d8-175">Při spuštění aplikace `OnGet` metoda vrátí řetězec zobrazující přepínači hodnoty třídy:</span><span class="sxs-lookup"><span data-stu-id="825d8-175">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="825d8-176">Možnosti zobrazení modelu nebo pomocí vkládání přímé zobrazení</span><span class="sxs-lookup"><span data-stu-id="825d8-176">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="825d8-177">Možnosti zobrazení modelu nebo s přístupem zobrazení vkládání je znázorněn příklad &num;4 v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="825d8-177">Options provided by a view model or with direct view injection is demonstrated as Example &num;4 in the sample app.</span></span>

<span data-ttu-id="825d8-178">Možnosti může být zadán v zobrazení modelu nebo vložením <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> přímo do zobrazení (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="825d8-178">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="825d8-179">Ukázková aplikace ukazuje, jak vložit `IOptionsMonitor<MyOptions>` s `@inject` – direktiva:</span><span class="sxs-lookup"><span data-stu-id="825d8-179">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="825d8-180">Při spuštění aplikace se hodnoty možnosti jsou zobrazeny na vykreslené stránce:</span><span class="sxs-lookup"><span data-stu-id="825d8-180">When the app is run, the options values are shown in the rendered page:</span></span>

![Možnosti hodnot možnost1: value1_from_json a možnost2: -1 se načítají z modelu a vkládání do zobrazení.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="825d8-182">Znovu načíst konfigurační data s IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="825d8-182">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="825d8-183">Probíhá opětovné načtení dat konfigurace pomocí <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> je ukázáno v příkladu &num;5 v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="825d8-183">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example &num;5 in the sample app.</span></span>

<span data-ttu-id="825d8-184"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> podporuje možnosti s minimální režie zpracování znovu načíst.</span><span class="sxs-lookup"><span data-stu-id="825d8-184"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="825d8-185">Možnosti jsou vypočten jednou každý požadavek při přistupovat a uložili do mezipaměti po dobu platnosti požadavku.</span><span class="sxs-lookup"><span data-stu-id="825d8-185">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="825d8-186">Následující příklad ukazuje, jak nové <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> vytvořené po *appsettings.json* změny (*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="825d8-186">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="825d8-187">Více požadavků na server vrátit konstantní hodnoty podle *appsettings.json* souborů, dokud je soubor změněn a konfigurace znovu načte.</span><span class="sxs-lookup"><span data-stu-id="825d8-187">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="825d8-188">Následující obrázek ukazuje úvodní `option1` a `option2` hodnoty načtené z *appsettings.json* souboru:</span><span class="sxs-lookup"><span data-stu-id="825d8-188">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="825d8-189">Změňte hodnoty *appsettings.json* do souboru `value1_from_json UPDATED` a `200`.</span><span class="sxs-lookup"><span data-stu-id="825d8-189">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="825d8-190">Uložit *appsettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="825d8-190">Save the *appsettings.json* file.</span></span> <span data-ttu-id="825d8-191">Aktualizujte prohlížeč, pokud chcete zobrazit, že jsou aktualizované hodnoty možnosti:</span><span class="sxs-lookup"><span data-stu-id="825d8-191">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="825d8-192">Možnosti podpory s IConfigureNamedOptions s názvem</span><span class="sxs-lookup"><span data-stu-id="825d8-192">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="825d8-193">Možnosti podpory s názvem <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> je znázorněn příklad &num;6 v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="825d8-193">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example &num;6 in the sample app.</span></span>

<span data-ttu-id="825d8-194">*S názvem možnosti* podpora umožňuje, aby aplikace k rozlišení mezi pojmenované možnosti konfigurace.</span><span class="sxs-lookup"><span data-stu-id="825d8-194">*Named options* support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="825d8-195">V ukázkové aplikaci s názvem možnosti jsou deklarovány pomocí [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), který volá [ConfigureNamedOptions\<TOptions >. Konfigurace](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) – metoda rozšíření:</span><span class="sxs-lookup"><span data-stu-id="825d8-195">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="825d8-196">Ukázková aplikace přistupuje k pojmenované možnosti s <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="825d8-196">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="825d8-197">Spuštěním ukázkové aplikace, jsou vráceny pojmenované možnosti:</span><span class="sxs-lookup"><span data-stu-id="825d8-197">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="825d8-198">`named_options_1` hodnoty jsou k dispozici z konfigurace, které jsou načteny z *appsettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="825d8-198">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="825d8-199">`named_options_2` hodnoty jsou k dispozici podle:</span><span class="sxs-lookup"><span data-stu-id="825d8-199">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="825d8-200">`named_options_2` Delegování v `ConfigureServices` pro `Option1`.</span><span class="sxs-lookup"><span data-stu-id="825d8-200">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="825d8-201">Výchozí hodnota pro `Option2` poskytované `MyOptions` třídy.</span><span class="sxs-lookup"><span data-stu-id="825d8-201">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="825d8-202">Všechny možnosti nakonfigurovat ConfigureAll – metoda</span><span class="sxs-lookup"><span data-stu-id="825d8-202">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="825d8-203">Nakonfigurujte všechny možnosti instance s <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> metody.</span><span class="sxs-lookup"><span data-stu-id="825d8-203">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="825d8-204">Následující kód konfiguruje `Option1` pro všechny konfigurace instance s hodnotou běžné.</span><span class="sxs-lookup"><span data-stu-id="825d8-204">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="825d8-205">Přidejte následující kód do ručně `Startup.ConfigureServices` metody:</span><span class="sxs-lookup"><span data-stu-id="825d8-205">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="825d8-206">Spuštěním ukázkové aplikace po přidání kódu vytvoří následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="825d8-206">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="825d8-207">Všechny možnosti jsou pojmenované instance.</span><span class="sxs-lookup"><span data-stu-id="825d8-207">All options are named instances.</span></span> <span data-ttu-id="825d8-208">Existující <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instancí jsou považovány za cílení `Options.DefaultName` instanci, která je `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="825d8-208">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="825d8-209"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> také implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="825d8-209"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="825d8-210">Výchozí implementace <xref:Microsoft.Extensions.Options.IOptionsFactory%601> obsahuje logiku pro každý odpovídajícím způsobem používat.</span><span class="sxs-lookup"><span data-stu-id="825d8-210">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="825d8-211">`null` Pojmenované možnost se používá cílit na všechny pojmenované instance místo konkrétní pojmenovanou instanci (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> a <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> Tato konvence).</span><span class="sxs-lookup"><span data-stu-id="825d8-211">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="825d8-212">OptionsBuilder API</span><span class="sxs-lookup"><span data-stu-id="825d8-212">OptionsBuilder API</span></span>

<span data-ttu-id="825d8-213"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> slouží ke konfiguraci `TOptions` instancí.</span><span class="sxs-lookup"><span data-stu-id="825d8-213"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="825d8-214">`OptionsBuilder` zjednodušuje vytváření, s názvem možnosti, jako je pouze jeden parametr do původní `AddOptions<TOptions>(string optionsName)` volat bez povolí, všechny následné volání.</span><span class="sxs-lookup"><span data-stu-id="825d8-214">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="825d8-215">Možnosti ověřování a `ConfigureOptions` přetížení, které přijímají závislostí služby jsou dostupné jen přes `OptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="825d8-215">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="825d8-216">Slouží ke konfiguraci možností služeb DI</span><span class="sxs-lookup"><span data-stu-id="825d8-216">Use DI services to configure options</span></span>

<span data-ttu-id="825d8-217">Přístupu k jiným službám od vkládání závislostí při konfiguraci možnosti dvěma způsoby:</span><span class="sxs-lookup"><span data-stu-id="825d8-217">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="825d8-218">Předání delegáta konfigurace k [konfigurovat](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) na [OptionsBuilder\<TOptions >](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="825d8-218">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="825d8-219">[OptionsBuilder\<TOptions >](xref:Microsoft.Extensions.Options.OptionsBuilder`1) poskytuje přetížení [konfigurovat](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) , které umožňují používat až pět služby můžete nakonfigurovat možnosti:</span><span class="sxs-lookup"><span data-stu-id="825d8-219">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="825d8-220">Vytvořit vlastní typ, který implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> nebo <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> a zaregistrujte se jako služba typu.</span><span class="sxs-lookup"><span data-stu-id="825d8-220">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="825d8-221">Doporučujeme, abyste předání konfigurace delegáta, kterého [konfigurovat](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), od vytvoření služby je složitější.</span><span class="sxs-lookup"><span data-stu-id="825d8-221">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="825d8-222">Vytváří se vlastní typ je ekvivalentní k co rozhraní udělá za vás při použití [konfigurovat](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="825d8-222">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="825d8-223">Volání [konfigurovat](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) zaregistruje přechodné obecný <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, které má konstruktor, který přijímá typy obecné služby určené.</span><span class="sxs-lookup"><span data-stu-id="825d8-223">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

::: moniker range=">= aspnetcore-2.2"

## <a name="options-validation"></a><span data-ttu-id="825d8-224">Možnosti ověřování</span><span class="sxs-lookup"><span data-stu-id="825d8-224">Options validation</span></span>

<span data-ttu-id="825d8-225">Možnosti ověřování umožňuje ověřit možnosti při nakonfigurování možností.</span><span class="sxs-lookup"><span data-stu-id="825d8-225">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="825d8-226">Volání `Validate` s metodu ověřování, která vrátí `true` Pokud možnosti jsou platné a `false` Pokud nejsou platné:</span><span class="sxs-lookup"><span data-stu-id="825d8-226">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

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

<span data-ttu-id="825d8-227">V předchozím příkladu nastaví možnosti pojmenovanou instanci na `optionalOptionsName`.</span><span class="sxs-lookup"><span data-stu-id="825d8-227">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="825d8-228">Výchozí možnosti instance je `Options.DefaultName`.</span><span class="sxs-lookup"><span data-stu-id="825d8-228">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="825d8-229">Ověření se spustí, jakmile se vytvoří instance možností.</span><span class="sxs-lookup"><span data-stu-id="825d8-229">Validation runs when the options instance is created.</span></span> <span data-ttu-id="825d8-230">Je zaručeno, že vaše instance možnosti předat čas ověření první, který je přístupný.</span><span class="sxs-lookup"><span data-stu-id="825d8-230">Your options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="825d8-231">Možnosti ověřování nepodporuje ochranu proti možnosti úprav po možnosti počáteční konfiguraci a ověřit.</span><span class="sxs-lookup"><span data-stu-id="825d8-231">Options validation doesn't guard against options modifications after the options are initially configured and validated.</span></span>

<span data-ttu-id="825d8-232">`Validate` Metoda přijímá `Func<TOptions, bool>`.</span><span class="sxs-lookup"><span data-stu-id="825d8-232">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="825d8-233">Chcete-li plně přizpůsobit ověřování, implementovat `IValidateOptions<TOptions>`, která umožňuje:</span><span class="sxs-lookup"><span data-stu-id="825d8-233">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="825d8-234">Ověření více typů možnosti: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="825d8-234">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="825d8-235">Ověření, který závisí na jiný typ možnosti: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="825d8-235">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="825d8-236">`IValidateOptions` ověří:</span><span class="sxs-lookup"><span data-stu-id="825d8-236">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="825d8-237">Konkrétní pojmenované instance možností.</span><span class="sxs-lookup"><span data-stu-id="825d8-237">A specific named options instance.</span></span>
* <span data-ttu-id="825d8-238">Všechny možnosti `name` je `null`.</span><span class="sxs-lookup"><span data-stu-id="825d8-238">All options when `name` is `null`.</span></span>

<span data-ttu-id="825d8-239">Vrátit `ValidateOptionsResult` od implementace rozhraní:</span><span class="sxs-lookup"><span data-stu-id="825d8-239">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="825d8-240">Data ověřování na základě poznámek je k dispozici [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) balíčku voláním `ValidateDataAnnotations` metodu na `OptionsBuilder<TOptions>`.</span><span class="sxs-lookup"><span data-stu-id="825d8-240">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the `ValidateDataAnnotations` method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="825d8-241">`Microsoft.Extensions.Options.DataAnnotations` je součástí [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app) (ASP.NET Core 2.2 nebo vyšší).</span><span class="sxs-lookup"><span data-stu-id="825d8-241">`Microsoft.Extensions.Options.DataAnnotations` is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.2 or later).</span></span>

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

<span data-ttu-id="825d8-242">V úvahu pro budoucí verzi se nemůžou dočkat, až ověřování (selhání rychle při spuštění).</span><span class="sxs-lookup"><span data-stu-id="825d8-242">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

::: moniker-end

## <a name="options-post-configuration"></a><span data-ttu-id="825d8-243">Po konfiguraci možností</span><span class="sxs-lookup"><span data-stu-id="825d8-243">Options post-configuration</span></span>

<span data-ttu-id="825d8-244">Po nastavení s <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="825d8-244">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="825d8-245">Po konfiguraci spuštění po všech <xref:Microsoft.Extensions.Options.IConfigureOptions%601> vyvolá konfigurace:</span><span class="sxs-lookup"><span data-stu-id="825d8-245">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="825d8-246"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> je k dispozici po konfigurace s názvem možností:</span><span class="sxs-lookup"><span data-stu-id="825d8-246"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="825d8-247">Použití <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> po konfiguraci všechny instance konfigurace:</span><span class="sxs-lookup"><span data-stu-id="825d8-247">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="825d8-248">Přístup k možnosti při spuštění</span><span class="sxs-lookup"><span data-stu-id="825d8-248">Accessing options during startup</span></span>

<span data-ttu-id="825d8-249"><xref:Microsoft.Extensions.Options.IOptions%601> a <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> lze použít v `Startup.Configure`, protože služby jsou sestaveny dříve, než `Configure` metody.</span><span class="sxs-lookup"><span data-stu-id="825d8-249"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="825d8-250">Nepoužívejte <xref:Microsoft.Extensions.Options.IOptions%601> nebo <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="825d8-250">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="825d8-251">Příčinou je pořadí registrace služby můžou existovat nekonzistentní možnosti dostane do stavu.</span><span class="sxs-lookup"><span data-stu-id="825d8-251">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="825d8-252">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="825d8-252">Additional resources</span></span>

* <xref:fundamentals/configuration/index>

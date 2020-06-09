---
title: Vzor možností v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak pomocí vzoru možností znázornit skupiny souvisejících nastavení v aplikacích ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/20/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/configuration/options
ms.openlocfilehash: 9a9febba060cca591f2cbcdc03cb4c35edcfdda7
ms.sourcegitcommit: 74d80a36103fdbd54baba0118535a4647f511913
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2020
ms.locfileid: "84529660"
---
# <a name="options-pattern-in-aspnet-core"></a><span data-ttu-id="84c59-103">Vzor možností v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="84c59-103">Options pattern in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="84c59-104">Od [Kirka Larkin](https://twitter.com/serpent5) a [Rick Anderson](https://twitter.com/RickAndMSFT).</span><span class="sxs-lookup"><span data-stu-id="84c59-104">By [Kirk Larkin](https://twitter.com/serpent5) and [Rick Anderson](https://twitter.com/RickAndMSFT).</span></span>

<span data-ttu-id="84c59-105">Vzor možností používá třídy k poskytnutí přístupu silného typu ke skupinám souvisejících nastavení.</span><span class="sxs-lookup"><span data-stu-id="84c59-105">The options pattern uses classes to provide strongly typed access to groups of related settings.</span></span> <span data-ttu-id="84c59-106">Když jsou [nastavení konfigurace](xref:fundamentals/configuration/index) izolována scénářem do samostatných tříd, aplikace dodržuje dva důležité principy technického řízení:</span><span class="sxs-lookup"><span data-stu-id="84c59-106">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="84c59-107">[Princip odděleníní rozhraní (ISP) nebo zapouzdření](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): scénáře (třídy) závislé na nastavení konfigurace závisejí pouze na nastavení konfigurace, která používají.</span><span class="sxs-lookup"><span data-stu-id="84c59-107">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="84c59-108">[Oddělení obav](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): nastavení různých částí aplikace nejsou závislá ani vzájemně závislá na sobě.</span><span class="sxs-lookup"><span data-stu-id="84c59-108">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="84c59-109">Možnosti také poskytují mechanismus pro ověření konfiguračních dat.</span><span class="sxs-lookup"><span data-stu-id="84c59-109">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="84c59-110">Další informace najdete v části [ověřování možností](#options-validation) .</span><span class="sxs-lookup"><span data-stu-id="84c59-110">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="84c59-111">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="84c59-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="optpat"></a>

## <a name="bind-hierarchical-configuration"></a><span data-ttu-id="84c59-112">Vytvoření vazby hierarchické konfigurace</span><span class="sxs-lookup"><span data-stu-id="84c59-112">Bind hierarchical configuration</span></span>

[!INCLUDE[](~/includes/bind.md)]

<a name="oi"></a>

## <a name="options-interfaces"></a><span data-ttu-id="84c59-113">Možnosti rozhraní</span><span class="sxs-lookup"><span data-stu-id="84c59-113">Options interfaces</span></span>

<span data-ttu-id="84c59-114"><xref:Microsoft.Extensions.Options.IOptions%601>:</span><span class="sxs-lookup"><span data-stu-id="84c59-114"><xref:Microsoft.Extensions.Options.IOptions%601>:</span></span>

* <span data-ttu-id="84c59-115">Nepodporuje ***:***</span><span class="sxs-lookup"><span data-stu-id="84c59-115">Does ***not*** support:</span></span>
  * <span data-ttu-id="84c59-116">Čtení konfiguračních dat po spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="84c59-116">Reading of configuration data after the app has started.</span></span>
  * [<span data-ttu-id="84c59-117">Pojmenované možnosti</span><span class="sxs-lookup"><span data-stu-id="84c59-117">Named options</span></span>](#named)
* <span data-ttu-id="84c59-118">Je zaregistrován jako typ [singleton](xref:fundamentals/dependency-injection#singleton) a může být vložen do jakékoli [životnosti služby](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="84c59-118">Is registered as a [Singleton](xref:fundamentals/dependency-injection#singleton) and can be injected into any [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="84c59-119"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>:</span><span class="sxs-lookup"><span data-stu-id="84c59-119"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>:</span></span>

* <span data-ttu-id="84c59-120">Je užitečná ve scénářích, kde by se měly přepočítat možnosti pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="84c59-120">Is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="84c59-121">Další informace najdete v tématu [použití IOptionsSnapshot ke čtení aktualizovaných dat](#ios).</span><span class="sxs-lookup"><span data-stu-id="84c59-121">For more information, see [Use IOptionsSnapshot to read updated data](#ios).</span></span>
* <span data-ttu-id="84c59-122">Je zaregistrován jako [obor](xref:fundamentals/dependency-injection#scoped) , a proto nemůže být vložen do služby typu singleton.</span><span class="sxs-lookup"><span data-stu-id="84c59-122">Is registered as [Scoped](xref:fundamentals/dependency-injection#scoped) and therefore cannot be injected into a Singleton service.</span></span>
* <span data-ttu-id="84c59-123">Podporuje [pojmenované možnosti](#named)</span><span class="sxs-lookup"><span data-stu-id="84c59-123">Supports [named options](#named)</span></span>

<span data-ttu-id="84c59-124"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span><span class="sxs-lookup"><span data-stu-id="84c59-124"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span></span>

* <span data-ttu-id="84c59-125">Slouží k načtení možností a správě oznámení možností pro `TOptions` instance.</span><span class="sxs-lookup"><span data-stu-id="84c59-125">Is used to retrieve options and manage options notifications for `TOptions` instances.</span></span>
* <span data-ttu-id="84c59-126">Je zaregistrován jako typ [singleton](xref:fundamentals/dependency-injection#singleton) a může být vložen do jakékoli [životnosti služby](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="84c59-126">Is registered as a [Singleton](xref:fundamentals/dependency-injection#singleton) and can be injected into any [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>
* <span data-ttu-id="84c59-127">Podporovaných</span><span class="sxs-lookup"><span data-stu-id="84c59-127">Supports:</span></span>
  * <span data-ttu-id="84c59-128">Oznámení změny</span><span class="sxs-lookup"><span data-stu-id="84c59-128">Change notifications</span></span>
  * [<span data-ttu-id="84c59-129">Pojmenované možnosti</span><span class="sxs-lookup"><span data-stu-id="84c59-129">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
  * [<span data-ttu-id="84c59-130">Znovu Načítatelné konfigurace</span><span class="sxs-lookup"><span data-stu-id="84c59-130">Reloadable configuration</span></span>](#ios)
  * <span data-ttu-id="84c59-131">Neplatných selektivních možností ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> )</span><span class="sxs-lookup"><span data-stu-id="84c59-131">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>
  
<span data-ttu-id="84c59-132">Scénáře po [konfiguraci](#options-post-configuration) umožňují nastavení nebo změnu možností po všech <xref:Microsoft.Extensions.Options.IConfigureOptions%601> konfiguracích.</span><span class="sxs-lookup"><span data-stu-id="84c59-132">[Post-configuration](#options-post-configuration) scenarios enable setting or changing options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="84c59-133"><xref:Microsoft.Extensions.Options.IOptionsFactory%601>zodpovídá za vytváření nových instancí možností.</span><span class="sxs-lookup"><span data-stu-id="84c59-133"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="84c59-134">Má jedinou <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> metodu.</span><span class="sxs-lookup"><span data-stu-id="84c59-134">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="84c59-135">Výchozí implementace přebírá všechny <xref:Microsoft.Extensions.Options.IConfigureOptions%601> Konfigurace a <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> a spustí nejprve všechny konfigurace, následované po konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="84c59-135">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="84c59-136">Rozlišuje mezi <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> a <xref:Microsoft.Extensions.Options.IConfigureOptions%601> a volá pouze příslušné rozhraní.</span><span class="sxs-lookup"><span data-stu-id="84c59-136">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="84c59-137"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>je používána <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> pro ukládání instancí do mezipaměti `TOptions` .</span><span class="sxs-lookup"><span data-stu-id="84c59-137"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="84c59-138"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>Zruší platnost instancí možností v monitorování tak, aby se hodnota přepočítala ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*> ).</span><span class="sxs-lookup"><span data-stu-id="84c59-138">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="84c59-139">Hodnoty lze ručně zavést pomocí <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> .</span><span class="sxs-lookup"><span data-stu-id="84c59-139">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="84c59-140"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*>Metoda se používá v případě, že by měly být všechny pojmenované instance znovu vytvořeny na vyžádání.</span><span class="sxs-lookup"><span data-stu-id="84c59-140">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<a name="ios"></a>

## <a name="use-ioptionssnapshot-to-read-updated-data"></a><span data-ttu-id="84c59-141">Použití IOptionsSnapshot ke čtení aktualizovaných dat</span><span class="sxs-lookup"><span data-stu-id="84c59-141">Use IOptionsSnapshot to read updated data</span></span>

<span data-ttu-id="84c59-142"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>Při použití se možnosti vypočítávají jednou za požadavek při otevření a ukládání do mezipaměti po dobu života žádosti.</span><span class="sxs-lookup"><span data-stu-id="84c59-142">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span> <span data-ttu-id="84c59-143">Změny konfigurace se čtou po spuštění aplikace při použití zprostředkovatelů konfigurace, které podporují čtení aktualizovaných hodnot konfigurace.</span><span class="sxs-lookup"><span data-stu-id="84c59-143">Changes to the configuration are read after the app starts when using configuration providers that support reading updated configuration values.</span></span>

<span data-ttu-id="84c59-144">Rozdíl mezi `IOptionsMonitor` a `IOptionsSnapshot` je to, že:</span><span class="sxs-lookup"><span data-stu-id="84c59-144">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="84c59-145">`IOptionsMonitor`je [Služba typu Singleton](xref:fundamentals/dependency-injection#singleton) , která načte aktuální hodnoty možností kdykoli, což je zvláště užitečné v závislostech singleton.</span><span class="sxs-lookup"><span data-stu-id="84c59-145">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="84c59-146">`IOptionsSnapshot`je [Oborová služba](xref:fundamentals/dependency-injection#scoped) a poskytuje snímek možností v době, kdy `IOptionsSnapshot<T>` je objekt vytvořen.</span><span class="sxs-lookup"><span data-stu-id="84c59-146">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="84c59-147">Snímky možností jsou navržené pro použití s přechodnými a vymezenými závislostmi.</span><span class="sxs-lookup"><span data-stu-id="84c59-147">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="84c59-148">Následující kód používá <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> .</span><span class="sxs-lookup"><span data-stu-id="84c59-148">The following code uses <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>.</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestSnap.cshtml.cs?name=snippet)]

<span data-ttu-id="84c59-149">Následující kód zaregistruje instanci konfigurace, která se `MyOptions` váže k:</span><span class="sxs-lookup"><span data-stu-id="84c59-149">The following code registers a configuration instance which `MyOptions` binds against:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="84c59-150">V předchozím kódu se po čtení aplikace změny konfiguračního souboru JSON po jeho spuštění.</span><span class="sxs-lookup"><span data-stu-id="84c59-150">In the preceding code, changes to the JSON configuration file after the app has started are read.</span></span>

## <a name="ioptionsmonitor"></a><span data-ttu-id="84c59-151">IOptionsMonitor</span><span class="sxs-lookup"><span data-stu-id="84c59-151">IOptionsMonitor</span></span>

<span data-ttu-id="84c59-152">Následující kód zaregistruje instanci konfigurace, která se `MyOptions` váže k.</span><span class="sxs-lookup"><span data-stu-id="84c59-152">The following code registers a configuration instance which `MyOptions` binds against.</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="84c59-153">Následující příklad používá <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> :</span><span class="sxs-lookup"><span data-stu-id="84c59-153">The following example uses <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestMonitor.cshtml.cs?name=snippet)]

<span data-ttu-id="84c59-154">V předchozím kódu se ve výchozím nastavení změny konfiguračního souboru JSON po spuštění aplikace čtou.</span><span class="sxs-lookup"><span data-stu-id="84c59-154">In the preceding code, by default, changes to the JSON configuration file after the app has started are read.</span></span>

<a name="named"></a>

## <a name="named-options-support-using-iconfigurenamedoptions"></a><span data-ttu-id="84c59-155">Podpora pojmenovaných možností pomocí IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="84c59-155">Named options support using IConfigureNamedOptions</span></span>

<span data-ttu-id="84c59-156">Pojmenované možnosti:</span><span class="sxs-lookup"><span data-stu-id="84c59-156">Named options:</span></span>

* <span data-ttu-id="84c59-157">Jsou užitečné v případě, že je více konfiguračních oddílů svázán se stejnými vlastnostmi.</span><span class="sxs-lookup"><span data-stu-id="84c59-157">Are useful when multiple configuration sections bind to the same properties.</span></span>
* <span data-ttu-id="84c59-158">Rozlišují velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="84c59-158">Are case sensitive.</span></span>

<span data-ttu-id="84c59-159">Vezměte v úvahu následující soubor *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="84c59-159">Consider the following *appsettings.json* file:</span></span>

[!code-json[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/appsettings.NO.json)]

<span data-ttu-id="84c59-160">Namísto vytváření dvou tříd pro vytvoření vazby `TopItem:Month` a `TopItem:Year` pro každou část se používá následující třída:</span><span class="sxs-lookup"><span data-stu-id="84c59-160">Rather than creating two classes to bind `TopItem:Month` and `TopItem:Year`, the following class is used for each section:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Models/TopItemSettings.cs)]

<span data-ttu-id="84c59-161">Následující kód konfiguruje pojmenované možnosti:</span><span class="sxs-lookup"><span data-stu-id="84c59-161">The following code configures the named options:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/StartupNO.cs?name=snippet_Example2)]

<span data-ttu-id="84c59-162">Následující kód zobrazí pojmenované možnosti:</span><span class="sxs-lookup"><span data-stu-id="84c59-162">The following code displays the named options:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestNO.cshtml.cs?name=snippet)]

<span data-ttu-id="84c59-163">Všechny možnosti jsou pojmenované instance.</span><span class="sxs-lookup"><span data-stu-id="84c59-163">All options are named instances.</span></span> <span data-ttu-id="84c59-164"><xref:Microsoft.Extensions.Options.IConfigureOptions%601>instance jsou považovány za cílení `Options.DefaultName` instance, což je `string.Empty` .</span><span class="sxs-lookup"><span data-stu-id="84c59-164"><xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="84c59-165"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>také implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> .</span><span class="sxs-lookup"><span data-stu-id="84c59-165"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="84c59-166">Výchozí implementace pro <xref:Microsoft.Extensions.Options.IOptionsFactory%601> má logiku odpovídajícím způsobem použít.</span><span class="sxs-lookup"><span data-stu-id="84c59-166">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="84c59-167">`null`Pojmenovaná možnost se používá pro cílení na všechny pojmenované instance místo konkrétní pojmenované instance.</span><span class="sxs-lookup"><span data-stu-id="84c59-167">The `null` named option is used to target all of the named instances instead of a specific named instance.</span></span> <span data-ttu-id="84c59-168"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*>a <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> použijte tuto konvenci.</span><span class="sxs-lookup"><span data-stu-id="84c59-168"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention.</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="84c59-169">Rozhraní API pro OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="84c59-169">OptionsBuilder API</span></span>

<span data-ttu-id="84c59-170"><xref:Microsoft.Extensions.Options.OptionsBuilder%601>se používá ke konfiguraci `TOptions` instancí.</span><span class="sxs-lookup"><span data-stu-id="84c59-170"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="84c59-171">`OptionsBuilder`zjednodušuje vytváření pojmenovaných možností, protože je jenom jedním parametrem počátečního `AddOptions<TOptions>(string optionsName)` volání, takže se neobjeví ve všech dalších voláních.</span><span class="sxs-lookup"><span data-stu-id="84c59-171">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="84c59-172">Ověřování možností a `ConfigureOptions` přetížení, která přijímají závislosti služby, jsou k dispozici pouze prostřednictvím `OptionsBuilder` .</span><span class="sxs-lookup"><span data-stu-id="84c59-172">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

<span data-ttu-id="84c59-173">`OptionsBuilder`se používá v sekci [ověřování možností](#val) .</span><span class="sxs-lookup"><span data-stu-id="84c59-173">`OptionsBuilder` is used in the [Options validation](#val) section.</span></span>

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="84c59-174">Ke konfiguraci možností použijte DI Services.</span><span class="sxs-lookup"><span data-stu-id="84c59-174">Use DI services to configure options</span></span>

<span data-ttu-id="84c59-175">Služby jsou dostupné ze injektáže závislosti při konfiguraci možností dvěma způsoby:</span><span class="sxs-lookup"><span data-stu-id="84c59-175">Services can be accessed from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="84c59-176">Předejte delegáta konfigurace [na](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) [OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="84c59-176">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="84c59-177">`OptionsBuilder<TOptions>`poskytuje přetížení [Konfigurace](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) , která umožňují použití až pěti služeb ke konfiguraci možností:</span><span class="sxs-lookup"><span data-stu-id="84c59-177">`OptionsBuilder<TOptions>` provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow use of up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="84c59-178">Vytvořte typ, který implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> nebo <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> a zaregistruje typ jako službu.</span><span class="sxs-lookup"><span data-stu-id="84c59-178">Create a type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="84c59-179">Doporučujeme předat delegáta [konfigurace, protože](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)vytvoření služby je složitější.</span><span class="sxs-lookup"><span data-stu-id="84c59-179">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="84c59-180">Vytvoření typu je ekvivalentní k čemu rozhraní při volání metody [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="84c59-180">Creating a type is equivalent to what the framework does when calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="84c59-181">Volání metody [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) provede přechodný obecný <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> , který má konstruktor, který přijímá obecné zadané typy služeb.</span><span class="sxs-lookup"><span data-stu-id="84c59-181">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

<a name="val"></a>

## <a name="options-validation"></a><span data-ttu-id="84c59-182">Ověřování možností</span><span class="sxs-lookup"><span data-stu-id="84c59-182">Options validation</span></span>

<span data-ttu-id="84c59-183">Ověřování možností povoluje ověření hodnot možností.</span><span class="sxs-lookup"><span data-stu-id="84c59-183">Options validation enables option values to be validated.</span></span>

<span data-ttu-id="84c59-184">Vezměte v úvahu následující soubor *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="84c59-184">Consider the following *appsettings.json* file:</span></span>

[!code-json[](~/fundamentals/configuration/options/samples/3.x/OptionsValidationSample/appsettings.Dev2.json)]

<span data-ttu-id="84c59-185">Následující třída se váže ke `"MyConfig"` konfiguračnímu oddílu a použije několik `DataAnnotations` pravidel:</span><span class="sxs-lookup"><span data-stu-id="84c59-185">The following class binds to the `"MyConfig"` configuration section and applies a couple of `DataAnnotations` rules:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Configuration/MyConfigOptions.cs?name=snippet)]

<span data-ttu-id="84c59-186">Následující kód:</span><span class="sxs-lookup"><span data-stu-id="84c59-186">The following code:</span></span>

* <span data-ttu-id="84c59-187">Volá <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions%2A> , aby se získal objekt [OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1) , který se váže ke `MyConfigOptions` třídě.</span><span class="sxs-lookup"><span data-stu-id="84c59-187">Calls <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions%2A> to get an [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) that binds to the `MyConfigOptions` class.</span></span>
* <span data-ttu-id="84c59-188">Volání <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations%2A> pro povolení ověřování pomocí `DataAnnotations` .</span><span class="sxs-lookup"><span data-stu-id="84c59-188">Calls <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations%2A> to enable validation using `DataAnnotations`.</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Startup.cs?name=snippet)]

<span data-ttu-id="84c59-189">`ValidateDataAnnotations`Metoda rozšíření je definována v balíčku NuGet [Microsoft. Extensions. Options. DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) .</span><span class="sxs-lookup"><span data-stu-id="84c59-189">The `ValidateDataAnnotations` extension method is defined in the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) NuGet package.</span></span> <span data-ttu-id="84c59-190">U webových aplikací, které používají `Microsoft.NET.Sdk.Web` sadu SDK, se na tento balíček odkazuje implicitně ze sdíleného rozhraní.</span><span class="sxs-lookup"><span data-stu-id="84c59-190">For web apps that use the `Microsoft.NET.Sdk.Web` SDK, this package is referenced implicitly from the shared framework.</span></span>

<span data-ttu-id="84c59-191">Následující kód zobrazí konfigurační hodnoty nebo chyby ověřování:</span><span class="sxs-lookup"><span data-stu-id="84c59-191">The following code displays the configuration values or the validation errors:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="84c59-192">Následující kód aplikuje složitější ověřovací pravidlo pomocí delegáta:</span><span class="sxs-lookup"><span data-stu-id="84c59-192">The following code applies a more complex validation rule using a delegate:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Startup2.cs?name=snippet)]

### <a name="ivalidateoptions-for-complex-validation"></a><span data-ttu-id="84c59-193">IValidateOptions pro komplexní ověřování</span><span class="sxs-lookup"><span data-stu-id="84c59-193">IValidateOptions for complex validation</span></span>

<span data-ttu-id="84c59-194">Následující třída implementuje <xref:Microsoft.Extensions.Options.IValidateOptions`1> :</span><span class="sxs-lookup"><span data-stu-id="84c59-194">The following class implements <xref:Microsoft.Extensions.Options.IValidateOptions`1>:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Configuration/MyConfigValidation.cs?name=snippet)]

<span data-ttu-id="84c59-195">`IValidateOptions`povoluje přesunutí ověřovacího kódu z `StartUp` a do třídy.</span><span class="sxs-lookup"><span data-stu-id="84c59-195">`IValidateOptions` enables moving the validation code out of `StartUp` and into a class.</span></span>

<span data-ttu-id="84c59-196">Pomocí předchozího kódu je ověřování povoleno v `Startup.ConfigureServices` s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="84c59-196">Using the preceding code, validation is enabled in `Startup.ConfigureServices` with the following code:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/StartupValidation.cs?name=snippet)]

<!-- The following comment doesn't seem that useful 
Options validation doesn't guard against options modifications after the options instance is created. For example, `IOptionsSnapshot` options are created and validated once per request when the options are first accessed. The `IOptionsSnapshot` options aren't validated again on subsequent access attempts *for the same request*.

The `Validate` method accepts a `Func<TOptions, bool>`. To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:

* Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`
* Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`

`IValidateOptions` validates:

* A specific named options instance.
* All options when `name` is `null`.

Return a `ValidateOptionsResult` from your implementation of the interface:

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`. `Microsoft.Extensions.Options.DataAnnotations` is implicitly referenced in ASP.NET Core apps.

-->

## <a name="options-post-configuration"></a><span data-ttu-id="84c59-197">Možnosti po konfiguraci</span><span class="sxs-lookup"><span data-stu-id="84c59-197">Options post-configuration</span></span>

<span data-ttu-id="84c59-198">Nastavení po konfiguraci nastavte na <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> .</span><span class="sxs-lookup"><span data-stu-id="84c59-198">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="84c59-199">Po dokončení všech konfigurací se spustí po konfiguraci <xref:Microsoft.Extensions.Options.IConfigureOptions%601> :</span><span class="sxs-lookup"><span data-stu-id="84c59-199">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="84c59-200"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>je k dispozici pro nastavení po konfiguraci s názvem:</span><span class="sxs-lookup"><span data-stu-id="84c59-200"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="84c59-201">Použijte <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> k následné konfiguraci všech instancí konfigurace:</span><span class="sxs-lookup"><span data-stu-id="84c59-201">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="84c59-202">Přístup k možnostem při spuštění</span><span class="sxs-lookup"><span data-stu-id="84c59-202">Accessing options during startup</span></span>

<span data-ttu-id="84c59-203"><xref:Microsoft.Extensions.Options.IOptions%601>a <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> lze jej použít v `Startup.Configure` , protože služby jsou vytvořeny před spuštěním `Configure` metody.</span><span class="sxs-lookup"><span data-stu-id="84c59-203"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, 
    IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="84c59-204">Nepoužívejte <xref:Microsoft.Extensions.Options.IOptions%601> nebo <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="84c59-204">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="84c59-205">Stav nekonzistentních možností může existovat v důsledku řazení registrací služby.</span><span class="sxs-lookup"><span data-stu-id="84c59-205">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

## <a name="optionsconfigurationextensions-nuget-package"></a><span data-ttu-id="84c59-206">Options – balíček NuGet. ConfigurationExtensions</span><span class="sxs-lookup"><span data-stu-id="84c59-206">Options.ConfigurationExtensions NuGet package</span></span>

<span data-ttu-id="84c59-207">Na balíček [Microsoft. Extensions. Options. ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) se implicitně odkazuje v aplikacích ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="84c59-207">The [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package is implicitly referenced in ASP.NET Core apps.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="84c59-208">Vzor možností používá třídy, které reprezentují skupiny souvisejících nastavení.</span><span class="sxs-lookup"><span data-stu-id="84c59-208">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="84c59-209">Když jsou [nastavení konfigurace](xref:fundamentals/configuration/index) izolována scénářem do samostatných tříd, aplikace dodržuje dva důležité principy technického řízení:</span><span class="sxs-lookup"><span data-stu-id="84c59-209">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="84c59-210">[Princip odděleníní rozhraní (ISP) nebo zapouzdření](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): scénáře (třídy) závislé na nastavení konfigurace závisejí pouze na nastavení konfigurace, která používají.</span><span class="sxs-lookup"><span data-stu-id="84c59-210">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="84c59-211">[Oddělení obav](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): nastavení různých částí aplikace nejsou závislá ani vzájemně závislá na sobě.</span><span class="sxs-lookup"><span data-stu-id="84c59-211">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="84c59-212">Možnosti také poskytují mechanismus pro ověření konfiguračních dat.</span><span class="sxs-lookup"><span data-stu-id="84c59-212">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="84c59-213">Další informace najdete v části [ověřování možností](#options-validation) .</span><span class="sxs-lookup"><span data-stu-id="84c59-213">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="84c59-214">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="84c59-214">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="84c59-215">Požadavky</span><span class="sxs-lookup"><span data-stu-id="84c59-215">Prerequisites</span></span>

<span data-ttu-id="84c59-216">Odkaz na balíček [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) nebo přidejte odkaz na balíček do balíčku [Microsoft. Extensions. Options. ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) .</span><span class="sxs-lookup"><span data-stu-id="84c59-216">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="84c59-217">Možnosti rozhraní</span><span class="sxs-lookup"><span data-stu-id="84c59-217">Options interfaces</span></span>

<span data-ttu-id="84c59-218"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>slouží k načtení možností a správě oznámení možností pro `TOptions` instance.</span><span class="sxs-lookup"><span data-stu-id="84c59-218"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="84c59-219"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>podporuje následující scénáře:</span><span class="sxs-lookup"><span data-stu-id="84c59-219"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="84c59-220">Oznámení změny</span><span class="sxs-lookup"><span data-stu-id="84c59-220">Change notifications</span></span>
* [<span data-ttu-id="84c59-221">Pojmenované možnosti</span><span class="sxs-lookup"><span data-stu-id="84c59-221">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="84c59-222">Znovu Načítatelné konfigurace</span><span class="sxs-lookup"><span data-stu-id="84c59-222">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="84c59-223">Neplatných selektivních možností ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> )</span><span class="sxs-lookup"><span data-stu-id="84c59-223">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="84c59-224">Scénáře po [konfiguraci](#options-post-configuration) umožňují nastavit nebo změnit možnosti po <xref:Microsoft.Extensions.Options.IConfigureOptions%601> výskytu všech konfigurací.</span><span class="sxs-lookup"><span data-stu-id="84c59-224">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="84c59-225"><xref:Microsoft.Extensions.Options.IOptionsFactory%601>zodpovídá za vytváření nových instancí možností.</span><span class="sxs-lookup"><span data-stu-id="84c59-225"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="84c59-226">Má jedinou <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> metodu.</span><span class="sxs-lookup"><span data-stu-id="84c59-226">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="84c59-227">Výchozí implementace přebírá všechny <xref:Microsoft.Extensions.Options.IConfigureOptions%601> Konfigurace a <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> a spustí nejprve všechny konfigurace, následované po konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="84c59-227">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="84c59-228">Rozlišuje mezi <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> a <xref:Microsoft.Extensions.Options.IConfigureOptions%601> a volá pouze příslušné rozhraní.</span><span class="sxs-lookup"><span data-stu-id="84c59-228">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="84c59-229"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>je používána <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> pro ukládání instancí do mezipaměti `TOptions` .</span><span class="sxs-lookup"><span data-stu-id="84c59-229"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="84c59-230"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>Zruší platnost instancí možností v monitorování tak, aby se hodnota přepočítala ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*> ).</span><span class="sxs-lookup"><span data-stu-id="84c59-230">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="84c59-231">Hodnoty lze ručně zavést pomocí <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> .</span><span class="sxs-lookup"><span data-stu-id="84c59-231">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="84c59-232"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*>Metoda se používá v případě, že by měly být všechny pojmenované instance znovu vytvořeny na vyžádání.</span><span class="sxs-lookup"><span data-stu-id="84c59-232">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="84c59-233"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>je užitečná ve scénářích, kde by se měly přepočítat možnosti pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="84c59-233"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="84c59-234">Další informace najdete v části [opětovné načtení konfiguračních dat pomocí IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) .</span><span class="sxs-lookup"><span data-stu-id="84c59-234">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="84c59-235"><xref:Microsoft.Extensions.Options.IOptions%601>dá se použít k podpoře možností.</span><span class="sxs-lookup"><span data-stu-id="84c59-235"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="84c59-236">Nicméně <xref:Microsoft.Extensions.Options.IOptions%601> nepodporuje předchozí scénáře <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> .</span><span class="sxs-lookup"><span data-stu-id="84c59-236">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="84c59-237">Můžete pokračovat <xref:Microsoft.Extensions.Options.IOptions%601> v používání existujících architektur a knihoven, které již rozhraní používají, <xref:Microsoft.Extensions.Options.IOptions%601> a nevyžadují scénáře, které poskytuje <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> .</span><span class="sxs-lookup"><span data-stu-id="84c59-237">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="84c59-238">Konfigurace obecných možností</span><span class="sxs-lookup"><span data-stu-id="84c59-238">General options configuration</span></span>

<span data-ttu-id="84c59-239">Konfigurace obecných možností se v ukázkové aplikaci ukazuje jako příklad 1.</span><span class="sxs-lookup"><span data-stu-id="84c59-239">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="84c59-240">Třída Options musí být neabstraktní s veřejným konstruktorem bez parametrů.</span><span class="sxs-lookup"><span data-stu-id="84c59-240">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="84c59-241">Následující třída `MyOptions` má dvě vlastnosti, `Option1` a `Option2` .</span><span class="sxs-lookup"><span data-stu-id="84c59-241">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="84c59-242">Nastavení výchozích hodnot je volitelné, ale konstruktor třídy v následujícím příkladu nastaví výchozí hodnotu `Option1` .</span><span class="sxs-lookup"><span data-stu-id="84c59-242">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="84c59-243">`Option2`má výchozí hodnotu nastavenou pomocí přímé inicializace vlastnosti (*modely/MyOptions. cs*):</span><span class="sxs-lookup"><span data-stu-id="84c59-243">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="84c59-244">`MyOptions`Třída je přidána do kontejneru služby s <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> konfigurací a je vázána na konfiguraci:</span><span class="sxs-lookup"><span data-stu-id="84c59-244">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="84c59-245">Následující model stránky používá [vkládání závislostí konstruktoru](xref:mvc/controllers/dependency-injection) s <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> pro přístup k nastavení (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="84c59-245">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="84c59-246">Soubor *appSettings. JSON* v ukázce určuje hodnoty pro `option1` a `option2` :</span><span class="sxs-lookup"><span data-stu-id="84c59-246">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="84c59-247">Při spuštění aplikace `OnGet` vrátí metoda modelu stránky řetězec zobrazující hodnoty třídy možností:</span><span class="sxs-lookup"><span data-stu-id="84c59-247">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="84c59-248">Když použijete vlastní <xref:System.Configuration.ConfigurationBuilder> nastavení pro načtení konfigurace možností z konfiguračního souboru, zkontrolujte, jestli je správně nastavená základní cesta:</span><span class="sxs-lookup"><span data-stu-id="84c59-248">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="84c59-249">Při načítání konfigurace možností ze souboru s nastavením prostřednictvím je explicitně nutné nastavit základní cestu <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="84c59-249">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="84c59-250">Konfigurace jednoduchých možností pomocí delegáta</span><span class="sxs-lookup"><span data-stu-id="84c59-250">Configure simple options with a delegate</span></span>

<span data-ttu-id="84c59-251">Konfigurace jednoduchých možností s delegátem je znázorněna jako příklad 2 v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="84c59-251">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="84c59-252">K nastavení hodnot možností použijte delegáta.</span><span class="sxs-lookup"><span data-stu-id="84c59-252">Use a delegate to set options values.</span></span> <span data-ttu-id="84c59-253">Ukázková aplikace používá `MyOptionsWithDelegateConfig` třídu (*modely/MyOptionsWithDelegateConfig. cs*):</span><span class="sxs-lookup"><span data-stu-id="84c59-253">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="84c59-254">V následujícím kódu <xref:Microsoft.Extensions.Options.IConfigureOptions%601> je do kontejneru služby přidána druhá služba.</span><span class="sxs-lookup"><span data-stu-id="84c59-254">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="84c59-255">Pomocí delegáta konfiguruje vazbu s `MyOptionsWithDelegateConfig` :</span><span class="sxs-lookup"><span data-stu-id="84c59-255">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="84c59-256">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="84c59-256">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="84c59-257">Můžete přidat více poskytovatelů konfigurace.</span><span class="sxs-lookup"><span data-stu-id="84c59-257">You can add multiple configuration providers.</span></span> <span data-ttu-id="84c59-258">Poskytovatelé konfigurace jsou k dispozici z balíčků NuGet a jsou aplikováni v pořadí, v jakém jsou registrováni.</span><span class="sxs-lookup"><span data-stu-id="84c59-258">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="84c59-259">Další informace naleznete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="84c59-259">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="84c59-260">Každé volání <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> přidá <xref:Microsoft.Extensions.Options.IConfigureOptions%601> službu do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="84c59-260">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="84c59-261">V předchozím příkladu `Option1` jsou hodnoty a `Option2` zadány v souboru *appSettings. JSON*, ale hodnoty `Option1` a `Option2` jsou přepsány nakonfigurovaným delegátem.</span><span class="sxs-lookup"><span data-stu-id="84c59-261">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="84c59-262">Je-li povolena více než jedna služba konfigurace, je v posledním zdroji konfigurace zadaný *Server WINS* a nastavena hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="84c59-262">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="84c59-263">Při spuštění aplikace `OnGet` vrátí metoda modelu stránky řetězec zobrazující hodnoty třídy možností:</span><span class="sxs-lookup"><span data-stu-id="84c59-263">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="84c59-264">Konfigurace podNastavení</span><span class="sxs-lookup"><span data-stu-id="84c59-264">Suboptions configuration</span></span>

<span data-ttu-id="84c59-265">Konfigurace dílčích možností je v ukázkové aplikaci znázorněna jako příklad 3.</span><span class="sxs-lookup"><span data-stu-id="84c59-265">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="84c59-266">Aplikace by měly vytvořit třídy možností, které se týkají konkrétních skupin scénářů (tříd) v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="84c59-266">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="84c59-267">Části aplikace, které vyžadují konfigurační hodnoty, by měly mít přístup jenom k hodnotám konfigurace, které používají.</span><span class="sxs-lookup"><span data-stu-id="84c59-267">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="84c59-268">Při vytváření vazby mezi možnostmi konfigurace jsou jednotlivé vlastnosti v typu možnosti vázány na konfigurační klíč formuláře `property[:sub-property:]` .</span><span class="sxs-lookup"><span data-stu-id="84c59-268">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="84c59-269">Například `MyOptions.Option1` vlastnost je svázána s klíčem `Option1` , který je načten z `option1` vlastnosti v souboru *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="84c59-269">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="84c59-270">V následujícím kódu <xref:Microsoft.Extensions.Options.IConfigureOptions%601> je do kontejneru služby přidána třetí služba.</span><span class="sxs-lookup"><span data-stu-id="84c59-270">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="84c59-271">Váže se `MySubOptions` k části `subsection` souboru *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="84c59-271">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="84c59-272">`GetSection`Metoda vyžaduje <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> obor názvů.</span><span class="sxs-lookup"><span data-stu-id="84c59-272">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="84c59-273">Soubor *appSettings. JSON* ukázky definuje `subsection` člena s klíči pro `suboption1` a `suboption2` :</span><span class="sxs-lookup"><span data-stu-id="84c59-273">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="84c59-274">`MySubOptions`Třída definuje vlastnosti `SubOption1` a `SubOption2` , aby obsahovala hodnoty možností (*modely/MySubOptions. cs*):</span><span class="sxs-lookup"><span data-stu-id="84c59-274">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="84c59-275">Metoda modelu stránky `OnGet` vrátí řetězec s hodnotami možností (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="84c59-275">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="84c59-276">Při spuštění aplikace `OnGet` vrátí metoda řetězec zobrazující hodnoty třídy dílčích možností:</span><span class="sxs-lookup"><span data-stu-id="84c59-276">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-injection"></a><span data-ttu-id="84c59-277">Vkládání možností</span><span class="sxs-lookup"><span data-stu-id="84c59-277">Options injection</span></span>

<span data-ttu-id="84c59-278">Vkládání možností je znázorněno jako příklad 4 v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="84c59-278">Options injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="84c59-279">Vložit <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> do:</span><span class="sxs-lookup"><span data-stu-id="84c59-279">Inject <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into:</span></span>

* <span data-ttu-id="84c59-280">RazorZobrazení stránky nebo MVC s [`@inject`](xref:mvc/views/razor#inject) Razor direktivou.</span><span class="sxs-lookup"><span data-stu-id="84c59-280">A Razor page or MVC view with the [`@inject`](xref:mvc/views/razor#inject) Razor directive.</span></span>
* <span data-ttu-id="84c59-281">Model stránky nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="84c59-281">A page or view model.</span></span>

<span data-ttu-id="84c59-282">Následující příklad z ukázkové aplikace se vloží <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> do modelu stránky (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="84c59-282">The following example from the sample app injects <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into a page model (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="84c59-283">Ukázková aplikace ukazuje, jak vložit `IOptionsMonitor<MyOptions>` pomocí `@inject` direktivy:</span><span class="sxs-lookup"><span data-stu-id="84c59-283">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="84c59-284">Po spuštění aplikace se hodnoty možností zobrazí na vykreslené stránce:</span><span class="sxs-lookup"><span data-stu-id="84c59-284">When the app is run, the options values are shown in the rendered page:</span></span>

![Hodnoty možností možnost1: value1_from_json a Možnost2:-1 jsou načteny z modelu a zavstřikem do zobrazení.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="84c59-286">Znovu načíst konfigurační data pomocí IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="84c59-286">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="84c59-287">Opětovné načtení konfiguračních dat pomocí <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> je v ukázkové aplikaci znázorněno v příkladu 5.</span><span class="sxs-lookup"><span data-stu-id="84c59-287">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="84c59-288"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>Při použití se možnosti vypočítávají jednou za požadavek při otevření a ukládání do mezipaměti po dobu života žádosti.</span><span class="sxs-lookup"><span data-stu-id="84c59-288">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="84c59-289">Rozdíl mezi `IOptionsMonitor` a `IOptionsSnapshot` je to, že:</span><span class="sxs-lookup"><span data-stu-id="84c59-289">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="84c59-290">`IOptionsMonitor`je [Služba typu Singleton](xref:fundamentals/dependency-injection#singleton) , která načte aktuální hodnoty možností kdykoli, což je zvláště užitečné v závislostech singleton.</span><span class="sxs-lookup"><span data-stu-id="84c59-290">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="84c59-291">`IOptionsSnapshot`je [Oborová služba](xref:fundamentals/dependency-injection#scoped) a poskytuje snímek možností v době, kdy `IOptionsSnapshot<T>` je objekt vytvořen.</span><span class="sxs-lookup"><span data-stu-id="84c59-291">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="84c59-292">Snímky možností jsou navržené pro použití s přechodnými a vymezenými závislostmi.</span><span class="sxs-lookup"><span data-stu-id="84c59-292">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="84c59-293">Následující příklad ukazuje, jak <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> se vytvoří nový po změnách *appSettings. JSON* (*pages/index. cshtml. cs*).</span><span class="sxs-lookup"><span data-stu-id="84c59-293">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="84c59-294">Více požadavků na server vrací konstantní hodnoty, které poskytuje soubor *appSettings. JSON* , dokud nedojde ke změně souboru a opětovnému načtení konfigurace.</span><span class="sxs-lookup"><span data-stu-id="84c59-294">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="84c59-295">Následující obrázek ukazuje počáteční `option1` `option2` hodnoty a hodnoty načtené ze souboru *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="84c59-295">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="84c59-296">Změňte hodnoty v souboru *appSettings. JSON* na `value1_from_json UPDATED` a `200` .</span><span class="sxs-lookup"><span data-stu-id="84c59-296">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="84c59-297">Uložte soubor *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="84c59-297">Save the *appsettings.json* file.</span></span> <span data-ttu-id="84c59-298">Aktualizujte prohlížeč, aby se zobrazily hodnoty možností, které jsou aktualizované:</span><span class="sxs-lookup"><span data-stu-id="84c59-298">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="84c59-299">Podpora pojmenovaných možností s IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="84c59-299">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="84c59-300">Pojmenovaná podpora s názvem <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> je znázorněna jako příklad 6 v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="84c59-300">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="84c59-301">Podpora pojmenovaných možností umožňuje aplikaci rozlišovat konfigurace pojmenovaných možností.</span><span class="sxs-lookup"><span data-stu-id="84c59-301">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="84c59-302">V ukázkové aplikaci jsou pojmenované možnosti deklarovány pomocí [OptionsServiceCollectionExtensions. Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), který volá [ConfigureNamedOptions \<TOptions> . Nakonfigurujte](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) metodu rozšíření.</span><span class="sxs-lookup"><span data-stu-id="84c59-302">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="84c59-303">U pojmenovaných možností se rozlišují velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="84c59-303">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="84c59-304">Ukázková aplikace přistupuje k pojmenovaným možnostem <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="84c59-304">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="84c59-305">Když spustíte ukázkovou aplikaci, vrátí se pojmenované možnosti:</span><span class="sxs-lookup"><span data-stu-id="84c59-305">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="84c59-306">`named_options_1`hodnoty se poskytují z konfigurace, které se načítají ze souboru *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="84c59-306">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="84c59-307">`named_options_2`hodnoty poskytuje:</span><span class="sxs-lookup"><span data-stu-id="84c59-307">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="84c59-308">`named_options_2`Delegát v `ConfigureServices` pro `Option1` .</span><span class="sxs-lookup"><span data-stu-id="84c59-308">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="84c59-309">Výchozí hodnota `Option2` poskytnutá `MyOptions` třídou</span><span class="sxs-lookup"><span data-stu-id="84c59-309">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="84c59-310">Konfigurace všech možností pomocí metody ConfigureAll</span><span class="sxs-lookup"><span data-stu-id="84c59-310">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="84c59-311">Nakonfigurujte všechny instance možností pomocí <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> metody.</span><span class="sxs-lookup"><span data-stu-id="84c59-311">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="84c59-312">Následující kód konfiguruje `Option1` pro všechny instance konfigurace se společnou hodnotou.</span><span class="sxs-lookup"><span data-stu-id="84c59-312">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="84c59-313">Do metody přidejte následující kód ručně `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="84c59-313">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="84c59-314">Spuštění ukázkové aplikace po přidání kódu vytvoří následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="84c59-314">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="84c59-315">Všechny možnosti jsou pojmenované instance.</span><span class="sxs-lookup"><span data-stu-id="84c59-315">All options are named instances.</span></span> <span data-ttu-id="84c59-316">Existující <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instance jsou považovány za cílení `Options.DefaultName` instance, což je `string.Empty` .</span><span class="sxs-lookup"><span data-stu-id="84c59-316">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="84c59-317"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>také implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> .</span><span class="sxs-lookup"><span data-stu-id="84c59-317"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="84c59-318">Výchozí implementace pro <xref:Microsoft.Extensions.Options.IOptionsFactory%601> má logiku odpovídajícím způsobem použít.</span><span class="sxs-lookup"><span data-stu-id="84c59-318">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="84c59-319">`null`Pojmenovaná možnost se používá pro cílení na všechny pojmenované instance místo konkrétní pojmenované instance ( <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> a <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> použití této konvence).</span><span class="sxs-lookup"><span data-stu-id="84c59-319">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="84c59-320">Rozhraní API pro OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="84c59-320">OptionsBuilder API</span></span>

<span data-ttu-id="84c59-321"><xref:Microsoft.Extensions.Options.OptionsBuilder%601>se používá ke konfiguraci `TOptions` instancí.</span><span class="sxs-lookup"><span data-stu-id="84c59-321"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="84c59-322">`OptionsBuilder`zjednodušuje vytváření pojmenovaných možností, protože je jenom jedním parametrem počátečního `AddOptions<TOptions>(string optionsName)` volání, takže se neobjeví ve všech dalších voláních.</span><span class="sxs-lookup"><span data-stu-id="84c59-322">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="84c59-323">Ověřování možností a `ConfigureOptions` přetížení, která přijímají závislosti služby, jsou k dispozici pouze prostřednictvím `OptionsBuilder` .</span><span class="sxs-lookup"><span data-stu-id="84c59-323">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="84c59-324">Ke konfiguraci možností použijte DI Services.</span><span class="sxs-lookup"><span data-stu-id="84c59-324">Use DI services to configure options</span></span>

<span data-ttu-id="84c59-325">K dalším službám můžete přistupovat pomocí injektáže závislosti při konfiguraci možností dvěma způsoby:</span><span class="sxs-lookup"><span data-stu-id="84c59-325">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="84c59-326">Předejte delegáta konfigurace [na](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) [OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="84c59-326">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="84c59-327">[OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1) poskytuje přetížení [Konfigurace](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) , která umožňují použít až pět služeb ke konfiguraci možností:</span><span class="sxs-lookup"><span data-stu-id="84c59-327">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="84c59-328">Vytvořte vlastní typ, který implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> nebo <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> a zaregistruje typ jako službu.</span><span class="sxs-lookup"><span data-stu-id="84c59-328">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="84c59-329">Doporučujeme předat delegáta [konfigurace, protože](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)vytvoření služby je složitější.</span><span class="sxs-lookup"><span data-stu-id="84c59-329">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="84c59-330">Vytvoření vlastního typu je ekvivalentní s tím, k čemu architektura slouží při použití možnosti [Konfigurovat](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="84c59-330">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="84c59-331">Volání metody [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) provede přechodný obecný <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> , který má konstruktor, který přijímá obecné zadané typy služeb.</span><span class="sxs-lookup"><span data-stu-id="84c59-331">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="84c59-332">Ověřování možností</span><span class="sxs-lookup"><span data-stu-id="84c59-332">Options validation</span></span>

<span data-ttu-id="84c59-333">Ověřování možností umožňuje ověřit možnosti při konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="84c59-333">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="84c59-334">Zavolejte `Validate` na metodu ověřování, která vrátí, `true` Pokud jsou možnosti platné, a `false` Pokud nejsou platné:</span><span class="sxs-lookup"><span data-stu-id="84c59-334">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

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

<span data-ttu-id="84c59-335">Předchozí příklad nastaví instanci pojmenované možnosti na `optionalOptionsName` .</span><span class="sxs-lookup"><span data-stu-id="84c59-335">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="84c59-336">Instance výchozích možností je `Options.DefaultName` .</span><span class="sxs-lookup"><span data-stu-id="84c59-336">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="84c59-337">Ověřování se spustí při vytvoření instance Options.</span><span class="sxs-lookup"><span data-stu-id="84c59-337">Validation runs when the options instance is created.</span></span> <span data-ttu-id="84c59-338">Je zaručeno, že instance možností před prvním tím, než se přistupuje k ověření.</span><span class="sxs-lookup"><span data-stu-id="84c59-338">An options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="84c59-339">Ověřování možností nechrání před úpravami možností po vytvoření instance Options.</span><span class="sxs-lookup"><span data-stu-id="84c59-339">Options validation doesn't guard against options modifications after the options instance is created.</span></span> <span data-ttu-id="84c59-340">Například `IOptionsSnapshot` Možnosti se vytváří a ověřují jednou za požadavek při prvním použití možností.</span><span class="sxs-lookup"><span data-stu-id="84c59-340">For example, `IOptionsSnapshot` options are created and validated once per request when the options are first accessed.</span></span> <span data-ttu-id="84c59-341">Tyto `IOptionsSnapshot` Možnosti se znovu neověřují u dalších pokusů o přístup *pro stejný požadavek*.</span><span class="sxs-lookup"><span data-stu-id="84c59-341">The `IOptionsSnapshot` options aren't validated again on subsequent access attempts *for the same request*.</span></span>

<span data-ttu-id="84c59-342">`Validate`Metoda přijímá `Func<TOptions, bool>` .</span><span class="sxs-lookup"><span data-stu-id="84c59-342">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="84c59-343">Pro úplné přizpůsobení ověřování, implementujte `IValidateOptions<TOptions>` , což umožňuje:</span><span class="sxs-lookup"><span data-stu-id="84c59-343">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="84c59-344">Ověřování více typů možností:`class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="84c59-344">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="84c59-345">Ověření, které závisí na jiném typu možnosti:`public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="84c59-345">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="84c59-346">`IValidateOptions`ověří</span><span class="sxs-lookup"><span data-stu-id="84c59-346">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="84c59-347">Konkrétní instance pojmenované možnosti.</span><span class="sxs-lookup"><span data-stu-id="84c59-347">A specific named options instance.</span></span>
* <span data-ttu-id="84c59-348">Všechny možnosti `name` , pokud je `null` .</span><span class="sxs-lookup"><span data-stu-id="84c59-348">All options when `name` is `null`.</span></span>

<span data-ttu-id="84c59-349">Vraťte se `ValidateOptionsResult` z vaší implementace rozhraní:</span><span class="sxs-lookup"><span data-stu-id="84c59-349">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="84c59-350">Ověřování na základě datových poznámek je k dispozici v balíčku [Microsoft. Extensions. Options. DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) pomocí volání <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> metody on `OptionsBuilder<TOptions>` .</span><span class="sxs-lookup"><span data-stu-id="84c59-350">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="84c59-351">`Microsoft.Extensions.Options.DataAnnotations`je součástí [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="84c59-351">`Microsoft.Extensions.Options.DataAnnotations` is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

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

<span data-ttu-id="84c59-352">Ověření Eager (při spuštění selže) je v budoucí verzi zváženo.</span><span class="sxs-lookup"><span data-stu-id="84c59-352">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="84c59-353">Možnosti po konfiguraci</span><span class="sxs-lookup"><span data-stu-id="84c59-353">Options post-configuration</span></span>

<span data-ttu-id="84c59-354">Nastavení po konfiguraci nastavte na <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> .</span><span class="sxs-lookup"><span data-stu-id="84c59-354">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="84c59-355">Po dokončení všech konfigurací se spustí po konfiguraci <xref:Microsoft.Extensions.Options.IConfigureOptions%601> :</span><span class="sxs-lookup"><span data-stu-id="84c59-355">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="84c59-356"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>je k dispozici pro nastavení po konfiguraci s názvem:</span><span class="sxs-lookup"><span data-stu-id="84c59-356"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="84c59-357">Použijte <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> k následné konfiguraci všech instancí konfigurace:</span><span class="sxs-lookup"><span data-stu-id="84c59-357">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="84c59-358">Přístup k možnostem při spuštění</span><span class="sxs-lookup"><span data-stu-id="84c59-358">Accessing options during startup</span></span>

<span data-ttu-id="84c59-359"><xref:Microsoft.Extensions.Options.IOptions%601>a <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> lze jej použít v `Startup.Configure` , protože služby jsou vytvořeny před spuštěním `Configure` metody.</span><span class="sxs-lookup"><span data-stu-id="84c59-359"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="84c59-360">Nepoužívejte <xref:Microsoft.Extensions.Options.IOptions%601> nebo <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="84c59-360">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="84c59-361">Stav nekonzistentních možností může existovat v důsledku řazení registrací služby.</span><span class="sxs-lookup"><span data-stu-id="84c59-361">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="84c59-362">Vzor možností používá třídy, které reprezentují skupiny souvisejících nastavení.</span><span class="sxs-lookup"><span data-stu-id="84c59-362">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="84c59-363">Když jsou [nastavení konfigurace](xref:fundamentals/configuration/index) izolována scénářem do samostatných tříd, aplikace dodržuje dva důležité principy technického řízení:</span><span class="sxs-lookup"><span data-stu-id="84c59-363">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="84c59-364">[Princip odděleníní rozhraní (ISP) nebo zapouzdření](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): scénáře (třídy) závislé na nastavení konfigurace závisejí pouze na nastavení konfigurace, která používají.</span><span class="sxs-lookup"><span data-stu-id="84c59-364">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="84c59-365">[Oddělení obav](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): nastavení různých částí aplikace nejsou závislá ani vzájemně závislá na sobě.</span><span class="sxs-lookup"><span data-stu-id="84c59-365">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="84c59-366">Možnosti také poskytují mechanismus pro ověření konfiguračních dat.</span><span class="sxs-lookup"><span data-stu-id="84c59-366">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="84c59-367">Další informace najdete v části [ověřování možností](#options-validation) .</span><span class="sxs-lookup"><span data-stu-id="84c59-367">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="84c59-368">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="84c59-368">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="84c59-369">Požadavky</span><span class="sxs-lookup"><span data-stu-id="84c59-369">Prerequisites</span></span>

<span data-ttu-id="84c59-370">Odkaz na balíček [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) nebo přidejte odkaz na balíček do balíčku [Microsoft. Extensions. Options. ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) .</span><span class="sxs-lookup"><span data-stu-id="84c59-370">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="84c59-371">Možnosti rozhraní</span><span class="sxs-lookup"><span data-stu-id="84c59-371">Options interfaces</span></span>

<span data-ttu-id="84c59-372"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>slouží k načtení možností a správě oznámení možností pro `TOptions` instance.</span><span class="sxs-lookup"><span data-stu-id="84c59-372"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="84c59-373"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>podporuje následující scénáře:</span><span class="sxs-lookup"><span data-stu-id="84c59-373"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="84c59-374">Oznámení změny</span><span class="sxs-lookup"><span data-stu-id="84c59-374">Change notifications</span></span>
* [<span data-ttu-id="84c59-375">Pojmenované možnosti</span><span class="sxs-lookup"><span data-stu-id="84c59-375">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="84c59-376">Znovu Načítatelné konfigurace</span><span class="sxs-lookup"><span data-stu-id="84c59-376">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="84c59-377">Neplatných selektivních možností ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> )</span><span class="sxs-lookup"><span data-stu-id="84c59-377">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="84c59-378">Scénáře po [konfiguraci](#options-post-configuration) umožňují nastavit nebo změnit možnosti po <xref:Microsoft.Extensions.Options.IConfigureOptions%601> výskytu všech konfigurací.</span><span class="sxs-lookup"><span data-stu-id="84c59-378">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="84c59-379"><xref:Microsoft.Extensions.Options.IOptionsFactory%601>zodpovídá za vytváření nových instancí možností.</span><span class="sxs-lookup"><span data-stu-id="84c59-379"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="84c59-380">Má jedinou <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> metodu.</span><span class="sxs-lookup"><span data-stu-id="84c59-380">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="84c59-381">Výchozí implementace přebírá všechny <xref:Microsoft.Extensions.Options.IConfigureOptions%601> Konfigurace a <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> a spustí nejprve všechny konfigurace, následované po konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="84c59-381">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="84c59-382">Rozlišuje mezi <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> a <xref:Microsoft.Extensions.Options.IConfigureOptions%601> a volá pouze příslušné rozhraní.</span><span class="sxs-lookup"><span data-stu-id="84c59-382">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="84c59-383"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>je používána <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> pro ukládání instancí do mezipaměti `TOptions` .</span><span class="sxs-lookup"><span data-stu-id="84c59-383"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="84c59-384"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>Zruší platnost instancí možností v monitorování tak, aby se hodnota přepočítala ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*> ).</span><span class="sxs-lookup"><span data-stu-id="84c59-384">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="84c59-385">Hodnoty lze ručně zavést pomocí <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> .</span><span class="sxs-lookup"><span data-stu-id="84c59-385">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="84c59-386"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*>Metoda se používá v případě, že by měly být všechny pojmenované instance znovu vytvořeny na vyžádání.</span><span class="sxs-lookup"><span data-stu-id="84c59-386">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="84c59-387"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>je užitečná ve scénářích, kde by se měly přepočítat možnosti pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="84c59-387"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="84c59-388">Další informace najdete v části [opětovné načtení konfiguračních dat pomocí IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) .</span><span class="sxs-lookup"><span data-stu-id="84c59-388">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="84c59-389"><xref:Microsoft.Extensions.Options.IOptions%601>dá se použít k podpoře možností.</span><span class="sxs-lookup"><span data-stu-id="84c59-389"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="84c59-390">Nicméně <xref:Microsoft.Extensions.Options.IOptions%601> nepodporuje předchozí scénáře <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> .</span><span class="sxs-lookup"><span data-stu-id="84c59-390">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="84c59-391">Můžete pokračovat <xref:Microsoft.Extensions.Options.IOptions%601> v používání existujících architektur a knihoven, které již rozhraní používají, <xref:Microsoft.Extensions.Options.IOptions%601> a nevyžadují scénáře, které poskytuje <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> .</span><span class="sxs-lookup"><span data-stu-id="84c59-391">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="84c59-392">Konfigurace obecných možností</span><span class="sxs-lookup"><span data-stu-id="84c59-392">General options configuration</span></span>

<span data-ttu-id="84c59-393">Konfigurace obecných možností se v ukázkové aplikaci ukazuje jako příklad 1.</span><span class="sxs-lookup"><span data-stu-id="84c59-393">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="84c59-394">Třída Options musí být neabstraktní s veřejným konstruktorem bez parametrů.</span><span class="sxs-lookup"><span data-stu-id="84c59-394">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="84c59-395">Následující třída `MyOptions` má dvě vlastnosti, `Option1` a `Option2` .</span><span class="sxs-lookup"><span data-stu-id="84c59-395">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="84c59-396">Nastavení výchozích hodnot je volitelné, ale konstruktor třídy v následujícím příkladu nastaví výchozí hodnotu `Option1` .</span><span class="sxs-lookup"><span data-stu-id="84c59-396">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="84c59-397">`Option2`má výchozí hodnotu nastavenou pomocí přímé inicializace vlastnosti (*modely/MyOptions. cs*):</span><span class="sxs-lookup"><span data-stu-id="84c59-397">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="84c59-398">`MyOptions`Třída je přidána do kontejneru služby s <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> konfigurací a je vázána na konfiguraci:</span><span class="sxs-lookup"><span data-stu-id="84c59-398">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="84c59-399">Následující model stránky používá [vkládání závislostí konstruktoru](xref:mvc/controllers/dependency-injection) s <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> pro přístup k nastavení (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="84c59-399">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="84c59-400">Soubor *appSettings. JSON* v ukázce určuje hodnoty pro `option1` a `option2` :</span><span class="sxs-lookup"><span data-stu-id="84c59-400">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="84c59-401">Při spuštění aplikace `OnGet` vrátí metoda modelu stránky řetězec zobrazující hodnoty třídy možností:</span><span class="sxs-lookup"><span data-stu-id="84c59-401">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="84c59-402">Když použijete vlastní <xref:System.Configuration.ConfigurationBuilder> nastavení pro načtení konfigurace možností z konfiguračního souboru, zkontrolujte, jestli je správně nastavená základní cesta:</span><span class="sxs-lookup"><span data-stu-id="84c59-402">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="84c59-403">Při načítání konfigurace možností ze souboru s nastavením prostřednictvím je explicitně nutné nastavit základní cestu <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="84c59-403">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="84c59-404">Konfigurace jednoduchých možností pomocí delegáta</span><span class="sxs-lookup"><span data-stu-id="84c59-404">Configure simple options with a delegate</span></span>

<span data-ttu-id="84c59-405">Konfigurace jednoduchých možností s delegátem je znázorněna jako příklad 2 v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="84c59-405">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="84c59-406">K nastavení hodnot možností použijte delegáta.</span><span class="sxs-lookup"><span data-stu-id="84c59-406">Use a delegate to set options values.</span></span> <span data-ttu-id="84c59-407">Ukázková aplikace používá `MyOptionsWithDelegateConfig` třídu (*modely/MyOptionsWithDelegateConfig. cs*):</span><span class="sxs-lookup"><span data-stu-id="84c59-407">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="84c59-408">V následujícím kódu <xref:Microsoft.Extensions.Options.IConfigureOptions%601> je do kontejneru služby přidána druhá služba.</span><span class="sxs-lookup"><span data-stu-id="84c59-408">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="84c59-409">Pomocí delegáta konfiguruje vazbu s `MyOptionsWithDelegateConfig` :</span><span class="sxs-lookup"><span data-stu-id="84c59-409">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="84c59-410">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="84c59-410">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="84c59-411">Můžete přidat více poskytovatelů konfigurace.</span><span class="sxs-lookup"><span data-stu-id="84c59-411">You can add multiple configuration providers.</span></span> <span data-ttu-id="84c59-412">Poskytovatelé konfigurace jsou k dispozici z balíčků NuGet a jsou aplikováni v pořadí, v jakém jsou registrováni.</span><span class="sxs-lookup"><span data-stu-id="84c59-412">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="84c59-413">Další informace naleznete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="84c59-413">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="84c59-414">Každé volání <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> přidá <xref:Microsoft.Extensions.Options.IConfigureOptions%601> službu do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="84c59-414">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="84c59-415">V předchozím příkladu `Option1` jsou hodnoty a `Option2` zadány v souboru *appSettings. JSON*, ale hodnoty `Option1` a `Option2` jsou přepsány nakonfigurovaným delegátem.</span><span class="sxs-lookup"><span data-stu-id="84c59-415">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="84c59-416">Je-li povolena více než jedna služba konfigurace, je v posledním zdroji konfigurace zadaný *Server WINS* a nastavena hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="84c59-416">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="84c59-417">Při spuštění aplikace `OnGet` vrátí metoda modelu stránky řetězec zobrazující hodnoty třídy možností:</span><span class="sxs-lookup"><span data-stu-id="84c59-417">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="84c59-418">Konfigurace podNastavení</span><span class="sxs-lookup"><span data-stu-id="84c59-418">Suboptions configuration</span></span>

<span data-ttu-id="84c59-419">Konfigurace dílčích možností je v ukázkové aplikaci znázorněna jako příklad 3.</span><span class="sxs-lookup"><span data-stu-id="84c59-419">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="84c59-420">Aplikace by měly vytvořit třídy možností, které se týkají konkrétních skupin scénářů (tříd) v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="84c59-420">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="84c59-421">Části aplikace, které vyžadují konfigurační hodnoty, by měly mít přístup jenom k hodnotám konfigurace, které používají.</span><span class="sxs-lookup"><span data-stu-id="84c59-421">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="84c59-422">Při vytváření vazby mezi možnostmi konfigurace jsou jednotlivé vlastnosti v typu možnosti vázány na konfigurační klíč formuláře `property[:sub-property:]` .</span><span class="sxs-lookup"><span data-stu-id="84c59-422">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="84c59-423">Například `MyOptions.Option1` vlastnost je svázána s klíčem `Option1` , který je načten z `option1` vlastnosti v souboru *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="84c59-423">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="84c59-424">V následujícím kódu <xref:Microsoft.Extensions.Options.IConfigureOptions%601> je do kontejneru služby přidána třetí služba.</span><span class="sxs-lookup"><span data-stu-id="84c59-424">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="84c59-425">Váže se `MySubOptions` k části `subsection` souboru *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="84c59-425">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="84c59-426">`GetSection`Metoda vyžaduje <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> obor názvů.</span><span class="sxs-lookup"><span data-stu-id="84c59-426">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="84c59-427">Soubor *appSettings. JSON* ukázky definuje `subsection` člena s klíči pro `suboption1` a `suboption2` :</span><span class="sxs-lookup"><span data-stu-id="84c59-427">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="84c59-428">`MySubOptions`Třída definuje vlastnosti `SubOption1` a `SubOption2` , aby obsahovala hodnoty možností (*modely/MySubOptions. cs*):</span><span class="sxs-lookup"><span data-stu-id="84c59-428">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="84c59-429">Metoda modelu stránky `OnGet` vrátí řetězec s hodnotami možností (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="84c59-429">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="84c59-430">Při spuštění aplikace `OnGet` vrátí metoda řetězec zobrazující hodnoty třídy dílčích možností:</span><span class="sxs-lookup"><span data-stu-id="84c59-430">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="84c59-431">Možnosti poskytované modelem zobrazení nebo vkládáním přímých zobrazení</span><span class="sxs-lookup"><span data-stu-id="84c59-431">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="84c59-432">Možnosti poskytované modelem zobrazení nebo vkládáním přímých zobrazení jsou v ukázkové aplikaci znázorněné jako příklad 4.</span><span class="sxs-lookup"><span data-stu-id="84c59-432">Options provided by a view model or with direct view injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="84c59-433">Možnosti lze zadat v modelu zobrazení nebo <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> přímým vložením do zobrazení (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="84c59-433">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="84c59-434">Ukázková aplikace ukazuje, jak vložit `IOptionsMonitor<MyOptions>` pomocí `@inject` direktivy:</span><span class="sxs-lookup"><span data-stu-id="84c59-434">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="84c59-435">Po spuštění aplikace se hodnoty možností zobrazí na vykreslené stránce:</span><span class="sxs-lookup"><span data-stu-id="84c59-435">When the app is run, the options values are shown in the rendered page:</span></span>

![Hodnoty možností možnost1: value1_from_json a Možnost2:-1 jsou načteny z modelu a zavstřikem do zobrazení.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="84c59-437">Znovu načíst konfigurační data pomocí IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="84c59-437">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="84c59-438">Opětovné načtení konfiguračních dat pomocí <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> je v ukázkové aplikaci znázorněno v příkladu 5.</span><span class="sxs-lookup"><span data-stu-id="84c59-438">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="84c59-439"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>podporuje opětovné načítání možností s minimální režií zpracování.</span><span class="sxs-lookup"><span data-stu-id="84c59-439"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="84c59-440">Možnosti jsou vypočítány jednou za požadavek při otevření a ukládání do mezipaměti po dobu života žádosti.</span><span class="sxs-lookup"><span data-stu-id="84c59-440">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="84c59-441">Následující příklad ukazuje, jak <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> se vytvoří nový po změnách *appSettings. JSON* (*pages/index. cshtml. cs*).</span><span class="sxs-lookup"><span data-stu-id="84c59-441">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="84c59-442">Více požadavků na server vrací konstantní hodnoty, které poskytuje soubor *appSettings. JSON* , dokud nedojde ke změně souboru a opětovnému načtení konfigurace.</span><span class="sxs-lookup"><span data-stu-id="84c59-442">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="84c59-443">Následující obrázek ukazuje počáteční `option1` `option2` hodnoty a hodnoty načtené ze souboru *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="84c59-443">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="84c59-444">Změňte hodnoty v souboru *appSettings. JSON* na `value1_from_json UPDATED` a `200` .</span><span class="sxs-lookup"><span data-stu-id="84c59-444">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="84c59-445">Uložte soubor *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="84c59-445">Save the *appsettings.json* file.</span></span> <span data-ttu-id="84c59-446">Aktualizujte prohlížeč, aby se zobrazily hodnoty možností, které jsou aktualizované:</span><span class="sxs-lookup"><span data-stu-id="84c59-446">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="84c59-447">Podpora pojmenovaných možností s IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="84c59-447">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="84c59-448">Pojmenovaná podpora s názvem <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> je znázorněna jako příklad 6 v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="84c59-448">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="84c59-449">Podpora pojmenovaných možností umožňuje aplikaci rozlišovat konfigurace pojmenovaných možností.</span><span class="sxs-lookup"><span data-stu-id="84c59-449">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="84c59-450">V ukázkové aplikaci jsou pojmenované možnosti deklarovány pomocí [OptionsServiceCollectionExtensions. Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), který volá [ConfigureNamedOptions \<TOptions> . Nakonfigurujte](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) metodu rozšíření.</span><span class="sxs-lookup"><span data-stu-id="84c59-450">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="84c59-451">U pojmenovaných možností se rozlišují velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="84c59-451">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="84c59-452">Ukázková aplikace přistupuje k pojmenovaným možnostem <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="84c59-452">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="84c59-453">Když spustíte ukázkovou aplikaci, vrátí se pojmenované možnosti:</span><span class="sxs-lookup"><span data-stu-id="84c59-453">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="84c59-454">`named_options_1`hodnoty se poskytují z konfigurace, které se načítají ze souboru *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="84c59-454">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="84c59-455">`named_options_2`hodnoty poskytuje:</span><span class="sxs-lookup"><span data-stu-id="84c59-455">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="84c59-456">`named_options_2`Delegát v `ConfigureServices` pro `Option1` .</span><span class="sxs-lookup"><span data-stu-id="84c59-456">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="84c59-457">Výchozí hodnota `Option2` poskytnutá `MyOptions` třídou</span><span class="sxs-lookup"><span data-stu-id="84c59-457">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="84c59-458">Konfigurace všech možností pomocí metody ConfigureAll</span><span class="sxs-lookup"><span data-stu-id="84c59-458">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="84c59-459">Nakonfigurujte všechny instance možností pomocí <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> metody.</span><span class="sxs-lookup"><span data-stu-id="84c59-459">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="84c59-460">Následující kód konfiguruje `Option1` pro všechny instance konfigurace se společnou hodnotou.</span><span class="sxs-lookup"><span data-stu-id="84c59-460">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="84c59-461">Do metody přidejte následující kód ručně `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="84c59-461">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="84c59-462">Spuštění ukázkové aplikace po přidání kódu vytvoří následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="84c59-462">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="84c59-463">Všechny možnosti jsou pojmenované instance.</span><span class="sxs-lookup"><span data-stu-id="84c59-463">All options are named instances.</span></span> <span data-ttu-id="84c59-464">Existující <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instance jsou považovány za cílení `Options.DefaultName` instance, což je `string.Empty` .</span><span class="sxs-lookup"><span data-stu-id="84c59-464">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="84c59-465"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>také implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> .</span><span class="sxs-lookup"><span data-stu-id="84c59-465"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="84c59-466">Výchozí implementace pro <xref:Microsoft.Extensions.Options.IOptionsFactory%601> má logiku odpovídajícím způsobem použít.</span><span class="sxs-lookup"><span data-stu-id="84c59-466">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="84c59-467">`null`Pojmenovaná možnost se používá pro cílení na všechny pojmenované instance místo konkrétní pojmenované instance ( <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> a <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> použití této konvence).</span><span class="sxs-lookup"><span data-stu-id="84c59-467">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="84c59-468">Rozhraní API pro OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="84c59-468">OptionsBuilder API</span></span>

<span data-ttu-id="84c59-469"><xref:Microsoft.Extensions.Options.OptionsBuilder%601>se používá ke konfiguraci `TOptions` instancí.</span><span class="sxs-lookup"><span data-stu-id="84c59-469"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="84c59-470">`OptionsBuilder`zjednodušuje vytváření pojmenovaných možností, protože je jenom jedním parametrem počátečního `AddOptions<TOptions>(string optionsName)` volání, takže se neobjeví ve všech dalších voláních.</span><span class="sxs-lookup"><span data-stu-id="84c59-470">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="84c59-471">Ověřování možností a `ConfigureOptions` přetížení, která přijímají závislosti služby, jsou k dispozici pouze prostřednictvím `OptionsBuilder` .</span><span class="sxs-lookup"><span data-stu-id="84c59-471">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="84c59-472">Ke konfiguraci možností použijte DI Services.</span><span class="sxs-lookup"><span data-stu-id="84c59-472">Use DI services to configure options</span></span>

<span data-ttu-id="84c59-473">K dalším službám můžete přistupovat pomocí injektáže závislosti při konfiguraci možností dvěma způsoby:</span><span class="sxs-lookup"><span data-stu-id="84c59-473">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="84c59-474">Předejte delegáta konfigurace [na](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) [OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="84c59-474">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="84c59-475">[OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1) poskytuje přetížení [Konfigurace](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) , která umožňují použít až pět služeb ke konfiguraci možností:</span><span class="sxs-lookup"><span data-stu-id="84c59-475">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="84c59-476">Vytvořte vlastní typ, který implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> nebo <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> a zaregistruje typ jako službu.</span><span class="sxs-lookup"><span data-stu-id="84c59-476">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="84c59-477">Doporučujeme předat delegáta [konfigurace, protože](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)vytvoření služby je složitější.</span><span class="sxs-lookup"><span data-stu-id="84c59-477">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="84c59-478">Vytvoření vlastního typu je ekvivalentní s tím, k čemu architektura slouží při použití možnosti [Konfigurovat](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="84c59-478">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="84c59-479">Volání metody [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) provede přechodný obecný <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> , který má konstruktor, který přijímá obecné zadané typy služeb.</span><span class="sxs-lookup"><span data-stu-id="84c59-479">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-post-configuration"></a><span data-ttu-id="84c59-480">Možnosti po konfiguraci</span><span class="sxs-lookup"><span data-stu-id="84c59-480">Options post-configuration</span></span>

<span data-ttu-id="84c59-481">Nastavení po konfiguraci nastavte na <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> .</span><span class="sxs-lookup"><span data-stu-id="84c59-481">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="84c59-482">Po dokončení všech konfigurací se spustí po konfiguraci <xref:Microsoft.Extensions.Options.IConfigureOptions%601> :</span><span class="sxs-lookup"><span data-stu-id="84c59-482">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="84c59-483"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>je k dispozici pro nastavení po konfiguraci s názvem:</span><span class="sxs-lookup"><span data-stu-id="84c59-483"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="84c59-484">Použijte <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> k následné konfiguraci všech instancí konfigurace:</span><span class="sxs-lookup"><span data-stu-id="84c59-484">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="84c59-485">Přístup k možnostem při spuštění</span><span class="sxs-lookup"><span data-stu-id="84c59-485">Accessing options during startup</span></span>

<span data-ttu-id="84c59-486"><xref:Microsoft.Extensions.Options.IOptions%601>a <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> lze jej použít v `Startup.Configure` , protože služby jsou vytvořeny před spuštěním `Configure` metody.</span><span class="sxs-lookup"><span data-stu-id="84c59-486"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="84c59-487">Nepoužívejte <xref:Microsoft.Extensions.Options.IOptions%601> nebo <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="84c59-487">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="84c59-488">Stav nekonzistentních možností může existovat v důsledku řazení registrací služby.</span><span class="sxs-lookup"><span data-stu-id="84c59-488">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="84c59-489">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="84c59-489">Additional resources</span></span>

* <xref:fundamentals/configuration/index>

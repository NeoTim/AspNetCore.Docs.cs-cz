---
title: Vzor možností v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak pomocí vzoru možností znázornit skupiny souvisejících nastavení v aplikacích ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/20/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/configuration/options
ms.openlocfilehash: 6eafad63bd4c49cee6a85108b37a8b61e1214bce
ms.sourcegitcommit: d7991068bc6b04063f4bd836fc5b9591d614d448
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91762331"
---
# <a name="options-pattern-in-aspnet-core"></a><span data-ttu-id="7207e-103">Vzor možností v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7207e-103">Options pattern in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7207e-104">Od [Kirka Larkin](https://twitter.com/serpent5) a [Rick Anderson](https://twitter.com/RickAndMSFT).</span><span class="sxs-lookup"><span data-stu-id="7207e-104">By [Kirk Larkin](https://twitter.com/serpent5) and [Rick Anderson](https://twitter.com/RickAndMSFT).</span></span>

<span data-ttu-id="7207e-105">Vzor možností používá třídy k poskytnutí přístupu silného typu ke skupinám souvisejících nastavení.</span><span class="sxs-lookup"><span data-stu-id="7207e-105">The options pattern uses classes to provide strongly typed access to groups of related settings.</span></span> <span data-ttu-id="7207e-106">Když jsou [nastavení konfigurace](xref:fundamentals/configuration/index) izolována scénářem do samostatných tříd, aplikace dodržuje dva důležité principy technického řízení:</span><span class="sxs-lookup"><span data-stu-id="7207e-106">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="7207e-107">[Princip odděleníní rozhraní (ISP) nebo zapouzdření](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): scénáře (třídy) závislé na nastavení konfigurace závisejí pouze na nastavení konfigurace, která používají.</span><span class="sxs-lookup"><span data-stu-id="7207e-107">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="7207e-108">[Oddělení obav](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): nastavení různých částí aplikace nejsou závislá ani vzájemně závislá na sobě.</span><span class="sxs-lookup"><span data-stu-id="7207e-108">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="7207e-109">Možnosti také poskytují mechanismus pro ověření konfiguračních dat.</span><span class="sxs-lookup"><span data-stu-id="7207e-109">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="7207e-110">Další informace najdete v části [ověřování možností](#options-validation) .</span><span class="sxs-lookup"><span data-stu-id="7207e-110">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="7207e-111">Toto téma poskytuje informace o vzoru možností v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7207e-111">This topic provides information on the options pattern in ASP.NET Core.</span></span> <span data-ttu-id="7207e-112">Informace o použití vzoru možností v konzolových aplikacích najdete v tématu [model možností v .NET](/dotnet/core/extensions/options).</span><span class="sxs-lookup"><span data-stu-id="7207e-112">For information on using the options pattern in console apps, see [Options pattern in .NET](/dotnet/core/extensions/options).</span></span>

<span data-ttu-id="7207e-113">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7207e-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="optpat"></a>

## <a name="bind-hierarchical-configuration"></a><span data-ttu-id="7207e-114">Vytvoření vazby hierarchické konfigurace</span><span class="sxs-lookup"><span data-stu-id="7207e-114">Bind hierarchical configuration</span></span>

[!INCLUDE[](~/includes/bind.md)]

<a name="oi"></a>

## <a name="options-interfaces"></a><span data-ttu-id="7207e-115">Možnosti rozhraní</span><span class="sxs-lookup"><span data-stu-id="7207e-115">Options interfaces</span></span>

<span data-ttu-id="7207e-116"><xref:Microsoft.Extensions.Options.IOptions%601>:</span><span class="sxs-lookup"><span data-stu-id="7207e-116"><xref:Microsoft.Extensions.Options.IOptions%601>:</span></span>

* <span data-ttu-id="7207e-117">Nepodporuje ***:***</span><span class="sxs-lookup"><span data-stu-id="7207e-117">Does ***not*** support:</span></span>
  * <span data-ttu-id="7207e-118">Čtení konfiguračních dat po spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="7207e-118">Reading of configuration data after the app has started.</span></span>
  * [<span data-ttu-id="7207e-119">Pojmenované možnosti</span><span class="sxs-lookup"><span data-stu-id="7207e-119">Named options</span></span>](#named)
* <span data-ttu-id="7207e-120">Je zaregistrován jako typ [singleton](xref:fundamentals/dependency-injection#singleton) a může být vložen do jakékoli [životnosti služby](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="7207e-120">Is registered as a [Singleton](xref:fundamentals/dependency-injection#singleton) and can be injected into any [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="7207e-121"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>:</span><span class="sxs-lookup"><span data-stu-id="7207e-121"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>:</span></span>

* <span data-ttu-id="7207e-122">Je užitečná ve scénářích, kde by se měly přepočítat možnosti pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="7207e-122">Is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="7207e-123">Další informace najdete v tématu [použití IOptionsSnapshot ke čtení aktualizovaných dat](#ios).</span><span class="sxs-lookup"><span data-stu-id="7207e-123">For more information, see [Use IOptionsSnapshot to read updated data](#ios).</span></span>
* <span data-ttu-id="7207e-124">Je zaregistrován jako [obor](xref:fundamentals/dependency-injection#scoped) , a proto nemůže být vložen do služby typu singleton.</span><span class="sxs-lookup"><span data-stu-id="7207e-124">Is registered as [Scoped](xref:fundamentals/dependency-injection#scoped) and therefore cannot be injected into a Singleton service.</span></span>
* <span data-ttu-id="7207e-125">Podporuje [pojmenované možnosti](#named)</span><span class="sxs-lookup"><span data-stu-id="7207e-125">Supports [named options](#named)</span></span>

<span data-ttu-id="7207e-126"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span><span class="sxs-lookup"><span data-stu-id="7207e-126"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span></span>

* <span data-ttu-id="7207e-127">Slouží k načtení možností a správě oznámení možností pro `TOptions` instance.</span><span class="sxs-lookup"><span data-stu-id="7207e-127">Is used to retrieve options and manage options notifications for `TOptions` instances.</span></span>
* <span data-ttu-id="7207e-128">Je zaregistrován jako typ [singleton](xref:fundamentals/dependency-injection#singleton) a může být vložen do jakékoli [životnosti služby](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="7207e-128">Is registered as a [Singleton](xref:fundamentals/dependency-injection#singleton) and can be injected into any [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>
* <span data-ttu-id="7207e-129">Podporovaných</span><span class="sxs-lookup"><span data-stu-id="7207e-129">Supports:</span></span>
  * <span data-ttu-id="7207e-130">Oznámení změny</span><span class="sxs-lookup"><span data-stu-id="7207e-130">Change notifications</span></span>
  * [<span data-ttu-id="7207e-131">Pojmenované možnosti</span><span class="sxs-lookup"><span data-stu-id="7207e-131">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
  * [<span data-ttu-id="7207e-132">Znovu Načítatelné konfigurace</span><span class="sxs-lookup"><span data-stu-id="7207e-132">Reloadable configuration</span></span>](#ios)
  * <span data-ttu-id="7207e-133">Neplatných selektivních možností ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> )</span><span class="sxs-lookup"><span data-stu-id="7207e-133">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>
  
<span data-ttu-id="7207e-134">Scénáře po [konfiguraci](#options-post-configuration) umožňují nastavení nebo změnu možností po všech <xref:Microsoft.Extensions.Options.IConfigureOptions%601> konfiguracích.</span><span class="sxs-lookup"><span data-stu-id="7207e-134">[Post-configuration](#options-post-configuration) scenarios enable setting or changing options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="7207e-135"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> zodpovídá za vytváření nových instancí možností.</span><span class="sxs-lookup"><span data-stu-id="7207e-135"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="7207e-136">Má jedinou <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> metodu.</span><span class="sxs-lookup"><span data-stu-id="7207e-136">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="7207e-137">Výchozí implementace přebírá všechny <xref:Microsoft.Extensions.Options.IConfigureOptions%601> Konfigurace a <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> a spustí nejprve všechny konfigurace, následované po konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="7207e-137">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="7207e-138">Rozlišuje mezi <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> a <xref:Microsoft.Extensions.Options.IConfigureOptions%601> a volá pouze příslušné rozhraní.</span><span class="sxs-lookup"><span data-stu-id="7207e-138">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="7207e-139"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> je používána <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> pro ukládání instancí do mezipaměti `TOptions` .</span><span class="sxs-lookup"><span data-stu-id="7207e-139"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="7207e-140"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>Zruší platnost instancí možností v monitorování tak, aby se hodnota přepočítala ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*> ).</span><span class="sxs-lookup"><span data-stu-id="7207e-140">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="7207e-141">Hodnoty lze ručně zavést pomocí <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> .</span><span class="sxs-lookup"><span data-stu-id="7207e-141">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="7207e-142"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*>Metoda se používá v případě, že by měly být všechny pojmenované instance znovu vytvořeny na vyžádání.</span><span class="sxs-lookup"><span data-stu-id="7207e-142">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<a name="ios"></a>

## <a name="use-ioptionssnapshot-to-read-updated-data"></a><span data-ttu-id="7207e-143">Použití IOptionsSnapshot ke čtení aktualizovaných dat</span><span class="sxs-lookup"><span data-stu-id="7207e-143">Use IOptionsSnapshot to read updated data</span></span>

<span data-ttu-id="7207e-144"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>Při použití se možnosti vypočítávají jednou za požadavek při otevření a ukládání do mezipaměti po dobu života žádosti.</span><span class="sxs-lookup"><span data-stu-id="7207e-144">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span> <span data-ttu-id="7207e-145">Změny konfigurace se čtou po spuštění aplikace při použití zprostředkovatelů konfigurace, které podporují čtení aktualizovaných hodnot konfigurace.</span><span class="sxs-lookup"><span data-stu-id="7207e-145">Changes to the configuration are read after the app starts when using configuration providers that support reading updated configuration values.</span></span>

<span data-ttu-id="7207e-146">Rozdíl mezi `IOptionsMonitor` a `IOptionsSnapshot` je to, že:</span><span class="sxs-lookup"><span data-stu-id="7207e-146">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="7207e-147">`IOptionsMonitor` je [Služba typu Singleton](xref:fundamentals/dependency-injection#singleton) , která načte aktuální hodnoty možností kdykoli, což je zvláště užitečné v závislostech singleton.</span><span class="sxs-lookup"><span data-stu-id="7207e-147">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="7207e-148">`IOptionsSnapshot` je [Oborová služba](xref:fundamentals/dependency-injection#scoped) a poskytuje snímek možností v době, kdy `IOptionsSnapshot<T>` je objekt vytvořen.</span><span class="sxs-lookup"><span data-stu-id="7207e-148">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="7207e-149">Snímky možností jsou navržené pro použití s přechodnými a vymezenými závislostmi.</span><span class="sxs-lookup"><span data-stu-id="7207e-149">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="7207e-150">Následující kód používá <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> .</span><span class="sxs-lookup"><span data-stu-id="7207e-150">The following code uses <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>.</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestSnap.cshtml.cs?name=snippet)]

<span data-ttu-id="7207e-151">Následující kód zaregistruje instanci konfigurace, která se `MyOptions` váže k:</span><span class="sxs-lookup"><span data-stu-id="7207e-151">The following code registers a configuration instance which `MyOptions` binds against:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="7207e-152">V předchozím kódu se po čtení aplikace změny konfiguračního souboru JSON po jeho spuštění.</span><span class="sxs-lookup"><span data-stu-id="7207e-152">In the preceding code, changes to the JSON configuration file after the app has started are read.</span></span>

## <a name="ioptionsmonitor"></a><span data-ttu-id="7207e-153">IOptionsMonitor</span><span class="sxs-lookup"><span data-stu-id="7207e-153">IOptionsMonitor</span></span>

<span data-ttu-id="7207e-154">Následující kód zaregistruje instanci konfigurace, která se `MyOptions` váže k.</span><span class="sxs-lookup"><span data-stu-id="7207e-154">The following code registers a configuration instance which `MyOptions` binds against.</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="7207e-155">Následující příklad používá <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> :</span><span class="sxs-lookup"><span data-stu-id="7207e-155">The following example uses <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestMonitor.cshtml.cs?name=snippet)]

<span data-ttu-id="7207e-156">V předchozím kódu se ve výchozím nastavení změny konfiguračního souboru JSON po spuštění aplikace čtou.</span><span class="sxs-lookup"><span data-stu-id="7207e-156">In the preceding code, by default, changes to the JSON configuration file after the app has started are read.</span></span>

<a name="named"></a>

## <a name="named-options-support-using-iconfigurenamedoptions"></a><span data-ttu-id="7207e-157">Podpora pojmenovaných možností pomocí IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="7207e-157">Named options support using IConfigureNamedOptions</span></span>

<span data-ttu-id="7207e-158">Pojmenované možnosti:</span><span class="sxs-lookup"><span data-stu-id="7207e-158">Named options:</span></span>

* <span data-ttu-id="7207e-159">Jsou užitečné v případě, že je více konfiguračních oddílů svázán se stejnými vlastnostmi.</span><span class="sxs-lookup"><span data-stu-id="7207e-159">Are useful when multiple configuration sections bind to the same properties.</span></span>
* <span data-ttu-id="7207e-160">Rozlišují velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="7207e-160">Are case sensitive.</span></span>

<span data-ttu-id="7207e-161">V souboru zvažte následující *appsettings.js* :</span><span class="sxs-lookup"><span data-stu-id="7207e-161">Consider the following *appsettings.json* file:</span></span>

[!code-json[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/appsettings.NO.json)]

<span data-ttu-id="7207e-162">Namísto vytváření dvou tříd pro vytvoření vazby `TopItem:Month` a `TopItem:Year` pro každou část se používá následující třída:</span><span class="sxs-lookup"><span data-stu-id="7207e-162">Rather than creating two classes to bind `TopItem:Month` and `TopItem:Year`, the following class is used for each section:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Models/TopItemSettings.cs)]

<span data-ttu-id="7207e-163">Následující kód konfiguruje pojmenované možnosti:</span><span class="sxs-lookup"><span data-stu-id="7207e-163">The following code configures the named options:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/StartupNO.cs?name=snippet_Example2)]

<span data-ttu-id="7207e-164">Následující kód zobrazí pojmenované možnosti:</span><span class="sxs-lookup"><span data-stu-id="7207e-164">The following code displays the named options:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestNO.cshtml.cs?name=snippet)]

<span data-ttu-id="7207e-165">Všechny možnosti jsou pojmenované instance.</span><span class="sxs-lookup"><span data-stu-id="7207e-165">All options are named instances.</span></span> <span data-ttu-id="7207e-166"><xref:Microsoft.Extensions.Options.IConfigureOptions%601> instance jsou považovány za cílení `Options.DefaultName` instance, což je `string.Empty` .</span><span class="sxs-lookup"><span data-stu-id="7207e-166"><xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="7207e-167"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> také implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> .</span><span class="sxs-lookup"><span data-stu-id="7207e-167"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="7207e-168">Výchozí implementace pro <xref:Microsoft.Extensions.Options.IOptionsFactory%601> má logiku odpovídajícím způsobem použít.</span><span class="sxs-lookup"><span data-stu-id="7207e-168">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="7207e-169">`null`Pojmenovaná možnost se používá pro cílení na všechny pojmenované instance místo konkrétní pojmenované instance.</span><span class="sxs-lookup"><span data-stu-id="7207e-169">The `null` named option is used to target all of the named instances instead of a specific named instance.</span></span> <span data-ttu-id="7207e-170"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> a <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> použijte tuto konvenci.</span><span class="sxs-lookup"><span data-stu-id="7207e-170"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention.</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="7207e-171">Rozhraní API pro OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="7207e-171">OptionsBuilder API</span></span>

<span data-ttu-id="7207e-172"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> se používá ke konfiguraci `TOptions` instancí.</span><span class="sxs-lookup"><span data-stu-id="7207e-172"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="7207e-173">`OptionsBuilder` zjednodušuje vytváření pojmenovaných možností, protože je jenom jedním parametrem počátečního `AddOptions<TOptions>(string optionsName)` volání, takže se neobjeví ve všech dalších voláních.</span><span class="sxs-lookup"><span data-stu-id="7207e-173">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="7207e-174">Ověřování možností a `ConfigureOptions` přetížení, která přijímají závislosti služby, jsou k dispozici pouze prostřednictvím `OptionsBuilder` .</span><span class="sxs-lookup"><span data-stu-id="7207e-174">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

<span data-ttu-id="7207e-175">`OptionsBuilder` se používá v sekci [ověřování možností](#val) .</span><span class="sxs-lookup"><span data-stu-id="7207e-175">`OptionsBuilder` is used in the [Options validation](#val) section.</span></span>

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="7207e-176">Ke konfiguraci možností použijte DI Services.</span><span class="sxs-lookup"><span data-stu-id="7207e-176">Use DI services to configure options</span></span>

<span data-ttu-id="7207e-177">Služby jsou dostupné ze injektáže závislosti při konfiguraci možností dvěma způsoby:</span><span class="sxs-lookup"><span data-stu-id="7207e-177">Services can be accessed from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="7207e-178">Předejte delegáta konfigurace [na](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) [OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="7207e-178">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="7207e-179">`OptionsBuilder<TOptions>` poskytuje přetížení [Konfigurace](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) , která umožňují použití až pěti služeb ke konfiguraci možností:</span><span class="sxs-lookup"><span data-stu-id="7207e-179">`OptionsBuilder<TOptions>` provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow use of up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="7207e-180">Vytvořte typ, který implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> nebo <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> a zaregistruje typ jako službu.</span><span class="sxs-lookup"><span data-stu-id="7207e-180">Create a type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="7207e-181">Doporučujeme předat delegáta [konfigurace, protože](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)vytvoření služby je složitější.</span><span class="sxs-lookup"><span data-stu-id="7207e-181">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="7207e-182">Vytvoření typu je ekvivalentní k čemu rozhraní při volání metody [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="7207e-182">Creating a type is equivalent to what the framework does when calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="7207e-183">Volání metody [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) provede přechodný obecný <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> , který má konstruktor, který přijímá obecné zadané typy služeb.</span><span class="sxs-lookup"><span data-stu-id="7207e-183">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

<a name="val"></a>

## <a name="options-validation"></a><span data-ttu-id="7207e-184">Ověřování možností</span><span class="sxs-lookup"><span data-stu-id="7207e-184">Options validation</span></span>

<span data-ttu-id="7207e-185">Ověřování možností povoluje ověření hodnot možností.</span><span class="sxs-lookup"><span data-stu-id="7207e-185">Options validation enables option values to be validated.</span></span>

<span data-ttu-id="7207e-186">V souboru zvažte následující *appsettings.js* :</span><span class="sxs-lookup"><span data-stu-id="7207e-186">Consider the following *appsettings.json* file:</span></span>

[!code-json[](~/fundamentals/configuration/options/samples/3.x/OptionsValidationSample/appsettings.Dev2.json)]

<span data-ttu-id="7207e-187">Následující třída se váže ke `"MyConfig"` konfiguračnímu oddílu a použije několik `DataAnnotations` pravidel:</span><span class="sxs-lookup"><span data-stu-id="7207e-187">The following class binds to the `"MyConfig"` configuration section and applies a couple of `DataAnnotations` rules:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Configuration/MyConfigOptions.cs?name=snippet)]

<span data-ttu-id="7207e-188">Následující kód:</span><span class="sxs-lookup"><span data-stu-id="7207e-188">The following code:</span></span>

* <span data-ttu-id="7207e-189">Volá <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions%2A> , aby se získal objekt [OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1) , který se váže ke `MyConfigOptions` třídě.</span><span class="sxs-lookup"><span data-stu-id="7207e-189">Calls <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions%2A> to get an [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) that binds to the `MyConfigOptions` class.</span></span>
* <span data-ttu-id="7207e-190">Volání <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations%2A> pro povolení ověřování pomocí `DataAnnotations` .</span><span class="sxs-lookup"><span data-stu-id="7207e-190">Calls <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations%2A> to enable validation using `DataAnnotations`.</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Startup.cs?name=snippet)]

<span data-ttu-id="7207e-191">`ValidateDataAnnotations`Metoda rozšíření je definována v balíčku NuGet [Microsoft. Extensions. Options. DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) .</span><span class="sxs-lookup"><span data-stu-id="7207e-191">The `ValidateDataAnnotations` extension method is defined in the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) NuGet package.</span></span> <span data-ttu-id="7207e-192">U webových aplikací, které používají `Microsoft.NET.Sdk.Web` sadu SDK, se na tento balíček odkazuje implicitně ze sdíleného rozhraní.</span><span class="sxs-lookup"><span data-stu-id="7207e-192">For web apps that use the `Microsoft.NET.Sdk.Web` SDK, this package is referenced implicitly from the shared framework.</span></span>

<span data-ttu-id="7207e-193">Následující kód zobrazí konfigurační hodnoty nebo chyby ověřování:</span><span class="sxs-lookup"><span data-stu-id="7207e-193">The following code displays the configuration values or the validation errors:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="7207e-194">Následující kód aplikuje složitější ověřovací pravidlo pomocí delegáta:</span><span class="sxs-lookup"><span data-stu-id="7207e-194">The following code applies a more complex validation rule using a delegate:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Startup2.cs?name=snippet)]

### <a name="ivalidateoptions-for-complex-validation"></a><span data-ttu-id="7207e-195">IValidateOptions pro komplexní ověřování</span><span class="sxs-lookup"><span data-stu-id="7207e-195">IValidateOptions for complex validation</span></span>

<span data-ttu-id="7207e-196">Následující třída implementuje <xref:Microsoft.Extensions.Options.IValidateOptions`1> :</span><span class="sxs-lookup"><span data-stu-id="7207e-196">The following class implements <xref:Microsoft.Extensions.Options.IValidateOptions`1>:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Configuration/MyConfigValidation.cs?name=snippet)]

<span data-ttu-id="7207e-197">`IValidateOptions` povoluje přesunutí ověřovacího kódu z `StartUp` a do třídy.</span><span class="sxs-lookup"><span data-stu-id="7207e-197">`IValidateOptions` enables moving the validation code out of `StartUp` and into a class.</span></span>

<span data-ttu-id="7207e-198">Pomocí předchozího kódu je ověřování povoleno v `Startup.ConfigureServices` s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="7207e-198">Using the preceding code, validation is enabled in `Startup.ConfigureServices` with the following code:</span></span>

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

## <a name="options-post-configuration"></a><span data-ttu-id="7207e-199">Možnosti po konfiguraci</span><span class="sxs-lookup"><span data-stu-id="7207e-199">Options post-configuration</span></span>

<span data-ttu-id="7207e-200">Nastavení po konfiguraci nastavte na <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> .</span><span class="sxs-lookup"><span data-stu-id="7207e-200">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="7207e-201">Po dokončení všech konfigurací se spustí po konfiguraci <xref:Microsoft.Extensions.Options.IConfigureOptions%601> :</span><span class="sxs-lookup"><span data-stu-id="7207e-201">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="7207e-202"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> je k dispozici pro nastavení po konfiguraci s názvem:</span><span class="sxs-lookup"><span data-stu-id="7207e-202"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="7207e-203">Použijte <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> k následné konfiguraci všech instancí konfigurace:</span><span class="sxs-lookup"><span data-stu-id="7207e-203">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="7207e-204">Přístup k možnostem při spuštění</span><span class="sxs-lookup"><span data-stu-id="7207e-204">Accessing options during startup</span></span>

<span data-ttu-id="7207e-205"><xref:Microsoft.Extensions.Options.IOptions%601> a <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> lze jej použít v `Startup.Configure` , protože služby jsou vytvořeny před spuštěním `Configure` metody.</span><span class="sxs-lookup"><span data-stu-id="7207e-205"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, 
    IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="7207e-206">Nepoužívejte <xref:Microsoft.Extensions.Options.IOptions%601> nebo <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="7207e-206">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="7207e-207">Stav nekonzistentních možností může existovat v důsledku řazení registrací služby.</span><span class="sxs-lookup"><span data-stu-id="7207e-207">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

## <a name="optionsconfigurationextensions-nuget-package"></a><span data-ttu-id="7207e-208">Balíček NuGet Options.ConfigurationExtensions</span><span class="sxs-lookup"><span data-stu-id="7207e-208">Options.ConfigurationExtensions NuGet package</span></span>

<span data-ttu-id="7207e-209">Na balíček [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) se implicitně odkazuje v ASP.NET Corech aplikacích.</span><span class="sxs-lookup"><span data-stu-id="7207e-209">The [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package is implicitly referenced in ASP.NET Core apps.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="7207e-210">Vzor možností používá třídy, které reprezentují skupiny souvisejících nastavení.</span><span class="sxs-lookup"><span data-stu-id="7207e-210">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="7207e-211">Když jsou [nastavení konfigurace](xref:fundamentals/configuration/index) izolována scénářem do samostatných tříd, aplikace dodržuje dva důležité principy technického řízení:</span><span class="sxs-lookup"><span data-stu-id="7207e-211">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="7207e-212">[Princip odděleníní rozhraní (ISP) nebo zapouzdření](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): scénáře (třídy) závislé na nastavení konfigurace závisejí pouze na nastavení konfigurace, která používají.</span><span class="sxs-lookup"><span data-stu-id="7207e-212">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="7207e-213">[Oddělení obav](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): nastavení různých částí aplikace nejsou závislá ani vzájemně závislá na sobě.</span><span class="sxs-lookup"><span data-stu-id="7207e-213">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="7207e-214">Možnosti také poskytují mechanismus pro ověření konfiguračních dat.</span><span class="sxs-lookup"><span data-stu-id="7207e-214">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="7207e-215">Další informace najdete v části [ověřování možností](#options-validation) .</span><span class="sxs-lookup"><span data-stu-id="7207e-215">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="7207e-216">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7207e-216">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7207e-217">Předpoklady</span><span class="sxs-lookup"><span data-stu-id="7207e-217">Prerequisites</span></span>

<span data-ttu-id="7207e-218">Odkaz na balíček [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) nebo přidejte odkaz na balíček do balíčku [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) .</span><span class="sxs-lookup"><span data-stu-id="7207e-218">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="7207e-219">Možnosti rozhraní</span><span class="sxs-lookup"><span data-stu-id="7207e-219">Options interfaces</span></span>

<span data-ttu-id="7207e-220"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> slouží k načtení možností a správě oznámení možností pro `TOptions` instance.</span><span class="sxs-lookup"><span data-stu-id="7207e-220"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="7207e-221"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> podporuje následující scénáře:</span><span class="sxs-lookup"><span data-stu-id="7207e-221"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="7207e-222">Oznámení změny</span><span class="sxs-lookup"><span data-stu-id="7207e-222">Change notifications</span></span>
* [<span data-ttu-id="7207e-223">Pojmenované možnosti</span><span class="sxs-lookup"><span data-stu-id="7207e-223">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="7207e-224">Znovu Načítatelné konfigurace</span><span class="sxs-lookup"><span data-stu-id="7207e-224">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="7207e-225">Neplatných selektivních možností ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> )</span><span class="sxs-lookup"><span data-stu-id="7207e-225">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="7207e-226">Scénáře po [konfiguraci](#options-post-configuration) umožňují nastavit nebo změnit možnosti po <xref:Microsoft.Extensions.Options.IConfigureOptions%601> výskytu všech konfigurací.</span><span class="sxs-lookup"><span data-stu-id="7207e-226">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="7207e-227"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> zodpovídá za vytváření nových instancí možností.</span><span class="sxs-lookup"><span data-stu-id="7207e-227"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="7207e-228">Má jedinou <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> metodu.</span><span class="sxs-lookup"><span data-stu-id="7207e-228">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="7207e-229">Výchozí implementace přebírá všechny <xref:Microsoft.Extensions.Options.IConfigureOptions%601> Konfigurace a <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> a spustí nejprve všechny konfigurace, následované po konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="7207e-229">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="7207e-230">Rozlišuje mezi <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> a <xref:Microsoft.Extensions.Options.IConfigureOptions%601> a volá pouze příslušné rozhraní.</span><span class="sxs-lookup"><span data-stu-id="7207e-230">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="7207e-231"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> je používána <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> pro ukládání instancí do mezipaměti `TOptions` .</span><span class="sxs-lookup"><span data-stu-id="7207e-231"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="7207e-232"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>Zruší platnost instancí možností v monitorování tak, aby se hodnota přepočítala ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*> ).</span><span class="sxs-lookup"><span data-stu-id="7207e-232">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="7207e-233">Hodnoty lze ručně zavést pomocí <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> .</span><span class="sxs-lookup"><span data-stu-id="7207e-233">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="7207e-234"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*>Metoda se používá v případě, že by měly být všechny pojmenované instance znovu vytvořeny na vyžádání.</span><span class="sxs-lookup"><span data-stu-id="7207e-234">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="7207e-235"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> je užitečná ve scénářích, kde by se měly přepočítat možnosti pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="7207e-235"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="7207e-236">Další informace najdete v části [opětovné načtení konfiguračních dat pomocí IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) .</span><span class="sxs-lookup"><span data-stu-id="7207e-236">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="7207e-237"><xref:Microsoft.Extensions.Options.IOptions%601> dá se použít k podpoře možností.</span><span class="sxs-lookup"><span data-stu-id="7207e-237"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="7207e-238">Nicméně <xref:Microsoft.Extensions.Options.IOptions%601> nepodporuje předchozí scénáře <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> .</span><span class="sxs-lookup"><span data-stu-id="7207e-238">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="7207e-239">Můžete pokračovat <xref:Microsoft.Extensions.Options.IOptions%601> v používání existujících architektur a knihoven, které již rozhraní používají, <xref:Microsoft.Extensions.Options.IOptions%601> a nevyžadují scénáře, které poskytuje <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> .</span><span class="sxs-lookup"><span data-stu-id="7207e-239">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="7207e-240">Konfigurace obecných možností</span><span class="sxs-lookup"><span data-stu-id="7207e-240">General options configuration</span></span>

<span data-ttu-id="7207e-241">Konfigurace obecných možností se v ukázkové aplikaci ukazuje jako příklad 1.</span><span class="sxs-lookup"><span data-stu-id="7207e-241">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="7207e-242">Třída Options musí být neabstraktní s veřejným konstruktorem bez parametrů.</span><span class="sxs-lookup"><span data-stu-id="7207e-242">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="7207e-243">Následující třída `MyOptions` má dvě vlastnosti, `Option1` a `Option2` .</span><span class="sxs-lookup"><span data-stu-id="7207e-243">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="7207e-244">Nastavení výchozích hodnot je volitelné, ale konstruktor třídy v následujícím příkladu nastaví výchozí hodnotu `Option1` .</span><span class="sxs-lookup"><span data-stu-id="7207e-244">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="7207e-245">`Option2` má výchozí hodnotu nastavenou pomocí přímé inicializace vlastnosti (*modely/MyOptions. cs*):</span><span class="sxs-lookup"><span data-stu-id="7207e-245">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="7207e-246">`MyOptions`Třída je přidána do kontejneru služby s <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> konfigurací a je vázána na konfiguraci:</span><span class="sxs-lookup"><span data-stu-id="7207e-246">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="7207e-247">Následující model stránky používá [vkládání závislostí konstruktoru](xref:mvc/controllers/dependency-injection) s <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> pro přístup k nastavení (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="7207e-247">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="7207e-248">Soubor *appsettings.jsv* ukázce určuje hodnoty pro `option1` a `option2` :</span><span class="sxs-lookup"><span data-stu-id="7207e-248">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="7207e-249">Při spuštění aplikace `OnGet` vrátí metoda modelu stránky řetězec zobrazující hodnoty třídy možností:</span><span class="sxs-lookup"><span data-stu-id="7207e-249">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="7207e-250">Když použijete vlastní <xref:System.Configuration.ConfigurationBuilder> nastavení pro načtení konfigurace možností z konfiguračního souboru, zkontrolujte, jestli je správně nastavená základní cesta:</span><span class="sxs-lookup"><span data-stu-id="7207e-250">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="7207e-251">Při načítání konfigurace možností ze souboru s nastavením prostřednictvím je explicitně nutné nastavit základní cestu <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="7207e-251">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="7207e-252">Konfigurace jednoduchých možností pomocí delegáta</span><span class="sxs-lookup"><span data-stu-id="7207e-252">Configure simple options with a delegate</span></span>

<span data-ttu-id="7207e-253">Konfigurace jednoduchých možností s delegátem je znázorněna jako příklad 2 v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="7207e-253">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="7207e-254">K nastavení hodnot možností použijte delegáta.</span><span class="sxs-lookup"><span data-stu-id="7207e-254">Use a delegate to set options values.</span></span> <span data-ttu-id="7207e-255">Ukázková aplikace používá `MyOptionsWithDelegateConfig` třídu (*modely/MyOptionsWithDelegateConfig. cs*):</span><span class="sxs-lookup"><span data-stu-id="7207e-255">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="7207e-256">V následujícím kódu <xref:Microsoft.Extensions.Options.IConfigureOptions%601> je do kontejneru služby přidána druhá služba.</span><span class="sxs-lookup"><span data-stu-id="7207e-256">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="7207e-257">Pomocí delegáta konfiguruje vazbu s `MyOptionsWithDelegateConfig` :</span><span class="sxs-lookup"><span data-stu-id="7207e-257">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="7207e-258">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="7207e-258">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="7207e-259">Můžete přidat více poskytovatelů konfigurace.</span><span class="sxs-lookup"><span data-stu-id="7207e-259">You can add multiple configuration providers.</span></span> <span data-ttu-id="7207e-260">Poskytovatelé konfigurace jsou k dispozici z balíčků NuGet a jsou aplikováni v pořadí, v jakém jsou registrováni.</span><span class="sxs-lookup"><span data-stu-id="7207e-260">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="7207e-261">Další informace naleznete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="7207e-261">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="7207e-262">Každé volání <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> přidá <xref:Microsoft.Extensions.Options.IConfigureOptions%601> službu do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="7207e-262">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="7207e-263">V předchozím příkladu jsou hodnoty `Option1` a uvedeny `Option2` v *appsettings.jsna*, ale hodnoty `Option1` a `Option2` jsou přepsány nakonfigurovaným delegátem.</span><span class="sxs-lookup"><span data-stu-id="7207e-263">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="7207e-264">Je-li povolena více než jedna služba konfigurace, je v posledním zdroji konfigurace zadaný *Server WINS* a nastavena hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="7207e-264">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="7207e-265">Při spuštění aplikace `OnGet` vrátí metoda modelu stránky řetězec zobrazující hodnoty třídy možností:</span><span class="sxs-lookup"><span data-stu-id="7207e-265">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="7207e-266">Konfigurace podNastavení</span><span class="sxs-lookup"><span data-stu-id="7207e-266">Suboptions configuration</span></span>

<span data-ttu-id="7207e-267">Konfigurace dílčích možností je v ukázkové aplikaci znázorněna jako příklad 3.</span><span class="sxs-lookup"><span data-stu-id="7207e-267">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="7207e-268">Aplikace by měly vytvořit třídy možností, které se týkají konkrétních skupin scénářů (tříd) v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="7207e-268">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="7207e-269">Části aplikace, které vyžadují konfigurační hodnoty, by měly mít přístup jenom k hodnotám konfigurace, které používají.</span><span class="sxs-lookup"><span data-stu-id="7207e-269">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="7207e-270">Při vytváření vazby mezi možnostmi konfigurace jsou jednotlivé vlastnosti v typu možnosti vázány na konfigurační klíč formuláře `property[:sub-property:]` .</span><span class="sxs-lookup"><span data-stu-id="7207e-270">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="7207e-271">Například `MyOptions.Option1` vlastnost je svázána s klíčem `Option1` , který je načten z `option1` vlastnosti v *appsettings.jsna*.</span><span class="sxs-lookup"><span data-stu-id="7207e-271">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="7207e-272">V následujícím kódu <xref:Microsoft.Extensions.Options.IConfigureOptions%601> je do kontejneru služby přidána třetí služba.</span><span class="sxs-lookup"><span data-stu-id="7207e-272">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="7207e-273">Váže `MySubOptions` se k části `subsection` *appsettings.jsv* souboru:</span><span class="sxs-lookup"><span data-stu-id="7207e-273">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="7207e-274">`GetSection`Metoda vyžaduje <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> obor názvů.</span><span class="sxs-lookup"><span data-stu-id="7207e-274">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="7207e-275">*appsettings.jsukázek v* souboru definuje `subsection` člena s klíči pro `suboption1` a `suboption2` :</span><span class="sxs-lookup"><span data-stu-id="7207e-275">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="7207e-276">`MySubOptions`Třída definuje vlastnosti `SubOption1` a `SubOption2` , aby obsahovala hodnoty možností (*modely/MySubOptions. cs*):</span><span class="sxs-lookup"><span data-stu-id="7207e-276">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="7207e-277">Metoda modelu stránky `OnGet` vrátí řetězec s hodnotami možností (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="7207e-277">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="7207e-278">Při spuštění aplikace `OnGet` vrátí metoda řetězec zobrazující hodnoty třídy dílčích možností:</span><span class="sxs-lookup"><span data-stu-id="7207e-278">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-injection"></a><span data-ttu-id="7207e-279">Vkládání možností</span><span class="sxs-lookup"><span data-stu-id="7207e-279">Options injection</span></span>

<span data-ttu-id="7207e-280">Vkládání možností je znázorněno jako příklad 4 v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="7207e-280">Options injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="7207e-281">Vložit <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> do:</span><span class="sxs-lookup"><span data-stu-id="7207e-281">Inject <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into:</span></span>

* <span data-ttu-id="7207e-282">RazorZobrazení stránky nebo MVC s [`@inject`](xref:mvc/views/razor#inject) Razor direktivou.</span><span class="sxs-lookup"><span data-stu-id="7207e-282">A Razor page or MVC view with the [`@inject`](xref:mvc/views/razor#inject) Razor directive.</span></span>
* <span data-ttu-id="7207e-283">Model stránky nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="7207e-283">A page or view model.</span></span>

<span data-ttu-id="7207e-284">Následující příklad z ukázkové aplikace se vloží <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> do modelu stránky (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="7207e-284">The following example from the sample app injects <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into a page model (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="7207e-285">Ukázková aplikace ukazuje, jak vložit `IOptionsMonitor<MyOptions>` pomocí `@inject` direktivy:</span><span class="sxs-lookup"><span data-stu-id="7207e-285">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="7207e-286">Po spuštění aplikace se hodnoty možností zobrazí na vykreslené stránce:</span><span class="sxs-lookup"><span data-stu-id="7207e-286">When the app is run, the options values are shown in the rendered page:</span></span>

![Hodnoty možností možnost1: value1_from_json a Možnost2:-1 jsou načteny z modelu a zavstřikem do zobrazení.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="7207e-288">Znovu načíst konfigurační data pomocí IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="7207e-288">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="7207e-289">Opětovné načtení konfiguračních dat pomocí <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> je v ukázkové aplikaci znázorněno v příkladu 5.</span><span class="sxs-lookup"><span data-stu-id="7207e-289">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="7207e-290"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>Při použití se možnosti vypočítávají jednou za požadavek při otevření a ukládání do mezipaměti po dobu života žádosti.</span><span class="sxs-lookup"><span data-stu-id="7207e-290">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="7207e-291">Rozdíl mezi `IOptionsMonitor` a `IOptionsSnapshot` je to, že:</span><span class="sxs-lookup"><span data-stu-id="7207e-291">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="7207e-292">`IOptionsMonitor` je [Služba typu Singleton](xref:fundamentals/dependency-injection#singleton) , která načte aktuální hodnoty možností kdykoli, což je zvláště užitečné v závislostech singleton.</span><span class="sxs-lookup"><span data-stu-id="7207e-292">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="7207e-293">`IOptionsSnapshot` je [Oborová služba](xref:fundamentals/dependency-injection#scoped) a poskytuje snímek možností v době, kdy `IOptionsSnapshot<T>` je objekt vytvořen.</span><span class="sxs-lookup"><span data-stu-id="7207e-293">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="7207e-294">Snímky možností jsou navržené pro použití s přechodnými a vymezenými závislostmi.</span><span class="sxs-lookup"><span data-stu-id="7207e-294">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="7207e-295">Následující příklad ukazuje, jak <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> se vytvoří nový po *appsettings.js* změny (*pages/index. cshtml. cs*).</span><span class="sxs-lookup"><span data-stu-id="7207e-295">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="7207e-296">Více požadavků na server vrací konstantní hodnoty, které poskytuje *appsettings.jsv* souboru, dokud nedojde ke změně souboru a obnovení konfigurace.</span><span class="sxs-lookup"><span data-stu-id="7207e-296">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="7207e-297">Následující obrázek ukazuje počáteční `option1` `option2` hodnoty a hodnoty načtené z *appsettings.js* souboru:</span><span class="sxs-lookup"><span data-stu-id="7207e-297">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="7207e-298">Změňte hodnoty v *appsettings.jsv* souboru na `value1_from_json UPDATED` a `200` .</span><span class="sxs-lookup"><span data-stu-id="7207e-298">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="7207e-299">Uložte *appsettings.jsdo* souboru.</span><span class="sxs-lookup"><span data-stu-id="7207e-299">Save the *appsettings.json* file.</span></span> <span data-ttu-id="7207e-300">Aktualizujte prohlížeč, aby se zobrazily hodnoty možností, které jsou aktualizované:</span><span class="sxs-lookup"><span data-stu-id="7207e-300">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="7207e-301">Podpora pojmenovaných možností s IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="7207e-301">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="7207e-302">Pojmenovaná podpora s názvem <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> je znázorněna jako příklad 6 v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="7207e-302">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="7207e-303">Podpora pojmenovaných možností umožňuje aplikaci rozlišovat konfigurace pojmenovaných možností.</span><span class="sxs-lookup"><span data-stu-id="7207e-303">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="7207e-304">V ukázkové aplikaci jsou pojmenované možnosti deklarovány s [OptionsServiceCollectionExtensions.Configurovat](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), která volá [ConfigureNamedOptions \<TOptions> . Nakonfigurujte](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) metodu rozšíření.</span><span class="sxs-lookup"><span data-stu-id="7207e-304">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="7207e-305">U pojmenovaných možností se rozlišují velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="7207e-305">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="7207e-306">Ukázková aplikace přistupuje k pojmenovaným možnostem <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="7207e-306">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="7207e-307">Když spustíte ukázkovou aplikaci, vrátí se pojmenované možnosti:</span><span class="sxs-lookup"><span data-stu-id="7207e-307">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="7207e-308">`named_options_1` hodnoty se poskytují z konfigurace, které se načítají z *appsettings.jsv* souboru.</span><span class="sxs-lookup"><span data-stu-id="7207e-308">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="7207e-309">`named_options_2` hodnoty poskytuje:</span><span class="sxs-lookup"><span data-stu-id="7207e-309">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="7207e-310">`named_options_2`Delegát v `ConfigureServices` pro `Option1` .</span><span class="sxs-lookup"><span data-stu-id="7207e-310">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="7207e-311">Výchozí hodnota `Option2` poskytnutá `MyOptions` třídou</span><span class="sxs-lookup"><span data-stu-id="7207e-311">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="7207e-312">Konfigurace všech možností pomocí metody ConfigureAll</span><span class="sxs-lookup"><span data-stu-id="7207e-312">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="7207e-313">Nakonfigurujte všechny instance možností pomocí <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> metody.</span><span class="sxs-lookup"><span data-stu-id="7207e-313">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="7207e-314">Následující kód konfiguruje `Option1` pro všechny instance konfigurace se společnou hodnotou.</span><span class="sxs-lookup"><span data-stu-id="7207e-314">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="7207e-315">Do metody přidejte následující kód ručně `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="7207e-315">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="7207e-316">Spuštění ukázkové aplikace po přidání kódu vytvoří následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="7207e-316">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="7207e-317">Všechny možnosti jsou pojmenované instance.</span><span class="sxs-lookup"><span data-stu-id="7207e-317">All options are named instances.</span></span> <span data-ttu-id="7207e-318">Existující <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instance jsou považovány za cílení `Options.DefaultName` instance, což je `string.Empty` .</span><span class="sxs-lookup"><span data-stu-id="7207e-318">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="7207e-319"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> také implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> .</span><span class="sxs-lookup"><span data-stu-id="7207e-319"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="7207e-320">Výchozí implementace pro <xref:Microsoft.Extensions.Options.IOptionsFactory%601> má logiku odpovídajícím způsobem použít.</span><span class="sxs-lookup"><span data-stu-id="7207e-320">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="7207e-321">`null`Pojmenovaná možnost se používá pro cílení na všechny pojmenované instance místo konkrétní pojmenované instance ( <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> a <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> použití této konvence).</span><span class="sxs-lookup"><span data-stu-id="7207e-321">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="7207e-322">Rozhraní API pro OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="7207e-322">OptionsBuilder API</span></span>

<span data-ttu-id="7207e-323"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> se používá ke konfiguraci `TOptions` instancí.</span><span class="sxs-lookup"><span data-stu-id="7207e-323"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="7207e-324">`OptionsBuilder` zjednodušuje vytváření pojmenovaných možností, protože je jenom jedním parametrem počátečního `AddOptions<TOptions>(string optionsName)` volání, takže se neobjeví ve všech dalších voláních.</span><span class="sxs-lookup"><span data-stu-id="7207e-324">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="7207e-325">Ověřování možností a `ConfigureOptions` přetížení, která přijímají závislosti služby, jsou k dispozici pouze prostřednictvím `OptionsBuilder` .</span><span class="sxs-lookup"><span data-stu-id="7207e-325">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="7207e-326">Ke konfiguraci možností použijte DI Services.</span><span class="sxs-lookup"><span data-stu-id="7207e-326">Use DI services to configure options</span></span>

<span data-ttu-id="7207e-327">K dalším službám můžete přistupovat pomocí injektáže závislosti při konfiguraci možností dvěma způsoby:</span><span class="sxs-lookup"><span data-stu-id="7207e-327">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="7207e-328">Předejte delegáta konfigurace [na](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) [OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="7207e-328">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="7207e-329">[OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1) poskytuje přetížení [Konfigurace](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) , která umožňují použít až pět služeb ke konfiguraci možností:</span><span class="sxs-lookup"><span data-stu-id="7207e-329">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="7207e-330">Vytvořte vlastní typ, který implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> nebo <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> a zaregistruje typ jako službu.</span><span class="sxs-lookup"><span data-stu-id="7207e-330">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="7207e-331">Doporučujeme předat delegáta [konfigurace, protože](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)vytvoření služby je složitější.</span><span class="sxs-lookup"><span data-stu-id="7207e-331">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="7207e-332">Vytvoření vlastního typu je ekvivalentní s tím, k čemu architektura slouží při použití možnosti [Konfigurovat](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="7207e-332">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="7207e-333">Volání metody [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) provede přechodný obecný <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> , který má konstruktor, který přijímá obecné zadané typy služeb.</span><span class="sxs-lookup"><span data-stu-id="7207e-333">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="7207e-334">Ověřování možností</span><span class="sxs-lookup"><span data-stu-id="7207e-334">Options validation</span></span>

<span data-ttu-id="7207e-335">Ověřování možností umožňuje ověřit možnosti při konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="7207e-335">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="7207e-336">Zavolejte `Validate` na metodu ověřování, která vrátí, `true` Pokud jsou možnosti platné, a `false` Pokud nejsou platné:</span><span class="sxs-lookup"><span data-stu-id="7207e-336">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

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

<span data-ttu-id="7207e-337">Předchozí příklad nastaví instanci pojmenované možnosti na `optionalOptionsName` .</span><span class="sxs-lookup"><span data-stu-id="7207e-337">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="7207e-338">Instance výchozích možností je `Options.DefaultName` .</span><span class="sxs-lookup"><span data-stu-id="7207e-338">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="7207e-339">Ověřování se spustí při vytvoření instance Options.</span><span class="sxs-lookup"><span data-stu-id="7207e-339">Validation runs when the options instance is created.</span></span> <span data-ttu-id="7207e-340">Je zaručeno, že instance možností před prvním tím, než se přistupuje k ověření.</span><span class="sxs-lookup"><span data-stu-id="7207e-340">An options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="7207e-341">Ověřování možností nechrání před úpravami možností po vytvoření instance Options.</span><span class="sxs-lookup"><span data-stu-id="7207e-341">Options validation doesn't guard against options modifications after the options instance is created.</span></span> <span data-ttu-id="7207e-342">Například `IOptionsSnapshot` Možnosti se vytváří a ověřují jednou za požadavek při prvním použití možností.</span><span class="sxs-lookup"><span data-stu-id="7207e-342">For example, `IOptionsSnapshot` options are created and validated once per request when the options are first accessed.</span></span> <span data-ttu-id="7207e-343">Tyto `IOptionsSnapshot` Možnosti se znovu neověřují u dalších pokusů o přístup *pro stejný požadavek*.</span><span class="sxs-lookup"><span data-stu-id="7207e-343">The `IOptionsSnapshot` options aren't validated again on subsequent access attempts *for the same request*.</span></span>

<span data-ttu-id="7207e-344">`Validate`Metoda přijímá `Func<TOptions, bool>` .</span><span class="sxs-lookup"><span data-stu-id="7207e-344">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="7207e-345">Pro úplné přizpůsobení ověřování, implementujte `IValidateOptions<TOptions>` , což umožňuje:</span><span class="sxs-lookup"><span data-stu-id="7207e-345">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="7207e-346">Ověřování více typů možností: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="7207e-346">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="7207e-347">Ověření, které závisí na jiném typu možnosti: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="7207e-347">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="7207e-348">`IValidateOptions` ověří</span><span class="sxs-lookup"><span data-stu-id="7207e-348">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="7207e-349">Konkrétní instance pojmenované možnosti.</span><span class="sxs-lookup"><span data-stu-id="7207e-349">A specific named options instance.</span></span>
* <span data-ttu-id="7207e-350">Všechny možnosti `name` , pokud je `null` .</span><span class="sxs-lookup"><span data-stu-id="7207e-350">All options when `name` is `null`.</span></span>

<span data-ttu-id="7207e-351">Vraťte se `ValidateOptionsResult` z vaší implementace rozhraní:</span><span class="sxs-lookup"><span data-stu-id="7207e-351">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="7207e-352">Ověřování na základě datových poznámek je k dispozici v balíčku [Microsoft. Extensions. Options. DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) pomocí volání <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> metody on `OptionsBuilder<TOptions>` .</span><span class="sxs-lookup"><span data-stu-id="7207e-352">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="7207e-353">`Microsoft.Extensions.Options.DataAnnotations` je součástí [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="7207e-353">`Microsoft.Extensions.Options.DataAnnotations` is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

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

<span data-ttu-id="7207e-354">Ověření Eager (při spuštění selže) je v budoucí verzi zváženo.</span><span class="sxs-lookup"><span data-stu-id="7207e-354">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="7207e-355">Možnosti po konfiguraci</span><span class="sxs-lookup"><span data-stu-id="7207e-355">Options post-configuration</span></span>

<span data-ttu-id="7207e-356">Nastavení po konfiguraci nastavte na <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> .</span><span class="sxs-lookup"><span data-stu-id="7207e-356">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="7207e-357">Po dokončení všech konfigurací se spustí po konfiguraci <xref:Microsoft.Extensions.Options.IConfigureOptions%601> :</span><span class="sxs-lookup"><span data-stu-id="7207e-357">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="7207e-358"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> je k dispozici pro nastavení po konfiguraci s názvem:</span><span class="sxs-lookup"><span data-stu-id="7207e-358"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="7207e-359">Použijte <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> k následné konfiguraci všech instancí konfigurace:</span><span class="sxs-lookup"><span data-stu-id="7207e-359">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="7207e-360">Přístup k možnostem při spuštění</span><span class="sxs-lookup"><span data-stu-id="7207e-360">Accessing options during startup</span></span>

<span data-ttu-id="7207e-361"><xref:Microsoft.Extensions.Options.IOptions%601> a <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> lze jej použít v `Startup.Configure` , protože služby jsou vytvořeny před spuštěním `Configure` metody.</span><span class="sxs-lookup"><span data-stu-id="7207e-361"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="7207e-362">Nepoužívejte <xref:Microsoft.Extensions.Options.IOptions%601> nebo <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="7207e-362">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="7207e-363">Stav nekonzistentních možností může existovat v důsledku řazení registrací služby.</span><span class="sxs-lookup"><span data-stu-id="7207e-363">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="7207e-364">Vzor možností používá třídy, které reprezentují skupiny souvisejících nastavení.</span><span class="sxs-lookup"><span data-stu-id="7207e-364">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="7207e-365">Když jsou [nastavení konfigurace](xref:fundamentals/configuration/index) izolována scénářem do samostatných tříd, aplikace dodržuje dva důležité principy technického řízení:</span><span class="sxs-lookup"><span data-stu-id="7207e-365">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="7207e-366">[Princip odděleníní rozhraní (ISP) nebo zapouzdření](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): scénáře (třídy) závislé na nastavení konfigurace závisejí pouze na nastavení konfigurace, která používají.</span><span class="sxs-lookup"><span data-stu-id="7207e-366">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="7207e-367">[Oddělení obav](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): nastavení různých částí aplikace nejsou závislá ani vzájemně závislá na sobě.</span><span class="sxs-lookup"><span data-stu-id="7207e-367">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="7207e-368">Možnosti také poskytují mechanismus pro ověření konfiguračních dat.</span><span class="sxs-lookup"><span data-stu-id="7207e-368">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="7207e-369">Další informace najdete v části [ověřování možností](#options-validation) .</span><span class="sxs-lookup"><span data-stu-id="7207e-369">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="7207e-370">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7207e-370">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7207e-371">Předpoklady</span><span class="sxs-lookup"><span data-stu-id="7207e-371">Prerequisites</span></span>

<span data-ttu-id="7207e-372">Odkaz na balíček [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) nebo přidejte odkaz na balíček do balíčku [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) .</span><span class="sxs-lookup"><span data-stu-id="7207e-372">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="7207e-373">Možnosti rozhraní</span><span class="sxs-lookup"><span data-stu-id="7207e-373">Options interfaces</span></span>

<span data-ttu-id="7207e-374"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> slouží k načtení možností a správě oznámení možností pro `TOptions` instance.</span><span class="sxs-lookup"><span data-stu-id="7207e-374"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="7207e-375"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> podporuje následující scénáře:</span><span class="sxs-lookup"><span data-stu-id="7207e-375"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="7207e-376">Oznámení změny</span><span class="sxs-lookup"><span data-stu-id="7207e-376">Change notifications</span></span>
* [<span data-ttu-id="7207e-377">Pojmenované možnosti</span><span class="sxs-lookup"><span data-stu-id="7207e-377">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="7207e-378">Znovu Načítatelné konfigurace</span><span class="sxs-lookup"><span data-stu-id="7207e-378">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="7207e-379">Neplatných selektivních možností ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> )</span><span class="sxs-lookup"><span data-stu-id="7207e-379">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="7207e-380">Scénáře po [konfiguraci](#options-post-configuration) umožňují nastavit nebo změnit možnosti po <xref:Microsoft.Extensions.Options.IConfigureOptions%601> výskytu všech konfigurací.</span><span class="sxs-lookup"><span data-stu-id="7207e-380">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="7207e-381"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> zodpovídá za vytváření nových instancí možností.</span><span class="sxs-lookup"><span data-stu-id="7207e-381"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="7207e-382">Má jedinou <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> metodu.</span><span class="sxs-lookup"><span data-stu-id="7207e-382">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="7207e-383">Výchozí implementace přebírá všechny <xref:Microsoft.Extensions.Options.IConfigureOptions%601> Konfigurace a <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> a spustí nejprve všechny konfigurace, následované po konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="7207e-383">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="7207e-384">Rozlišuje mezi <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> a <xref:Microsoft.Extensions.Options.IConfigureOptions%601> a volá pouze příslušné rozhraní.</span><span class="sxs-lookup"><span data-stu-id="7207e-384">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="7207e-385"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> je používána <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> pro ukládání instancí do mezipaměti `TOptions` .</span><span class="sxs-lookup"><span data-stu-id="7207e-385"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="7207e-386"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>Zruší platnost instancí možností v monitorování tak, aby se hodnota přepočítala ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*> ).</span><span class="sxs-lookup"><span data-stu-id="7207e-386">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="7207e-387">Hodnoty lze ručně zavést pomocí <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> .</span><span class="sxs-lookup"><span data-stu-id="7207e-387">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="7207e-388"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*>Metoda se používá v případě, že by měly být všechny pojmenované instance znovu vytvořeny na vyžádání.</span><span class="sxs-lookup"><span data-stu-id="7207e-388">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="7207e-389"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> je užitečná ve scénářích, kde by se měly přepočítat možnosti pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="7207e-389"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="7207e-390">Další informace najdete v části [opětovné načtení konfiguračních dat pomocí IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) .</span><span class="sxs-lookup"><span data-stu-id="7207e-390">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="7207e-391"><xref:Microsoft.Extensions.Options.IOptions%601> dá se použít k podpoře možností.</span><span class="sxs-lookup"><span data-stu-id="7207e-391"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="7207e-392">Nicméně <xref:Microsoft.Extensions.Options.IOptions%601> nepodporuje předchozí scénáře <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> .</span><span class="sxs-lookup"><span data-stu-id="7207e-392">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="7207e-393">Můžete pokračovat <xref:Microsoft.Extensions.Options.IOptions%601> v používání existujících architektur a knihoven, které již rozhraní používají, <xref:Microsoft.Extensions.Options.IOptions%601> a nevyžadují scénáře, které poskytuje <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> .</span><span class="sxs-lookup"><span data-stu-id="7207e-393">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="7207e-394">Konfigurace obecných možností</span><span class="sxs-lookup"><span data-stu-id="7207e-394">General options configuration</span></span>

<span data-ttu-id="7207e-395">Konfigurace obecných možností se v ukázkové aplikaci ukazuje jako příklad 1.</span><span class="sxs-lookup"><span data-stu-id="7207e-395">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="7207e-396">Třída Options musí být neabstraktní s veřejným konstruktorem bez parametrů.</span><span class="sxs-lookup"><span data-stu-id="7207e-396">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="7207e-397">Následující třída `MyOptions` má dvě vlastnosti, `Option1` a `Option2` .</span><span class="sxs-lookup"><span data-stu-id="7207e-397">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="7207e-398">Nastavení výchozích hodnot je volitelné, ale konstruktor třídy v následujícím příkladu nastaví výchozí hodnotu `Option1` .</span><span class="sxs-lookup"><span data-stu-id="7207e-398">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="7207e-399">`Option2` má výchozí hodnotu nastavenou pomocí přímé inicializace vlastnosti (*modely/MyOptions. cs*):</span><span class="sxs-lookup"><span data-stu-id="7207e-399">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="7207e-400">`MyOptions`Třída je přidána do kontejneru služby s <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> konfigurací a je vázána na konfiguraci:</span><span class="sxs-lookup"><span data-stu-id="7207e-400">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="7207e-401">Následující model stránky používá [vkládání závislostí konstruktoru](xref:mvc/controllers/dependency-injection) s <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> pro přístup k nastavení (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="7207e-401">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="7207e-402">Soubor *appsettings.jsv* ukázce určuje hodnoty pro `option1` a `option2` :</span><span class="sxs-lookup"><span data-stu-id="7207e-402">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="7207e-403">Při spuštění aplikace `OnGet` vrátí metoda modelu stránky řetězec zobrazující hodnoty třídy možností:</span><span class="sxs-lookup"><span data-stu-id="7207e-403">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="7207e-404">Když použijete vlastní <xref:System.Configuration.ConfigurationBuilder> nastavení pro načtení konfigurace možností z konfiguračního souboru, zkontrolujte, jestli je správně nastavená základní cesta:</span><span class="sxs-lookup"><span data-stu-id="7207e-404">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="7207e-405">Při načítání konfigurace možností ze souboru s nastavením prostřednictvím je explicitně nutné nastavit základní cestu <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="7207e-405">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="7207e-406">Konfigurace jednoduchých možností pomocí delegáta</span><span class="sxs-lookup"><span data-stu-id="7207e-406">Configure simple options with a delegate</span></span>

<span data-ttu-id="7207e-407">Konfigurace jednoduchých možností s delegátem je znázorněna jako příklad 2 v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="7207e-407">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="7207e-408">K nastavení hodnot možností použijte delegáta.</span><span class="sxs-lookup"><span data-stu-id="7207e-408">Use a delegate to set options values.</span></span> <span data-ttu-id="7207e-409">Ukázková aplikace používá `MyOptionsWithDelegateConfig` třídu (*modely/MyOptionsWithDelegateConfig. cs*):</span><span class="sxs-lookup"><span data-stu-id="7207e-409">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="7207e-410">V následujícím kódu <xref:Microsoft.Extensions.Options.IConfigureOptions%601> je do kontejneru služby přidána druhá služba.</span><span class="sxs-lookup"><span data-stu-id="7207e-410">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="7207e-411">Pomocí delegáta konfiguruje vazbu s `MyOptionsWithDelegateConfig` :</span><span class="sxs-lookup"><span data-stu-id="7207e-411">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="7207e-412">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="7207e-412">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="7207e-413">Můžete přidat více poskytovatelů konfigurace.</span><span class="sxs-lookup"><span data-stu-id="7207e-413">You can add multiple configuration providers.</span></span> <span data-ttu-id="7207e-414">Poskytovatelé konfigurace jsou k dispozici z balíčků NuGet a jsou aplikováni v pořadí, v jakém jsou registrováni.</span><span class="sxs-lookup"><span data-stu-id="7207e-414">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="7207e-415">Další informace naleznete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="7207e-415">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="7207e-416">Každé volání <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> přidá <xref:Microsoft.Extensions.Options.IConfigureOptions%601> službu do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="7207e-416">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="7207e-417">V předchozím příkladu jsou hodnoty `Option1` a uvedeny `Option2` v *appsettings.jsna*, ale hodnoty `Option1` a `Option2` jsou přepsány nakonfigurovaným delegátem.</span><span class="sxs-lookup"><span data-stu-id="7207e-417">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="7207e-418">Je-li povolena více než jedna služba konfigurace, je v posledním zdroji konfigurace zadaný *Server WINS* a nastavena hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="7207e-418">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="7207e-419">Při spuštění aplikace `OnGet` vrátí metoda modelu stránky řetězec zobrazující hodnoty třídy možností:</span><span class="sxs-lookup"><span data-stu-id="7207e-419">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="7207e-420">Konfigurace podNastavení</span><span class="sxs-lookup"><span data-stu-id="7207e-420">Suboptions configuration</span></span>

<span data-ttu-id="7207e-421">Konfigurace dílčích možností je v ukázkové aplikaci znázorněna jako příklad 3.</span><span class="sxs-lookup"><span data-stu-id="7207e-421">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="7207e-422">Aplikace by měly vytvořit třídy možností, které se týkají konkrétních skupin scénářů (tříd) v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="7207e-422">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="7207e-423">Části aplikace, které vyžadují konfigurační hodnoty, by měly mít přístup jenom k hodnotám konfigurace, které používají.</span><span class="sxs-lookup"><span data-stu-id="7207e-423">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="7207e-424">Při vytváření vazby mezi možnostmi konfigurace jsou jednotlivé vlastnosti v typu možnosti vázány na konfigurační klíč formuláře `property[:sub-property:]` .</span><span class="sxs-lookup"><span data-stu-id="7207e-424">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="7207e-425">Například `MyOptions.Option1` vlastnost je svázána s klíčem `Option1` , který je načten z `option1` vlastnosti v *appsettings.jsna*.</span><span class="sxs-lookup"><span data-stu-id="7207e-425">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="7207e-426">V následujícím kódu <xref:Microsoft.Extensions.Options.IConfigureOptions%601> je do kontejneru služby přidána třetí služba.</span><span class="sxs-lookup"><span data-stu-id="7207e-426">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="7207e-427">Váže `MySubOptions` se k části `subsection` *appsettings.jsv* souboru:</span><span class="sxs-lookup"><span data-stu-id="7207e-427">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="7207e-428">`GetSection`Metoda vyžaduje <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> obor názvů.</span><span class="sxs-lookup"><span data-stu-id="7207e-428">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="7207e-429">*appsettings.jsukázek v* souboru definuje `subsection` člena s klíči pro `suboption1` a `suboption2` :</span><span class="sxs-lookup"><span data-stu-id="7207e-429">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="7207e-430">`MySubOptions`Třída definuje vlastnosti `SubOption1` a `SubOption2` , aby obsahovala hodnoty možností (*modely/MySubOptions. cs*):</span><span class="sxs-lookup"><span data-stu-id="7207e-430">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="7207e-431">Metoda modelu stránky `OnGet` vrátí řetězec s hodnotami možností (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="7207e-431">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="7207e-432">Při spuštění aplikace `OnGet` vrátí metoda řetězec zobrazující hodnoty třídy dílčích možností:</span><span class="sxs-lookup"><span data-stu-id="7207e-432">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="7207e-433">Možnosti poskytované modelem zobrazení nebo vkládáním přímých zobrazení</span><span class="sxs-lookup"><span data-stu-id="7207e-433">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="7207e-434">Možnosti poskytované modelem zobrazení nebo vkládáním přímých zobrazení jsou v ukázkové aplikaci znázorněné jako příklad 4.</span><span class="sxs-lookup"><span data-stu-id="7207e-434">Options provided by a view model or with direct view injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="7207e-435">Možnosti lze zadat v modelu zobrazení nebo <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> přímým vložením do zobrazení (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="7207e-435">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="7207e-436">Ukázková aplikace ukazuje, jak vložit `IOptionsMonitor<MyOptions>` pomocí `@inject` direktivy:</span><span class="sxs-lookup"><span data-stu-id="7207e-436">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="7207e-437">Po spuštění aplikace se hodnoty možností zobrazí na vykreslené stránce:</span><span class="sxs-lookup"><span data-stu-id="7207e-437">When the app is run, the options values are shown in the rendered page:</span></span>

![Hodnoty možností možnost1: value1_from_json a Možnost2:-1 jsou načteny z modelu a zavstřikem do zobrazení.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="7207e-439">Znovu načíst konfigurační data pomocí IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="7207e-439">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="7207e-440">Opětovné načtení konfiguračních dat pomocí <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> je v ukázkové aplikaci znázorněno v příkladu 5.</span><span class="sxs-lookup"><span data-stu-id="7207e-440">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="7207e-441"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> podporuje opětovné načítání možností s minimální režií zpracování.</span><span class="sxs-lookup"><span data-stu-id="7207e-441"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="7207e-442">Možnosti jsou vypočítány jednou za požadavek při otevření a ukládání do mezipaměti po dobu života žádosti.</span><span class="sxs-lookup"><span data-stu-id="7207e-442">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="7207e-443">Následující příklad ukazuje, jak <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> se vytvoří nový po *appsettings.js* změny (*pages/index. cshtml. cs*).</span><span class="sxs-lookup"><span data-stu-id="7207e-443">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="7207e-444">Více požadavků na server vrací konstantní hodnoty, které poskytuje *appsettings.jsv* souboru, dokud nedojde ke změně souboru a obnovení konfigurace.</span><span class="sxs-lookup"><span data-stu-id="7207e-444">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="7207e-445">Následující obrázek ukazuje počáteční `option1` `option2` hodnoty a hodnoty načtené z *appsettings.js* souboru:</span><span class="sxs-lookup"><span data-stu-id="7207e-445">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="7207e-446">Změňte hodnoty v *appsettings.jsv* souboru na `value1_from_json UPDATED` a `200` .</span><span class="sxs-lookup"><span data-stu-id="7207e-446">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="7207e-447">Uložte *appsettings.jsdo* souboru.</span><span class="sxs-lookup"><span data-stu-id="7207e-447">Save the *appsettings.json* file.</span></span> <span data-ttu-id="7207e-448">Aktualizujte prohlížeč, aby se zobrazily hodnoty možností, které jsou aktualizované:</span><span class="sxs-lookup"><span data-stu-id="7207e-448">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="7207e-449">Podpora pojmenovaných možností s IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="7207e-449">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="7207e-450">Pojmenovaná podpora s názvem <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> je znázorněna jako příklad 6 v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="7207e-450">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="7207e-451">Podpora pojmenovaných možností umožňuje aplikaci rozlišovat konfigurace pojmenovaných možností.</span><span class="sxs-lookup"><span data-stu-id="7207e-451">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="7207e-452">V ukázkové aplikaci jsou pojmenované možnosti deklarovány s [OptionsServiceCollectionExtensions.Configurovat](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), která volá [ConfigureNamedOptions \<TOptions> . Nakonfigurujte](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) metodu rozšíření.</span><span class="sxs-lookup"><span data-stu-id="7207e-452">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="7207e-453">U pojmenovaných možností se rozlišují velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="7207e-453">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="7207e-454">Ukázková aplikace přistupuje k pojmenovaným možnostem <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="7207e-454">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="7207e-455">Když spustíte ukázkovou aplikaci, vrátí se pojmenované možnosti:</span><span class="sxs-lookup"><span data-stu-id="7207e-455">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="7207e-456">`named_options_1` hodnoty se poskytují z konfigurace, které se načítají z *appsettings.jsv* souboru.</span><span class="sxs-lookup"><span data-stu-id="7207e-456">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="7207e-457">`named_options_2` hodnoty poskytuje:</span><span class="sxs-lookup"><span data-stu-id="7207e-457">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="7207e-458">`named_options_2`Delegát v `ConfigureServices` pro `Option1` .</span><span class="sxs-lookup"><span data-stu-id="7207e-458">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="7207e-459">Výchozí hodnota `Option2` poskytnutá `MyOptions` třídou</span><span class="sxs-lookup"><span data-stu-id="7207e-459">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="7207e-460">Konfigurace všech možností pomocí metody ConfigureAll</span><span class="sxs-lookup"><span data-stu-id="7207e-460">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="7207e-461">Nakonfigurujte všechny instance možností pomocí <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> metody.</span><span class="sxs-lookup"><span data-stu-id="7207e-461">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="7207e-462">Následující kód konfiguruje `Option1` pro všechny instance konfigurace se společnou hodnotou.</span><span class="sxs-lookup"><span data-stu-id="7207e-462">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="7207e-463">Do metody přidejte následující kód ručně `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="7207e-463">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="7207e-464">Spuštění ukázkové aplikace po přidání kódu vytvoří následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="7207e-464">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="7207e-465">Všechny možnosti jsou pojmenované instance.</span><span class="sxs-lookup"><span data-stu-id="7207e-465">All options are named instances.</span></span> <span data-ttu-id="7207e-466">Existující <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instance jsou považovány za cílení `Options.DefaultName` instance, což je `string.Empty` .</span><span class="sxs-lookup"><span data-stu-id="7207e-466">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="7207e-467"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> také implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> .</span><span class="sxs-lookup"><span data-stu-id="7207e-467"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="7207e-468">Výchozí implementace pro <xref:Microsoft.Extensions.Options.IOptionsFactory%601> má logiku odpovídajícím způsobem použít.</span><span class="sxs-lookup"><span data-stu-id="7207e-468">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="7207e-469">`null`Pojmenovaná možnost se používá pro cílení na všechny pojmenované instance místo konkrétní pojmenované instance ( <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> a <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> použití této konvence).</span><span class="sxs-lookup"><span data-stu-id="7207e-469">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="7207e-470">Rozhraní API pro OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="7207e-470">OptionsBuilder API</span></span>

<span data-ttu-id="7207e-471"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> se používá ke konfiguraci `TOptions` instancí.</span><span class="sxs-lookup"><span data-stu-id="7207e-471"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="7207e-472">`OptionsBuilder` zjednodušuje vytváření pojmenovaných možností, protože je jenom jedním parametrem počátečního `AddOptions<TOptions>(string optionsName)` volání, takže se neobjeví ve všech dalších voláních.</span><span class="sxs-lookup"><span data-stu-id="7207e-472">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="7207e-473">Ověřování možností a `ConfigureOptions` přetížení, která přijímají závislosti služby, jsou k dispozici pouze prostřednictvím `OptionsBuilder` .</span><span class="sxs-lookup"><span data-stu-id="7207e-473">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="7207e-474">Ke konfiguraci možností použijte DI Services.</span><span class="sxs-lookup"><span data-stu-id="7207e-474">Use DI services to configure options</span></span>

<span data-ttu-id="7207e-475">K dalším službám můžete přistupovat pomocí injektáže závislosti při konfiguraci možností dvěma způsoby:</span><span class="sxs-lookup"><span data-stu-id="7207e-475">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="7207e-476">Předejte delegáta konfigurace [na](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) [OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="7207e-476">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="7207e-477">[OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1) poskytuje přetížení [Konfigurace](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) , která umožňují použít až pět služeb ke konfiguraci možností:</span><span class="sxs-lookup"><span data-stu-id="7207e-477">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="7207e-478">Vytvořte vlastní typ, který implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> nebo <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> a zaregistruje typ jako službu.</span><span class="sxs-lookup"><span data-stu-id="7207e-478">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="7207e-479">Doporučujeme předat delegáta [konfigurace, protože](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)vytvoření služby je složitější.</span><span class="sxs-lookup"><span data-stu-id="7207e-479">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="7207e-480">Vytvoření vlastního typu je ekvivalentní s tím, k čemu architektura slouží při použití možnosti [Konfigurovat](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="7207e-480">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="7207e-481">Volání metody [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) provede přechodný obecný <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> , který má konstruktor, který přijímá obecné zadané typy služeb.</span><span class="sxs-lookup"><span data-stu-id="7207e-481">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-post-configuration"></a><span data-ttu-id="7207e-482">Možnosti po konfiguraci</span><span class="sxs-lookup"><span data-stu-id="7207e-482">Options post-configuration</span></span>

<span data-ttu-id="7207e-483">Nastavení po konfiguraci nastavte na <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> .</span><span class="sxs-lookup"><span data-stu-id="7207e-483">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="7207e-484">Po dokončení všech konfigurací se spustí po konfiguraci <xref:Microsoft.Extensions.Options.IConfigureOptions%601> :</span><span class="sxs-lookup"><span data-stu-id="7207e-484">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="7207e-485"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> je k dispozici pro nastavení po konfiguraci s názvem:</span><span class="sxs-lookup"><span data-stu-id="7207e-485"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="7207e-486">Použijte <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> k následné konfiguraci všech instancí konfigurace:</span><span class="sxs-lookup"><span data-stu-id="7207e-486">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="7207e-487">Přístup k možnostem při spuštění</span><span class="sxs-lookup"><span data-stu-id="7207e-487">Accessing options during startup</span></span>

<span data-ttu-id="7207e-488"><xref:Microsoft.Extensions.Options.IOptions%601> a <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> lze jej použít v `Startup.Configure` , protože služby jsou vytvořeny před spuštěním `Configure` metody.</span><span class="sxs-lookup"><span data-stu-id="7207e-488"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="7207e-489">Nepoužívejte <xref:Microsoft.Extensions.Options.IOptions%601> nebo <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="7207e-489">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="7207e-490">Stav nekonzistentních možností může existovat v důsledku řazení registrací služby.</span><span class="sxs-lookup"><span data-stu-id="7207e-490">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="7207e-491">Další materiály</span><span class="sxs-lookup"><span data-stu-id="7207e-491">Additional resources</span></span>

* <xref:fundamentals/configuration/index>

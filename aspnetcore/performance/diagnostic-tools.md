---
title: Nástroje pro diagnostiku výkonu
author: mjrousos
description: Užitečné nástroje pro diagnostiku problémů s výkonem v aplikacích ASP.NET Core.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: performance/diagnostic-tools
ms.openlocfilehash: 82c724ec647dfe5547db775ebaf8c2479bb258bd
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775853"
---
# <a name="performance-diagnostic-tools"></a><span data-ttu-id="09228-103">Diagnostické nástroje výkonu</span><span class="sxs-lookup"><span data-stu-id="09228-103">Performance Diagnostic Tools</span></span>

<span data-ttu-id="09228-104">O [Jan Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="09228-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="09228-105">Tento článek obsahuje seznam nástrojů pro diagnostiku problémů s výkonem v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="09228-105">This article lists tools for diagnosing performance issues in ASP.NET Core.</span></span>

## <a name="visual-studio-diagnostic-tools"></a><span data-ttu-id="09228-106">Diagnostické nástroje sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="09228-106">Visual Studio Diagnostic Tools</span></span>

<span data-ttu-id="09228-107">K zahájení zkoumání problémů s výkonem je vhodné místo pro [profilaci a diagnostické nástroje](/visualstudio/profiling) , které jsou součástí sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="09228-107">The [profiling and diagnostic tools](/visualstudio/profiling) built into Visual Studio are a good place to start investigating performance issues.</span></span> <span data-ttu-id="09228-108">Tyto nástroje jsou výkonné a praktické pro použití ve vývojovém prostředí sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="09228-108">These tools are powerful and convenient to use from the Visual Studio development environment.</span></span> <span data-ttu-id="09228-109">Nástroje umožňují analýzu využití CPU, využití paměti a událostí výkonu v aplikacích ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="09228-109">The tooling allows analysis of CPU usage, memory usage, and performance events in ASP.NET Core apps.</span></span> <span data-ttu-id="09228-110">Předdefinovaná aplikace usnadňuje profilaci v době vývoje.</span><span class="sxs-lookup"><span data-stu-id="09228-110">Being built-in makes profiling easy at development time.</span></span>

<span data-ttu-id="09228-111">Další informace jsou k dispozici v [dokumentaci k sadě Visual Studio](/visualstudio/profiling/profiling-overview).</span><span class="sxs-lookup"><span data-stu-id="09228-111">More information is available in [Visual Studio documentation](/visualstudio/profiling/profiling-overview).</span></span>

## <a name="application-insights"></a><span data-ttu-id="09228-112">Application Insights</span><span class="sxs-lookup"><span data-stu-id="09228-112">Application Insights</span></span>

<span data-ttu-id="09228-113">[Application Insights](/azure/application-insights/app-insights-overview) poskytuje podrobné údaje o výkonu vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="09228-113">[Application Insights](/azure/application-insights/app-insights-overview) provides in-depth performance data for your app.</span></span> <span data-ttu-id="09228-114">Application Insights automaticky shromažďuje data podle míry odezvy, míry selhání, doby odezvy závislostí a dalších.</span><span class="sxs-lookup"><span data-stu-id="09228-114">Application Insights automatically collects data on response rates, failure rates, dependency response times, and more.</span></span> <span data-ttu-id="09228-115">Application Insights podporuje protokolování vlastních událostí a metrik, které jsou specifické pro vaši aplikaci.</span><span class="sxs-lookup"><span data-stu-id="09228-115">Application Insights supports logging custom events and metrics specific to your app.</span></span>

<span data-ttu-id="09228-116">Azure Application Insights poskytuje několik způsobů, jak poskytnout přehledy o monitorovaných aplikacích:</span><span class="sxs-lookup"><span data-stu-id="09228-116">Azure Application Insights provides multiple ways to give insights on monitored apps:</span></span>

- <span data-ttu-id="09228-117">[Mapa aplikace](/azure/application-insights/app-insights-app-map) – pomáhá odhalit slabá místa výkonu nebo neúspěšné pokusy v rámci všech komponent distribuovaných aplikací.</span><span class="sxs-lookup"><span data-stu-id="09228-117">[Application Map](/azure/application-insights/app-insights-app-map) – helps spot performance bottlenecks or failure hot-spots across all components of distributed apps.</span></span>
- <span data-ttu-id="09228-118">[Azure Průzkumník metrik](/azure/azure-monitor/platform/metrics-getting-started) je komponentou portál Microsoft Azure, která umožňuje vykreslovat grafy, vizuálně korelovat trendy a prozkoumat špičky a rozmítají metriky v hodnotách metriky.</span><span class="sxs-lookup"><span data-stu-id="09228-118">[Azure Metrics Explorer](/azure/azure-monitor/platform/metrics-getting-started) is a component of the Microsoft Azure portal that allows plotting charts, visually correlating trends, and investigating spikes and dips in metrics' values.</span></span>
- <span data-ttu-id="09228-119">[Okno výkon na portálu Application Insights](/azure/application-insights/app-insights-tutorial-performance):</span><span class="sxs-lookup"><span data-stu-id="09228-119">[Performance blade in Application Insights portal](/azure/application-insights/app-insights-tutorial-performance):</span></span>

  - <span data-ttu-id="09228-120">Zobrazuje podrobnosti o výkonu pro různé operace v monitorované aplikaci.</span><span class="sxs-lookup"><span data-stu-id="09228-120">Shows performance details for different operations in the monitored app.</span></span>
  - <span data-ttu-id="09228-121">Umožňuje přechod na jedinou operaci pro kontrolu všech částí nebo závislostí, které přispívají k dlouhou dobu trvání.</span><span class="sxs-lookup"><span data-stu-id="09228-121">Allows drilling into a single operation to check all parts/dependencies that contribute to a long duration.</span></span>
  - <span data-ttu-id="09228-122">Profiler můžete z tohoto místa vyvolat, abyste mohli shromažďovat trasování výkonu na vyžádání.</span><span class="sxs-lookup"><span data-stu-id="09228-122">Profiler can be invoked from here to collect performance traces on-demand.</span></span>

- <span data-ttu-id="09228-123">[Azure Application Insights Profiler](/azure/azure-monitor/app/profiler) umožňuje pravidelné profilování aplikací .NET a na vyžádání.</span><span class="sxs-lookup"><span data-stu-id="09228-123">[Azure Application Insights Profiler](/azure/azure-monitor/app/profiler) allows regular and on-demand profiling of .NET apps.</span></span>  <span data-ttu-id="09228-124">Azure Portal zobrazuje zaznamenaná trasování výkonu se zásobníky volání a Hot Paths.</span><span class="sxs-lookup"><span data-stu-id="09228-124">Azure portal shows captured performance traces with call stacks and hot paths.</span></span> <span data-ttu-id="09228-125">Trasovací soubory je také možné stáhnout pro hlubší analýzu pomocí PerfView.</span><span class="sxs-lookup"><span data-stu-id="09228-125">The trace files can also be downloaded for deeper analysis using PerfView.</span></span>

<span data-ttu-id="09228-126">Application Insights lze použít v různých prostředích:</span><span class="sxs-lookup"><span data-stu-id="09228-126">Application Insights can be used in a variety environments:</span></span>

- <span data-ttu-id="09228-127">Optimalizováno pro práci v Azure.</span><span class="sxs-lookup"><span data-stu-id="09228-127">Optimized to work in Azure.</span></span>
- <span data-ttu-id="09228-128">Funguje v produkčním, vývojovém a přípravném prostředí.</span><span class="sxs-lookup"><span data-stu-id="09228-128">Works in production, development, and staging.</span></span>
- <span data-ttu-id="09228-129">Funguje místně ze sady [Visual Studio](/azure/application-insights/app-insights-visual-studio) nebo v jiných hostujících prostředích.</span><span class="sxs-lookup"><span data-stu-id="09228-129">Works locally from [Visual Studio](/azure/application-insights/app-insights-visual-studio) or in other hosting environments.</span></span>

<span data-ttu-id="09228-130">Další informace najdete v tématu [Application Insights pro ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="09228-130">For more information, see [Application Insights for ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="perfview"></a><span data-ttu-id="09228-131">PerfView</span><span class="sxs-lookup"><span data-stu-id="09228-131">PerfView</span></span>

<span data-ttu-id="09228-132">[PerfView](https://github.com/Microsoft/perfview) je nástroj pro analýzu výkonu vytvořený týmem .NET konkrétně pro diagnostiku problémů s výkonem .NET.</span><span class="sxs-lookup"><span data-stu-id="09228-132">[PerfView](https://github.com/Microsoft/perfview) is a performance analysis tool created by the .NET team specifically for diagnosing .NET performance issues.</span></span> <span data-ttu-id="09228-133">PerfView umožňuje analýzu využití CPU, chování paměti a GC, událostí výkonu a času chodu na zdi.</span><span class="sxs-lookup"><span data-stu-id="09228-133">PerfView allows analysis of CPU usage, memory and GC behavior, performance events, and wall clock time.</span></span>

<span data-ttu-id="09228-134">Další informace o PerfView a o tom, jak začít s [výukovým kurzem PerfView](https://channel9.msdn.com/Series/PerfView-Tutorial) nebo najdete v uživatelské příručce k dispozici v nástroji nebo [na GitHubu](https://github.com/Microsoft/perfview).</span><span class="sxs-lookup"><span data-stu-id="09228-134">You can learn more about PerfView and how to get started with [PerfView video tutorials](https://channel9.msdn.com/Series/PerfView-Tutorial) or by reading the user's guide available in the tool or [on GitHub](https://github.com/Microsoft/perfview).</span></span>

## <a name="windows-performance-toolkit"></a><span data-ttu-id="09228-135">Sada Windows Performance Toolkit</span><span class="sxs-lookup"><span data-stu-id="09228-135">Windows Performance Toolkit</span></span>

<span data-ttu-id="09228-136">[Sada Windows Performance Toolkit](/windows-hardware/test/wpt/) (WPT) se skládá ze dvou součástí: Windows Performance Recording (WPR) a Windows Performance Analyzer (WPA).</span><span class="sxs-lookup"><span data-stu-id="09228-136">[Windows Performance Toolkit](/windows-hardware/test/wpt/) (WPT) consists of two components: Windows Performance Recorder (WPR) and Windows Performance Analyzer (WPA).</span></span> <span data-ttu-id="09228-137">Nástroje vytváří podrobné výkonnostní profily operačních systémů a aplikací Windows.</span><span class="sxs-lookup"><span data-stu-id="09228-137">The tools produce in-depth performance profiles of Windows operating systems and apps.</span></span> <span data-ttu-id="09228-138">WPT má bohatší možnosti vizualizace dat, ale shromažďování dat je méně výkonné než PerfView.</span><span class="sxs-lookup"><span data-stu-id="09228-138">WPT has richer ways of visualizing data, but its data collecting is less powerful than PerfView's.</span></span>

## <a name="perfcollect"></a><span data-ttu-id="09228-139">PerfCollect</span><span class="sxs-lookup"><span data-stu-id="09228-139">PerfCollect</span></span>

<span data-ttu-id="09228-140">I když je PerfView užitečným nástrojem pro analýzu výkonu pro .NET, spouští se jenom ve Windows, takže ho nemůžete použít ke shromažďování trasování z ASP.NET Corech aplikací spuštěných v prostředích Linux.</span><span class="sxs-lookup"><span data-stu-id="09228-140">While PerfView is a useful performance analysis tool for .NET scenarios, it only runs on Windows so you can't use it to collect traces from ASP.NET Core apps running in Linux environments.</span></span>

<span data-ttu-id="09228-141">[PerfCollect](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md) je skript bash, který používá nativní nástroje pro profilaci systému Linux ([perf](https://perf.wiki.kernel.org/index.php/Main_Page) and [LTTng](https://lttng.org/)) ke shromáždění trasování na platformě Linux, které lze analyzovat pomocí PerfView.</span><span class="sxs-lookup"><span data-stu-id="09228-141">[PerfCollect](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md) is a bash script that uses native Linux profiling tools ([Perf](https://perf.wiki.kernel.org/index.php/Main_Page) and [LTTng](https://lttng.org/)) to collect traces on Linux that can be analyzed by PerfView.</span></span> <span data-ttu-id="09228-142">PerfCollect je užitečné v případě, že se problémy s výkonem zobrazují v prostředích Linux, kde PerfView nelze použít přímo.</span><span class="sxs-lookup"><span data-stu-id="09228-142">PerfCollect is useful when performance problems show up in Linux environments where PerfView can't be used directly.</span></span> <span data-ttu-id="09228-143">Místo toho může PerfCollect shromažďovat trasování z aplikací .NET Core, které jsou poté analyzovány na počítači s Windows pomocí PerfView.</span><span class="sxs-lookup"><span data-stu-id="09228-143">Instead, PerfCollect can collect traces from .NET Core apps that are then analyzed on a Windows computer using PerfView.</span></span>

<span data-ttu-id="09228-144">Další informace o tom, jak nainstalovat a začít používat PerfCollect, jsou k dispozici [na GitHubu](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md).</span><span class="sxs-lookup"><span data-stu-id="09228-144">More information about how to install and get started with PerfCollect is available [on GitHub](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md).</span></span>

## <a name="other-third-party-performance-tools"></a><span data-ttu-id="09228-145">Další nástroje pro sledování výkonu třetích stran</span><span class="sxs-lookup"><span data-stu-id="09228-145">Other Third-party Performance Tools</span></span>

<span data-ttu-id="09228-146">Následující seznam obsahuje některé nástroje pro sledování výkonu třetích stran, které jsou užitečné při vyšetřování výkonu aplikací .NET Core.</span><span class="sxs-lookup"><span data-stu-id="09228-146">The following lists some third-party performance tools that are useful in performance investigation of .NET Core applications.</span></span>

- [<span data-ttu-id="09228-147">MiniProfiler</span><span class="sxs-lookup"><span data-stu-id="09228-147">MiniProfiler</span></span>](https://miniprofiler.com/)
- <span data-ttu-id="09228-148">dotTrace a dotMemory z JetBrains</span><span class="sxs-lookup"><span data-stu-id="09228-148">dotTrace and dotMemory from JetBrains</span></span>
- <span data-ttu-id="09228-149">VTune od Intel</span><span class="sxs-lookup"><span data-stu-id="09228-149">VTune from Intel</span></span>

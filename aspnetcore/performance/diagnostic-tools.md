---
title: Diagnostické nástroje výkonu
author: mjrousos
description: Užitečné nástroje pro diagnostiku problémů s výkonem v aplikacích ASP.NET Core.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 12/07/2018
uid: performance/diagnostic-tools
ms.openlocfilehash: 3093b7d646e4fa943334c7b1e70ddc007ab18780
ms.sourcegitcommit: 1ea1b4fc58055c62728143388562689f1ef96cb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53329212"
---
# <a name="performance-diagnostic-tools"></a><span data-ttu-id="b91f1-103">Diagnostické nástroje výkonu</span><span class="sxs-lookup"><span data-stu-id="b91f1-103">Performance Diagnostic Tools</span></span>

<span data-ttu-id="b91f1-104">Podle [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="b91f1-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="b91f1-105">Tento článek obsahuje seznam nástrojů pro diagnostiku problémů s výkonem v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b91f1-105">This article lists tools for diagnosing performance issues in ASP.NET Core.</span></span>

## <a name="visual-studio-diagnostic-tools"></a><span data-ttu-id="b91f1-106">Diagnostické nástroje sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b91f1-106">Visual Studio Diagnostic Tools</span></span>

<span data-ttu-id="b91f1-107">[Profilování a diagnostické nástroje](/visualstudio/profiling) integrované do sady Visual Studio jsou dobrým začátkem prozkoumání problémů s výkonem.</span><span class="sxs-lookup"><span data-stu-id="b91f1-107">The [profiling and diagnostic tools](/visualstudio/profiling) built into Visual Studio are a good place to start investigating performance issues.</span></span> <span data-ttu-id="b91f1-108">Tyto nástroje jsou výkonné a pohodlné používat ho z vývojového prostředí sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="b91f1-108">These tools are powerful and convenient to use from the Visual Studio development environment.</span></span> <span data-ttu-id="b91f1-109">Nástroje umožňuje analýzu využití procesoru, využití paměti a události související s výkonem v aplikacích ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b91f1-109">The tooling allows analysis of CPU usage, memory usage, and performance events in ASP.NET Core apps.</span></span>

<span data-ttu-id="b91f1-110">Další informace jsou k dispozici v [dokumentace k sadě Visual Studio](/visualstudio/profiling/profiling-overview).</span><span class="sxs-lookup"><span data-stu-id="b91f1-110">More information is available in [Visual Studio documentation](/visualstudio/profiling/profiling-overview).</span></span>

## <a name="application-insights"></a><span data-ttu-id="b91f1-111">Application Insights</span><span class="sxs-lookup"><span data-stu-id="b91f1-111">Application Insights</span></span>

<span data-ttu-id="b91f1-112">[Application Insights](/azure/application-insights/app-insights-overview) vaší aplikaci poskytuje údaje o výkonu podrobné.</span><span class="sxs-lookup"><span data-stu-id="b91f1-112">[Application Insights](/azure/application-insights/app-insights-overview) provides in-depth performance data for your app.</span></span> <span data-ttu-id="b91f1-113">Application Insights automaticky shromažďuje údaje o míry odezvy, míra selhání, doby odezvy závislosti a další.</span><span class="sxs-lookup"><span data-stu-id="b91f1-113">Application Insights automatically collects data on response rates, failure rates, dependency response times, and more.</span></span> <span data-ttu-id="b91f1-114">Application Insights podporuje protokolování vlastních událostí a metrik, které jsou specifické pro vaši aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b91f1-114">Application Insights supports logging custom events and metrics specific to your app.</span></span>

<span data-ttu-id="b91f1-115">Application Insights můžete použít v různých prostředích:</span><span class="sxs-lookup"><span data-stu-id="b91f1-115">Application Insights can be used in a variety environments:</span></span>

* <span data-ttu-id="b91f1-116">Optimalizovaná pro práci v Azure.</span><span class="sxs-lookup"><span data-stu-id="b91f1-116">Optimized to work in Azure.</span></span>
* <span data-ttu-id="b91f1-117">Lze použít v produkčním prostředí, vývoj a přípravy.</span><span class="sxs-lookup"><span data-stu-id="b91f1-117">Works in production, development, and staging.</span></span>
* <span data-ttu-id="b91f1-118">Funguje místně z [sady Visual Studio](/azure/application-insights/app-insights-visual-studio) nebo v jiných prostředích hostingu.</span><span class="sxs-lookup"><span data-stu-id="b91f1-118">Works locally from [Visual Studio](/azure/application-insights/app-insights-visual-studio) or in other hosting environments.</span></span>

<span data-ttu-id="b91f1-119">Další informace najdete v tématu [Application Insights pro ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="b91f1-119">For more information, see [Application Insights for ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="perfview"></a><span data-ttu-id="b91f1-120">PerfView</span><span class="sxs-lookup"><span data-stu-id="b91f1-120">PerfView</span></span>

<span data-ttu-id="b91f1-121">[PerfView](https://github.com/Microsoft/perfview) je nástroj pro analýzu výkonu vytvořené týmem .NET speciálně pro diagnostiku problémů s výkonem .NET.</span><span class="sxs-lookup"><span data-stu-id="b91f1-121">[PerfView](https://github.com/Microsoft/perfview) is a performance analysis tool created by the .NET team specifically for diagnosing .NET performance issues.</span></span> <span data-ttu-id="b91f1-122">PerfView umožňuje analýzu využití procesoru využití paměti a uvolňování paměti chování, události související s výkonem a skutečný čas.</span><span class="sxs-lookup"><span data-stu-id="b91f1-122">PerfView allows analysis of CPU usage, memory and GC behavior, performance events, and wall clock time.</span></span>

<span data-ttu-id="b91f1-123">Další informace o PerfView a jak začít pracovat s [PerfView videokurzy](http://channel9.msdn.com/Series/PerfView-Tutorial) nebo načtením v uživatelské příručce k dispozici v nástroji nebo [na Githubu](https://github.com/Microsoft/perfview).</span><span class="sxs-lookup"><span data-stu-id="b91f1-123">You can learn more about PerfView and how to get started with [PerfView video tutorials](http://channel9.msdn.com/Series/PerfView-Tutorial) or by reading the user's guide available in the tool or [on GitHub](https://github.com/Microsoft/perfview).</span></span>

## <a name="perfcollect"></a><span data-ttu-id="b91f1-124">PerfCollect</span><span class="sxs-lookup"><span data-stu-id="b91f1-124">PerfCollect</span></span>

<span data-ttu-id="b91f1-125">Nástroj pro analýzu výkonu užitečné pro scénáře .NET při PerfView pouze běží na Windows proto jej nelze použít ke shromažďování trasování z aplikací ASP.NET Core spuštěné v prostředí Linux.</span><span class="sxs-lookup"><span data-stu-id="b91f1-125">While PerfView is a useful performance analysis tool for .NET scenarios, it only runs on Windows so you can't use it to collect traces from ASP.NET Core apps running in Linux environments.</span></span>

<span data-ttu-id="b91f1-126">[PerfCollect](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md) je bash skript, který používá nativní Linux nástroje pro profilaci ([výkonu](https://perf.wiki.kernel.org/index.php/Main_Page) a [LTTng](https://lttng.org/)) Chcete-li shromažďovat trasování na Linuxu, která mohou být analyzovány pomocí nástroje PerfView.</span><span class="sxs-lookup"><span data-stu-id="b91f1-126">[PerfCollect](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md) is a bash script that uses native Linux profiling tools ([Perf](https://perf.wiki.kernel.org/index.php/Main_Page) and [LTTng](https://lttng.org/)) to collect traces on Linux that can be analyzed by PerfView.</span></span> <span data-ttu-id="b91f1-127">PerfCollect je užitečné, když se zobrazí problémy s výkonem v prostředí Linux kdy PerfView nejde použít přímo.</span><span class="sxs-lookup"><span data-stu-id="b91f1-127">PerfCollect is useful when performance problems show up in Linux environments where PerfView can't be used directly.</span></span> <span data-ttu-id="b91f1-128">Místo toho můžete PerfCollect shromažďovat trasování z aplikace .NET Core, které jsou pak analyzovány na počítači Windows pomocí nástroje PerfView.</span><span class="sxs-lookup"><span data-stu-id="b91f1-128">Instead, PerfCollect can collect traces from .NET Core apps that are then analyzed on a Windows computer using PerfView.</span></span>

<span data-ttu-id="b91f1-129">Další informace o tom, jak nainstalovat a začít s PerfCollect je k dispozici [na Githubu](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md).</span><span class="sxs-lookup"><span data-stu-id="b91f1-129">More information about how to install and get started with PerfCollect is available [on GitHub](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md).</span></span>

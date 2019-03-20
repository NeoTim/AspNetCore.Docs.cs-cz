---
title: Diagnostické nástroje výkonu
author: mjrousos
description: Užitečné nástroje pro diagnostiku problémů s výkonem v aplikacích ASP.NET Core.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 12/07/2018
uid: performance/diagnostic-tools
ms.openlocfilehash: 0fb9944d1082090f4f58f641a26ee96286920456
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58209999"
---
# <a name="performance-diagnostic-tools"></a>Diagnostické nástroje výkonu

Podle [Mike Rousos](https://github.com/mjrousos)

Tento článek obsahuje seznam nástrojů pro diagnostiku problémů s výkonem v ASP.NET Core.

## <a name="visual-studio-diagnostic-tools"></a>Diagnostické nástroje sady Visual Studio

[Nástroje pro diagnostiku a profilování](/visualstudio/profiling) integrované do sady Visual Studio jsou dobrým začátkem hledání problémů s výkonem. Tyto nástroje jsou výkonné a používají se pohodlně přímo z vývojového prostředí sady Visual Studio. Umožňují analýzu využití procesoru, využití paměti a událostí souvisejících s výkonem v aplikacích ASP.NET Core. Profilování v době vývoje je snadné díky jejich integraci.

Další informace jsou k dispozici v [dokumentaci k sadě Visual Studio](/visualstudio/profiling/profiling-overview).

## <a name="application-insights"></a>Application Insights

[Application Insights](/azure/application-insights/app-insights-overview) poskytuje podrobné údaje o výkonu vaší aplikace. Application Insights automaticky shromažďuje například údaje o rychlosti odezvy, míře selhání nebo době odezvy závislostí. Kromě toho podporuje také protokolování vlastních událostí a metrik, které jsou specifické pro vaši aplikaci.

Azure Application Insights poskytuje informace z monitorovaných aplikací několika způsoby:

- [Mapa aplikace](/azure/application-insights/app-insights-app-map) pomáhá s hledáním míst způsobujících problémy s výkonem nebo selhání za běhu napříč všemi součástmi distribuované aplikace.
- [Okno metrik na portálu Application Insights](/azure/application-insights/app-insights-metrics-explorer?toc=/azure/azure-monitor/toc.json) ukazuje naměřené hodnoty a počty událostí.
- [Okno výkonu portálu Application Insights](/azure/application-insights/app-insights-tutorial-performance):

  - Zobrazí podrobnosti o výkonu pro různé operace v monitorované aplikaci.
  - Umožňuje přechod do konkrétní operace a kontrolu všech částí/závislostí, které přispívají k dlouhé době jejího trvání.
  - Z tohoto místa může být vyvolán i Profiler a na vyžádání shromažďovat trasovací informace výkonu.

- [Azure Application Insights Profiler](/azure/azure-monitor/app/profiler) umožňuje profilovat aplikace .NET pravidelně i na vyžádání.  Portál Azure zobrazuje záznamy trasování výkonu se zásobníky volání a kritické cesty. Trasovací soubory si můžete také stáhnout pro hlubší analýzu pomocí nástroje PerfView.

Application Insights můžete použít v různých prostředích:

- Optimalizovaná pro práci v Azure.
- Lze použít v produkčním, vývojovém i přípravném prostředí.
- Funguje lokálně ze [sady Visual Studio](/azure/application-insights/app-insights-visual-studio) nebo v jiných hostitelských prostředích.

Další informace najdete v tématu [Application Insights pro ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).

## <a name="perfview"></a>PerfView

[PerfView](https://github.com/Microsoft/perfview) je nástroj pro analýzu výkonu vytvořený týmem .NET speciálně pro diagnostiku problémů s výkonem .NET. PerfView umožňuje analýzu využití procesoru, využití paměti, chování uvolňování paměti, událostí souvisejících s výkonem a skutečného času.

Další informace o PerfView a jak s ním začít pracovat najdete ve [videokurzech](http://channel9.msdn.com/Series/PerfView-Tutorial) nebo si je můžete přečíst v uživatelské příručce, která je k dispozici přímo v nástroji, nebo [na GitHubu](https://github.com/Microsoft/perfview).

## <a name="windows-performance-toolkit"></a>Sada Windows Performance Toolkit

[Sada Windows Performance Toolkit](/windows-hardware/test/wpt/) (WPT) obsahuje dvě součásti: Windows Performance Recorder (WPR) a Windows Performance Analyzer (WPA). Tyto nástroje vytvářejí podrobné profily výkonu operačních systémů Windows a aplikací. WPT nabízí bohatší způsoby znázornění dat, ale je méně efektivní než PerfView při jejich shromažďování.

## <a name="perfcollect"></a>PerfCollect

Přestože je PerfView užitečným nástrojem pro analýzu výkonu pro scénáře .NET, běží pouze na Windows, takže jej nelze použít ke shromažďování trasování z aplikací ASP.NET Core spuštěných v prostředí Linuxu.

[PerfCollect](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md) je bash skript, který používá nativní linuxové nástroje pro profilaci ([Perf](https://perf.wiki.kernel.org/index.php/Main_Page) a [LTTng](https://lttng.org/)) a sběr trasovacích informací, jež mohou být analyzovány pomocí nástroje PerfView. PerfCollect je užitečný, když se projeví problémy s výkonem v prostředí Linuxu, kde nejde použít přímo PerfView. Místo toho můžete shromažďovat trasování z aplikace .NET Core prostřednictvím PerfCollect a potom je analyzovat na počítači s Windows pomocí nástroje PerfView.

Další informace o tom, jak nainstalovat a začít s PerfCollect, jsou k dispozici [na Githubu](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md).

## <a name="other-third-party-performance-tools"></a>Další nástroje třetích stran pro měření výkonu

V následujícím seznamu jsou uvedeny některé nástroje třetích stran pro měření výkonu, které jsou užitečné při prošetřování výkonu aplikací .NET Core.

- [MiniProfiler](https://miniprofiler.com/)
- dotTrace a dotMemory od JetBrains
- VTune od Intelu

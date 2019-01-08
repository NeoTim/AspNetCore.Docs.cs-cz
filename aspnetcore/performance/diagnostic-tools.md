---
title: Diagnostické nástroje výkonu
author: mjrousos
description: Užitečné nástroje pro diagnostiku problémů s výkonem v aplikacích ASP.NET Core.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 12/07/2018
uid: performance/diagnostic-tools
ms.openlocfilehash: 0b1de069e7892fff451617f2c6570fa789808c4f
ms.sourcegitcommit: 97d7a00bd39c83a8f6bccb9daa44130a509f75ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54099049"
---
# <a name="performance-diagnostic-tools"></a>Diagnostické nástroje výkonu

Podle [Mike Rousos](https://github.com/mjrousos)

Tento článek obsahuje seznam nástrojů pro diagnostiku problémů s výkonem v ASP.NET Core.

## <a name="visual-studio-diagnostic-tools"></a>Diagnostické nástroje sady Visual Studio

[Profilování a diagnostické nástroje](/visualstudio/profiling) integrované do sady Visual Studio jsou dobrým začátkem prozkoumání problémů s výkonem. Tyto nástroje jsou výkonné a pohodlné používat ho z vývojového prostředí sady Visual Studio. Nástroje umožňuje analýzu využití procesoru, využití paměti a události související s výkonem v aplikacích ASP.NET Core. Integrované se díky snadné profilace v době vývoje.

Další informace jsou k dispozici v [dokumentace k sadě Visual Studio](/visualstudio/profiling/profiling-overview).

## <a name="application-insights"></a>Application Insights

[Application Insights](/azure/application-insights/app-insights-overview) vaší aplikaci poskytuje údaje o výkonu podrobné. Application Insights automaticky shromažďuje údaje o míry odezvy, míra selhání, doby odezvy závislosti a další. Application Insights podporuje protokolování vlastních událostí a metrik, které jsou specifické pro vaši aplikaci.

Azure Application Insights poskytuje několik způsobů, jak poskytovat informací v monitorovaných aplikacích:

- [Mapa aplikace](/azure/application-insights/app-insights-app-map) – pomáhá problémových míst výkonu přímé nebo selhání za běhu – už pro všechny součásti distribuované aplikace.
- [Do okna metrik na portálu Application Insights](/azure/application-insights/app-insights-metrics-explorer?toc=/azure/azure-monitor/toc.json) ukazuje měří hodnoty a počty událostí.
- [Okno výkon portálu Application Insights](/azure/application-insights/app-insights-tutorial-performance):

  - Zobrazí podrobnosti o výkonu pro různé operace v monitorované aplikaci.
  - Umožňuje přechod do jedné operace zkontrolovat všechny části/závislosti, které přispívají k dlouhá doba trvání.
  - Profiler mohou být vyvolány z tohoto místa ke shromažďování výkonu trasování na vyžádání.

- [Azure Application Insights Profiler](/azure/azure-monitor/app/profiler) umožňuje pravidelné a na vyžádání profilace aplikace .NET.  Azure portal zobrazuje zaznamenat trasování výkonu se zásobníky volání a horké cesty. Trasovací soubory můžete také stáhnout pro hlubší analýzu pomocí nástroje PerfView.

Application Insights můžete použít v různých prostředích:

* Optimalizovaná pro práci v Azure.
* Lze použít v produkčním prostředí, vývoj a přípravy.
* Funguje místně z [sady Visual Studio](/azure/application-insights/app-insights-visual-studio) nebo v jiných prostředích hostingu.

Další informace najdete v tématu [Application Insights pro ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).

## <a name="perfview"></a>PerfView

[PerfView](https://github.com/Microsoft/perfview) je nástroj pro analýzu výkonu vytvořené týmem .NET speciálně pro diagnostiku problémů s výkonem .NET. PerfView umožňuje analýzu využití procesoru využití paměti a uvolňování paměti chování, události související s výkonem a skutečný čas.

Další informace o PerfView a jak začít pracovat s [PerfView videokurzy](http://channel9.msdn.com/Series/PerfView-Tutorial) nebo načtením v uživatelské příručce k dispozici v nástroji nebo [na Githubu](https://github.com/Microsoft/perfview).

## <a name="windows-performance-toolkit"></a>Sada Windows Performance Toolkit

[Sada Windows Performance Toolkit](/windows-hardware/test/wpt/) (subsystému WPF) obsahuje dvě součásti: Windows Performance Recorder (požadavku webové části) a Windows Performance Analyzer (WPA). Nástroje pro vytváření podrobné výkon profilů operačních systémů Windows a aplikací. Subsystému WPF má novým způsobem ke znázornění dat, ale je méně efektivní než PerfView na svá data shromažďování.

## <a name="perfcollect"></a>PerfCollect

Nástroj pro analýzu výkonu užitečné pro scénáře .NET při PerfView pouze běží na Windows proto jej nelze použít ke shromažďování trasování z aplikací ASP.NET Core spuštěné v prostředí Linux.

[PerfCollect](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md) je bash skript, který používá nativní Linux nástroje pro profilaci ([výkonu](https://perf.wiki.kernel.org/index.php/Main_Page) a [LTTng](https://lttng.org/)) Chcete-li shromažďovat trasování na Linuxu, která mohou být analyzovány pomocí nástroje PerfView. PerfCollect je užitečné, když se zobrazí problémy s výkonem v prostředí Linux kdy PerfView nejde použít přímo. Místo toho můžete PerfCollect shromažďovat trasování z aplikace .NET Core, které jsou pak analyzovány na počítači Windows pomocí nástroje PerfView.

Další informace o tom, jak nainstalovat a začít s PerfCollect je k dispozici [na Githubu](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md).

## <a name="other-third-party-performance-tools"></a>Další nástroje třetích stran výkonu

V následujícím seznamu uvedeny některé nástroje výkonu třetích stran, které jsou užitečné při vyšetřování výkonu aplikací .NET Core.

- [MiniProfiler](https://miniprofiler.com/)
- dotTrace a dotMemory od JetBrains
- VTune od Intelu

---
title: Nástroje pro diagnostiku výkonu
author: mjrousos
description: Užitečné nástroje pro diagnostiku problémů s výkonem v aplikacích ASP.NET Core.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
uid: performance/diagnostic-tools
ms.openlocfilehash: d273897b9ad26d57eb94b196b58f14019a96d07d
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78661075"
---
# <a name="performance-diagnostic-tools"></a>Diagnostické nástroje výkonu

O [Jan Rousos](https://github.com/mjrousos)

Tento článek obsahuje seznam nástrojů pro diagnostiku problémů s výkonem v ASP.NET Core.

## <a name="visual-studio-diagnostic-tools"></a>Diagnostické nástroje sady Visual Studio

K zahájení zkoumání problémů s výkonem je vhodné místo pro [profilaci a diagnostické nástroje](/visualstudio/profiling) , které jsou součástí sady Visual Studio. Tyto nástroje jsou výkonné a používají se pohodlně přímo z vývojového prostředí sady Visual Studio. Umožňují analýzu využití procesoru, využití paměti a událostí souvisejících s výkonem v aplikacích ASP.NET Core. Profilování v době vývoje je snadné díky jejich integraci.

Další informace jsou k dispozici v [dokumentaci k sadě Visual Studio](/visualstudio/profiling/profiling-overview).

## <a name="application-insights"></a>Application Insights

[Application Insights](/azure/application-insights/app-insights-overview) poskytuje podrobné údaje o výkonu vaší aplikace. Application Insights automaticky shromažďuje například údaje o rychlosti odezvy, míře selhání nebo době odezvy závislostí. Kromě toho podporuje také protokolování vlastních událostí a metrik, které jsou specifické pro vaši aplikaci.

Azure Application Insights poskytuje informace z monitorovaných aplikací několika způsoby:

- [Mapa aplikace](/azure/application-insights/app-insights-app-map) – pomáhá odhalit slabá místa výkonu nebo neúspěšné pokusy v rámci všech komponent distribuovaných aplikací.
- [Azure Průzkumník metrik](/azure/azure-monitor/platform/metrics-getting-started) je komponentou portál Microsoft Azure, která umožňuje vykreslovat grafy, vizuálně korelovat trendy a prozkoumat špičky a rozmítají metriky v hodnotách metriky.
- [Okno výkon na portálu Application Insights](/azure/application-insights/app-insights-tutorial-performance):

  - Zobrazuje podrobnosti o výkonu pro různé operace v monitorované aplikaci.
  - Umožňuje přechod do konkrétní operace a kontrolu všech částí/závislostí, které přispívají k dlouhé době jejího trvání.
  - Z tohoto místa může být vyvolán i Profiler a na vyžádání shromažďovat trasovací informace výkonu.

- [Azure Application Insights Profiler](/azure/azure-monitor/app/profiler) umožňuje pravidelné profilování aplikací .NET a na vyžádání.  Portál Azure zobrazuje záznamy trasování výkonu se zásobníky volání a kritické cesty. Trasovací soubory si můžete také stáhnout pro hlubší analýzu pomocí nástroje PerfView.

Application Insights lze použít v různých prostředích:

- Optimalizovaná pro práci v Azure.
- Lze použít v produkčním, vývojovém i přípravném prostředí.
- Funguje místně ze sady [Visual Studio](/azure/application-insights/app-insights-visual-studio) nebo v jiných hostujících prostředích.

Další informace najdete v tématu [Application Insights pro ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).

## <a name="perfview"></a>PerfView

[PerfView](https://github.com/Microsoft/perfview) je nástroj pro analýzu výkonu vytvořený týmem .NET konkrétně pro diagnostiku problémů s výkonem .NET. PerfView umožňuje analýzu využití procesoru, využití paměti, chování uvolňování paměti, událostí souvisejících s výkonem a skutečného času.

Další informace o PerfView a o tom, jak začít s [výukovým kurzem PerfView](https://channel9.msdn.com/Series/PerfView-Tutorial) nebo najdete v uživatelské příručce k dispozici v nástroji nebo [na GitHubu](https://github.com/Microsoft/perfview).

## <a name="windows-performance-toolkit"></a>Sada Windows Performance Toolkit

[Sada Windows Performance Toolkit](/windows-hardware/test/wpt/) (WPT) se skládá ze dvou součástí: Windows Performance Recording (WPR) a Windows Performance Analyzer (WPA). Tyto nástroje vytvářejí podrobné profily výkonu operačních systémů Windows a aplikací. WPT nabízí bohatší způsoby znázornění dat, ale je méně efektivní než PerfView při jejich shromažďování.

## <a name="perfcollect"></a>PerfCollect

Přestože je PerfView užitečným nástrojem pro analýzu výkonu pro scénáře .NET, běží pouze na Windows, takže jej nelze použít ke shromažďování trasování z aplikací ASP.NET Core spuštěných v prostředí Linuxu.

[PerfCollect](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md) je skript bash, který používá nativní nástroje pro profilaci systému Linux ([perf](https://perf.wiki.kernel.org/index.php/Main_Page) and [LTTng](https://lttng.org/)) ke shromáždění trasování na platformě Linux, které lze analyzovat pomocí PerfView. PerfCollect je užitečný, když se projeví problémy s výkonem v prostředí Linuxu, kde nejde použít přímo PerfView. Místo toho můžete shromažďovat trasování z aplikace .NET Core prostřednictvím PerfCollect a potom je analyzovat na počítači s Windows pomocí nástroje PerfView.

Další informace o tom, jak nainstalovat a začít používat PerfCollect, jsou k dispozici [na GitHubu](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md).

## <a name="other-third-party-performance-tools"></a>Další nástroje třetích stran pro měření výkonu

V následujícím seznamu jsou uvedeny některé nástroje třetích stran pro měření výkonu, které jsou užitečné při prošetřování výkonu aplikací .NET Core.

- [MiniProfiler](https://miniprofiler.com/)
- dotTrace a dotMemory z JetBrains
- VTune od Intel

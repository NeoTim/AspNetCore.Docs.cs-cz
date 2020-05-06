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
# <a name="performance-diagnostic-tools"></a>Diagnostické nástroje výkonu

O [Jan Rousos](https://github.com/mjrousos)

Tento článek obsahuje seznam nástrojů pro diagnostiku problémů s výkonem v ASP.NET Core.

## <a name="visual-studio-diagnostic-tools"></a>Diagnostické nástroje sady Visual Studio

K zahájení zkoumání problémů s výkonem je vhodné místo pro [profilaci a diagnostické nástroje](/visualstudio/profiling) , které jsou součástí sady Visual Studio. Tyto nástroje jsou výkonné a praktické pro použití ve vývojovém prostředí sady Visual Studio. Nástroje umožňují analýzu využití CPU, využití paměti a událostí výkonu v aplikacích ASP.NET Core. Předdefinovaná aplikace usnadňuje profilaci v době vývoje.

Další informace jsou k dispozici v [dokumentaci k sadě Visual Studio](/visualstudio/profiling/profiling-overview).

## <a name="application-insights"></a>Application Insights

[Application Insights](/azure/application-insights/app-insights-overview) poskytuje podrobné údaje o výkonu vaší aplikace. Application Insights automaticky shromažďuje data podle míry odezvy, míry selhání, doby odezvy závislostí a dalších. Application Insights podporuje protokolování vlastních událostí a metrik, které jsou specifické pro vaši aplikaci.

Azure Application Insights poskytuje několik způsobů, jak poskytnout přehledy o monitorovaných aplikacích:

- [Mapa aplikace](/azure/application-insights/app-insights-app-map) – pomáhá odhalit slabá místa výkonu nebo neúspěšné pokusy v rámci všech komponent distribuovaných aplikací.
- [Azure Průzkumník metrik](/azure/azure-monitor/platform/metrics-getting-started) je komponentou portál Microsoft Azure, která umožňuje vykreslovat grafy, vizuálně korelovat trendy a prozkoumat špičky a rozmítají metriky v hodnotách metriky.
- [Okno výkon na portálu Application Insights](/azure/application-insights/app-insights-tutorial-performance):

  - Zobrazuje podrobnosti o výkonu pro různé operace v monitorované aplikaci.
  - Umožňuje přechod na jedinou operaci pro kontrolu všech částí nebo závislostí, které přispívají k dlouhou dobu trvání.
  - Profiler můžete z tohoto místa vyvolat, abyste mohli shromažďovat trasování výkonu na vyžádání.

- [Azure Application Insights Profiler](/azure/azure-monitor/app/profiler) umožňuje pravidelné profilování aplikací .NET a na vyžádání.  Azure Portal zobrazuje zaznamenaná trasování výkonu se zásobníky volání a Hot Paths. Trasovací soubory je také možné stáhnout pro hlubší analýzu pomocí PerfView.

Application Insights lze použít v různých prostředích:

- Optimalizováno pro práci v Azure.
- Funguje v produkčním, vývojovém a přípravném prostředí.
- Funguje místně ze sady [Visual Studio](/azure/application-insights/app-insights-visual-studio) nebo v jiných hostujících prostředích.

Další informace najdete v tématu [Application Insights pro ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).

## <a name="perfview"></a>PerfView

[PerfView](https://github.com/Microsoft/perfview) je nástroj pro analýzu výkonu vytvořený týmem .NET konkrétně pro diagnostiku problémů s výkonem .NET. PerfView umožňuje analýzu využití CPU, chování paměti a GC, událostí výkonu a času chodu na zdi.

Další informace o PerfView a o tom, jak začít s [výukovým kurzem PerfView](https://channel9.msdn.com/Series/PerfView-Tutorial) nebo najdete v uživatelské příručce k dispozici v nástroji nebo [na GitHubu](https://github.com/Microsoft/perfview).

## <a name="windows-performance-toolkit"></a>Sada Windows Performance Toolkit

[Sada Windows Performance Toolkit](/windows-hardware/test/wpt/) (WPT) se skládá ze dvou součástí: Windows Performance Recording (WPR) a Windows Performance Analyzer (WPA). Nástroje vytváří podrobné výkonnostní profily operačních systémů a aplikací Windows. WPT má bohatší možnosti vizualizace dat, ale shromažďování dat je méně výkonné než PerfView.

## <a name="perfcollect"></a>PerfCollect

I když je PerfView užitečným nástrojem pro analýzu výkonu pro .NET, spouští se jenom ve Windows, takže ho nemůžete použít ke shromažďování trasování z ASP.NET Corech aplikací spuštěných v prostředích Linux.

[PerfCollect](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md) je skript bash, který používá nativní nástroje pro profilaci systému Linux ([perf](https://perf.wiki.kernel.org/index.php/Main_Page) and [LTTng](https://lttng.org/)) ke shromáždění trasování na platformě Linux, které lze analyzovat pomocí PerfView. PerfCollect je užitečné v případě, že se problémy s výkonem zobrazují v prostředích Linux, kde PerfView nelze použít přímo. Místo toho může PerfCollect shromažďovat trasování z aplikací .NET Core, které jsou poté analyzovány na počítači s Windows pomocí PerfView.

Další informace o tom, jak nainstalovat a začít používat PerfCollect, jsou k dispozici [na GitHubu](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md).

## <a name="other-third-party-performance-tools"></a>Další nástroje pro sledování výkonu třetích stran

Následující seznam obsahuje některé nástroje pro sledování výkonu třetích stran, které jsou užitečné při vyšetřování výkonu aplikací .NET Core.

- [MiniProfiler](https://miniprofiler.com/)
- dotTrace a dotMemory z JetBrains
- VTune od Intel

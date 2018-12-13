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
# <a name="performance-diagnostic-tools"></a>Diagnostické nástroje výkonu

Podle [Mike Rousos](https://github.com/mjrousos)

Tento článek obsahuje seznam nástrojů pro diagnostiku problémů s výkonem v ASP.NET Core.

## <a name="visual-studio-diagnostic-tools"></a>Diagnostické nástroje sady Visual Studio

[Profilování a diagnostické nástroje](/visualstudio/profiling) integrované do sady Visual Studio jsou dobrým začátkem prozkoumání problémů s výkonem. Tyto nástroje jsou výkonné a pohodlné používat ho z vývojového prostředí sady Visual Studio. Nástroje umožňuje analýzu využití procesoru, využití paměti a události související s výkonem v aplikacích ASP.NET Core.

Další informace jsou k dispozici v [dokumentace k sadě Visual Studio](/visualstudio/profiling/profiling-overview).

## <a name="application-insights"></a>Application Insights

[Application Insights](/azure/application-insights/app-insights-overview) vaší aplikaci poskytuje údaje o výkonu podrobné. Application Insights automaticky shromažďuje údaje o míry odezvy, míra selhání, doby odezvy závislosti a další. Application Insights podporuje protokolování vlastních událostí a metrik, které jsou specifické pro vaši aplikaci.

Application Insights můžete použít v různých prostředích:

* Optimalizovaná pro práci v Azure.
* Lze použít v produkčním prostředí, vývoj a přípravy.
* Funguje místně z [sady Visual Studio](/azure/application-insights/app-insights-visual-studio) nebo v jiných prostředích hostingu.

Další informace najdete v tématu [Application Insights pro ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).

## <a name="perfview"></a>PerfView

[PerfView](https://github.com/Microsoft/perfview) je nástroj pro analýzu výkonu vytvořené týmem .NET speciálně pro diagnostiku problémů s výkonem .NET. PerfView umožňuje analýzu využití procesoru využití paměti a uvolňování paměti chování, události související s výkonem a skutečný čas.

Další informace o PerfView a jak začít pracovat s [PerfView videokurzy](http://channel9.msdn.com/Series/PerfView-Tutorial) nebo načtením v uživatelské příručce k dispozici v nástroji nebo [na Githubu](https://github.com/Microsoft/perfview).

## <a name="perfcollect"></a>PerfCollect

Nástroj pro analýzu výkonu užitečné pro scénáře .NET při PerfView pouze běží na Windows proto jej nelze použít ke shromažďování trasování z aplikací ASP.NET Core spuštěné v prostředí Linux.

[PerfCollect](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md) je bash skript, který používá nativní Linux nástroje pro profilaci ([výkonu](https://perf.wiki.kernel.org/index.php/Main_Page) a [LTTng](https://lttng.org/)) Chcete-li shromažďovat trasování na Linuxu, která mohou být analyzovány pomocí nástroje PerfView. PerfCollect je užitečné, když se zobrazí problémy s výkonem v prostředí Linux kdy PerfView nejde použít přímo. Místo toho můžete PerfCollect shromažďovat trasování z aplikace .NET Core, které jsou pak analyzovány na počítači Windows pomocí nástroje PerfView.

Další informace o tom, jak nainstalovat a začít s PerfCollect je k dispozici [na Githubu](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md).

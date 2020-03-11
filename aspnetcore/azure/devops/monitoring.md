---
title: Monitorování a ladění – DevOps s využitím ASP.NET Core a Azure
author: CamSoper
description: Monitorování a ladění kódu jako součást řešení DevOps s ASP.NET Core a Azure
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 07/10/2019
uid: azure/devops/monitor
ms.openlocfilehash: 1d8ed99f4387dbc99929164c558cc2ce14bd9ea0
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78659500"
---
# <a name="monitor-and-debug"></a>Monitorování a ladění

S aplikaci nasadili a vytvořili kanál DevOps, je důležité pochopit, jak monitorovat a řešení potíží s aplikací.

V této části budete provádět následující úlohy:

* Najít základní monitorování a řešení potíží s dat na webu Azure Portal
* Zjistěte, jak Azure Monitor poskytuje hlubší pohled na metriky v rámci všech služeb Azure
* Připojení webové aplikace pomocí Application Insights pro aplikace pro profilaci
* Zapnutí protokolování a zjistíte, kde ke stažení protokolů
* Stream protokolů v reálném čase
* Zjistíte, kde nastavit výstrahy
* Další informace o vzdálené ladění Azure App Service web apps.

## <a name="basic-monitoring-and-troubleshooting"></a>Základní monitorování a řešení potíží

Aplikace služby App Service web apps je snadné sledování v reálném čase. Na webu Azure portal vykreslí metriky na snadno pochopitelné tabulky a grafy.

1. Otevřete [Azure Portal](https://portal.azure.com)a potom přejděte do *mywebapp\<unique_number\>* App Service.

1. Karta **Přehled** zobrazuje užitečné informace, včetně grafů, které zobrazují nedávné metriky.

    ![Snímek obrazovky zobrazující Přehled panelu](./media/monitoring/overview.png)

    * **Http 5xx**: počet chyb na straně serveru, obvykle výjimky v kódu ASP.NET Core.
    * **Data v**: příchozí data přicházejí do vaší webové aplikace.
    * **Data odchozí**: odchozí přenos dat z vaší webové aplikace na klienty.
    * **Požadavky**: počet požadavků HTTP.
    * **Průměrná doba odezvy**: Průměrná doba, jakou webové aplikace reagují na požadavky HTTP.

    Několik samoobslužné nástroje pro řešení potíží a optimalizace, které také najdete na této stránce.

    ![Snímek obrazovky znázorňující samoobslužné nástroje](./media/monitoring/wizards.png)

    * **Diagnostiku a řešení problémů** je samoobslužný Poradce při potížích.
    * **Application Insights** je pro profilaci výkonu a aplikace a zabývá se dále v této části.
    * **App Service Advisor** vytváří doporučení pro optimalizaci prostředí aplikace.

## <a name="advanced-monitoring"></a>Pokročilé sledování

[Azure monitor](/azure/monitoring-and-diagnostics/) je centralizovaná služba pro monitorování všech metrik a nastavení výstrah napříč službami Azure. V rámci Azure Monitor správci podrobně sledovat výkon a identifikovat trendy. Každá služba Azure nabízí svou vlastní [sadu metrik](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions) pro Azure monitor.

## <a name="profile-with-application-insights"></a>Profil Application insights

[Application Insights](/azure/application-insights/app-insights-overview) je služba Azure, která slouží k analýze výkonu a stability webových aplikací a způsobu jejich používání uživateli. Data ze služby Application Insights je rozsáhlejší a podrobnější než u Azure Monitor. Data můžete zadat, vývojáři a správci s informace o klíči pro zlepšení aplikace. Application Insights je přidat do prostředku služby Azure App Service bez jakýchkoli změn kódu.

1. Otevřete [Azure Portal](https://portal.azure.com)a potom přejděte do *mywebapp\<unique_number\>* App Service.
1. Na kartě **Přehled** klikněte na dlaždici **Application Insights** .

    ![Dlaždici služby Application Insights](./media/monitoring/app-insights.png)

1. Zvolte přepínač **vytvořit nový prostředek** . Použijte výchozí název prostředku a vyberte umístění pro prostředek služby Application Insights. Umístění se nemusí shodovat s vaší webové aplikace.

    ![Nastavení Application Insights](./media/monitoring/new-app-insights.png)

1. Pro **modul runtime/rozhraní**vyberte možnost **ASP.NET Core**. Přijměte výchozí nastavení.
1. Vyberte **OK**. Pokud se zobrazí výzva k potvrzení, vyberte **pokračovat**.
1. Po vytvoření prostředku, klikněte na název prostředku Application Insights a přejít přímo na stránku Application Insights.

    ![Nový prostředek Application Insights je připravený](./media/monitoring/new-app-insights-done.png)

Při použití aplikace, data hromadí. Výběrem **aktualizovat** znovu načtěte okno s novými daty.

![Karta Přehled Application Insights](./media/monitoring/app-insights-overview.png)

Application Insights poskytuje užitečné informace na straně serveru bez další konfigurace. Pokud chcete získat co nejvíc hodnot z Application Insights, [Instrumentujte svoji aplikaci pomocí Application Insights SDK](/azure/application-insights/app-insights-asp-net-core). Pokud správně nakonfigurovaná, které služba poskytuje začátku do konce monitorování v rámci webového serveru a prohlížeče, včetně výkonu na straně klienta. Další informace najdete v dokumentaci k [Application Insights](/azure/application-insights/app-insights-overview).

## <a name="logging"></a>Protokolování

Protokoly webového serveru a aplikace jsou zakázané ve výchozím nastavení ve službě Azure App Service. Povolení protokolů pomocí následujících kroků:

1. Otevřete [Azure Portal](https://portal.azure.com)a přejděte do *mywebapp\<unique_number\>* App Service.
1. V nabídce vlevo přejděte dolů k části **monitorování** . Vyberte **diagnostické protokoly**.

    ![Diagnostické protokoly odkaz](./media/monitoring/logging.png)

1. Zapněte **protokolování aplikace (systém souborů)** . Pokud se zobrazí výzva, klikněte na políčko pro instalaci rozšíření povolíte protokolování ve webové aplikaci app.
1. Nastavte **protokolování webového serveru** do **systému souborů**.
1. Zadejte **dobu uchování** ve dnech. Například 30.
1. Klikněte na **Uložit**.

Pro webovou aplikaci se generují protokoly ASP.NET Core a web server (App Service). Stahování lze provádět pomocí FTP/FTPS informace zobrazené. Heslo je stejná jako přihlašovací údaje pro nasazení, vytvořili dříve v tomto průvodci. Protokoly můžou být [streamované přímo do vašeho místního počítače pomocí PowerShellu nebo Azure CLI](/azure/app-service/web-sites-enable-diagnostic-log#download). Protokoly lze také [Zobrazit v Application Insights](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights).

## <a name="log-streaming"></a>Streamování protokolů

Protokoly aplikací a webového serveru můžete streamování v reálném čase prostřednictvím portálu.

1. Otevřete [Azure Portal](https://portal.azure.com)a přejděte do *mywebapp\<unique_number\>* App Service.
1. V nabídce vlevo přejděte dolů k části **monitorování** a vyberte **log Stream**.

    ![Snímek obrazovky znázorňující protokolu stream odkaz](./media/monitoring/log-stream.png)

Protokoly lze také [streamovat prostřednictvím rozhraní příkazového řádku Azure CLI nebo Azure PowerShell](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs), včetně Cloud Shell.

## <a name="alerts"></a>Výstrahy

Azure Monitor také poskytuje [výstrahy v reálném čase](/azure/monitoring-and-diagnostics/insights-alerts-portal) na základě metrik, událostí správy a dalších kritérií.

> *Poznámka: upozornění na metriky webové aplikace je dostupné jenom ve službě výstrahy (Classic).*

[Službu Výstrahy (Classic)](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal) najdete v Azure monitor nebo v části **monitorování** nastavení App Service.

![Upozornění (klasická) odkaz](./media/monitoring/alerts.png)

## <a name="live-debugging"></a>Živé ladění

Azure App Service lze [ladit vzdáleně pomocí sady Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) , když protokoly neposkytují dostatek informací. Ale vzdálené ladění vyžaduje, aby aplikace kompilována se symboly ladění. Ladění nelze provádět v produkčním prostředí, s výjimkou jako poslední možnost.

## <a name="conclusion"></a>Závěr

V této části jste dokončili následující úlohy:

* Najít základní monitorování a řešení potíží s dat na webu Azure Portal
* Zjistěte, jak Azure Monitor poskytuje hlubší pohled na metriky v rámci všech služeb Azure
* Připojení webové aplikace pomocí Application Insights pro aplikace pro profilaci
* Zapnutí protokolování a zjistíte, kde ke stažení protokolů
* Stream protokolů v reálném čase
* Zjistíte, kde nastavit výstrahy
* Další informace o vzdálené ladění Azure App Service web apps.

## <a name="additional-reading"></a>Další čtení

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [Monitorování výkonu webových aplikací Azure pomocí Application Insights](/azure/application-insights/app-insights-azure-web-apps)
* [Povolit protokolování diagnostiky pro webové aplikace v Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log)
* [Řešení potíží s webovou aplikací v Azure App Service pomocí sady Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [Vytváření klasických upozornění na metriky v Azure Monitor pro služby Azure – Azure Portal](/azure/monitoring-and-diagnostics/insights-alerts-portal)

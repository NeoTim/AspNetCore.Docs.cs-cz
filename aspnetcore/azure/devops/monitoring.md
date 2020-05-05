---
title: Monitorování a ladění – DevOps s využitím ASP.NET Core a Azure
author: CamSoper
description: Monitorování a ladění kódu jako součást řešení DevOps s využitím ASP.NET Core a Azure
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 07/10/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: azure/devops/monitor
ms.openlocfilehash: 3af36a37124968e13952e8bf5de1b643265a4a5b
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82766885"
---
# <a name="monitor-and-debug"></a>Monitorování a ladění

Když jste nasadili aplikaci a vytvořili kanál DevOps, je důležité pochopit, jak monitorovat a řešit potíže s aplikací.

V této části provedete následující úlohy:

* Vyhledání základních dat monitorování a řešení potíží v Azure Portal
* Přečtěte si, jak Azure Monitor poskytuje hlubší přehled o metrikách napříč všemi službami Azure.
* Připojení webové aplikace s Application Insights pro profilaci aplikace
* Zapnout protokolování a zjistit, kde stahovat protokoly
* Streamování protokolů v reálném čase
* Informace o nastavení výstrah
* Přečtěte si o vzdáleném ladění Azure App Service Web Apps.

## <a name="basic-monitoring-and-troubleshooting"></a>Základní monitorování a řešení potíží

App Service webové aplikace lze snadno sledovat v reálném čase. Azure Portal vykresluje metriky v snadno srozumitelných grafech a grafech.

1. Otevřete [Azure Portal](https://portal.azure.com)a potom přejděte na App Service *unique_number\<\> MyWebApp* .

1. Karta **Přehled** zobrazuje užitečné informace, včetně grafů, které zobrazují nedávné metriky.

    ![Snímek obrazovky znázorňující panel přehledu](./media/monitoring/overview.png)

    * **Http 5xx**: počet chyb na straně serveru, obvykle výjimky v kódu ASP.NET Core.
    * **Data v**: příchozí data přicházejí do vaší webové aplikace.
    * **Data odchozí**: odchozí přenos dat z vaší webové aplikace na klienty.
    * **Požadavky**: počet požadavků HTTP.
    * **Průměrná doba odezvy**: Průměrná doba, jakou webové aplikace reagují na požadavky HTTP.

    Na této stránce se taky nachází několik samoobslužných nástrojů pro řešení potíží a optimalizaci.

    ![Snímek obrazovky zobrazující nástroje pro samoobslužné služby](./media/monitoring/wizards.png)

    * **Diagnostiku a řešení problémů** je samoobslužný Poradce při potížích.
    * **Application Insights** je pro profilaci výkonu a aplikace a zabývá se dále v této části.
    * **App Service Advisor** vytváří doporučení pro optimalizaci prostředí aplikace.

## <a name="advanced-monitoring"></a>Pokročilé sledování

[Azure monitor](/azure/monitoring-and-diagnostics/) je centralizovaná služba pro monitorování všech metrik a nastavení výstrah napříč službami Azure. V rámci Azure Monitor můžou správci podrobně sledovat výkon a identifikovat trendy. Každá služba Azure nabízí svou vlastní [sadu metrik](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions) pro Azure monitor.

## <a name="profile-with-application-insights"></a>Profil s Application Insights

[Application Insights](/azure/application-insights/app-insights-overview) je služba Azure, která slouží k analýze výkonu a stability webových aplikací a způsobu jejich používání uživateli. Data z Application Insights jsou širší a hlubší než Azure Monitor. Data mohou vývojářům a správcům poskytnout klíčové informace pro zlepšení aplikací. Application Insights lze přidat do prostředku Azure App Service bez změny kódu.

1. Otevřete [Azure Portal](https://portal.azure.com)a potom přejděte na App Service *unique_number\<\> MyWebApp* .
1. Na kartě **Přehled** klikněte na dlaždici **Application Insights** .

    ![Dlaždice Application Insights](./media/monitoring/app-insights.png)

1. Zvolte přepínač **vytvořit nový prostředek** . Použijte výchozí název prostředku a vyberte umístění prostředku Application Insights. Toto umístění nemusí odpovídat vaší webové aplikaci.

    ![Instalace Application Insights](./media/monitoring/new-app-insights.png)

1. Pro **modul runtime/rozhraní**vyberte možnost **ASP.NET Core**. Přijměte výchozí nastavení.
1. Vyberte **OK**. Pokud se zobrazí výzva k potvrzení, vyberte **pokračovat**.
1. Po vytvoření prostředku klikněte na název prostředku Application Insights, abyste mohli přejít přímo na stránku Application Insights.

    ![Nový prostředek Application Insights je připravený.](./media/monitoring/new-app-insights-done.png)

Při použití aplikace se shromažďují data. Výběrem **aktualizovat** znovu načtěte okno s novými daty.

![Karta Přehled Application Insights](./media/monitoring/app-insights-overview.png)

Application Insights poskytuje užitečné informace na straně serveru bez další konfigurace. Pokud chcete získat co nejvíc hodnot z Application Insights, [Instrumentujte svoji aplikaci pomocí Application Insights SDK](/azure/application-insights/app-insights-asp-net-core). Když je služba správně nakonfigurovaná, poskytuje kompletní monitorování mezi webovým serverem a prohlížečem, včetně výkonu na straně klienta. Další informace najdete v dokumentaci k [Application Insights](/azure/application-insights/app-insights-overview).

## <a name="logging"></a>protokolování

Protokoly webového serveru a aplikace jsou ve výchozím nastavení ve Azure App Service zakázané. Povolte protokoly pomocí následujících kroků:

1. Otevřete [Azure Portal](https://portal.azure.com)a přejděte do App Service *unique_number\<\> MyWebApp* .
1. V nabídce vlevo přejděte dolů k části **monitorování** . Vyberte **diagnostické protokoly**.

    ![Odkaz na diagnostické protokoly](./media/monitoring/logging.png)

1. Zapněte **protokolování aplikace (systém souborů)**. Pokud se zobrazí výzva, kliknutím na toto políčko nainstalujete rozšíření a povolíte protokolování aplikace ve webové aplikaci.
1. Nastavte **protokolování webového serveru** do **systému souborů**.
1. Zadejte **dobu uchování** ve dnech. Například 30.
1. Klikněte na **Uložit**.

Pro webovou aplikaci jsou vygenerovány protokoly ASP.NET Core a webový server (App Service). Můžete je stáhnout pomocí zobrazených informací FTP/FTPS. Heslo je stejné jako přihlašovací údaje pro nasazení vytvořené dříve v této příručce. Protokoly můžou být [streamované přímo do vašeho místního počítače pomocí PowerShellu nebo Azure CLI](/azure/app-service/web-sites-enable-diagnostic-log#download). Protokoly lze také [Zobrazit v Application Insights](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights).

## <a name="log-streaming"></a>Streamování protokolů

Protokoly aplikací a webového serveru můžou být v reálném čase streamované prostřednictvím portálu.

1. Otevřete [Azure Portal](https://portal.azure.com)a přejděte do App Service *unique_number\<\> MyWebApp* .
1. V nabídce vlevo přejděte dolů k části **monitorování** a vyberte **log Stream**.

    ![Snímek obrazovky ukazující odkaz na Stream protokolu](./media/monitoring/log-stream.png)

Protokoly lze také [streamovat prostřednictvím rozhraní příkazového řádku Azure CLI nebo Azure PowerShell](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs), včetně Cloud Shell.

## <a name="alerts"></a>Výstrahy

Azure Monitor také poskytuje [výstrahy v reálném čase](/azure/monitoring-and-diagnostics/insights-alerts-portal) na základě metrik, událostí správy a dalších kritérií.

> *Poznámka: upozornění na metriky webové aplikace je dostupné jenom ve službě výstrahy (Classic).*

[Službu Výstrahy (Classic)](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal) najdete v Azure monitor nebo v části **monitorování** nastavení App Service.

![Odkaz výstrahy (Classic)](./media/monitoring/alerts.png)

## <a name="live-debugging"></a>Živé ladění

Azure App Service lze [ladit vzdáleně pomocí sady Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) , když protokoly neposkytují dostatek informací. Vzdálené ladění ale vyžaduje, aby se aplikace kompilována se symboly ladění. Ladění by se nemělo provádět v produkčním prostředí, s výjimkou poslední.

## <a name="conclusion"></a>Závěr

V této části jste dokončili následující úlohy:

* Vyhledání základních dat monitorování a řešení potíží v Azure Portal
* Přečtěte si, jak Azure Monitor poskytuje hlubší přehled o metrikách napříč všemi službami Azure.
* Připojení webové aplikace s Application Insights pro profilaci aplikace
* Zapnout protokolování a zjistit, kde stahovat protokoly
* Streamování protokolů v reálném čase
* Informace o nastavení výstrah
* Přečtěte si o vzdáleném ladění Azure App Service Web Apps.

## <a name="additional-reading"></a>Další čtení

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [Monitorování výkonu webových aplikací Azure pomocí Application Insights](/azure/application-insights/app-insights-azure-web-apps)
* [Povolit protokolování diagnostiky pro webové aplikace v Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log)
* [Řešení potíží s webovou aplikací v Azure App Service pomocí sady Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [Vytváření klasických upozornění na metriky v Azure Monitor pro služby Azure – Azure Portal](/azure/monitoring-and-diagnostics/insights-alerts-portal)

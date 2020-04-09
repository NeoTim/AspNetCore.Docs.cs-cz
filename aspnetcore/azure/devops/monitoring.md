---
title: Monitorování a ladění – devops s ASP.NET jádra a Azure
author: CamSoper
description: Monitorování a ladění kódu jako součást řešení DevOps s ASP.NET Core a Azure
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 07/10/2019
uid: azure/devops/monitor
ms.openlocfilehash: 1d8ed99f4387dbc99929164c558cc2ce14bd9ea0
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78659500"
---
# <a name="monitor-and-debug"></a>Sledování a ladění

Po nasazení aplikace a vytvoření kanálu DevOps je důležité pochopit, jak sledovat a řešit problémy s aplikací.

V této části dokončíte následující úkoly:

* Vyhledání základních dat monitorování a řešení potíží na webu Azure Portal
* Zjistěte, jak Azure Monitor poskytuje hlubší pohled na metriky napříč všemi službami Azure
* Připojení webové aplikace pomocí Application Insights pro profilování aplikací
* Zapnutí protokolování a zjištění, kde stáhnout protokoly
* Streamovat protokoly v reálném čase
* Informace o tom, kde nastavit výstrahy
* Přečtěte si o vzdáleném ladění webových aplikací Azure App Service.

## <a name="basic-monitoring-and-troubleshooting"></a>Základní monitorování a řešení problémů

Webové aplikace App Service lze snadno sledovat v reálném čase. Portál Azure vykresluje metriky v snadno pochopitelných grafech a grafech.

1. Otevřete [portál Azure](https://portal.azure.com)a přejděte na *\<mywebapp unique_number\> * App Service.

1. Karta **Přehled** zobrazuje užitečné informace "na první pohled", včetně grafů zobrazujících poslední metriky.

    ![Snímek obrazovky s panelem přehled](./media/monitoring/overview.png)

    * **Http 5xx**: Počet chyb na straně serveru, obvykle výjimky v ASP.NET core kódu.
    * **Data v**: Příchozí přenos dat přicházející do webové aplikace.
    * **Data Out**: Data odchozí z webové aplikace klientům.
    * **Požadavky**: Počet požadavků HTTP.
    * **Průměrná doba odezvy**: Průměrná doba, po kterou má webová aplikace reagovat na požadavky HTTP.

    Na této stránce najdete také několik samoobslužných nástrojů pro řešení potíží a optimalizaci.

    ![Snímek obrazovky se samoobslužným nástroji](./media/monitoring/wizards.png)

    * **Diagnostikovat a řešit problémy** je samoobslužný poradce při potížích.
    * **Application Insights** je pro profilování výkonu a chování aplikací a je popsáno dále v této části.
    * **App Service Advisor** vydává doporučení k vyladění prostředí aplikace.

## <a name="advanced-monitoring"></a>Pokročilé sledování

[Azure Monitor](/azure/monitoring-and-diagnostics/) je centralizovaná služba pro monitorování všech metrik a nastavení výstrah napříč službami Azure. V rámci Azure Monitor, správci můžete granularly sledovat výkon a identifikovat trendy. Každá služba Azure nabízí vlastní [sadu metrik](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions) pro Azure Monitor.

## <a name="profile-with-application-insights"></a>Profil s přehledy aplikací

[Application Insights](/azure/application-insights/app-insights-overview) je služba Azure pro analýzu výkonu a stability webových aplikací a způsobu, jakým je uživatelé používají. Data z Application Insights je širší a hlubší než Azure Monitor. Data mohou vývojářům a správcům poskytnout klíčové informace pro vylepšení aplikací. Přehledy aplikací lze přidat do prostředku služby Azure App Service bez změn kódu.

1. Otevřete [portál Azure](https://portal.azure.com)a přejděte na *\<mywebapp unique_number\> * App Service.
1. Na kartě **Přehled** klikněte na dlaždici **Přehled y aplikací.**

    ![Dlaždice Přehledy aplikací](./media/monitoring/app-insights.png)

1. Vyberte **přepínací** tlačítko Vytvořit nový zdroj. Použijte výchozí název prostředku a vyberte umístění pro prostředek Application Insights. Umístění se nemusí shodovat s umístěním vaší webové aplikace.

    ![Nastavení přehledů aplikací](./media/monitoring/new-app-insights.png)

1. Pro **runtime/framework**vyberte **ASP.NET Core**. Přijměte výchozí nastavení.
1. Vyberte **OK**. Pokud se zobrazí výzva k potvrzení, vyberte **pokračovat**.
1. Po vytvoření prostředku klikněte na název prostředku Application Insights a přejděte přímo na stránku Přehledy aplikací.

    ![Nový prostředek Application Insights je připraven](./media/monitoring/new-app-insights-done.png)

Při použití aplikace se data hromadí. Vyberte **Aktualizovat,** chcete-li znovu načíst okno s novými daty.

![Karta Přehledy aplikací – přehled](./media/monitoring/app-insights-overview.png)

Application Insights poskytuje užitečné informace na straně serveru bez další konfigurace. Chcete-li získat co největší hodnotu z Application Insights, [instrumentujte aplikaci pomocí sady Application Insights SDK](/azure/application-insights/app-insights-asp-net-core). Při správné konfiguraci poskytuje služba komplexní monitorování na webovém serveru a v prohlížeči, včetně výkonu na straně klienta. Další informace naleznete v [dokumentaci k application insights](/azure/application-insights/app-insights-overview).

## <a name="logging"></a>Protokolování

Protokoly webového serveru a aplikací jsou ve výchozí službě Azure App Service ve výchozím nastavení zakázané. Povolte protokoly pomocí následujících kroků:

1. Otevřete [portál Azure](https://portal.azure.com)a přejděte na *\<mywebapp unique_number\> * App Service.
1. V nabídce vlevo přejděte dolů do části **Monitorování.** Vyberte **možnost Protokoly diagnostiky**.

    ![Propojení diagnostických protokolů](./media/monitoring/logging.png)

1. Zapněte **protokolování aplikací (souborový systém).** Pokud se zobrazí výzva, kliknutím na toto pole nainstalujte rozšíření a povolte protokolování aplikací ve webové aplikaci.
1. Nastavte **protokolování webového serveru** na **systém souborů**.
1. Zadejte **dobu uchovávání ve** dnech. Například 30.
1. Klikněte na **Uložit**.

ASP.NET pro webovou aplikaci jsou generovány protokoly Core a web server (App Service). Lze je stáhnout pomocí zobrazených informací FTP/FTPS. Heslo je stejné jako pověření nasazení vytvořené dříve v této příručce. Protokoly lze [streamovat přímo do místního počítače pomocí PowerShellu nebo Azure CLI](/azure/app-service/web-sites-enable-diagnostic-log#download). Protokoly lze [také zobrazit v Application Insights](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights).

## <a name="log-streaming"></a>Protokolovat streamování

Protokoly aplikací a webových serverů lze streamovat v reálném čase prostřednictvím portálu.

1. Otevřete [portál Azure](https://portal.azure.com)a přejděte na *\<mywebapp unique_number\> * App Service.
1. V nabídce vlevo přejděte dolů do části **Sledování** a vyberte **Log stream**.

    ![Snímek obrazovky s odkazem na datový proud protokolu](./media/monitoring/log-stream.png)

Protokoly můžete také [datových proudů prostřednictvím Azure CLI nebo Azure PowerShell](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs), včetně prostřednictvím cloudshellu.

## <a name="alerts"></a>Výstrahy

Azure Monitor také poskytuje [výstrahy v reálném čase](/azure/monitoring-and-diagnostics/insights-alerts-portal) na základě metrik, událostí správy a dalších kritérií.

> *Poznámka: V současné době upozornění na metriky webové aplikace je k dispozici pouze ve službě Výstrahy (klasické).*

[Služba Výstrahy (klasické)](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal) najdete ve službě Azure Monitor nebo v části **Monitorování** v nastavení služby App Service.

![Odkaz Upozornění (klasické)](./media/monitoring/alerts.png)

## <a name="live-debugging"></a>Živé ladění

Azure App Service lze [ladit vzdáleně s Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) při protokoly neposkytují dostatek informací. Vzdálené ladění však vyžaduje, aby aplikace byla kompilována se symboly ladění. Ladění by nemělo být provedeno v produkčním prostředí, s výjimkou poslední možnosti.

## <a name="conclusion"></a>Závěr

V této části jste dokončili následující úkoly:

* Vyhledání základních dat monitorování a řešení potíží na webu Azure Portal
* Zjistěte, jak Azure Monitor poskytuje hlubší pohled na metriky napříč všemi službami Azure
* Připojení webové aplikace pomocí Application Insights pro profilování aplikací
* Zapnutí protokolování a zjištění, kde stáhnout protokoly
* Streamovat protokoly v reálném čase
* Informace o tom, kde nastavit výstrahy
* Přečtěte si o vzdáleném ladění webových aplikací Azure App Service.

## <a name="additional-reading"></a>Dodatečné čtení

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [Sledování výkonu webových aplikací Azure pomocí přehledů aplikací](/azure/application-insights/app-insights-azure-web-apps)
* [Povolení protokolování diagnostiky pro webové aplikace ve službě Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log)
* [Poradce při potížích s webovou aplikací ve službě Azure App Service pomocí Visual Studia](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [Vytváření klasických upozornění na metriky ve službách Azure Monitor pro Azure – portál Azure](/azure/monitoring-and-diagnostics/insights-alerts-portal)

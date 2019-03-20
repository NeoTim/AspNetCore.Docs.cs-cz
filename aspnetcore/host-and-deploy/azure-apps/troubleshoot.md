---
title: Řešení potíží s ASP.NET Core ve službě Azure App Service
author: guardrex
description: Zjistěte, jak diagnostikovat problémy s nasazením služby ASP.NET Core Azure App Service.
ms.author: riande
ms.custom: mvc
ms.date: 03/06/2019
uid: host-and-deploy/azure-apps/troubleshoot
ms.openlocfilehash: 36c2bdfa585a0fd54ca93bf4c0edb4cf6f7d934a
ms.sourcegitcommit: 57792e5f594db1574742588017c708350958bdf0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2019
ms.locfileid: "58265450"
---
# <a name="troubleshoot-aspnet-core-on-azure-app-service"></a>Řešení potíží s ASP.NET Core ve službě Azure App Service

Podle [Luke Latham](https://github.com/guardrex)

[!INCLUDE [Azure App Service Preview Notice](../../includes/azure-apps-preview-notice.md)]

Tento článek obsahuje pokyny o tom, jak Diagnostika ASP.NET Core problém při spuštění aplikace pomocí diagnostických nástrojů služby Azure App Service. Další pomoc při řešení potíží, najdete v části [Přehled diagnostiky služby Azure App Service](/azure/app-service/app-service-diagnostics) a [jak: Monitorování aplikací ve službě Azure App Service](/azure/app-service/web-sites-monitor) v dokumentaci k Azure.

## <a name="app-startup-errors"></a>Chyby při spuštění aplikace

**502.5 zpracovat selhání** pracovní proces se nezdaří. Aplikace se nespustí.

[Modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) pokusy o spuštění pracovního procesu, ale nepovede spustit. Zkoumání v protokolu událostí aplikace často pomáhá při řešení tohoto typu problému. Přístup k protokolu je podrobně [protokolu událostí aplikace](#application-event-log) oddílu.

*502.5 selhání procesu* při nesprávně nakonfigurované aplikace způsobí, že se pracovní proces selže, vrátí se chybová stránka:

![Okno prohlížeče zobrazující stránku 502.5 selhání procesu](troubleshoot/_static/process-failure-page.png)

**Chyba 500 interní Server**

Spuštění aplikace, ale chybu brání splnění žádosti. na serveru.

Při spuštění nebo při vytváření odpovědi, k této chybě dochází v kódu aplikace. Odpověď může obsahovat žádný obsah nebo se může zobrazit odpovědi *500 – Interní chyba serveru* v prohlížeči. V protokolu událostí aplikace obvykle hlásí, že aplikace se normálně spustit. Z pohledu serveru, který je správný. Aplikace začal, ale nemůže generovat platnou odpověď. [Spusťte aplikaci v konzole Kudu](#run-the-app-in-the-kudu-console) nebo [povolit protokol stdout modul ASP.NET Core](#aspnet-core-module-stdout-log) k vyřešení tohoto problému.

**Obnovení připojení**

Pokud dojde k chybě po odeslání hlavičky, bude příliš pozdě pro server k odeslání **500 – Interní chyba serveru** , když dojde k chybě. Často se to stane, když dojde k chybě při serializaci složitých objektů pro odpověď. Tento typ chyby se zobrazí jako *obnovení připojení* chyba na straně klienta. [Protokolování aplikací](xref:fundamentals/logging/index) mohou pomoci při řešení těchto typů chyb.

## <a name="default-startup-limits"></a>Výchozí omezení při spuštění

Modul ASP.NET Core je nakonfigurovaná s výchozí *startupTimeLimit* 120 sekund. Když necháte na výchozí hodnotu, aplikace může trvat až dvě minuty, spusťte před modulu protokoly selhání procesu. Informace o konfiguraci modulu najdete v tématu [atributy elementu aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).

## <a name="troubleshoot-app-startup-errors"></a>Řešení chyb při spuštění aplikace

### <a name="application-event-log"></a>Protokol událostí aplikace

Chcete-li získat přístup k protokolu událostí aplikace, použijte **diagnostikovat a řešit problémy** okna na webu Azure Portal:

1. Na webu Azure Portal, otevřete aplikaci v **App Services**.
1. Vyberte **diagnostikovat a řešit problémy**.
1. Vyberte **diagnostické nástroje** záhlaví.
1. V části **Support Tools**, vyberte **události aplikace** tlačítko.
1. Zkontrolujte nejnovější chybu poskytované *IIS AspNetCoreModule* nebo *IIS AspNetCoreModule V2* položku v **zdroj** sloupce.

O alternativu k použití **diagnostikovat a řešit problémy** okno je prozkoumat soubor protokolu událostí aplikace přímo pomocí [Kudu](https://github.com/projectkudu/kudu/wiki):

1. Otevřít **Rozšířené nástroje** v **nástroje pro vývoj** oblasti. Vyberte **Přejít&rarr;**  tlačítko. Otevře se konzola Kudu v nové záložce prohlížeče nebo v okně.
1. V navigačním panelu v horní části stránky otevřete **konzolou pro ladění** a vyberte **CMD**.
1. Otevřít **LogFiles** složky.
1. Vyberte ikonu tužky vedle *eventlog.xml* souboru.
1. Vyhledejte v protokolu. Přejděte do dolní části zobrazíte nejnovější události v protokolu.

### <a name="run-the-app-in-the-kudu-console"></a>Spusťte aplikaci v konzole Kudu

Mnoho chyb při spuštění nevytvářejí užitečné informace v protokolu událostí aplikace. Aplikaci můžete spustit [Kudu](https://github.com/projectkudu/kudu/wiki) vzdálené spuštění konzoly ke zjištění chyby:

1. Otevřít **Rozšířené nástroje** v **nástroje pro vývoj** oblasti. Vyberte **Přejít&rarr;**  tlačítko. Otevře se konzola Kudu v nové záložce prohlížeče nebo v okně.
1. V navigačním panelu v horní části stránky otevřete **konzolou pro ladění** a vyberte **CMD**.

#### <a name="test-a-32-bit-x86-app"></a>Testování aplikací pro 32bitovou (x 86)

##### <a name="current-release"></a>Aktuální verze

1. `cd d:\home\site\wwwroot`
1. Spuštění aplikace:
   * Pokud je aplikace [nasazení závisí na architektuře](/dotnet/core/deploying/#framework-dependent-deployments-fdd):

     ```console
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * Pokud je aplikace [samostatná nasazení](/dotnet/core/deploying/#self-contained-deployments-scd):

     ```console
     {ASSEMBLY NAME}.exe
     ```

Výstup z aplikace zobrazuje všechny chyby konzoly je přesměrovaná do konzoly Kudu.

##### <a name="framework-dependent-deployment-running-on-a-preview-release"></a>Nasazení závisí na architektuře, které běží na verzi preview

*Vyžaduje instalaci ASP.NET Core {VERSION} (x86) lokality rozšíření modulu Runtime.*

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` je verze modulu runtime)
1. Spuštění aplikace: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

Výstup z aplikace zobrazuje všechny chyby konzoly je přesměrovaná do konzoly Kudu.

#### <a name="test-a-64-bit-x64-app"></a>Testování aplikace s 64bitovou (x 64)

##### <a name="current-release"></a>Aktuální verze

* Pokud aplikace je 64bitová verze (x64) [nasazení závisí na architektuře](/dotnet/core/deploying/#framework-dependent-deployments-fdd):
  1. `cd D:\Program Files\dotnet`
  1. Spuštění aplikace: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`
* Pokud je aplikace [samostatná nasazení](/dotnet/core/deploying/#self-contained-deployments-scd):
  1. `cd D:\home\site\wwwroot`
  1. Spuštění aplikace: `{ASSEMBLY NAME}.exe`

Výstup z aplikace zobrazuje všechny chyby konzoly je přesměrovaná do konzoly Kudu.

##### <a name="framework-dependent-deployment-running-on-a-preview-release"></a>Nasazení závisí na architektuře, které běží na verzi preview

*Vyžaduje instalaci ASP.NET Core {VERSION} (x64) lokality rozšíření modulu Runtime.*

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` je verze modulu runtime)
1. Spuštění aplikace: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

Výstup z aplikace zobrazuje všechny chyby konzoly je přesměrovaná do konzoly Kudu.

### <a name="aspnet-core-module-stdout-log"></a>ASP.NET Core modulu stdout protokolu

Protokol stdout modul ASP.NET Core zaznamenává často užitečné chybové zprávy nebyl nalezen v protokolu událostí aplikace. Povolení a zobrazení protokolů stdout:

1. Přejděte **diagnostikovat a řešit problémy** okna na webu Azure Portal.
1. V části **vyberte KATEGORII problému**, vyberte **vypnutí webové aplikace** tlačítko.
1. V části **navrhované řešení** > **povolit přesměrování protokolu Stdout**, vyberte tlačítko **otevřete konzoly Kudu můžete upravit soubor Web.Config**.
1. V Kudu **konzole diagnostiky**, otevřete složky do cesty **lokality** > **wwwroot**. Posuňte se dolů zobrazit *web.config* soubor v dolní části seznamu.
1. Klikněte na ikonu tužky vedle *web.config* souboru.
1. Nastavte **stdoutLogEnabled** k `true` a změnit **stdoutLogFile** cestu k: `\\?\%home%\LogFiles\stdout`.
1. Vyberte **Uložit** uložte aktualizovaný *web.config* souboru.
1. Vytvořte žádost do aplikace.
1. Vraťte se do portálu Azure portal. Vyberte **Rozšířené nástroje** okna portálu **nástroje pro vývoj** oblasti. Vyberte **Přejít&rarr;**  tlačítko. Otevře se konzola Kudu v nové záložce prohlížeče nebo v okně.
1. V navigačním panelu v horní části stránky otevřete **konzolou pro ladění** a vyberte **CMD**.
1. Vyberte **LogFiles** složky.
1. Zkontrolujte **změněné** sloupci a vyberte ikonu tužky a upravte standardního výstupu skriptu protokolů pomocí data poslední úpravy.
1. Po otevření souboru protokolu, zobrazí se chyba.

Zakážete protokolování stdout po dokončení odstraňování potíží:

1. V Kudu **konzole diagnostiky**, vraťte se na cestu **lokality** > **wwwroot** zobrazíte *web.config* souboru. Otevřít **web.config** soubor znovu tak, že vyberete ikonu tužky.
1. Nastavte **stdoutLogEnabled** k `false`.
1. Vyberte **Uložit** k uložení souboru.

> [!WARNING]
> Nepodařilo se zakázat protokol stdout může vést k selhání aplikace nebo serveru. Neexistuje žádné omezení velikosti souboru protokolu nebo počet souborů protokolů, které jsou vytvořeny. Používejte pouze stdout protokolování k řešení problémů při spuštění aplikace.
>
> Pro obecné protokolování v aplikaci ASP.NET Core po spuštění, použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly. Další informace najdete v tématu [zprostředkovatele přihlášení třetí strany](xref:fundamentals/logging/index#third-party-logging-providers).

::: moniker range=">= aspnetcore-2.2"

### <a name="aspnet-core-module-debug-log"></a>Protokol ladění modulu ASP.NET Core

Protokol ladění modul ASP.NET Core nabízí další, podrobnější protokolování z modulu ASP.NET Core. Povolení a zobrazení protokolů stdout:

1. Pokud chcete povolit rozšířené diagnostický protokol, proveďte jednu z těchto:
   * Postupujte podle pokynů v [rozšířené diagnostické protokoly](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) konfigurace aplikace pro rozšířené protokolování diagnostiky. Opětovné nasazení aplikace.
   * Přidat `<handlerSettings>` ukazuje [rozšířené diagnostické protokoly](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) živé aplikace *web.config* soubor pomocí konzoly Kudu:
     1. Otevřít **Rozšířené nástroje** v **nástroje pro vývoj** oblasti. Vyberte **Přejít&rarr;**  tlačítko. Otevře se konzola Kudu v nové záložce prohlížeče nebo v okně.
     1. V navigačním panelu v horní části stránky otevřete **konzolou pro ladění** a vyberte **CMD**.
     1. Otevření složky a cesta **lokality** > **wwwroot**. Upravit *web.config* souboru tak, že vyberete tlačítko s tužkou. Přidat `<handlerSettings>` jak můžete vidět v [rozšířené diagnostické protokoly](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs). Vyberte tlačítko **Uložit**.
1. Otevřít **Rozšířené nástroje** v **nástroje pro vývoj** oblasti. Vyberte **Přejít&rarr;**  tlačítko. Otevře se konzola Kudu v nové záložce prohlížeče nebo v okně.
1. V navigačním panelu v horní části stránky otevřete **konzolou pro ladění** a vyberte **CMD**.
1. Otevření složky a cesta **lokality** > **wwwroot**. Pokud jste nezadali jste cestu *aspnetcore-debug.log* souboru, soubor se zobrazí v seznamu. Pokud jste zadali cestu, přejděte do umístění souboru protokolu.
1. Otevřete soubor protokolu s tlačítkem tužky vedle jeho názvu.

Zakážete protokolování ladění po dokončení odstraňování potíží:

1. Zakázat protokol vylepšené ladění, proveďte některý z následujících akcí:
   * Odeberte `<handlerSettings>` z *web.config* soubor místně a aplikaci znovu nasaďte.
   * Upravit pomocí konzoly Kudu *web.config* souboru a odebrat `<handlerSettings>` oddílu. Uložte soubor.

> [!WARNING]
> Nepodařilo se zakázat protokol ladění může vést k selhání aplikace nebo serveru. Neexistuje žádné omezení velikosti souborů protokolu. Pouze pomocí protokolování ladění k řešení problémů při spuštění aplikace.
>
> Pro obecné protokolování v aplikaci ASP.NET Core po spuštění, použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly. Další informace najdete v tématu [zprostředkovatele přihlášení třetí strany](xref:fundamentals/logging/index#third-party-logging-providers).

::: moniker-end

## <a name="common-startup-errors"></a>Běžné chyby spuštění

Viz <xref:host-and-deploy/azure-iis-errors-reference>. Většina běžných problémů, které brání spuštění aplikace jsou zahrnuté v referenčním tématu.

## <a name="slow-or-hanging-app"></a>Pomalá nebo Změ aplikace

Když aplikace reaguje pomalu nebo přestane reagovat na vyžádání, naleznete v následujících článcích:

* [Řešení problémů s výkonem pomalých webových aplikací ve službě Azure App Service](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [Pomocí rozšíření webu dojít k chybě služby Diagnostika zachycení výpisu paměti pro výjimek přerušované problémy nebo problémy s výkonem ve webové aplikaci Azure](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

## <a name="remote-debugging"></a>Vzdálené ladění

V následujících tématech:

* [Vzdálené ladění webových aplikací části řešení potíží s webovou aplikací ve službě Azure App Service pomocí sady Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) (dokumentace k Azure)
* [Vzdálené ladění ASP.NET Core ve službě IIS v Azure v sadě Visual Studio 2017](/visualstudio/debugger/remote-debugging-azure) (dokumentace k sadě Visual Studio)

## <a name="application-insights"></a>Application Insights

[Application Insights](https://azure.microsoft.com/services/application-insights/) poskytuje telemetrická data z aplikací hostovaných ve službě Azure App Service, včetně protokolování a generování sestav funkce chyb. Application Insights může jenom nahlásit to o chybách, ke kterým dochází po spuštění aplikace, když funkce protokolování aplikace budou k dispozici. Další informace najdete v tématu [Application Insights pro ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).

## <a name="monitoring-blades"></a>Monitorování oken

Monitorování okna poskytují alternativní řešení potíží s prostředí pro metod popsaných výše v tomto tématu. Těchto oknech lze diagnostikovat chyby 500-series.

Potvrďte, že jsou nainstalované rozšíření ASP.NET Core. Pokud nejsou rozšíření nainstalována, nainstalujte je ručně:

1. V **nástroje pro vývoj** části okna, vyberte **rozšíření** okno.
1. **Rozšíření ASP.NET Core** by se měla zobrazit v seznamu.
1. Pokud není nainstalovaná rozšíření, vyberte **přidat** tlačítko.
1. Zvolte **rozšíření ASP.NET Core** ze seznamu.
1. Vyberte **OK** přijměte právní podmínky.
1. Vyberte **OK** na **přidat rozšíření** okno.
1. Informační zprávy určuje, kdy jsou úspěšně nainstalováni rozšíření.

Pokud není povoleno protokolování stdout, postupujte podle těchto kroků:

1. Na webu Azure Portal, vyberte **Rozšířené nástroje** okna portálu **nástroje pro vývoj** oblasti. Vyberte **Přejít&rarr;**  tlačítko. Otevře se konzola Kudu v nové záložce prohlížeče nebo v okně.
1. V navigačním panelu v horní části stránky otevřete **konzolou pro ladění** a vyberte **CMD**.
1. Otevření složky a cesta **lokality** > **wwwroot** a posuňte se dolů zobrazit *web.config* soubor v dolní části seznamu.
1. Klikněte na ikonu tužky vedle *web.config* souboru.
1. Nastavte **stdoutLogEnabled** k `true` a změnit **stdoutLogFile** cestu k: `\\?\%home%\LogFiles\stdout`.
1. Vyberte **Uložit** uložte aktualizovaný *web.config* souboru.

Přejděte k aktivaci protokolování diagnostiky:

1. Na webu Azure Portal, vyberte **diagnostické protokoly** okno.
1. Vyberte **na** přepnout **protokolování aplikace (systém souborů)** a **podrobné chybové zprávy**. Vyberte **Uložit** tlačítko v horní části okna.
1. Chcete-li zahrnout trasování chybných požadavků, označované také jako protokolování se nezdařil požadavek událostí do vyrovnávací paměti (FREB), vyberte **na** přepínače **chybných požadavků**.
1. Vyberte **stream protokolů** okno, ve kterém je okamžitě uvedený v části **diagnostické protokoly** okno na portálu.
1. Vytvořte žádost do aplikace.
1. V rámci datového proudu dat protokolu je označeno příčinu chyby.

Je potřeba zakázat protokolování stdout po dokončení odstraňování potíží. Přečtěte si pokyny v [protokolů stdout modul ASP.NET Core](#aspnet-core-module-stdout-log) oddílu.

Chcete-li zobrazit protokoly trasování neúspěšných požadavků (protokoly FREB):

1. Přejděte **diagnostikovat a řešit problémy** okna na webu Azure Portal.
1. Vyberte **nepovedlo vyžádat trasování protokoly** z **SUPPORT TOOLS** oblasti na bočním panelu.

Zobrazit [část povolit protokolování diagnostiky pro webové aplikace v Azure App Service tématu trasování požadavku se nezdařilo](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) a [nejčastějších dotazech týkajících se výkonu aplikace pro Web Apps v Azure: Jak můžu zapnout trasování chybných požadavků? ](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) Další informace.

Další informace najdete v tématu [povolit protokolování diagnostiky pro webové aplikace ve službě Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).

> [!WARNING]
> Nepodařilo se zakázat protokol stdout může vést k selhání aplikace nebo serveru. Neexistuje žádné omezení velikosti souboru protokolu nebo počet souborů protokolů, které jsou vytvořeny.
>
> Pro rutiny protokolování v aplikaci ASP.NET Core, použijte protokolování knihovnu, která omezuje velikost souboru protokolu a otočí protokoly. Další informace najdete v tématu [zprostředkovatele přihlášení třetí strany](xref:fundamentals/logging/index#third-party-logging-providers).

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [Řešení potíží s webovou aplikací ve službě Azure App Service pomocí sady Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [Řešení potíží s chybami HTTP typu "502 – Chybná brána" a "503 Služba není dostupná" ve službě Azure web apps](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [Řešení problémů s výkonem pomalých webových aplikací ve službě Azure App Service](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [Nejčastějších dotazech týkajících se výkonu aplikace pro Web Apps v Azure](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [Azure Web App izolovaného prostoru (omezení spuštění modulu runtime služby App Service)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [Azure Friday: Azure App Service diagnostiku a řešení potíží s prostředí (12 pětiminutové video)](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

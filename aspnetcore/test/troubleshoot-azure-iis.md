---
title: Řešení potíží ASP.NET jádro ve službě Azure App Service a IIS
author: rick-anderson
description: Zjistěte, jak diagnostikovat problémy s nasazením služby Azure App Service a Internet Information Services (IIS) aplikací ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: test/troubleshoot-azure-iis
ms.openlocfilehash: 671f68da2ea261cb8ae32a9d5ef875217859054d
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78655328"
---
# <a name="troubleshoot-aspnet-core-on-azure-app-service-and-iis"></a>Řešení potíží ASP.NET jádro ve službě Azure App Service a IIS

Podle [Justin Kotalik](https://github.com/jkotalik)

::: moniker range=">= aspnetcore-3.0"

Tento článek obsahuje informace o běžných chybách při spuštění aplikace a pokyny, jak diagnostikovat chyby při nasazení aplikace do služby Azure App Service nebo IIS:

[Chyby při spuštění aplikace](#app-startup-errors)  
Vysvětluje běžné scénáře stavového kódu HTTP při spuštění.

[Poradce při potížích se službou Azure App Service](#troubleshoot-on-azure-app-service)  
Poskytuje rady pro řešení potíží pro aplikace nasazené do služby Azure App Service.

[Odstraňování potíží ve službě IIS](#troubleshoot-on-iis)  
Poskytuje rady pro řešení potíží s aplikacemi nasazenými do služby IIS nebo spuštěných místně ve službě IIS Express. Pokyny platí pro nasazení plochy systému Windows Server i Windows.

[Vymazat mezipaměti balíčků](#clear-package-caches)  
Vysvětluje, co dělat, když nesouvislé balíčky přerušit aplikaci při provádění hlavních upgradů nebo změny verze balíčku.

[Další zdroje](#additional-resources)  
Obsahuje další témata pro řešení potíží.

## <a name="app-startup-errors"></a>Chyby při spuštění aplikace

V sadě Visual Studio, ASP.NET projektu Core výchozí [iis express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hostování během ladění. A *502.5 - Selhání procesu* nebo *500.30 - Start Selhání,* ke kterému dochází při ladění místně lze diagnostikovat pomocí rady v tomto tématu.

### <a name="40314-forbidden"></a>403.14 Zakázáno

Aplikace se nespustí. Je zaznamenána následující chyba:

```
The Web server is configured to not list the contents of this directory.
```

Chyba je obvykle způsobena přerušeným nasazením v hostitelském systému, který zahrnuje některý z následujících scénářů:

* Aplikace je nasazena do nesprávné složky v hostitelském systému.
* Procesu nasazení se nepodařilo přesunout všechny soubory a složky aplikace do složky nasazení v hostitelském systému.
* Soubor *web.config* chybí v nasazení nebo je obsah souboru *web.config* poškozen.

Proveďte následující kroky:

1. Odstraňte všechny soubory a složky ze složky nasazení v hostitelském systému.
1. Znovu nasadit obsah složky *publikování* aplikace do hostitelského systému pomocí normální metody nasazení, jako je Visual Studio, PowerShell nebo ruční nasazení:
   * Zkontrolujte, zda je soubor *web.config* v nasazení přítomen a zda je jeho obsah správný.
   * Při hostování ve službě Azure App Service zkontrolujte, že se aplikace nasadí do `D:\home\site\wwwroot` složky.
   * Pokud je aplikace hostována službou IIS, zkontrolujte, zda je aplikace nasazena do **fyzické cesty** služby IIS zobrazené v **základním nastavení** **správce služby IIS**.
1. Zkontrolujte, zda jsou všechny soubory a složky aplikace nasazeny porovnáním nasazení v hostitelském systému s obsahem složky *publikování* projektu.

Další informace o rozložení publikované aplikace ASP.NET <xref:host-and-deploy/directory-structure>Core najdete v tématu . Další informace o souboru *web.config* naleznete v tématu <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.

### <a name="500-internal-server-error"></a>500 – Interní chyba serveru

Aplikace se spustí, ale chyba zabrání serveru v plnění požadavku.

K této chybě dochází v kódu aplikace při spuštění nebo při vytváření odpovědi. Odpověď může obsahovat žádný obsah nebo odpověď může zobrazit jako *500 Vnitřní chyba serveru* v prohlížeči. Protokol událostí aplikace obvykle uvádí, že aplikace byla spuštěna normálně. Z pohledu serveru je to správné. Aplikace se spustila, ale nemůže generovat platnou odpověď. Spusťte aplikaci na příkazovém řádku na serveru nebo povolte protokol stdout ASP.NET Core Module k řešení problému.

### <a name="5000-in-process-handler-load-failure"></a>Selhání zatížení obslužné rutiny procesu 500.0

Pracovní proces se nezdaří. Aplikace se nespustí.

Došlo k neznámé chybě načítání ASP.NET součástmi [základního modulu.](xref:host-and-deploy/aspnet-core-module) Proveďte jednu z následujících akcí:

* Obraťte se [na podporu společnosti Microsoft](https://support.microsoft.com/oas/default.aspx?prid=15832) (vyberte nástroje pro **vývojáře** **a potom ASP.NET jádrem).**
* Zeptejte se na Stack Přetečení.
* Posuzte problém na našem [úložišti GitHub](https://github.com/dotnet/AspNetCore).

### <a name="50030-in-process-startup-failure"></a>500.30 Selhání spuštění v průběhu procesu

Pracovní proces se nezdaří. Aplikace se nespustí.

[ASP.NET základní modul](xref:host-and-deploy/aspnet-core-module) se pokusí spustit .NET Core CLR v procesu, ale nepodaří spustit. Příčinu selhání spuštění procesu lze obvykle určit z položek v protokolu událostí aplikace a ASP.NET protokolu stdout základního modulu.

Běžné podmínky selhání:

* Aplikace je nesprávně nakonfigurována kvůli cílení na verzi sdíleného rozhraní ASP.NET Core, která není k dispozici. Zkontrolujte, které verze ASP.NET sdíleného rozhraní Core jsou nainstalovány v cílovém počítači.
* Použití azure key vault, nedostatek oprávnění k trezoru klíčů. Zkontrolujte zásady přístupu v cílovém trezoru klíčů a ujistěte se, že jsou udělena správná oprávnění.

### <a name="50031-ancm-failed-to-find-native-dependencies"></a>500.31 ANCM se nepodařilo najít nativní závislosti

Pracovní proces se nezdaří. Aplikace se nespustí.

Základní [modul ASP.NET](xref:host-and-deploy/aspnet-core-module) pokusí spustit modul .NET Core v procesu, ale nepodaří se jej spustit. Nejčastější příčinou tohoto selhání při spuštění `Microsoft.NETCore.App` `Microsoft.AspNetCore.App` je, když není nainstalován nebo runtime. Pokud se aplikace nasadí na cílovou ASP.NET Core 3.0 a tato verze v počítači neexistuje, dojde k této chybě. Následuje ukázková chybová zpráva:

```
The specified framework 'Microsoft.NETCore.App', version '3.0.0' was not found.
  - The following frameworks were found:
      2.2.1 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview5-27626-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27713-13 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27714-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27723-08 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
```

Chybová zpráva obsahuje seznam všech nainstalovaných verzí rozhraní .NET Core a verze požadované aplikací. Chcete-li tuto chybu opravit, buď:

* Nainstalujte do počítače příslušnou verzi rozhraní .NET Core.
* Změňte aplikaci tak, aby cílila na verzi .NET Core, která je v počítači k dispozici.
* Publikujte aplikaci jako [samostatné nasazení](/dotnet/core/deploying/#self-contained-deployments-scd).

Při spuštění ve `ASPNETCORE_ENVIRONMENT` vývoji (proměnná prostředí je nastavena na `Development`) konkrétní chyba je zapsána do odpovědi HTTP. Příčina selhání spuštění procesu je také nalezena v protokolu událostí aplikace.

### <a name="50032-ancm-failed-to-load-dll"></a>500.32 ANCM se nepodařilo načíst dll

Pracovní proces se nezdaří. Aplikace se nespustí.

Nejčastější příčinou této chyby je, že aplikace je publikována pro nekompatibilní architekturu procesoru. Pokud pracovní proces běží jako 32bitová aplikace a aplikace byla publikována na cíl 64bit, dojde k této chybě.

Chcete-li tuto chybu opravit, buď:

* Znovu publikujte aplikaci pro stejnou architekturu procesoru jako pracovní proces.
* Publikujte aplikaci jako [nasazení závislé na rámci](/dotnet/core/deploying/#framework-dependent-executables-fde).

### <a name="50033-ancm-request-handler-load-failure"></a>500.33 Ancm Request Handler Selhání zatížení

Pracovní proces se nezdaří. Aplikace se nespustí.

Aplikace neodkazovala na `Microsoft.AspNetCore.App` rozhraní. ASP.NET core `Microsoft.AspNetCore.App` modulu může hostovat pouze aplikace, které cílí na [architekturu](xref:host-and-deploy/aspnet-core-module).

Chcete-li tuto chybu opravit, zkontrolujte, zda aplikace cílí na architekturu. `Microsoft.AspNetCore.App` Zkontrolujte, `.runtimeconfig.json` zda chcete ověřit rámec, na který se aplikace zaměřuje.

### <a name="50034-ancm-mixed-hosting-models-not-supported"></a>500.34 CM smíšené hostingové modely nejsou podporovány

Pracovní proces nemůže spustit v procesu aplikace a mimo proces aplikace ve stejném procesu.

Chcete-li tuto chybu opravit, spouštějte aplikace v samostatných fondech aplikací služby IIS.

### <a name="50035-ancm-multiple-in-process-applications-in-same-process"></a>500.35 ANCM více in-process aplikací ve stejném procesu

Pracovní proces nemůže ve stejném procesu spouštět více aplikací v procesu.

Chcete-li tuto chybu opravit, spouštějte aplikace v samostatných fondech aplikací služby IIS.

### <a name="50036-ancm-out-of-process-handler-load-failure"></a>500.36 ANCM selhání zatížení obslužné rutiny

Obslužná rutina požadavku mimo proces *aspnetcorev2_outofprocess.dll*není vedle souboru *aspnetcorev2.dll.* To znamená poškozenou instalaci [ASP.NET základního modulu](xref:host-and-deploy/aspnet-core-module).

Chcete-li tuto chybu opravit, opravte instalaci [sady .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (pro službu IIS) nebo sady Visual Studio (pro službu IIS Express).

### <a name="50037-ancm-failed-to-start-within-startup-time-limit"></a>500.37 ANCM se nepodařilo spustit v rámci časového limitu spuštění

Ancm se nepodařilo spustit v rámci provied čas spuštění časového limitu. Ve výchozím nastavení je časový limit 120 sekund.

K této chybě může dojít při spuštění velkého počtu aplikací ve stejném počítači. Při spuštění zkontrolujte špičky využití procesoru a paměti na serveru. Možná budete muset rozložit proces spuštění více aplikací.

### <a name="5025-process-failure"></a>502.5 – Selhání procesu

Pracovní proces se nezdaří. Aplikace se nespustí.

Základní [modul ASP.NET](xref:host-and-deploy/aspnet-core-module) se pokusí spustit pracovní proces, ale nepodaří se jej spustit. Příčinu selhání spuštění procesu lze obvykle určit z položek v protokolu událostí aplikace a ASP.NET protokolu stdout základního modulu.

Obvyklá podmínka selhání je, že aplikace je nesprávně nakonfigurovaná kvůli cílení na verzi sdíleného rozhraní ASP.NET Core, která není k dispozici. Zkontrolujte, které verze ASP.NET sdíleného rozhraní Core jsou nainstalovány v cílovém počítači. Sdílená *architektura* je sada sestavení (*soubory DLL),* které jsou nainstalovány v počítači a odkazovány metabalíčkem, například `Microsoft.AspNetCore.App`. Odkaz na metabalíček může určit minimální požadovanou verzi. Další informace naleznete [v tématu sdílený rámec](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).

Chybová stránka *502.5 Process Failure* je vrácena, když selhání konfigurace hostitele nebo aplikace způsobí selhání pracovního procesu:

### <a name="failed-to-start-application-errorcode-0x800700c1"></a>Spuštění aplikace se nezdařilo (ErrorCode '0x800700c1')

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

Spuštění aplikace se nezdařilo, protože nebylo možné načíst sestavení aplikace (*DLL*).

K této chybě dochází, pokud je neodpovídající bitness mezi publikovanou aplikací a w3wp/iisexpress procesu.

Zkontrolujte, zda je 32bitové nastavení fondu aplikací správné:

1. Vyberte fond aplikací ve **fondech aplikací**Správce služby IIS .
1. V části Upravit fond aplikací v panelu **Akce** vyberte **Upřesnit nastavení** v části Upravit **fond aplikací.**
1. Nastavit **povolit 32bitové aplikace**:
   * Pokud nasazujete 32bitovou (x86) `True`aplikaci, nastavte hodnotu na .
   * Pokud nasazujete 64bitovou (x64) `False`aplikaci, nastavte hodnotu na .

Zkontrolujte, zda v souboru `<Platform>` projektu není konflikt mezi vlastností MSBuild a publikovanou bitness aplikace.

### <a name="connection-reset"></a>Resetování připojení

Pokud dojde k chybě po odeslání záhlaví, je příliš pozdě na to, aby server odeslal **chybu interního serveru 500,** když dojde k chybě. K tomu často dochází, když dojde k chybě během serializace složitých objektů pro odpověď. Tento typ chyby se zobrazí jako chyba *obnovení připojení* v klientovi. [Protokolování aplikací](xref:fundamentals/logging/index) může pomoci při řešení těchto typů chyb.

### <a name="default-startup-limits"></a>Výchozí limity pro spuštění

[Základní modul ASP.NET](xref:host-and-deploy/aspnet-core-module) je nakonfigurován s výchozím *spouštěcím limitem* 120 sekund. Pokud zůstane na výchozí hodnotu, aplikace může trvat až dvě minuty ke spuštění, než modul zaznamená selhání procesu. Informace o konfiguraci modulu naleznete v [tématu Atributy prvku aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).

## <a name="troubleshoot-on-azure-app-service"></a>Poradce při potížích se službou Azure App Service

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a>Protokol událostí aplikace (služba Azure App Service)

Chcete-li získat přístup k protokolu událostí aplikace, použijte okno **Diagnostikovat a vyřešit problémy** na webu Azure Portal:

1. Na webu Azure Portal otevřete aplikaci ve **Službách aplikací**.
1. Vyberte **Diagnostikovat a řešit problémy**.
1. Vyberte nadpis **Diagnostické nástroje.**
1. V části **Nástroje podpory**vyberte tlačítko **Události aplikace.**
1. Prohlédněte si nejnovější chybu poskytovanou položkou *AspNetCoreModule* nebo *IIS AspNetCoreModule V2* ve sloupci **Zdroj.**

Alternativou k použití **funkce Diagnostika a řešení problémů** je prozkoumat soubor protokolu událostí aplikace přímo pomocí [kudu](https://github.com/projectkudu/kudu/wiki):

1. Otevřete **rozšířené nástroje** v oblasti **vývojových nástrojů.** Vyberte tlačítko **Přejít.&rarr; ** Konzole Kudu se otevře v novém kartě prohlížeče nebo v okně.
1. Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **možnost CMD**.
1. Otevřete složku **LogFiles.**
1. Vyberte ikonu tužky vedle souboru *eventlog.xml.*
1. Prohlédněte si protokol. Přejděte do dolní části protokolu zobrazíte nejnovější události.

### <a name="run-the-app-in-the-kudu-console"></a>Spuštění aplikace v konzoli Kudu

Mnoho chyb při spuštění nevytváří užitečné informace v protokolu událostí aplikace. Aplikaci můžete spustit v konzole vzdáleného spuštění [Kudu](https://github.com/projectkudu/kudu/wiki) a zjistit chybu:

1. Otevřete **rozšířené nástroje** v oblasti **vývojových nástrojů.** Vyberte tlačítko **Přejít.&rarr; ** Konzole Kudu se otevře v novém kartě prohlížeče nebo v okně.
1. Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **možnost CMD**.

#### <a name="test-a-32-bit-x86-app"></a>Testování 32bitové aplikace (x86)

**Aktuální verze**

1. `cd d:\home\site\wwwroot`
1. Spusťte aplikaci:
   * Pokud je aplikace [nasazení závislé na rámci](/dotnet/core/deploying/#framework-dependent-deployments-fdd):

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * Pokud se jedná o [samostatné nasazení](/dotnet/core/deploying/#self-contained-deployments-scd)aplikace :

     ```console
     {ASSEMBLY NAME}.exe
     ```

Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.

**Nasazení závislé na rozhraní spuštěné ve verzi preview**

*Vyžaduje instalaci rozšíření webu ASP.NET Core {VERSION} (x86).*

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`(`{X.Y}` je runtime verze)
1. Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.

#### <a name="test-a-64-bit-x64-app"></a>Testování 64bitové aplikace (x64)

**Aktuální verze**

* Pokud je aplikace 64bitové (x64) [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd):
  1. `cd D:\Program Files\dotnet`
  1. Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`
* Pokud se jedná o [samostatné nasazení](/dotnet/core/deploying/#self-contained-deployments-scd)aplikace :
  1. `cd D:\home\site\wwwroot`
  1. Spusťte aplikaci: `{ASSEMBLY NAME}.exe`

Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.

**Nasazení závislé na rozhraní spuštěné ve verzi preview**

*Vyžaduje instalaci rozšíření webu ASP.NET Core {VERSION} (x64).*

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`(`{X.Y}` je runtime verze)
1. Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a>ASP.NET protokol stdout modulu (služba Azure App Service)

Protokol stdout ASP.NET Core Module často zaznamenává užitečné chybové zprávy, které nebyly nalezeny v protokolu událostí aplikace. Povolení a zobrazení protokolů stdout:

1. Přejděte na okno **Diagnostikovat a vyřešit problémy** na webu Azure Portal.
1. V **části SELECT PROBLEM CATEGORY**vyberte tlačítko Web App **Down** .
1. V části **Navrhovaná řešení** > **Povolit přesměrování protokolu Stdout**vyberte tlačítko **pro otevření konzoly Kudu a upravte web.Config**.
1. V **diagnostické konzole**Kudu otevřete složky webu **site** > **wwwroot**. Posunutím dolů zobrazíte soubor *web.config* v dolní části seznamu.
1. Klikněte na ikonu tužky vedle souboru *web.config.*
1. Nastavte **stdoutLogEnabled** na `true` cestu **stdoutLogFile** `\\?\%home%\LogFiles\stdout`a změňte ji na: .
1. Vyberte **Uložit,** chcete-li uložit aktualizovaný soubor *web.config.*
1. Požádejte o to do aplikace.
1. Vraťte se na Azure Portal. Vyberte okno **Pokročilé nástroje** v oblasti **NÁSTROJE VÝVOJE.** Vyberte tlačítko **Přejít.&rarr; ** Konzole Kudu se otevře v novém kartě prohlížeče nebo v okně.
1. Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **možnost CMD**.
1. Vyberte složku **LogFiles.**
1. Zkontrolujte sloupec **Změněno** a vyberte ikonu tužky a upravte protokol stdout s nejnovějším datem změny.
1. Po otevření souboru protokolu se zobrazí chyba.

Zakažte protokolování stdout po dokončení řešení potíží:

1. V diagnostické **konzole**Kudu se vraťte na **web** > **wwwroot** cesty a odhalte soubor *web.config.* Znovu otevřete soubor **web.config** výběrem ikony tužky.
1. Nastavte **stdoutLogEnabled** na `false`.
1. Chcete-li soubor uložit, vyberte **Uložit.**

Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

> [!WARNING]
> Selhání zakázání protokolu stdout může vést k selhání aplikace nebo serveru. Neexistuje žádné omezení na velikost souboru protokolu nebo počet vytvořených souborů protokolu. K řešení problémů se spuštěním aplikace používejte protokolování stdout pouze.
>
> Pro obecné protokolování v aplikaci ASP.NET Core po spuštění použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otáčí protokoly. Další informace naleznete [v tématu zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).

### <a name="aspnet-core-module-debug-log-azure-app-service"></a>protokol ladění ASP.NET základního modulu (služba Azure App Service)

Protokol ladění ASP.NET core modul poskytuje další, hlubší protokolování z ASP.NET core modulu. Povolení a zobrazení protokolů stdout:

1. Chcete-li povolit rozšířený diagnostický protokol, proveďte jednu z následujících akcí:
   * Podle pokynů v [rozšířené diagnostické protokoly](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) nakonfigurovat aplikaci pro rozšířené diagnostické protokolování. Znovu nasadit aplikaci.
   * Přidejte `<handlerSettings>` zobrazené v [rozšířených diagnostických protokolech](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) do souboru *web.config* živé aplikace pomocí konzoly Kudu:
     1. Otevřete **rozšířené nástroje** v oblasti **vývojových nástrojů.** Vyberte tlačítko **Přejít.&rarr; ** Konzole Kudu se otevře v novém kartě prohlížeče nebo v okně.
     1. Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **možnost CMD**.
     1. Otevřete složky na **webu** > cesty**wwwroot**. Upravte soubor *web.config* výběrem tlačítka tužky. Přidejte `<handlerSettings>` oddíl, jak je znázorněno v [rozšířené diagnostické protokoly](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs). Vyberte tlačítko **Uložit**.
1. Otevřete **rozšířené nástroje** v oblasti **vývojových nástrojů.** Vyberte tlačítko **Přejít.&rarr; ** Konzole Kudu se otevře v novém kartě prohlížeče nebo v okně.
1. Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **možnost CMD**.
1. Otevřete složky na **webu** > cesty**wwwroot**. Pokud jste nezadali cestu pro soubor *aspnetcore-debug.log,* soubor se zobrazí v seznamu. Pokud jste zadali cestu, přejděte do umístění souboru protokolu.
1. Otevřete soubor protokolu pomocí tlačítka tužky vedle názvu souboru.

Zakažte protokolování ladění po dokončení řešení potíží:

Chcete-li protokol rozšířeného ladění zakázat, proveďte některou z následujících akcí:

* Odeberte `<handlerSettings>` soubor *web.config* místně a znovu nasadit aplikaci.
* Pomocí konzoly Kudu upravte soubor *web.config* a odeberte `<handlerSettings>` oddíl. Uložte soubor.

Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.

> [!WARNING]
> Selhání zakázání protokolu ladění může vést k selhání aplikace nebo serveru. Neexistuje žádné omezení na velikost souboru protokolu. Protokolování ladění používejte pouze k řešení potíží se spuštěním aplikace.
>
> Pro obecné protokolování v aplikaci ASP.NET Core po spuštění použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otáčí protokoly. Další informace naleznete [v tématu zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).

### <a name="slow-or-hanging-app-azure-app-service"></a>Pomalá nebo zavěšená aplikace (Azure App Service)

Když aplikace reaguje pomalu nebo se při žádosti zablokuje, přečtěte si následující články:

* [Řešení potíží s výkonem pomalých webových aplikací ve službě Azure App Service](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [Použití rozšíření webu Diagnostikátor selhání k zachycení výpisu pro občasné výjimky problémy nebo problémy s výkonem na Azure Web App](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a>Monitorovací nože

Monitorování blades poskytují alternativní řešení potíží k metodám popsaným výše v tématu. Tyto čepele lze použít k diagnostice chyb řady 500.

Zkontrolujte, zda jsou nainstalovány ASP.NET rozšíření core. Pokud rozšíření nejsou nainstalovaná, nainstalujte je ručně:

1. V části **Čepele NÁSTROJE PRO VÝVOJ** vyberte okno **Rozšíření.**
1. V seznamu by se měl **zobrazit ASP.NET rozšíření jádra.**
1. Pokud rozšíření nejsou nainstalovaná, vyberte tlačítko **Přidat.**
1. Ze seznamu zvolte **ASP.NET jádrová rozšíření.**
1. Chcete-li přijmout zákonné podmínky, vyberte **možnost OK.**
1. V okně **Přidat prodlužovací** okno vyberte **OK.**
1. Informační zpráva o automaticky otevíraná okna označuje, kdy jsou rozšíření úspěšně nainstalována.

Pokud protokolování stdout není povoleno, postupujte takto:

1. Na webu Azure Portal vyberte okno **Rozšířené nástroje** v oblasti NÁSTROJE **PRO VÝVOJ.** Vyberte tlačítko **Přejít.&rarr; ** Konzole Kudu se otevře v novém kartě prohlížeče nebo v okně.
1. Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **možnost CMD**.
1. Otevřete složky na **webu** > cesty **wwwroot** a posuňte se dolů a odhalte soubor *web.config* v dolní části seznamu.
1. Klikněte na ikonu tužky vedle souboru *web.config.*
1. Nastavte **stdoutLogEnabled** na `true` cestu **stdoutLogFile** `\\?\%home%\LogFiles\stdout`a změňte ji na: .
1. Vyberte **Uložit,** chcete-li uložit aktualizovaný soubor *web.config.*

Postupujte k aktivaci diagnostického protokolování:

1. Na webu Azure Portal vyberte okno **Protokoly diagnostiky.**
1. Vyberte přepínač **Zapnuto** pro **protokolování aplikací (souborový systém)** a **Podrobné chybové zprávy**. V horní části nože vyberte tlačítko **Uložit.**
1. Chcete-li zahrnout trasování neúspěšných požadavků, známé také jako protokolfreb (Failed Request Event Buffering), vyberte přepínač **Zapnuto** pro **trasování neúspěšných požadavků**.
1. Vyberte okno **protokolu datového proudu,** který je uveden okamžitě pod **protokoly protokoly diagnostiky** na portálu.
1. Požádejte o to do aplikace.
1. V rámci dat datového proudu protokolu je uvedena příčina chyby.

Nezapomeňte zakázat stdout protokolování po dokončení řešení potíží.

Zobrazení protokolů trasování neúspěšných požadavků (protokoly FREB):

1. Přejděte na okno **Diagnostikovat a vyřešit problémy** na webu Azure Portal.
1. V yberte **Protokoly trasování neúspěšných požadavků** v oblasti **NÁSTROJE PODPORY** na postranním panelu.

Další informace [najdete v tématu Sledování neúspěšných požadavků v tématu Povolit diagnostiku pro webové aplikace v tématu Služby aplikací](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) a [v nejčastějších dotazech k výkonu aplikací pro webové aplikace v Azure: Jak zapnu trasování neúspěšných požadavků?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)

Další informace najdete [v tématu Povolení protokolování diagnostiky pro webové aplikace ve službě Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).

> [!WARNING]
> Selhání zakázání protokolu stdout může vést k selhání aplikace nebo serveru. Neexistuje žádné omezení na velikost souboru protokolu nebo počet vytvořených souborů protokolu.
>
> Pro rutinní protokolování v aplikaci ASP.NET Core použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otáčí protokoly. Další informace naleznete [v tématu zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).

## <a name="troubleshoot-on-iis"></a>Odstraňování potíží ve službě IIS

### <a name="application-event-log-iis"></a>Protokol událostí aplikace (IIS)

Přístup k protokolu událostí aplikace:

1. Otevřete nabídku Start, vyhledejte *Prohlížeč událostí*a vyberte aplikaci **Prohlížeč událostí.**
1. V **Prohlížeči událostí**otevřete uzel **Protokoly systému Windows.**
1. Výběrem **možnosti Aplikace** otevřete protokol událostí aplikace.
1. Vyhledejte chyby spojené s aplikací se selháním. Chyby mají hodnotu *modulu AspNetCore služby IIS* nebo *modulu AspNetCore služby IIS Express* ve sloupci *Zdroj.*

### <a name="run-the-app-at-a-command-prompt"></a>Spuštění aplikace na příkazovém řádku

Mnoho chyb při spuštění nevytváří užitečné informace v protokolu událostí aplikace. Příčinu některých chyb můžete najít spuštěním aplikace na příkazovém řádku v hostitelském systému.

#### <a name="framework-dependent-deployment"></a>Nasazení závislé na rámci

Pokud je aplikace [nasazení závislé na rámci](/dotnet/core/deploying/#framework-dependent-deployments-fdd):

1. Na příkazovém řádku přejděte do složky nasazení a spusťte aplikaci spuštěním sestavení aplikace pomocí *dotnet.exe*. V následujícím příkazu nahraďte název sestavení \<aplikace assembly_name `dotnet .\<assembly_name>.dll`>: .
1. Výstup konzoly z aplikace, zobrazující případné chyby, je zapsán do okna konzoly.
1. Pokud dojde k chybám při podání požadavku na aplikaci, podat žádost na hostitele a port, kde Kestrel naslouchá. Pomocí výchozího hostitele a příspěvku `http://localhost:5000/`požádejte o místo . Pokud aplikace reaguje normálně na adrese koncového bodu Kestrel, problém je pravděpodobnější související s konfigurací hostování a méně pravděpodobné v rámci aplikace.

#### <a name="self-contained-deployment"></a>Samostatné nasazení

Pokud se jedná o [samostatné nasazení](/dotnet/core/deploying/#self-contained-deployments-scd)aplikace :

1. Na příkazovém řádku přejděte do složky nasazení a spusťte spustitelný soubor aplikace. V následujícím příkazu nahraďte název sestavení \<aplikace assembly_name `<assembly_name>.exe`>: .
1. Výstup konzoly z aplikace, zobrazující případné chyby, je zapsán do okna konzoly.
1. Pokud dojde k chybám při podání požadavku na aplikaci, podat žádost na hostitele a port, kde Kestrel naslouchá. Pomocí výchozího hostitele a příspěvku `http://localhost:5000/`požádejte o místo . Pokud aplikace reaguje normálně na adrese koncového bodu Kestrel, problém je pravděpodobnější související s konfigurací hostování a méně pravděpodobné v rámci aplikace.

### <a name="aspnet-core-module-stdout-log-iis"></a>ASP.NET protokol stdout modulu (IIS)

Povolení a zobrazení protokolů stdout:

1. Přejděte do složky nasazení webu v hostitelském systému.
1. Pokud složka *protokolů* není k dispozici, vytvořte složku. Pokyny k povolení msbuild vytvořit složku *protokolů* v nasazení automaticky, naleznete v tématu [directory struktura.](xref:host-and-deploy/directory-structure)
1. Upravte soubor *web.config.* Nastavte **stdoutLogEnabled** a `true` změňte cestu **stdoutLogFile** tak, `.\logs\stdout`aby přecózovala na složku *protokolů* (například ). `stdout`v cestě je předpona názvu souboru protokolu. Časové razítko, ID procesu a přípona souboru jsou přidány automaticky při vytvoření protokolu. Jako `stdout` předpona názvu souboru je typický soubor protokolu pojmenován *stdout_20180205184032_5412.log*.
1. Ujistěte se, že identita fondu aplikací má oprávnění k zápisu do složky *protokolů.*
1. Uložte aktualizovaný soubor *web.config.*
1. Požádejte o to do aplikace.
1. Přejděte do složky *protokolů.* Vyhledejte a otevřete nejnovější protokol stdout.
1. Prostudujte si protokol pro chyby.

Zakažte protokolování stdout po dokončení řešení potíží:

1. Upravte soubor *web.config.*
1. Nastavte **stdoutLogEnabled** na `false`.
1. Uložte soubor.

Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

> [!WARNING]
> Selhání zakázání protokolu stdout může vést k selhání aplikace nebo serveru. Neexistuje žádné omezení na velikost souboru protokolu nebo počet vytvořených souborů protokolu.
>
> Pro rutinní protokolování v aplikaci ASP.NET Core použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otáčí protokoly. Další informace naleznete [v tématu zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).

### <a name="aspnet-core-module-debug-log-iis"></a>protokol ladění základního modulu ASP.NET (IIS)

Přidejte do souboru *web.config* aplikace následující nastavení obslužné rutiny, která umožní ASP.NET protokol ladění základního modulu:

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

Zkontrolujte, zda existuje cesta zadaná pro protokol a zda má identita fondu aplikací oprávnění k zápisu do umístění.

Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.

### <a name="enable-the-developer-exception-page"></a>Povolení stránky výjimky pro vývojáře

`ASPNETCORE_ENVIRONMENT` [Proměnnou prostředí lze přidat do web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) pro spuštění aplikace ve vývojovém prostředí. Pokud prostředí není přepsáno při spuštění `UseEnvironment` aplikace tvůrcem hostitele, nastavení proměnné prostředí umožňuje, aby se [stránka výjimky pro vývojáře](xref:fundamentals/error-handling) zobrazila při spuštění aplikace.

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="InProcess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

Nastavení proměnné prostředí `ASPNETCORE_ENVIRONMENT` pro se doporučuje pouze pro použití na pracovních a testovacích serverech, které nejsou vystaveny Internetu. Po odstranění potíží odeberte proměnnou prostředí ze souboru *web.config.* Informace o nastavení proměnných prostředí v *souboru web.config*naleznete v [tématu podřízený prvek environmentVariables aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).

### <a name="obtain-data-from-an-app"></a>Získání dat z aplikace

Pokud je aplikace schopná reagovat na požadavky, získejte požadavek, připojení a další data z aplikace pomocí terminálového inline middlewaru. Další informace a ukázkový <xref:test/troubleshoot#obtain-data-from-an-app>kód naleznete v tématu .

### <a name="slow-or-hanging-app-iis"></a>Pomalá nebo závěsná aplikace (IIS)

*Výpis stavu systému* je snímek paměti systému a může pomoci určit příčinu selhání aplikace, selhání při spuštění nebo pomalé aplikace.

#### <a name="app-crashes-or-encounters-an-exception"></a>Aplikace dojde k chybě nebo narazí na výjimku

Získání a analýza výpisu z [programu Zasílání zpráv o chybách systému Windows (WER)](/windows/desktop/wer/windows-error-reporting):

1. Vytvořte složku, ve které `c:\dumps`budou uloženy soubory s výpisem stavu systému . Fond aplikací musí mít přístup pro zápis do složky.
1. Spusťte [skript EnableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):
   * Pokud aplikace používá [model hostování v procesu](xref:host-and-deploy/iis/index#in-process-hosting-model), spusťte skript pro *w3wp.exe*:

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * Pokud aplikace používá [mimoprocesový model hostování](xref:host-and-deploy/iis/index#out-of-process-hosting-model), spusťte skript pro *dotnet.exe*:

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. Spusťte aplikaci za podmínek, které způsobují selhání dojít.
1. Po selhání spusťte [skript DisableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):
   * Pokud aplikace používá [model hostování v procesu](xref:host-and-deploy/iis/index#in-process-hosting-model), spusťte skript pro *w3wp.exe*:

     ```console
     .\DisableDumps w3wp.exe
     ```

   * Pokud aplikace používá [mimoprocesový model hostování](xref:host-and-deploy/iis/index#out-of-process-hosting-model), spusťte skript pro *dotnet.exe*:

     ```console
     .\DisableDumps dotnet.exe
     ```

Po zhroucení aplikace a výpis kolekce je kompletní, aplikace je povoleno ukončit normálně. Skript PowerShell udává WER tak, aby shromažďoval až pět výpisů v aplikaci.

> [!WARNING]
> Výpisy stavu systému může zabírají velké množství místa na disku (až několik gigabajtů každý).

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>Aplikace přestane reagovat, selže při spuštění nebo běží normálně

Když aplikace *přestane reagovat* (přestane reagovat, ale nezhroutí se), selže při spuštění nebo běží normálně, najdete v [tématu Soubory výpisu uživatelského režimu: Výběr nejvhodnějšího nástroje](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) pro výběr vhodného nástroje pro vytvoření výpisu.

#### <a name="analyze-the-dump"></a>Analyzovat výpis

Výpis lze analyzovat pomocí několika přístupů. Další informace naleznete [v tématu Analýza souboru s výpisem stavu uživatelského režimu](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).

## <a name="clear-package-caches"></a>Vymazat mezipaměti balíčků

Funkční aplikace může selhat ihned po upgradu sady .NET Core SDK ve vývojovém počítači nebo změně verze balíčku v rámci aplikace. V některých případech nesouvislé balíčky může přerušit aplikaci při provádění hlavních upgradů. Většinu těchto problémů lze opravit podle následujících pokynů:

1. Odstraňte složky *bin* a *obj.*
1. Vymazat mezipaměti balíčku spuštěním [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) z příkazového prostředí.

   Vymazání mezipaměti balíčků lze také provést pomocí nástroje [nuget.exe](https://www.nuget.org/downloads) a spuštěnípříkazu `nuget locals all -clear`. *nuget.exe* není přibalená instalace s operačním systémem Windows desktop a musí být získány odděleně od [webu NuGet](https://www.nuget.org/downloads).

1. Obnovte a znovu vytvořte projekt.
1. Před opětovnou nasazením aplikace odstraňte všechny soubory ve složce nasazení na serveru.

## <a name="additional-resources"></a>Další zdroje

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a>Dokumentace k Azure

* [Application Insights pro ASP.NET Core](/azure/application-insights/app-insights-asp-net-core)
* [Část Řešení potíží s webovou aplikací ve službě Azure App Service ve vzdáleném ladění webových aplikací pomocí Visual Studia](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [Přehled diagnostiky služby Azure App Service](/azure/app-service/app-service-diagnostics)
* [Postup: Monitorování aplikací ve službě Azure App Service](/azure/app-service/web-sites-monitor)
* [Poradce při potížích s webovou aplikací ve službě Azure App Service pomocí Visual Studia](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [Poradce při potížích s chybami HTTP "502 chybná brána" a "503 služba není k dispozici" ve svých webových aplikacích Azure](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [Řešení potíží s výkonem pomalých webových aplikací ve službě Azure App Service](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [Časté otázky k výkonu aplikací pro webové aplikace v Azure](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [Azure Web App izolovaného prostoru (omezení spuštění služby App Service)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12minutové video)](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a>Dokumentace sady Visual Studio

* [Vzdálené ladění ASP.NET jádro ve službě IIS v Azure ve Visual Studiu 2017](/visualstudio/debugger/remote-debugging-azure)
* [Vzdálené ladění ASP.NET jádra ve vzdáleném počítači služby IIS v sadě Visual Studio 2017](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [Informace o ladění pomocí sady Visual Studio](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a>Dokumentace kódu visual studia

* [Ladění pomocí kódu sady Visual Studio](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Tento článek obsahuje informace o běžných chybách při spuštění aplikace a pokyny, jak diagnostikovat chyby při nasazení aplikace do služby Azure App Service nebo IIS:

[Chyby při spuštění aplikace](#app-startup-errors)  
Vysvětluje běžné scénáře stavového kódu HTTP při spuštění.

[Poradce při potížích se službou Azure App Service](#troubleshoot-on-azure-app-service)  
Poskytuje rady pro řešení potíží pro aplikace nasazené do služby Azure App Service.

[Odstraňování potíží ve službě IIS](#troubleshoot-on-iis)  
Poskytuje rady pro řešení potíží s aplikacemi nasazenými do služby IIS nebo spuštěných místně ve službě IIS Express. Pokyny platí pro nasazení plochy systému Windows Server i Windows.

[Vymazat mezipaměti balíčků](#clear-package-caches)  
Vysvětluje, co dělat, když nesouvislé balíčky přerušit aplikaci při provádění hlavních upgradů nebo změny verze balíčku.

[Další zdroje](#additional-resources)  
Obsahuje další témata pro řešení potíží.

## <a name="app-startup-errors"></a>Chyby při spuštění aplikace

V sadě Visual Studio, ASP.NET projektu Core výchozí [iis express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hostování během ladění. A *502.5 - Selhání procesu* nebo *500.30 - Start Selhání,* ke kterému dochází při ladění místně lze diagnostikovat pomocí rady v tomto tématu.

### <a name="40314-forbidden"></a>403.14 Zakázáno

Aplikace se nespustí. Je zaznamenána následující chyba:

```
The Web server is configured to not list the contents of this directory.
```

Chyba je obvykle způsobena přerušeným nasazením v hostitelském systému, který zahrnuje některý z následujících scénářů:

* Aplikace je nasazena do nesprávné složky v hostitelském systému.
* Procesu nasazení se nepodařilo přesunout všechny soubory a složky aplikace do složky nasazení v hostitelském systému.
* Soubor *web.config* chybí v nasazení nebo je obsah souboru *web.config* poškozen.

Proveďte následující kroky:

1. Odstraňte všechny soubory a složky ze složky nasazení v hostitelském systému.
1. Znovu nasadit obsah složky *publikování* aplikace do hostitelského systému pomocí normální metody nasazení, jako je Visual Studio, PowerShell nebo ruční nasazení:
   * Zkontrolujte, zda je soubor *web.config* v nasazení přítomen a zda je jeho obsah správný.
   * Při hostování ve službě Azure App Service zkontrolujte, že se aplikace nasadí do `D:\home\site\wwwroot` složky.
   * Pokud je aplikace hostována službou IIS, zkontrolujte, zda je aplikace nasazena do **fyzické cesty** služby IIS zobrazené v **základním nastavení** **správce služby IIS**.
1. Zkontrolujte, zda jsou všechny soubory a složky aplikace nasazeny porovnáním nasazení v hostitelském systému s obsahem složky *publikování* projektu.

Další informace o rozložení publikované aplikace ASP.NET <xref:host-and-deploy/directory-structure>Core najdete v tématu . Další informace o souboru *web.config* naleznete v tématu <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.

### <a name="500-internal-server-error"></a>500 – Interní chyba serveru

Aplikace se spustí, ale chyba zabrání serveru v plnění požadavku.

K této chybě dochází v kódu aplikace při spuštění nebo při vytváření odpovědi. Odpověď může obsahovat žádný obsah nebo odpověď může zobrazit jako *500 Vnitřní chyba serveru* v prohlížeči. Protokol událostí aplikace obvykle uvádí, že aplikace byla spuštěna normálně. Z pohledu serveru je to správné. Aplikace se spustila, ale nemůže generovat platnou odpověď. Spusťte aplikaci na příkazovém řádku na serveru nebo povolte protokol stdout ASP.NET Core Module k řešení problému.

### <a name="5000-in-process-handler-load-failure"></a>Selhání zatížení obslužné rutiny procesu 500.0

Pracovní proces se nezdaří. Aplikace se nespustí.

Základní [modul ASP.NET](xref:host-and-deploy/aspnet-core-module) nenalezl clr jádra .NET a našel obslužnou rutinu požadavku v procesu (*aspnetcorev2_inprocess.dll*). Zkontrolujte, zda:

* Aplikace cílí buď na balíček [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet, nebo na [metabalíček Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).
* Verze sdíleného rozhraní ASP.NET Core, na kterou je aplikace nainstalovaná v cílovém počítači.

### <a name="5000-out-of-process-handler-load-failure"></a>500.0 Selhání zatížení obslužné rutiny mimo proces

Pracovní proces se nezdaří. Aplikace se nespustí.

[ASP.NET core modul](xref:host-and-deploy/aspnet-core-module) unese nalezení obslužné rutiny požadavků mimo proces hostování. Ujistěte se, *že aspnetcorev2_outofprocess.dll* je k dispozici v podsložce vedle *aspnetcorev2.dll*.

### <a name="5025-process-failure"></a>502.5 – Selhání procesu

Pracovní proces se nezdaří. Aplikace se nespustí.

Základní [modul ASP.NET](xref:host-and-deploy/aspnet-core-module) se pokusí spustit pracovní proces, ale nepodaří se jej spustit. Příčinu selhání spuštění procesu lze obvykle určit z položek v protokolu událostí aplikace a ASP.NET protokolu stdout základního modulu.

Obvyklá podmínka selhání je, že aplikace je nesprávně nakonfigurovaná kvůli cílení na verzi sdíleného rozhraní ASP.NET Core, která není k dispozici. Zkontrolujte, které verze ASP.NET sdíleného rozhraní Core jsou nainstalovány v cílovém počítači. Sdílená *architektura* je sada sestavení (*soubory DLL),* které jsou nainstalovány v počítači a odkazovány metabalíčkem, například `Microsoft.AspNetCore.App`. Odkaz na metabalíček může určit minimální požadovanou verzi. Další informace naleznete [v tématu sdílený rámec](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).

Chybová stránka *502.5 Process Failure* je vrácena, když selhání konfigurace hostitele nebo aplikace způsobí selhání pracovního procesu:

### <a name="failed-to-start-application-errorcode-0x800700c1"></a>Spuštění aplikace se nezdařilo (ErrorCode '0x800700c1')

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

Spuštění aplikace se nezdařilo, protože nebylo možné načíst sestavení aplikace (*DLL*).

K této chybě dochází, pokud je neodpovídající bitness mezi publikovanou aplikací a w3wp/iisexpress procesu.

Zkontrolujte, zda je 32bitové nastavení fondu aplikací správné:

1. Vyberte fond aplikací ve **fondech aplikací**Správce služby IIS .
1. V části Upravit fond aplikací v panelu **Akce** vyberte **Upřesnit nastavení** v části Upravit **fond aplikací.**
1. Nastavit **povolit 32bitové aplikace**:
   * Pokud nasazujete 32bitovou (x86) `True`aplikaci, nastavte hodnotu na .
   * Pokud nasazujete 64bitovou (x64) `False`aplikaci, nastavte hodnotu na .

Zkontrolujte, zda v souboru `<Platform>` projektu není konflikt mezi vlastností MSBuild a publikovanou bitness aplikace.

### <a name="connection-reset"></a>Resetování připojení

Pokud dojde k chybě po odeslání záhlaví, je příliš pozdě na to, aby server odeslal **chybu interního serveru 500,** když dojde k chybě. K tomu často dochází, když dojde k chybě během serializace složitých objektů pro odpověď. Tento typ chyby se zobrazí jako chyba *obnovení připojení* v klientovi. [Protokolování aplikací](xref:fundamentals/logging/index) může pomoci při řešení těchto typů chyb.

### <a name="default-startup-limits"></a>Výchozí limity pro spuštění

[Základní modul ASP.NET](xref:host-and-deploy/aspnet-core-module) je nakonfigurován s výchozím *spouštěcím limitem* 120 sekund. Pokud zůstane na výchozí hodnotu, aplikace může trvat až dvě minuty ke spuštění, než modul zaznamená selhání procesu. Informace o konfiguraci modulu naleznete v [tématu Atributy prvku aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).

## <a name="troubleshoot-on-azure-app-service"></a>Poradce při potížích se službou Azure App Service

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a>Protokol událostí aplikace (služba Azure App Service)

Chcete-li získat přístup k protokolu událostí aplikace, použijte okno **Diagnostikovat a vyřešit problémy** na webu Azure Portal:

1. Na webu Azure Portal otevřete aplikaci ve **Službách aplikací**.
1. Vyberte **Diagnostikovat a řešit problémy**.
1. Vyberte nadpis **Diagnostické nástroje.**
1. V části **Nástroje podpory**vyberte tlačítko **Události aplikace.**
1. Prohlédněte si nejnovější chybu poskytovanou položkou *AspNetCoreModule* nebo *IIS AspNetCoreModule V2* ve sloupci **Zdroj.**

Alternativou k použití **funkce Diagnostika a řešení problémů** je prozkoumat soubor protokolu událostí aplikace přímo pomocí [kudu](https://github.com/projectkudu/kudu/wiki):

1. Otevřete **rozšířené nástroje** v oblasti **vývojových nástrojů.** Vyberte tlačítko **Přejít.&rarr; ** Konzole Kudu se otevře v novém kartě prohlížeče nebo v okně.
1. Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **možnost CMD**.
1. Otevřete složku **LogFiles.**
1. Vyberte ikonu tužky vedle souboru *eventlog.xml.*
1. Prohlédněte si protokol. Přejděte do dolní části protokolu zobrazíte nejnovější události.

### <a name="run-the-app-in-the-kudu-console"></a>Spuštění aplikace v konzoli Kudu

Mnoho chyb při spuštění nevytváří užitečné informace v protokolu událostí aplikace. Aplikaci můžete spustit v konzole vzdáleného spuštění [Kudu](https://github.com/projectkudu/kudu/wiki) a zjistit chybu:

1. Otevřete **rozšířené nástroje** v oblasti **vývojových nástrojů.** Vyberte tlačítko **Přejít.&rarr; ** Konzole Kudu se otevře v novém kartě prohlížeče nebo v okně.
1. Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **možnost CMD**.

#### <a name="test-a-32-bit-x86-app"></a>Testování 32bitové aplikace (x86)

**Aktuální verze**

1. `cd d:\home\site\wwwroot`
1. Spusťte aplikaci:
   * Pokud je aplikace [nasazení závislé na rámci](/dotnet/core/deploying/#framework-dependent-deployments-fdd):

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * Pokud se jedná o [samostatné nasazení](/dotnet/core/deploying/#self-contained-deployments-scd)aplikace :

     ```console
     {ASSEMBLY NAME}.exe
     ```

Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.

**Nasazení závislé na rozhraní spuštěné ve verzi preview**

*Vyžaduje instalaci rozšíření webu ASP.NET Core {VERSION} (x86).*

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`(`{X.Y}` je runtime verze)
1. Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.

#### <a name="test-a-64-bit-x64-app"></a>Testování 64bitové aplikace (x64)

**Aktuální verze**

* Pokud je aplikace 64bitové (x64) [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd):
  1. `cd D:\Program Files\dotnet`
  1. Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`
* Pokud se jedná o [samostatné nasazení](/dotnet/core/deploying/#self-contained-deployments-scd)aplikace :
  1. `cd D:\home\site\wwwroot`
  1. Spusťte aplikaci: `{ASSEMBLY NAME}.exe`

Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.

**Nasazení závislé na rozhraní spuštěné ve verzi preview**

*Vyžaduje instalaci rozšíření webu ASP.NET Core {VERSION} (x64).*

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`(`{X.Y}` je runtime verze)
1. Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a>ASP.NET protokol stdout modulu (služba Azure App Service)

Protokol stdout ASP.NET Core Module často zaznamenává užitečné chybové zprávy, které nebyly nalezeny v protokolu událostí aplikace. Povolení a zobrazení protokolů stdout:

1. Přejděte na okno **Diagnostikovat a vyřešit problémy** na webu Azure Portal.
1. V **části SELECT PROBLEM CATEGORY**vyberte tlačítko Web App **Down** .
1. V části **Navrhovaná řešení** > **Povolit přesměrování protokolu Stdout**vyberte tlačítko **pro otevření konzoly Kudu a upravte web.Config**.
1. V **diagnostické konzole**Kudu otevřete složky webu **site** > **wwwroot**. Posunutím dolů zobrazíte soubor *web.config* v dolní části seznamu.
1. Klikněte na ikonu tužky vedle souboru *web.config.*
1. Nastavte **stdoutLogEnabled** na `true` cestu **stdoutLogFile** `\\?\%home%\LogFiles\stdout`a změňte ji na: .
1. Vyberte **Uložit,** chcete-li uložit aktualizovaný soubor *web.config.*
1. Požádejte o to do aplikace.
1. Vraťte se na Azure Portal. Vyberte okno **Pokročilé nástroje** v oblasti **NÁSTROJE VÝVOJE.** Vyberte tlačítko **Přejít.&rarr; ** Konzole Kudu se otevře v novém kartě prohlížeče nebo v okně.
1. Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **možnost CMD**.
1. Vyberte složku **LogFiles.**
1. Zkontrolujte sloupec **Změněno** a vyberte ikonu tužky a upravte protokol stdout s nejnovějším datem změny.
1. Po otevření souboru protokolu se zobrazí chyba.

Zakažte protokolování stdout po dokončení řešení potíží:

1. V diagnostické **konzole**Kudu se vraťte na **web** > **wwwroot** cesty a odhalte soubor *web.config.* Znovu otevřete soubor **web.config** výběrem ikony tužky.
1. Nastavte **stdoutLogEnabled** na `false`.
1. Chcete-li soubor uložit, vyberte **Uložit.**

Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

> [!WARNING]
> Selhání zakázání protokolu stdout může vést k selhání aplikace nebo serveru. Neexistuje žádné omezení na velikost souboru protokolu nebo počet vytvořených souborů protokolu. K řešení problémů se spuštěním aplikace používejte protokolování stdout pouze.
>
> Pro obecné protokolování v aplikaci ASP.NET Core po spuštění použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otáčí protokoly. Další informace naleznete [v tématu zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).

### <a name="aspnet-core-module-debug-log-azure-app-service"></a>protokol ladění ASP.NET základního modulu (služba Azure App Service)

Protokol ladění ASP.NET core modul poskytuje další, hlubší protokolování z ASP.NET core modulu. Povolení a zobrazení protokolů stdout:

1. Chcete-li povolit rozšířený diagnostický protokol, proveďte jednu z následujících akcí:
   * Podle pokynů v [rozšířené diagnostické protokoly](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) nakonfigurovat aplikaci pro rozšířené diagnostické protokolování. Znovu nasadit aplikaci.
   * Přidejte `<handlerSettings>` zobrazené v [rozšířených diagnostických protokolech](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) do souboru *web.config* živé aplikace pomocí konzoly Kudu:
     1. Otevřete **rozšířené nástroje** v oblasti **vývojových nástrojů.** Vyberte tlačítko **Přejít.&rarr; ** Konzole Kudu se otevře v novém kartě prohlížeče nebo v okně.
     1. Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **možnost CMD**.
     1. Otevřete složky na **webu** > cesty**wwwroot**. Upravte soubor *web.config* výběrem tlačítka tužky. Přidejte `<handlerSettings>` oddíl, jak je znázorněno v [rozšířené diagnostické protokoly](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs). Vyberte tlačítko **Uložit**.
1. Otevřete **rozšířené nástroje** v oblasti **vývojových nástrojů.** Vyberte tlačítko **Přejít.&rarr; ** Konzole Kudu se otevře v novém kartě prohlížeče nebo v okně.
1. Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **možnost CMD**.
1. Otevřete složky na **webu** > cesty**wwwroot**. Pokud jste nezadali cestu pro soubor *aspnetcore-debug.log,* soubor se zobrazí v seznamu. Pokud jste zadali cestu, přejděte do umístění souboru protokolu.
1. Otevřete soubor protokolu pomocí tlačítka tužky vedle názvu souboru.

Zakažte protokolování ladění po dokončení řešení potíží:

Chcete-li protokol rozšířeného ladění zakázat, proveďte některou z následujících akcí:

* Odeberte `<handlerSettings>` soubor *web.config* místně a znovu nasadit aplikaci.
* Pomocí konzoly Kudu upravte soubor *web.config* a odeberte `<handlerSettings>` oddíl. Uložte soubor.

Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.

> [!WARNING]
> Selhání zakázání protokolu ladění může vést k selhání aplikace nebo serveru. Neexistuje žádné omezení na velikost souboru protokolu. Protokolování ladění používejte pouze k řešení potíží se spuštěním aplikace.
>
> Pro obecné protokolování v aplikaci ASP.NET Core po spuštění použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otáčí protokoly. Další informace naleznete [v tématu zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).

### <a name="slow-or-hanging-app-azure-app-service"></a>Pomalá nebo zavěšená aplikace (Azure App Service)

Když aplikace reaguje pomalu nebo se při žádosti zablokuje, přečtěte si následující články:

* [Řešení potíží s výkonem pomalých webových aplikací ve službě Azure App Service](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [Použití rozšíření webu Diagnostikátor selhání k zachycení výpisu pro občasné výjimky problémy nebo problémy s výkonem na Azure Web App](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a>Monitorovací nože

Monitorování blades poskytují alternativní řešení potíží k metodám popsaným výše v tématu. Tyto čepele lze použít k diagnostice chyb řady 500.

Zkontrolujte, zda jsou nainstalovány ASP.NET rozšíření core. Pokud rozšíření nejsou nainstalovaná, nainstalujte je ručně:

1. V části **Čepele NÁSTROJE PRO VÝVOJ** vyberte okno **Rozšíření.**
1. V seznamu by se měl **zobrazit ASP.NET rozšíření jádra.**
1. Pokud rozšíření nejsou nainstalovaná, vyberte tlačítko **Přidat.**
1. Ze seznamu zvolte **ASP.NET jádrová rozšíření.**
1. Chcete-li přijmout zákonné podmínky, vyberte **možnost OK.**
1. V okně **Přidat prodlužovací** okno vyberte **OK.**
1. Informační zpráva o automaticky otevíraná okna označuje, kdy jsou rozšíření úspěšně nainstalována.

Pokud protokolování stdout není povoleno, postupujte takto:

1. Na webu Azure Portal vyberte okno **Rozšířené nástroje** v oblasti NÁSTROJE **PRO VÝVOJ.** Vyberte tlačítko **Přejít.&rarr; ** Konzole Kudu se otevře v novém kartě prohlížeče nebo v okně.
1. Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **možnost CMD**.
1. Otevřete složky na **webu** > cesty **wwwroot** a posuňte se dolů a odhalte soubor *web.config* v dolní části seznamu.
1. Klikněte na ikonu tužky vedle souboru *web.config.*
1. Nastavte **stdoutLogEnabled** na `true` cestu **stdoutLogFile** `\\?\%home%\LogFiles\stdout`a změňte ji na: .
1. Vyberte **Uložit,** chcete-li uložit aktualizovaný soubor *web.config.*

Postupujte k aktivaci diagnostického protokolování:

1. Na webu Azure Portal vyberte okno **Protokoly diagnostiky.**
1. Vyberte přepínač **Zapnuto** pro **protokolování aplikací (souborový systém)** a **Podrobné chybové zprávy**. V horní části nože vyberte tlačítko **Uložit.**
1. Chcete-li zahrnout trasování neúspěšných požadavků, známé také jako protokolfreb (Failed Request Event Buffering), vyberte přepínač **Zapnuto** pro **trasování neúspěšných požadavků**.
1. Vyberte okno **protokolu datového proudu,** který je uveden okamžitě pod **protokoly protokoly diagnostiky** na portálu.
1. Požádejte o to do aplikace.
1. V rámci dat datového proudu protokolu je uvedena příčina chyby.

Nezapomeňte zakázat stdout protokolování po dokončení řešení potíží.

Zobrazení protokolů trasování neúspěšných požadavků (protokoly FREB):

1. Přejděte na okno **Diagnostikovat a vyřešit problémy** na webu Azure Portal.
1. V yberte **Protokoly trasování neúspěšných požadavků** v oblasti **NÁSTROJE PODPORY** na postranním panelu.

Další informace [najdete v tématu Sledování neúspěšných požadavků v tématu Povolit diagnostiku pro webové aplikace v tématu Služby aplikací](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) a [v nejčastějších dotazech k výkonu aplikací pro webové aplikace v Azure: Jak zapnu trasování neúspěšných požadavků?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)

Další informace najdete [v tématu Povolení protokolování diagnostiky pro webové aplikace ve službě Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).

> [!WARNING]
> Selhání zakázání protokolu stdout může vést k selhání aplikace nebo serveru. Neexistuje žádné omezení na velikost souboru protokolu nebo počet vytvořených souborů protokolu.
>
> Pro rutinní protokolování v aplikaci ASP.NET Core použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otáčí protokoly. Další informace naleznete [v tématu zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).

## <a name="troubleshoot-on-iis"></a>Odstraňování potíží ve službě IIS

### <a name="application-event-log-iis"></a>Protokol událostí aplikace (IIS)

Přístup k protokolu událostí aplikace:

1. Otevřete nabídku Start, vyhledejte *Prohlížeč událostí*a vyberte aplikaci **Prohlížeč událostí.**
1. V **Prohlížeči událostí**otevřete uzel **Protokoly systému Windows.**
1. Výběrem **možnosti Aplikace** otevřete protokol událostí aplikace.
1. Vyhledejte chyby spojené s aplikací se selháním. Chyby mají hodnotu *modulu AspNetCore služby IIS* nebo *modulu AspNetCore služby IIS Express* ve sloupci *Zdroj.*

### <a name="run-the-app-at-a-command-prompt"></a>Spuštění aplikace na příkazovém řádku

Mnoho chyb při spuštění nevytváří užitečné informace v protokolu událostí aplikace. Příčinu některých chyb můžete najít spuštěním aplikace na příkazovém řádku v hostitelském systému.

#### <a name="framework-dependent-deployment"></a>Nasazení závislé na rámci

Pokud je aplikace [nasazení závislé na rámci](/dotnet/core/deploying/#framework-dependent-deployments-fdd):

1. Na příkazovém řádku přejděte do složky nasazení a spusťte aplikaci spuštěním sestavení aplikace pomocí *dotnet.exe*. V následujícím příkazu nahraďte název sestavení \<aplikace assembly_name `dotnet .\<assembly_name>.dll`>: .
1. Výstup konzoly z aplikace, zobrazující případné chyby, je zapsán do okna konzoly.
1. Pokud dojde k chybám při podání požadavku na aplikaci, podat žádost na hostitele a port, kde Kestrel naslouchá. Pomocí výchozího hostitele a příspěvku `http://localhost:5000/`požádejte o místo . Pokud aplikace reaguje normálně na adrese koncového bodu Kestrel, problém je pravděpodobnější související s konfigurací hostování a méně pravděpodobné v rámci aplikace.

#### <a name="self-contained-deployment"></a>Samostatné nasazení

Pokud se jedná o [samostatné nasazení](/dotnet/core/deploying/#self-contained-deployments-scd)aplikace :

1. Na příkazovém řádku přejděte do složky nasazení a spusťte spustitelný soubor aplikace. V následujícím příkazu nahraďte název sestavení \<aplikace assembly_name `<assembly_name>.exe`>: .
1. Výstup konzoly z aplikace, zobrazující případné chyby, je zapsán do okna konzoly.
1. Pokud dojde k chybám při podání požadavku na aplikaci, podat žádost na hostitele a port, kde Kestrel naslouchá. Pomocí výchozího hostitele a příspěvku `http://localhost:5000/`požádejte o místo . Pokud aplikace reaguje normálně na adrese koncového bodu Kestrel, problém je pravděpodobnější související s konfigurací hostování a méně pravděpodobné v rámci aplikace.

### <a name="aspnet-core-module-stdout-log-iis"></a>ASP.NET protokol stdout modulu (IIS)

Povolení a zobrazení protokolů stdout:

1. Přejděte do složky nasazení webu v hostitelském systému.
1. Pokud složka *protokolů* není k dispozici, vytvořte složku. Pokyny k povolení msbuild vytvořit složku *protokolů* v nasazení automaticky, naleznete v tématu [directory struktura.](xref:host-and-deploy/directory-structure)
1. Upravte soubor *web.config.* Nastavte **stdoutLogEnabled** a `true` změňte cestu **stdoutLogFile** tak, `.\logs\stdout`aby přecózovala na složku *protokolů* (například ). `stdout`v cestě je předpona názvu souboru protokolu. Časové razítko, ID procesu a přípona souboru jsou přidány automaticky při vytvoření protokolu. Jako `stdout` předpona názvu souboru je typický soubor protokolu pojmenován *stdout_20180205184032_5412.log*.
1. Ujistěte se, že identita fondu aplikací má oprávnění k zápisu do složky *protokolů.*
1. Uložte aktualizovaný soubor *web.config.*
1. Požádejte o to do aplikace.
1. Přejděte do složky *protokolů.* Vyhledejte a otevřete nejnovější protokol stdout.
1. Prostudujte si protokol pro chyby.

Zakažte protokolování stdout po dokončení řešení potíží:

1. Upravte soubor *web.config.*
1. Nastavte **stdoutLogEnabled** na `false`.
1. Uložte soubor.

Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

> [!WARNING]
> Selhání zakázání protokolu stdout může vést k selhání aplikace nebo serveru. Neexistuje žádné omezení na velikost souboru protokolu nebo počet vytvořených souborů protokolu.
>
> Pro rutinní protokolování v aplikaci ASP.NET Core použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otáčí protokoly. Další informace naleznete [v tématu zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).

### <a name="aspnet-core-module-debug-log-iis"></a>protokol ladění základního modulu ASP.NET (IIS)

Přidejte do souboru *web.config* aplikace následující nastavení obslužné rutiny, která umožní ASP.NET protokol ladění základního modulu:

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

Zkontrolujte, zda existuje cesta zadaná pro protokol a zda má identita fondu aplikací oprávnění k zápisu do umístění.

Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.

### <a name="enable-the-developer-exception-page"></a>Povolení stránky výjimky pro vývojáře

`ASPNETCORE_ENVIRONMENT` [Proměnnou prostředí lze přidat do web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) pro spuštění aplikace ve vývojovém prostředí. Pokud prostředí není přepsáno při spuštění `UseEnvironment` aplikace tvůrcem hostitele, nastavení proměnné prostředí umožňuje, aby se [stránka výjimky pro vývojáře](xref:fundamentals/error-handling) zobrazila při spuštění aplikace.

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="InProcess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

Nastavení proměnné prostředí `ASPNETCORE_ENVIRONMENT` pro se doporučuje pouze pro použití na pracovních a testovacích serverech, které nejsou vystaveny Internetu. Po odstranění potíží odeberte proměnnou prostředí ze souboru *web.config.* Informace o nastavení proměnných prostředí v *souboru web.config*naleznete v [tématu podřízený prvek environmentVariables aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).

### <a name="obtain-data-from-an-app"></a>Získání dat z aplikace

Pokud je aplikace schopná reagovat na požadavky, získejte požadavek, připojení a další data z aplikace pomocí terminálového inline middlewaru. Další informace a ukázkový <xref:test/troubleshoot#obtain-data-from-an-app>kód naleznete v tématu .

### <a name="slow-or-hanging-app-iis"></a>Pomalá nebo závěsná aplikace (IIS)

*Výpis stavu systému* je snímek paměti systému a může pomoci určit příčinu selhání aplikace, selhání při spuštění nebo pomalé aplikace.

#### <a name="app-crashes-or-encounters-an-exception"></a>Aplikace dojde k chybě nebo narazí na výjimku

Získání a analýza výpisu z [programu Zasílání zpráv o chybách systému Windows (WER)](/windows/desktop/wer/windows-error-reporting):

1. Vytvořte složku, ve které `c:\dumps`budou uloženy soubory s výpisem stavu systému . Fond aplikací musí mít přístup pro zápis do složky.
1. Spusťte [skript EnableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):
   * Pokud aplikace používá [model hostování v procesu](xref:host-and-deploy/iis/index#in-process-hosting-model), spusťte skript pro *w3wp.exe*:

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * Pokud aplikace používá [mimoprocesový model hostování](xref:host-and-deploy/iis/index#out-of-process-hosting-model), spusťte skript pro *dotnet.exe*:

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. Spusťte aplikaci za podmínek, které způsobují selhání dojít.
1. Po selhání spusťte [skript DisableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):
   * Pokud aplikace používá [model hostování v procesu](xref:host-and-deploy/iis/index#in-process-hosting-model), spusťte skript pro *w3wp.exe*:

     ```console
     .\DisableDumps w3wp.exe
     ```

   * Pokud aplikace používá [mimoprocesový model hostování](xref:host-and-deploy/iis/index#out-of-process-hosting-model), spusťte skript pro *dotnet.exe*:

     ```console
     .\DisableDumps dotnet.exe
     ```

Po zhroucení aplikace a výpis kolekce je kompletní, aplikace je povoleno ukončit normálně. Skript PowerShell udává WER tak, aby shromažďoval až pět výpisů v aplikaci.

> [!WARNING]
> Výpisy stavu systému může zabírají velké množství místa na disku (až několik gigabajtů každý).

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>Aplikace přestane reagovat, selže při spuštění nebo běží normálně

Když aplikace *přestane reagovat* (přestane reagovat, ale nezhroutí se), selže při spuštění nebo běží normálně, najdete v [tématu Soubory výpisu uživatelského režimu: Výběr nejvhodnějšího nástroje](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) pro výběr vhodného nástroje pro vytvoření výpisu.

#### <a name="analyze-the-dump"></a>Analyzovat výpis

Výpis lze analyzovat pomocí několika přístupů. Další informace naleznete [v tématu Analýza souboru s výpisem stavu uživatelského režimu](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).

## <a name="clear-package-caches"></a>Vymazat mezipaměti balíčků

Funkční aplikace může selhat ihned po upgradu sady .NET Core SDK ve vývojovém počítači nebo změně verze balíčku v rámci aplikace. V některých případech nesouvislé balíčky může přerušit aplikaci při provádění hlavních upgradů. Většinu těchto problémů lze opravit podle následujících pokynů:

1. Odstraňte složky *bin* a *obj.*
1. Vymazat mezipaměti balíčku spuštěním [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) z příkazového prostředí.

   Vymazání mezipaměti balíčků lze také provést pomocí nástroje [nuget.exe](https://www.nuget.org/downloads) a spuštěnípříkazu `nuget locals all -clear`. *nuget.exe* není přibalená instalace s operačním systémem Windows desktop a musí být získány odděleně od [webu NuGet](https://www.nuget.org/downloads).

1. Obnovte a znovu vytvořte projekt.
1. Před opětovnou nasazením aplikace odstraňte všechny soubory ve složce nasazení na serveru.

## <a name="additional-resources"></a>Další zdroje

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a>Dokumentace k Azure

* [Application Insights pro ASP.NET Core](/azure/application-insights/app-insights-asp-net-core)
* [Část Řešení potíží s webovou aplikací ve službě Azure App Service ve vzdáleném ladění webových aplikací pomocí Visual Studia](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [Přehled diagnostiky služby Azure App Service](/azure/app-service/app-service-diagnostics)
* [Postup: Monitorování aplikací ve službě Azure App Service](/azure/app-service/web-sites-monitor)
* [Poradce při potížích s webovou aplikací ve službě Azure App Service pomocí Visual Studia](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [Poradce při potížích s chybami HTTP "502 chybná brána" a "503 služba není k dispozici" ve svých webových aplikacích Azure](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [Řešení potíží s výkonem pomalých webových aplikací ve službě Azure App Service](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [Časté otázky k výkonu aplikací pro webové aplikace v Azure](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [Azure Web App izolovaného prostoru (omezení spuštění služby App Service)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12minutové video)](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a>Dokumentace sady Visual Studio

* [Vzdálené ladění ASP.NET jádro ve službě IIS v Azure ve Visual Studiu 2017](/visualstudio/debugger/remote-debugging-azure)
* [Vzdálené ladění ASP.NET jádra ve vzdáleném počítači služby IIS v sadě Visual Studio 2017](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [Informace o ladění pomocí sady Visual Studio](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a>Dokumentace kódu visual studia

* [Ladění pomocí kódu sady Visual Studio](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Tento článek obsahuje informace o běžných chybách při spuštění aplikace a pokyny, jak diagnostikovat chyby při nasazení aplikace do služby Azure App Service nebo IIS:

[Chyby při spuštění aplikace](#app-startup-errors)  
Vysvětluje běžné scénáře stavového kódu HTTP při spuštění.

[Poradce při potížích se službou Azure App Service](#troubleshoot-on-azure-app-service)  
Poskytuje rady pro řešení potíží pro aplikace nasazené do služby Azure App Service.

[Odstraňování potíží ve službě IIS](#troubleshoot-on-iis)  
Poskytuje rady pro řešení potíží s aplikacemi nasazenými do služby IIS nebo spuštěných místně ve službě IIS Express. Pokyny platí pro nasazení plochy systému Windows Server i Windows.

[Vymazat mezipaměti balíčků](#clear-package-caches)  
Vysvětluje, co dělat, když nesouvislé balíčky přerušit aplikaci při provádění hlavních upgradů nebo změny verze balíčku.

[Další zdroje](#additional-resources)  
Obsahuje další témata pro řešení potíží.

## <a name="app-startup-errors"></a>Chyby při spuštění aplikace

V sadě Visual Studio, ASP.NET projektu Core výchozí [iis express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hostování během ladění. *502.5 Selhání procesu,* ke kterému dochází při místním ladění lze diagnostikovat pomocí rady v tomto tématu.

### <a name="40314-forbidden"></a>403.14 Zakázáno

Aplikace se nespustí. Je zaznamenána následující chyba:

```
The Web server is configured to not list the contents of this directory.
```

Chyba je obvykle způsobena přerušeným nasazením v hostitelském systému, který zahrnuje některý z následujících scénářů:

* Aplikace je nasazena do nesprávné složky v hostitelském systému.
* Procesu nasazení se nepodařilo přesunout všechny soubory a složky aplikace do složky nasazení v hostitelském systému.
* Soubor *web.config* chybí v nasazení nebo je obsah souboru *web.config* poškozen.

Proveďte následující kroky:

1. Odstraňte všechny soubory a složky ze složky nasazení v hostitelském systému.
1. Znovu nasadit obsah složky *publikování* aplikace do hostitelského systému pomocí normální metody nasazení, jako je Visual Studio, PowerShell nebo ruční nasazení:
   * Zkontrolujte, zda je soubor *web.config* v nasazení přítomen a zda je jeho obsah správný.
   * Při hostování ve službě Azure App Service zkontrolujte, že se aplikace nasadí do `D:\home\site\wwwroot` složky.
   * Pokud je aplikace hostována službou IIS, zkontrolujte, zda je aplikace nasazena do **fyzické cesty** služby IIS zobrazené v **základním nastavení** **správce služby IIS**.
1. Zkontrolujte, zda jsou všechny soubory a složky aplikace nasazeny porovnáním nasazení v hostitelském systému s obsahem složky *publikování* projektu.

Další informace o rozložení publikované aplikace ASP.NET <xref:host-and-deploy/directory-structure>Core najdete v tématu . Další informace o souboru *web.config* naleznete v tématu <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.

### <a name="500-internal-server-error"></a>500 – Interní chyba serveru

Aplikace se spustí, ale chyba zabrání serveru v plnění požadavku.

K této chybě dochází v kódu aplikace při spuštění nebo při vytváření odpovědi. Odpověď může obsahovat žádný obsah nebo odpověď může zobrazit jako *500 Vnitřní chyba serveru* v prohlížeči. Protokol událostí aplikace obvykle uvádí, že aplikace byla spuštěna normálně. Z pohledu serveru je to správné. Aplikace se spustila, ale nemůže generovat platnou odpověď. Spusťte aplikaci na příkazovém řádku na serveru nebo povolte protokol stdout ASP.NET Core Module k řešení problému.

### <a name="5025-process-failure"></a>502.5 – Selhání procesu

Pracovní proces se nezdaří. Aplikace se nespustí.

Základní [modul ASP.NET](xref:host-and-deploy/aspnet-core-module) se pokusí spustit pracovní proces, ale nepodaří se jej spustit. Příčinu selhání spuštění procesu lze obvykle určit z položek v protokolu událostí aplikace a ASP.NET protokolu stdout základního modulu.

Obvyklá podmínka selhání je, že aplikace je nesprávně nakonfigurovaná kvůli cílení na verzi sdíleného rozhraní ASP.NET Core, která není k dispozici. Zkontrolujte, které verze ASP.NET sdíleného rozhraní Core jsou nainstalovány v cílovém počítači. Sdílená *architektura* je sada sestavení (*soubory DLL),* které jsou nainstalovány v počítači a odkazovány metabalíčkem, například `Microsoft.AspNetCore.App`. Odkaz na metabalíček může určit minimální požadovanou verzi. Další informace naleznete [v tématu sdílený rámec](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).

Chybová stránka *502.5 Process Failure* je vrácena, když selhání konfigurace hostitele nebo aplikace způsobí selhání pracovního procesu:

### <a name="failed-to-start-application-errorcode-0x800700c1"></a>Spuštění aplikace se nezdařilo (ErrorCode '0x800700c1')

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

Spuštění aplikace se nezdařilo, protože nebylo možné načíst sestavení aplikace (*DLL*).

K této chybě dochází, pokud je neodpovídající bitness mezi publikovanou aplikací a w3wp/iisexpress procesu.

Zkontrolujte, zda je 32bitové nastavení fondu aplikací správné:

1. Vyberte fond aplikací ve **fondech aplikací**Správce služby IIS .
1. V části Upravit fond aplikací v panelu **Akce** vyberte **Upřesnit nastavení** v části Upravit **fond aplikací.**
1. Nastavit **povolit 32bitové aplikace**:
   * Pokud nasazujete 32bitovou (x86) `True`aplikaci, nastavte hodnotu na .
   * Pokud nasazujete 64bitovou (x64) `False`aplikaci, nastavte hodnotu na .

Zkontrolujte, zda v souboru `<Platform>` projektu není konflikt mezi vlastností MSBuild a publikovanou bitness aplikace.

### <a name="connection-reset"></a>Resetování připojení

Pokud dojde k chybě po odeslání záhlaví, je příliš pozdě na to, aby server odeslal **chybu interního serveru 500,** když dojde k chybě. K tomu často dochází, když dojde k chybě během serializace složitých objektů pro odpověď. Tento typ chyby se zobrazí jako chyba *obnovení připojení* v klientovi. [Protokolování aplikací](xref:fundamentals/logging/index) může pomoci při řešení těchto typů chyb.

### <a name="default-startup-limits"></a>Výchozí limity pro spuštění

[Základní modul ASP.NET](xref:host-and-deploy/aspnet-core-module) je nakonfigurován s výchozím *spouštěcím limitem* 120 sekund. Pokud zůstane na výchozí hodnotu, aplikace může trvat až dvě minuty ke spuštění, než modul zaznamená selhání procesu. Informace o konfiguraci modulu naleznete v [tématu Atributy prvku aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).

## <a name="troubleshoot-on-azure-app-service"></a>Poradce při potížích se službou Azure App Service

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a>Protokol událostí aplikace (služba Azure App Service)

Chcete-li získat přístup k protokolu událostí aplikace, použijte okno **Diagnostikovat a vyřešit problémy** na webu Azure Portal:

1. Na webu Azure Portal otevřete aplikaci ve **Službách aplikací**.
1. Vyberte **Diagnostikovat a řešit problémy**.
1. Vyberte nadpis **Diagnostické nástroje.**
1. V části **Nástroje podpory**vyberte tlačítko **Události aplikace.**
1. Prohlédněte si nejnovější chybu poskytovanou položkou *AspNetCoreModule* nebo *IIS AspNetCoreModule V2* ve sloupci **Zdroj.**

Alternativou k použití **funkce Diagnostika a řešení problémů** je prozkoumat soubor protokolu událostí aplikace přímo pomocí [kudu](https://github.com/projectkudu/kudu/wiki):

1. Otevřete **rozšířené nástroje** v oblasti **vývojových nástrojů.** Vyberte tlačítko **Přejít.&rarr; ** Konzole Kudu se otevře v novém kartě prohlížeče nebo v okně.
1. Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **možnost CMD**.
1. Otevřete složku **LogFiles.**
1. Vyberte ikonu tužky vedle souboru *eventlog.xml.*
1. Prohlédněte si protokol. Přejděte do dolní části protokolu zobrazíte nejnovější události.

### <a name="run-the-app-in-the-kudu-console"></a>Spuštění aplikace v konzoli Kudu

Mnoho chyb při spuštění nevytváří užitečné informace v protokolu událostí aplikace. Aplikaci můžete spustit v konzole vzdáleného spuštění [Kudu](https://github.com/projectkudu/kudu/wiki) a zjistit chybu:

1. Otevřete **rozšířené nástroje** v oblasti **vývojových nástrojů.** Vyberte tlačítko **Přejít.&rarr; ** Konzole Kudu se otevře v novém kartě prohlížeče nebo v okně.
1. Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **možnost CMD**.

#### <a name="test-a-32-bit-x86-app"></a>Testování 32bitové aplikace (x86)

**Aktuální verze**

1. `cd d:\home\site\wwwroot`
1. Spusťte aplikaci:
   * Pokud je aplikace [nasazení závislé na rámci](/dotnet/core/deploying/#framework-dependent-deployments-fdd):

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * Pokud se jedná o [samostatné nasazení](/dotnet/core/deploying/#self-contained-deployments-scd)aplikace :

     ```console
     {ASSEMBLY NAME}.exe
     ```

Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.

**Nasazení závislé na rozhraní spuštěné ve verzi preview**

*Vyžaduje instalaci rozšíření webu ASP.NET Core {VERSION} (x86).*

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`(`{X.Y}` je runtime verze)
1. Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.

#### <a name="test-a-64-bit-x64-app"></a>Testování 64bitové aplikace (x64)

**Aktuální verze**

* Pokud je aplikace 64bitové (x64) [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd):
  1. `cd D:\Program Files\dotnet`
  1. Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`
* Pokud se jedná o [samostatné nasazení](/dotnet/core/deploying/#self-contained-deployments-scd)aplikace :
  1. `cd D:\home\site\wwwroot`
  1. Spusťte aplikaci: `{ASSEMBLY NAME}.exe`

Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.

**Nasazení závislé na rozhraní spuštěné ve verzi preview**

*Vyžaduje instalaci rozšíření webu ASP.NET Core {VERSION} (x64).*

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`(`{X.Y}` je runtime verze)
1. Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a>ASP.NET protokol stdout modulu (služba Azure App Service)

Protokol stdout ASP.NET Core Module často zaznamenává užitečné chybové zprávy, které nebyly nalezeny v protokolu událostí aplikace. Povolení a zobrazení protokolů stdout:

1. Přejděte na okno **Diagnostikovat a vyřešit problémy** na webu Azure Portal.
1. V **části SELECT PROBLEM CATEGORY**vyberte tlačítko Web App **Down** .
1. V části **Navrhovaná řešení** > **Povolit přesměrování protokolu Stdout**vyberte tlačítko **pro otevření konzoly Kudu a upravte web.Config**.
1. V **diagnostické konzole**Kudu otevřete složky webu **site** > **wwwroot**. Posunutím dolů zobrazíte soubor *web.config* v dolní části seznamu.
1. Klikněte na ikonu tužky vedle souboru *web.config.*
1. Nastavte **stdoutLogEnabled** na `true` cestu **stdoutLogFile** `\\?\%home%\LogFiles\stdout`a změňte ji na: .
1. Vyberte **Uložit,** chcete-li uložit aktualizovaný soubor *web.config.*
1. Požádejte o to do aplikace.
1. Vraťte se na Azure Portal. Vyberte okno **Pokročilé nástroje** v oblasti **NÁSTROJE VÝVOJE.** Vyberte tlačítko **Přejít.&rarr; ** Konzole Kudu se otevře v novém kartě prohlížeče nebo v okně.
1. Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **možnost CMD**.
1. Vyberte složku **LogFiles.**
1. Zkontrolujte sloupec **Změněno** a vyberte ikonu tužky a upravte protokol stdout s nejnovějším datem změny.
1. Po otevření souboru protokolu se zobrazí chyba.

Zakažte protokolování stdout po dokončení řešení potíží:

1. V diagnostické **konzole**Kudu se vraťte na **web** > **wwwroot** cesty a odhalte soubor *web.config.* Znovu otevřete soubor **web.config** výběrem ikony tužky.
1. Nastavte **stdoutLogEnabled** na `false`.
1. Chcete-li soubor uložit, vyberte **Uložit.**

Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

> [!WARNING]
> Selhání zakázání protokolu stdout může vést k selhání aplikace nebo serveru. Neexistuje žádné omezení na velikost souboru protokolu nebo počet vytvořených souborů protokolu. K řešení problémů se spuštěním aplikace používejte protokolování stdout pouze.
>
> Pro obecné protokolování v aplikaci ASP.NET Core po spuštění použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otáčí protokoly. Další informace naleznete [v tématu zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).

### <a name="slow-or-hanging-app-azure-app-service"></a>Pomalá nebo zavěšená aplikace (Azure App Service)

Když aplikace reaguje pomalu nebo se při žádosti zablokuje, přečtěte si následující články:

* [Řešení potíží s výkonem pomalých webových aplikací ve službě Azure App Service](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [Použití rozšíření webu Diagnostikátor selhání k zachycení výpisu pro občasné výjimky problémy nebo problémy s výkonem na Azure Web App](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a>Monitorovací nože

Monitorování blades poskytují alternativní řešení potíží k metodám popsaným výše v tématu. Tyto čepele lze použít k diagnostice chyb řady 500.

Zkontrolujte, zda jsou nainstalovány ASP.NET rozšíření core. Pokud rozšíření nejsou nainstalovaná, nainstalujte je ručně:

1. V části **Čepele NÁSTROJE PRO VÝVOJ** vyberte okno **Rozšíření.**
1. V seznamu by se měl **zobrazit ASP.NET rozšíření jádra.**
1. Pokud rozšíření nejsou nainstalovaná, vyberte tlačítko **Přidat.**
1. Ze seznamu zvolte **ASP.NET jádrová rozšíření.**
1. Chcete-li přijmout zákonné podmínky, vyberte **možnost OK.**
1. V okně **Přidat prodlužovací** okno vyberte **OK.**
1. Informační zpráva o automaticky otevíraná okna označuje, kdy jsou rozšíření úspěšně nainstalována.

Pokud protokolování stdout není povoleno, postupujte takto:

1. Na webu Azure Portal vyberte okno **Rozšířené nástroje** v oblasti NÁSTROJE **PRO VÝVOJ.** Vyberte tlačítko **Přejít.&rarr; ** Konzole Kudu se otevře v novém kartě prohlížeče nebo v okně.
1. Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **možnost CMD**.
1. Otevřete složky na **webu** > cesty **wwwroot** a posuňte se dolů a odhalte soubor *web.config* v dolní části seznamu.
1. Klikněte na ikonu tužky vedle souboru *web.config.*
1. Nastavte **stdoutLogEnabled** na `true` cestu **stdoutLogFile** `\\?\%home%\LogFiles\stdout`a změňte ji na: .
1. Vyberte **Uložit,** chcete-li uložit aktualizovaný soubor *web.config.*

Postupujte k aktivaci diagnostického protokolování:

1. Na webu Azure Portal vyberte okno **Protokoly diagnostiky.**
1. Vyberte přepínač **Zapnuto** pro **protokolování aplikací (souborový systém)** a **Podrobné chybové zprávy**. V horní části nože vyberte tlačítko **Uložit.**
1. Chcete-li zahrnout trasování neúspěšných požadavků, známé také jako protokolfreb (Failed Request Event Buffering), vyberte přepínač **Zapnuto** pro **trasování neúspěšných požadavků**.
1. Vyberte okno **protokolu datového proudu,** který je uveden okamžitě pod **protokoly protokoly diagnostiky** na portálu.
1. Požádejte o to do aplikace.
1. V rámci dat datového proudu protokolu je uvedena příčina chyby.

Nezapomeňte zakázat stdout protokolování po dokončení řešení potíží.

Zobrazení protokolů trasování neúspěšných požadavků (protokoly FREB):

1. Přejděte na okno **Diagnostikovat a vyřešit problémy** na webu Azure Portal.
1. V yberte **Protokoly trasování neúspěšných požadavků** v oblasti **NÁSTROJE PODPORY** na postranním panelu.

Další informace [najdete v tématu Sledování neúspěšných požadavků v tématu Povolit diagnostiku pro webové aplikace v tématu Služby aplikací](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) a [v nejčastějších dotazech k výkonu aplikací pro webové aplikace v Azure: Jak zapnu trasování neúspěšných požadavků?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)

Další informace najdete [v tématu Povolení protokolování diagnostiky pro webové aplikace ve službě Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).

> [!WARNING]
> Selhání zakázání protokolu stdout může vést k selhání aplikace nebo serveru. Neexistuje žádné omezení na velikost souboru protokolu nebo počet vytvořených souborů protokolu.
>
> Pro rutinní protokolování v aplikaci ASP.NET Core použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otáčí protokoly. Další informace naleznete [v tématu zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).

## <a name="troubleshoot-on-iis"></a>Odstraňování potíží ve službě IIS

### <a name="application-event-log-iis"></a>Protokol událostí aplikace (IIS)

Přístup k protokolu událostí aplikace:

1. Otevřete nabídku Start, vyhledejte *Prohlížeč událostí*a vyberte aplikaci **Prohlížeč událostí.**
1. V **Prohlížeči událostí**otevřete uzel **Protokoly systému Windows.**
1. Výběrem **možnosti Aplikace** otevřete protokol událostí aplikace.
1. Vyhledejte chyby spojené s aplikací se selháním. Chyby mají hodnotu *modulu AspNetCore služby IIS* nebo *modulu AspNetCore služby IIS Express* ve sloupci *Zdroj.*

### <a name="run-the-app-at-a-command-prompt"></a>Spuštění aplikace na příkazovém řádku

Mnoho chyb při spuštění nevytváří užitečné informace v protokolu událostí aplikace. Příčinu některých chyb můžete najít spuštěním aplikace na příkazovém řádku v hostitelském systému.

#### <a name="framework-dependent-deployment"></a>Nasazení závislé na rámci

Pokud je aplikace [nasazení závislé na rámci](/dotnet/core/deploying/#framework-dependent-deployments-fdd):

1. Na příkazovém řádku přejděte do složky nasazení a spusťte aplikaci spuštěním sestavení aplikace pomocí *dotnet.exe*. V následujícím příkazu nahraďte název sestavení \<aplikace assembly_name `dotnet .\<assembly_name>.dll`>: .
1. Výstup konzoly z aplikace, zobrazující případné chyby, je zapsán do okna konzoly.
1. Pokud dojde k chybám při podání požadavku na aplikaci, podat žádost na hostitele a port, kde Kestrel naslouchá. Pomocí výchozího hostitele a příspěvku `http://localhost:5000/`požádejte o místo . Pokud aplikace reaguje normálně na adrese koncového bodu Kestrel, problém je pravděpodobnější související s konfigurací hostování a méně pravděpodobné v rámci aplikace.

#### <a name="self-contained-deployment"></a>Samostatné nasazení

Pokud se jedná o [samostatné nasazení](/dotnet/core/deploying/#self-contained-deployments-scd)aplikace :

1. Na příkazovém řádku přejděte do složky nasazení a spusťte spustitelný soubor aplikace. V následujícím příkazu nahraďte název sestavení \<aplikace assembly_name `<assembly_name>.exe`>: .
1. Výstup konzoly z aplikace, zobrazující případné chyby, je zapsán do okna konzoly.
1. Pokud dojde k chybám při podání požadavku na aplikaci, podat žádost na hostitele a port, kde Kestrel naslouchá. Pomocí výchozího hostitele a příspěvku `http://localhost:5000/`požádejte o místo . Pokud aplikace reaguje normálně na adrese koncového bodu Kestrel, problém je pravděpodobnější související s konfigurací hostování a méně pravděpodobné v rámci aplikace.

### <a name="aspnet-core-module-stdout-log-iis"></a>ASP.NET protokol stdout modulu (IIS)

Povolení a zobrazení protokolů stdout:

1. Přejděte do složky nasazení webu v hostitelském systému.
1. Pokud složka *protokolů* není k dispozici, vytvořte složku. Pokyny k povolení msbuild vytvořit složku *protokolů* v nasazení automaticky, naleznete v tématu [directory struktura.](xref:host-and-deploy/directory-structure)
1. Upravte soubor *web.config.* Nastavte **stdoutLogEnabled** a `true` změňte cestu **stdoutLogFile** tak, `.\logs\stdout`aby přecózovala na složku *protokolů* (například ). `stdout`v cestě je předpona názvu souboru protokolu. Časové razítko, ID procesu a přípona souboru jsou přidány automaticky při vytvoření protokolu. Jako `stdout` předpona názvu souboru je typický soubor protokolu pojmenován *stdout_20180205184032_5412.log*.
1. Ujistěte se, že identita fondu aplikací má oprávnění k zápisu do složky *protokolů.*
1. Uložte aktualizovaný soubor *web.config.*
1. Požádejte o to do aplikace.
1. Přejděte do složky *protokolů.* Vyhledejte a otevřete nejnovější protokol stdout.
1. Prostudujte si protokol pro chyby.

Zakažte protokolování stdout po dokončení řešení potíží:

1. Upravte soubor *web.config.*
1. Nastavte **stdoutLogEnabled** na `false`.
1. Uložte soubor.

Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

> [!WARNING]
> Selhání zakázání protokolu stdout může vést k selhání aplikace nebo serveru. Neexistuje žádné omezení na velikost souboru protokolu nebo počet vytvořených souborů protokolu.
>
> Pro rutinní protokolování v aplikaci ASP.NET Core použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otáčí protokoly. Další informace naleznete [v tématu zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).

### <a name="enable-the-developer-exception-page"></a>Povolení stránky výjimky pro vývojáře

`ASPNETCORE_ENVIRONMENT` [Proměnnou prostředí lze přidat do web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) pro spuštění aplikace ve vývojovém prostředí. Pokud prostředí není přepsáno při spuštění `UseEnvironment` aplikace tvůrcem hostitele, nastavení proměnné prostředí umožňuje, aby se [stránka výjimky pro vývojáře](xref:fundamentals/error-handling) zobrazila při spuštění aplikace.

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

Nastavení proměnné prostředí `ASPNETCORE_ENVIRONMENT` pro se doporučuje pouze pro použití na pracovních a testovacích serverech, které nejsou vystaveny Internetu. Po odstranění potíží odeberte proměnnou prostředí ze souboru *web.config.* Informace o nastavení proměnných prostředí v *souboru web.config*naleznete v [tématu podřízený prvek environmentVariables aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).

### <a name="obtain-data-from-an-app"></a>Získání dat z aplikace

Pokud je aplikace schopná reagovat na požadavky, získejte požadavek, připojení a další data z aplikace pomocí terminálového inline middlewaru. Další informace a ukázkový <xref:test/troubleshoot#obtain-data-from-an-app>kód naleznete v tématu .

### <a name="slow-or-hanging-app-iis"></a>Pomalá nebo závěsná aplikace (IIS)

*Výpis stavu systému* je snímek paměti systému a může pomoci určit příčinu selhání aplikace, selhání při spuštění nebo pomalé aplikace.

#### <a name="app-crashes-or-encounters-an-exception"></a>Aplikace dojde k chybě nebo narazí na výjimku

Získání a analýza výpisu z [programu Zasílání zpráv o chybách systému Windows (WER)](/windows/desktop/wer/windows-error-reporting):

1. Vytvořte složku, ve které `c:\dumps`budou uloženy soubory s výpisem stavu systému . Fond aplikací musí mít přístup pro zápis do složky.
1. Spusťte [skript EnableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):
   * Pokud aplikace používá [model hostování v procesu](xref:host-and-deploy/iis/index#in-process-hosting-model), spusťte skript pro *w3wp.exe*:

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * Pokud aplikace používá [mimoprocesový model hostování](xref:host-and-deploy/iis/index#out-of-process-hosting-model), spusťte skript pro *dotnet.exe*:

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. Spusťte aplikaci za podmínek, které způsobují selhání dojít.
1. Po selhání spusťte [skript DisableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):
   * Pokud aplikace používá [model hostování v procesu](xref:host-and-deploy/iis/index#in-process-hosting-model), spusťte skript pro *w3wp.exe*:

     ```console
     .\DisableDumps w3wp.exe
     ```

   * Pokud aplikace používá [mimoprocesový model hostování](xref:host-and-deploy/iis/index#out-of-process-hosting-model), spusťte skript pro *dotnet.exe*:

     ```console
     .\DisableDumps dotnet.exe
     ```

Po zhroucení aplikace a výpis kolekce je kompletní, aplikace je povoleno ukončit normálně. Skript PowerShell udává WER tak, aby shromažďoval až pět výpisů v aplikaci.

> [!WARNING]
> Výpisy stavu systému může zabírají velké množství místa na disku (až několik gigabajtů každý).

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>Aplikace přestane reagovat, selže při spuštění nebo běží normálně

Když aplikace *přestane reagovat* (přestane reagovat, ale nezhroutí se), selže při spuštění nebo běží normálně, najdete v [tématu Soubory výpisu uživatelského režimu: Výběr nejvhodnějšího nástroje](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) pro výběr vhodného nástroje pro vytvoření výpisu.

#### <a name="analyze-the-dump"></a>Analyzovat výpis

Výpis lze analyzovat pomocí několika přístupů. Další informace naleznete [v tématu Analýza souboru s výpisem stavu uživatelského režimu](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).

## <a name="clear-package-caches"></a>Vymazat mezipaměti balíčků

Funkční aplikace může selhat ihned po upgradu sady .NET Core SDK ve vývojovém počítači nebo změně verze balíčku v rámci aplikace. V některých případech nesouvislé balíčky může přerušit aplikaci při provádění hlavních upgradů. Většinu těchto problémů lze opravit podle následujících pokynů:

1. Odstraňte složky *bin* a *obj.*
1. Vymazat mezipaměti balíčku spuštěním [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) z příkazového prostředí.

   Vymazání mezipaměti balíčků lze také provést pomocí nástroje [nuget.exe](https://www.nuget.org/downloads) a spuštěnípříkazu `nuget locals all -clear`. *nuget.exe* není přibalená instalace s operačním systémem Windows desktop a musí být získány odděleně od [webu NuGet](https://www.nuget.org/downloads).

1. Obnovte a znovu vytvořte projekt.
1. Před opětovnou nasazením aplikace odstraňte všechny soubory ve složce nasazení na serveru.

## <a name="additional-resources"></a>Další zdroje

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a>Dokumentace k Azure

* [Application Insights pro ASP.NET Core](/azure/application-insights/app-insights-asp-net-core)
* [Část Řešení potíží s webovou aplikací ve službě Azure App Service ve vzdáleném ladění webových aplikací pomocí Visual Studia](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [Přehled diagnostiky služby Azure App Service](/azure/app-service/app-service-diagnostics)
* [Postup: Monitorování aplikací ve službě Azure App Service](/azure/app-service/web-sites-monitor)
* [Poradce při potížích s webovou aplikací ve službě Azure App Service pomocí Visual Studia](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [Poradce při potížích s chybami HTTP "502 chybná brána" a "503 služba není k dispozici" ve svých webových aplikacích Azure](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [Řešení potíží s výkonem pomalých webových aplikací ve službě Azure App Service](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [Časté otázky k výkonu aplikací pro webové aplikace v Azure](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [Azure Web App izolovaného prostoru (omezení spuštění služby App Service)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12minutové video)](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a>Dokumentace sady Visual Studio

* [Vzdálené ladění ASP.NET jádro ve službě IIS v Azure ve Visual Studiu 2017](/visualstudio/debugger/remote-debugging-azure)
* [Vzdálené ladění ASP.NET jádra ve vzdáleném počítači služby IIS v sadě Visual Studio 2017](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [Informace o ladění pomocí sady Visual Studio](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a>Dokumentace kódu visual studia

* [Ladění pomocí kódu sady Visual Studio](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end

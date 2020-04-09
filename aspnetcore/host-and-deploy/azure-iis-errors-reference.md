---
title: Odkaz na běžné chyby pro službu Azure App Service a službu IIS s ASP.NET Core
author: rick-anderson
description: Získejte rady pro řešení potíží s běžnými chybami při hostování aplikací ASP.NET Core ve službě Azure Apps Service a IIS.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: host-and-deploy/azure-iis-errors-reference
ms.openlocfilehash: 635c4cf6f12e62ca7e795b3b3b47e9445b945551
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511597"
---
# <a name="common-errors-reference-for-azure-app-service-and-iis-with-aspnet-core"></a>Odkaz na běžné chyby pro službu Azure App Service a službu IIS s ASP.NET Core

::: moniker range=">= aspnetcore-2.2"

Toto téma popisuje běžné chyby a poskytuje rady pro řešení potíží s konkrétními chybami při hostování aplikací ASP.NET Core ve službě Azure Apps Service a IIS.

Obecné pokyny pro řešení <xref:test/troubleshoot-azure-iis>potíží naleznete v tématu .

Shromážděte následující údaje:

* Chování prohlížeče (stavový kód a chybová zpráva)
* Položky protokolu událostí aplikace
  * Azure App &ndash; <xref:test/troubleshoot-azure-iis>Service viz .
  * IIS
    1. V nabídce **Windows** vyberte **Start,** zadejte *Prohlížeč událostí*a stiskněte **Enter**.
    1. Po otevření **Prohlížeče událostí** rozbalte **aplikaci** **Protokoly** > systému Windows v postranním panelu.
* Položky protokolů stdout a ladění modulu ASP.NET Core
  * Azure App &ndash; <xref:test/troubleshoot-azure-iis>Service viz .
  * IIS &ndash; Postupujte podle pokynů v části [Protokol vytváření a přesměrování](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection) a rozšířené diagnostické [protokoly](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) v tématu ASP.NET základní modul.

Porovnejte informace o chybě s následujícími běžnými chybami. Pokud je nalezena shoda, postupujte podle pokynů pro řešení potíží.

Seznam chyb v tomto tématu není vyčerpávající. Pokud narazíte na chybu, která zde není uvedena, otevřete nový problém pomocí tlačítka **Zpětná vazba obsahu** v dolní části tohoto tématu s podrobnými pokyny, jak chybu reprodukovat.

[!INCLUDE[Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

## <a name="os-upgrade-removed-the-32-bit-aspnet-core-module"></a>Upgrade operačního systému odstranil 32bitový ASP.NET core modul

**Protokol aplikace:** Modul DLL **C:\WINDOWS\system32\inetsrv\aspnetcore.dll** se nepodařilo načíst. Data jsou chyba.

Řešení potíží:

Soubory jiného operačního systému v adresáři **C:\Windows\SysWOW64\inetsrv** nejsou během upgradu operačního systému zachovány. Pokud je ASP.NET základní modul nainstalován před upgradem operačního systému a potom je po upgradu operačního systému spuštěn jakýkoli fond aplikací v 32bitovém režimu, dochází k tomuto problému. Po upgradu operačního systému opravte základní modul ASP.NET. Viz [Instalace balíčku .NET Core Hosting](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle). Po spuštění instalačního programu vyberte **Možnost Opravit.**

## <a name="missing-site-extension-32-bit-x86-and-64-bit-x64-site-extensions-installed-or-wrong-process-bitness-set"></a>Chybějící rozšíření webu, nainstalováná 32bitová (x86) a 64bitová (x64) rozšíření lokality nebo nesprávná sada bitisek procesu

*Platí pro aplikace hostované službou Azure App Services.*

* **Prohlížeč:** Chyba protokolu HTTP 500.0 - selhání načtení obslužné rutiny v procesu ANCM

* **Protokol aplikace:** Vyvolání hostfxr najít obslužnou rutinu neprocesového požadavku se nezdařilo bez nalezení nativní závislosti. Obslužná rutina neprocesových požadavků nebyla k nalezení. Zachycený výstup z vyvolání hostfxr: Nebylo možné najít žádnou kompatibilní verzi architektury. Zadaná architektura Microsoft.AspNetCore.App, verze {VERSION}-preview-\*' nebyla nalezena. Nepodařilo se spustit aplikaci '/LM/W3SVC/1416782824/ROOT', ErrorCode '0x8000ffff'.

* **ASP.NET základní modul stdout Log:** Nebylo možné najít žádnou kompatibilní verzi architektury. Zadaná architektura Microsoft.AspNetCore.App, verze {VERSION}-preview-\*' nebyla nalezena.

* **ASP.NET protokol ladění základního modulu:** Vyvolání hostfxr najít obslužnou rutinu neprocesového požadavku se nezdařilo bez nalezení nativní závislosti. To s největší pravděpodobností znamená, že aplikace je nesprávně nakonfigurována, zkontrolujte verze Microsoft.NetCore.App a Microsoft.AspNetCore.App, které jsou cílem aplikace a jsou nainstalovány v počítači. Vrácený neúspěšný výsledek hresult: 0x8000ffffff. Obslužná rutina neprocesových požadavků nebyla k nalezení. Nebylo možné najít žádnou kompatibilní verzi architektury. Zadaná architektura Microsoft.AspNetCore.App, verze {VERSION}-preview-\*' nebyla nalezena.

Řešení potíží:

* Pokud aplikaci spustíte v běhu ve verzi preview, nainstalujte buď 32bitové (x86) **nebo** 64bitové (x64) rozšíření webu, které odpovídá bitvitosti aplikace a runtime verzi aplikace. **Neinstalujte obě rozšíření ani více verzí rozšíření za běhu.**

  * ASP.NET jádro {RUNTIME VERSION} (x86) Runtime
  * ASP.NET jádro {RUNTIME VERZE} (x64) Modul runtime

  Restartujte aplikaci. Počkejte několik sekund, než se aplikace restartuje.

* Pokud je aplikace spuštěna v době runtime náhledu a jsou [nainstalována](xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension) rozšíření webu 32bit (x86) a 64bit (x64), odinstalujte rozšíření webu, které neodpovídá bittě. Po odebrání rozšíření webu restartujte aplikaci. Počkejte několik sekund, než se aplikace restartuje.

* Pokud spuštění aplikace v době běhu ve verzi preview a bitness rozšíření webu odpovídá bittě aplikace, zkontrolujte, zda *verze runtime* rozšíření náhledu webu odpovídá verzi runtime aplikace.

* Zkontrolujte, zda **platforma** aplikace v **nastavení aplikace** odpovídá bittvosti aplikace.

Další informace naleznete v tématu <xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension>.

## <a name="an-x86-app-is-deployed-but-the-app-pool-isnt-enabled-for-32-bit-apps"></a>Aplikace x86 je nasazená, ale fond aplikací není povolený pro 32bitové aplikace

* **Prohlížeč:** Chyba protokolu HTTP 500.30 – selhání spuštění procesu ANCM

* **Protokol aplikace:** Aplikace '/LM/W3SVC/5/ROOT' s fyzickým kořenovým adresářem {PATH}' zasáhla neočekávanou spravovanou výjimku, kód výjimky = '0xe0434352'. Další informace naleznete v protokolech stderr. Aplikaci '/LM/W3SVC/5/ROOT' s fyzickým kořenovým adresářem {PATH} se nepodařilo načíst clr a spravovanou aplikaci. Pracovní podproces CLR předčasně ukončen.

* **ASP.NET základní modul stdout Log:** Soubor protokolu je vytvořen, ale prázdný.

* **ASP.NET protokol ladění základního modulu:** Vrácený neúspěšný hresult: 0x8007023e

Tento scénář je zachycen sadou SDK při publikování samostatné aplikace. Sada SDK vytvoří chybu, `win10-x64` pokud rid neodpovídá cíl platformy (například RID s `<PlatformTarget>x86</PlatformTarget>` v souboru projektu).

Řešení potíží:

Pro nasazení závislé na rozhraní`<PlatformTarget>x86</PlatformTarget>`x86 ( ) povolte fond aplikací služby IIS pro 32bitové aplikace. Ve Správci služby IIS otevřete **rozšířené nastavení** fondu aplikací a nastavte **možnost Povolit 32bitové aplikace** na **hodnotu True**.

## <a name="platform-conflicts-with-rid"></a>Konflikty platformy se zásadou RID

* **Prohlížeč:** Chyba protokolu HTTP 502.5 – selhání procesu

* **Protokol aplikace:** Aplikaci "MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' s fyzickým kořenovým adresářem C:\{PATH}\' se nepodařilo spustit proces s příkazovým řádkem "C:\{PATH}{ASSEMBLY}. {exe|dll}, ErrorCode = '0x80004005 : ff.

* **ASP.NET základní modul stdout Log:** Neošetřená výjimka: System.BadImageFormatException: Nelze načíst soubor nebo sestavení {ASSEMBLY}.dll.I. Došlo k pokusu o načtení programu s nesprávným formátem.

Řešení potíží:

* Potvrďte, že aplikace běží místně na Kestrel. Selhání procesu může být důsledkem problému v rámci aplikace. Další informace naleznete v tématu <xref:test/troubleshoot-azure-iis>.

* Pokud k této výjimce dojde při nasazení aplikací Azure Apps při upgradu aplikace a nasazení novějších sestavení, ručně odstraňte všechny soubory z předchozího nasazení. Přetrvávající nekompatibilní sestavení může mít `System.BadImageFormatException` za následek výjimku při nasazování upgradované aplikace.

## <a name="uri-endpoint-wrong-or-stopped-website"></a>Koncový bod URI je nesprávný nebo zastavený web

* **Prohlížeč:** ERR_CONNECTION_REFUSED **--OR--** Nelze se připojit

* **Protokol aplikace:** Žádná položka

* **ASP.NET základní modul stdout Log:** Soubor protokolu není vytvořen.

* **ASP.NET protokol ladění základního modulu:** Soubor protokolu není vytvořen.

Řešení potíží:

* Zkontrolujte, zda se pro aplikaci používá správný koncový bod IDENTIFIKÁTORURI. Zkontrolujte vázání.

* Zkontrolujte, zda web iis není ve stavu *Zastaveno.*

## <a name="corewebengine-or-w3svc-server-features-disabled"></a>Funkce serveru CoreWebEngine nebo W3SVC jsou zakázány

**Výjimka operačního es:** Chcete-li používat ASP.NET základnímodul, musí být nainstalovány funkce IIS 7.0 CoreWebEngine a W3SVC.

Řešení potíží:

Zkontrolujte, zda je povolena správná role a funkce. Viz [Konfigurace služby IIS](xref:host-and-deploy/iis/index#iis-configuration).

## <a name="incorrect-website-physical-path-or-app-missing"></a>Chybí nesprávná fyzická cesta nebo aplikace

* **Prohlížeč:** 403 Zakázáno - Přístup je odepřen **--OR--** 403.14 Zakázáno - Webový server je nakonfigurován tak, aby neuvádět obsah tohoto adresáře.

* **Protokol aplikace:** Žádná položka

* **ASP.NET základní modul stdout Log:** Soubor protokolu není vytvořen.

* **ASP.NET protokol ladění základního modulu:** Soubor protokolu není vytvořen.

Řešení potíží:

Zkontrolujte základní **nastavení** webu služby IIS a složku fyzické aplikace. Zkontrolujte, zda je aplikace ve složce na webu služby IIS **Fyzická cesta**.

## <a name="incorrect-role-aspnet-core-module-not-installed-or-incorrect-permissions"></a>Nesprávná role, ASP.NET základní modul není nainstalován nebo nesprávná oprávnění

* **Prohlížeč:** 500.19 Vnitřní chyba serveru - Požadovaná stránka není přístupná, protože související konfigurační data pro stránku jsou neplatná. **...NEBO--** Tuto stránku nelze zobrazit.

* **Protokol aplikace:** Žádná položka

* **ASP.NET základní modul stdout Log:** Soubor protokolu není vytvořen.

* **ASP.NET protokol ladění základního modulu:** Soubor protokolu není vytvořen.

Řešení potíží:

* Zkontrolujte, zda je povolena správná role. Viz [Konfigurace služby IIS](xref:host-and-deploy/iis/index#iis-configuration).

* **Spouštějte funkce & funkce & programy** nebo **aplikace** a ověřte, zda je nainstalován systém Windows **Server Hosting.** Pokud **systém Windows Server Hosting** není v seznamu nainstalovaných programů přítomen, stáhněte a nainstalujte sadu .NET Core Hosting Bundle.

  [Aktuální instalační program .NET Core Hosting Bundle (přímé stahování)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  Další informace naleznete [v tématu Instalace sady .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).

* Ujistěte se, že > **identita** **modelu procesu** **fondu** > aplikací je nastavena na **ApplicationPoolIdentity** nebo vlastní identita má správná oprávnění pro přístup ke složce nasazení aplikace.

* Pokud jste odinstalovali balíček ASP.NET Core Hosting a nainstalovali starší verzi hostingového balíčku, soubor *applicationHost.config* neobsahuje oddíl pro ASP.NET Core Module. Otevřete *soubor applicationHost.config* na *adrese %windir%/System32/inetsrv/config* a vyhledejte skupinu oddílů. `<configuration><configSections><sectionGroup name="system.webServer">` Pokud ve skupině oddílů chybí oddíl pro základní modul ASP.NET, přidejte prvek oddílu:

  ```xml
  <section name="aspNetCore" overrideModeDefault="Allow" />
  ```

  Případně nainstalujte nejnovější verzi ASP.NET core hostingbundle. Nejnovější verze je zpětně kompatibilní s podporovanými aplikacemi ASP.NET Core.

## <a name="incorrect-processpath-missing-path-variable-hosting-bundle-not-installed-systemiis-not-restarted-vc-redistributable-not-installed-or-dotnetexe-access-violation"></a>Nesprávná processPath, chybějící proměnná PATH, hostingový balíček není nainstalován, systém/IIS není restartován, VC++ Redistributable není nainstalován, nebo dotnet.exe narušení přístupu

* **Prohlížeč:** Chyba protokolu HTTP 500.0 - selhání načtení obslužné rutiny v procesu ANCM

* **Protokol aplikace:** Aplikace "MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' s fyzickým\{kořenovým adresářem C: PATH}\' se nepodařilo spustit proces s příkazovým řádkem "{...}" ', ErrorCode = '0x80070002 : 0. Aplikaci {PATH} nebylo možné spustit. Spustitelný soubor nebyl nalezen v {PATH}. Nepodařilo se spustit aplikaci '/LM/W3SVC/2/ROOT', ErrorCode '0x8007023e'.

* **ASP.NET základní modul stdout Log:** Soubor protokolu není vytvořen.

* **ASP.NET protokol ladění základního modulu:** Protokol událostí: Aplikace {PATH} nebyla schopna spustit. Spustitelný soubor nebyl nalezen v {PATH}. Vrácený neúspěšný hresult: 0x8007023e

Řešení potíží:

* Potvrďte, že aplikace běží místně na Kestrel. Selhání procesu může být důsledkem problému v rámci aplikace. Další informace naleznete v tématu <xref:test/troubleshoot-azure-iis>.

* Zkontrolujte atribut *processPath* `<aspNetCore>` na elementu v *web.config* a ověřte, zda se jedná `dotnet` o nasazení závislé na rámci (FDD) nebo `.\{ASSEMBLY}.exe` pro samostatné nasazení [(SCD).](/dotnet/core/deploying/#self-contained-deployments-scd)

* Pro FDD *dotnet.exe* nemusí být přístupné prostřednictvím nastavení PATH. Zkontrolujte, zda v nastavení cesty systému existuje *c:\Program Files\dotnet.\\ *

* Pro FDD *dotnet.exe* nemusí být přístupné pro identitu uživatele fondu aplikací. Zkontrolujte, zda má identita uživatele fondu aplikací přístup k adresáři *C:\Program Files\dotnet.* Zkontrolujte, zda pro identitu uživatele fondu aplikací v adresářích *C:\Program Files\dotnet* a app nejsou nakonfigurována žádná pravidla odepření.

* FDD pravděpodobně byly nasazeny a .NET Core nainstalován bez restartování služby IIS. Restartujte server nebo restartujte službu IIS spuštěním **služby net stop byl /y** následovaný **net start w3svc** z příkazového řádku.

* FDD může být nasazenbez instalace .NET Core runtime na hostitelském systému. Pokud není nainstalován a nenainstalovaný runtime .NET Core, spusťte v systému **instalační program .NET Core Hosting Bundle.**

  [Aktuální instalační program .NET Core Hosting Bundle (přímé stahování)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  Další informace naleznete [v tématu Instalace sady .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).

  Pokud je vyžadován určitý běh, stáhněte jej z [archivu stahování .NET](https://dotnet.microsoft.com/download/archives) a nainstalujte jej do systému. Dokončete instalaci restartováním systému nebo restartováním služby IIS spuštěním **služby NET Stop byl /y** následovaný **net start w3svc** z příkazového řádku.

## <a name="incorrect-arguments-of-aspnetcore-element"></a>Nesprávné argumenty \<elementu aspNetCore>

* **Prohlížeč:** Chyba protokolu HTTP 500.0 - selhání načtení obslužné rutiny v procesu ANCM

* **Protokol aplikace:** Vyvolání hostfxr najít obslužnou rutinu neprocesového požadavku se nezdařilo bez nalezení nativní závislosti. To s největší pravděpodobností znamená, že aplikace je nesprávně nakonfigurována, zkontrolujte verze Microsoft.NetCore.App a Microsoft.AspNetCore.App, které jsou cílem aplikace a jsou nainstalovány v počítači. Obslužná rutina neprocesových požadavků nebyla k nalezení. Zachycený výstup z vyvolání hostfxr: Chtěli jste spustit příkazy dotnet SDK? Nainstalujte dotnet SDK https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 z: Nepodařilo se spustit aplikaci '/LM/W3SVC/3/ROOT', ErrorCode '0x8000ffff'.

* **ASP.NET základní modul stdout Log:** Chtěli jste spustit příkazy sady Dotnet SDK? Nainstalujte dotnet SDK z:https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409

* **ASP.NET protokol ladění základního modulu:** Vyvolání hostfxr najít obslužnou rutinu neprocesového požadavku se nezdařilo bez nalezení nativní závislosti. To s největší pravděpodobností znamená, že aplikace je nesprávně nakonfigurována, zkontrolujte verze Microsoft.NetCore.App a Microsoft.AspNetCore.App, které jsou cílem aplikace a jsou nainstalovány v počítači. Nepodařilo HRESULT vrácena: 0x8000ffff Nelze najít obslužnou rutinu neprocesových požadavků. Zachycený výstup z vyvolání hostfxr: Chtěli jste spustit příkazy dotnet SDK? Nainstalujte dotnet SDK https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 z: Vráceno hRESULT: 0x8000ffffff

Řešení potíží:

* Potvrďte, že aplikace běží místně na Kestrel. Selhání procesu může být důsledkem problému v rámci aplikace. Další informace naleznete v tématu <xref:test/troubleshoot-azure-iis>.

* Zkontrolujte atribut *argumentů* na elementu v `<aspNetCore>` *souboru web.config* `.\{ASSEMBLY}.dll` a ověřte, zda se jedná o (a) pro nasazení závislé na rámci (FDD); nebo (b) není k dispozici`arguments=""`prázdný řetězec ( ) nebo seznam`arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"`argumentů aplikace ( ) pro samostatné nasazení (SCD).

## <a name="missing-net-core-shared-framework"></a>Chybí sdílený rámec .NET Core

* **Prohlížeč:** Chyba protokolu HTTP 500.0 - selhání načtení obslužné rutiny v procesu ANCM

* **Protokol aplikace:** Vyvolání hostfxr najít obslužnou rutinu neprocesového požadavku se nezdařilo bez nalezení nativní závislosti. To s největší pravděpodobností znamená, že aplikace je nesprávně nakonfigurována, zkontrolujte verze Microsoft.NetCore.App a Microsoft.AspNetCore.App, které jsou cílem aplikace a jsou nainstalovány v počítači. Obslužná rutina neprocesových požadavků nebyla k nalezení. Zachycený výstup z vyvolání hostfxr: Nebylo možné najít žádnou kompatibilní verzi architektury. Zadaná architektura Microsoft.AspNetCore.App, verze {VERSION}, nebyla nalezena.

Nepodařilo se spustit aplikaci '/LM/W3SVC/5/ROOT', ErrorCode '0x8000ffff'.

* **ASP.NET základní modul stdout Log:** Nebylo možné najít žádnou kompatibilní verzi architektury. Zadaná architektura Microsoft.AspNetCore.App, verze {VERSION}, nebyla nalezena.

* **ASP.NET protokol ladění základního modulu:** Vrácený neúspěšný hresult: 0x8000ffffff

Řešení potíží:

Pro nasazení závislé na rámci (FDD) potvrďte, že správný čas runtime nainstalován v systému.

## <a name="stopped-application-pool"></a>Zastavený fond aplikací

* **Prohlížeč:** Služba 503 není k dispozici

* **Protokol aplikace:** Žádná položka

* **ASP.NET základní modul stdout Log:** Soubor protokolu není vytvořen.

* **ASP.NET protokol ladění základního modulu:** Soubor protokolu není vytvořen.

Řešení potíží:

Zkontrolujte, zda fond aplikací není ve stavu *Zastaveno.*

## <a name="sub-application-includes-a-handlers-section"></a>Dílčí aplikace obsahuje \<oddíl> obslužných rutin

* **Prohlížeč:** Chyba protokolu HTTP 500.19 – vnitřní chyba serveru

* **Protokol aplikace:** Žádná položka

* **ASP.NET základní modul stdout Log:** Kořenový soubor protokolu je vytvořen a zobrazuje normální provoz. Soubor protokolu podaplikace není vytvořen.

* **ASP.NET protokol ladění základního modulu:** Kořenový soubor protokolu je vytvořen a zobrazuje normální provoz. Soubor protokolu podaplikace není vytvořen.

Řešení potíží:

Zkontrolujte, zda soubor *web.config* podaplikace neobsahuje `<handlers>` oddíl nebo že podaplikace nedědí obslužné rutiny nadřazené aplikace.

Část `<system.webServer>` *web.config* nadřazené aplikace je `<location>` umístěna uvnitř prvku. Vlastnost <xref:System.Configuration.SectionInformation.InheritInChildApplications*> je nastavena tak, aby `false` označovala, že nastavení zadaná v [ \<umístění>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) prvek nejsou zděděna aplikacemi, které jsou umístěny v podadresáři nadřazené aplikace. Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module>.

## <a name="stdout-log-path-incorrect"></a>stdout cesta protokolu nesprávná

* **Prohlížeč:** Aplikace reaguje normálně.

* **Protokol aplikace:** Nelze spustit přesměrování stdout v souboru C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll. Zpráva o výjimce: HRESULT 0x80070005 vrácena na adrese {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84. Nelze zastavit přesměrování stdout v souboru C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll. Zpráva o výjimce: HRESULT 0x80070002 vrácena na {PATH}. Nelze spustit přesměrování stdout v souboru {PATH}\aspnetcorev2_inprocess.dll.

* **ASP.NET základní modul stdout Log:** Soubor protokolu není vytvořen.

* **protokol ladění ASP.NET jádra:** Nelze spustit přesměrování stdout v souboru C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll. Zpráva o výjimce: HRESULT 0x80070005 vrácena na adrese {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84. Nelze zastavit přesměrování stdout v souboru C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll. Zpráva o výjimce: HRESULT 0x80070002 vrácena na {PATH}. Nelze spustit přesměrování stdout v souboru {PATH}\aspnetcorev2_inprocess.dll.

Řešení potíží:

* Cesta `stdoutLogFile` zadaná `<aspNetCore>` v prvku *web.config* neexistuje. Další informace naleznete [v tématu ASP.NET Core Module: Vytvoření protokolu a přesměrování](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection).

* Uživatel fondu aplikací nemá přístup pro zápis do cesty protokolu stdout.

## <a name="application-configuration-general-issue"></a>Obecný problém konfigurace aplikace

* **Prohlížeč:** Chyba protokolu HTTP 500.0 - Selhání načítání obslužné rutiny ancm v procesu **--OR--** Chyba protokolu HTTP 500.30 - Selhání spuštění procesu ANCM

* **Protokol aplikace:** Proměnné

* **ASP.NET základní modul stdout Log:** Soubor protokolu je vytvořen, ale prázdný nebo vytvořen s normálními položkami až do selhání bodu aplikace.

* **ASP.NET protokol ladění základního modulu:** Proměnné

Řešení potíží:

Proces se nepodařilo spustit, s největší pravděpodobností z důvodu konfigurace aplikace nebo problému s programováním.

Další informace najdete v následujících tématech:

* <xref:test/troubleshoot-azure-iis>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Toto téma popisuje běžné chyby a poskytuje rady pro řešení potíží s konkrétními chybami při hostování aplikací ASP.NET Core ve službě Azure Apps Service a IIS.

Obecné pokyny pro řešení <xref:test/troubleshoot-azure-iis>potíží naleznete v tématu .

Shromážděte následující údaje:

* Chování prohlížeče (stavový kód a chybová zpráva)
* Položky protokolu událostí aplikace
  * Azure App &ndash; <xref:test/troubleshoot-azure-iis>Service viz .
  * IIS
    1. V nabídce **Windows** vyberte **Start,** zadejte *Prohlížeč událostí*a stiskněte **Enter**.
    1. Po otevření **Prohlížeče událostí** rozbalte **aplikaci** **Protokoly** > systému Windows v postranním panelu.
* Položky protokolů stdout a ladění modulu ASP.NET Core
  * Azure App &ndash; <xref:test/troubleshoot-azure-iis>Service viz .
  * IIS &ndash; Postupujte podle pokynů v části [Protokol vytváření a přesměrování](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection) a rozšířené diagnostické [protokoly](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) v tématu ASP.NET základní modul.

Porovnejte informace o chybě s následujícími běžnými chybami. Pokud je nalezena shoda, postupujte podle pokynů pro řešení potíží.

Seznam chyb v tomto tématu není vyčerpávající. Pokud narazíte na chybu, která zde není uvedena, otevřete nový problém pomocí tlačítka **Zpětná vazba obsahu** v dolní části tohoto tématu s podrobnými pokyny, jak chybu reprodukovat.

[!INCLUDE[Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

## <a name="os-upgrade-removed-the-32-bit-aspnet-core-module"></a>Upgrade operačního systému odstranil 32bitový ASP.NET core modul

**Protokol aplikace:** Modul DLL **C:\WINDOWS\system32\inetsrv\aspnetcore.dll** se nepodařilo načíst. Data jsou chyba.

Řešení potíží:

Soubory jiného operačního systému v adresáři **C:\Windows\SysWOW64\inetsrv** nejsou během upgradu operačního systému zachovány. Pokud je ASP.NET základní modul nainstalován před upgradem operačního systému a potom je po upgradu operačního systému spuštěn jakýkoli fond aplikací v 32bitovém režimu, dochází k tomuto problému. Po upgradu operačního systému opravte základní modul ASP.NET. Viz [Instalace balíčku .NET Core Hosting](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle). Po spuštění instalačního programu vyberte **Možnost Opravit.**

## <a name="missing-site-extension-32-bit-x86-and-64-bit-x64-site-extensions-installed-or-wrong-process-bitness-set"></a>Chybějící rozšíření webu, nainstalováná 32bitová (x86) a 64bitová (x64) rozšíření lokality nebo nesprávná sada bitisek procesu

*Platí pro aplikace hostované službou Azure App Services.*

* **Prohlížeč:** Chyba protokolu HTTP 500.0 - selhání načtení obslužné rutiny v procesu ANCM

* **Protokol aplikace:** Vyvolání hostfxr najít obslužnou rutinu neprocesového požadavku se nezdařilo bez nalezení nativní závislosti. Obslužná rutina neprocesových požadavků nebyla k nalezení. Zachycený výstup z vyvolání hostfxr: Nebylo možné najít žádnou kompatibilní verzi architektury. Zadaná architektura Microsoft.AspNetCore.App, verze {VERSION}-preview-\*' nebyla nalezena. Nepodařilo se spustit aplikaci '/LM/W3SVC/1416782824/ROOT', ErrorCode '0x8000ffff'.

* **ASP.NET základní modul stdout Log:** Nebylo možné najít žádnou kompatibilní verzi architektury. Zadaná architektura Microsoft.AspNetCore.App, verze {VERSION}-preview-\*' nebyla nalezena.

Řešení potíží:

* Pokud aplikaci spustíte v běhu ve verzi preview, nainstalujte buď 32bitové (x86) **nebo** 64bitové (x64) rozšíření webu, které odpovídá bitvitosti aplikace a runtime verzi aplikace. **Neinstalujte obě rozšíření ani více verzí rozšíření za běhu.**

  * ASP.NET jádro {RUNTIME VERSION} (x86) Runtime
  * ASP.NET jádro {RUNTIME VERZE} (x64) Modul runtime

  Restartujte aplikaci. Počkejte několik sekund, než se aplikace restartuje.

* Pokud je aplikace spuštěna v době runtime náhledu a jsou [nainstalována](xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension) rozšíření webu 32bit (x86) a 64bit (x64), odinstalujte rozšíření webu, které neodpovídá bittě. Po odebrání rozšíření webu restartujte aplikaci. Počkejte několik sekund, než se aplikace restartuje.

* Pokud spuštění aplikace v době běhu ve verzi preview a bitness rozšíření webu odpovídá bittě aplikace, zkontrolujte, zda *verze runtime* rozšíření náhledu webu odpovídá verzi runtime aplikace.

* Zkontrolujte, zda **platforma** aplikace v **nastavení aplikace** odpovídá bittvosti aplikace.

Další informace naleznete v tématu <xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension>.

## <a name="an-x86-app-is-deployed-but-the-app-pool-isnt-enabled-for-32-bit-apps"></a>Aplikace x86 je nasazená, ale fond aplikací není povolený pro 32bitové aplikace

* **Prohlížeč:** Chyba protokolu HTTP 500.30 – selhání spuštění procesu ANCM

* **Protokol aplikace:** Aplikace '/LM/W3SVC/5/ROOT' s fyzickým kořenovým adresářem {PATH}' zasáhla neočekávanou spravovanou výjimku, kód výjimky = '0xe0434352'. Další informace naleznete v protokolech stderr. Aplikaci '/LM/W3SVC/5/ROOT' s fyzickým kořenovým adresářem {PATH} se nepodařilo načíst clr a spravovanou aplikaci. Pracovní podproces CLR předčasně ukončen.

* **ASP.NET základní modul stdout Log:** Soubor protokolu je vytvořen, ale prázdný.

Tento scénář je zachycen sadou SDK při publikování samostatné aplikace. Sada SDK vytvoří chybu, `win10-x64` pokud rid neodpovídá cíl platformy (například RID s `<PlatformTarget>x86</PlatformTarget>` v souboru projektu).

Řešení potíží:

Pro nasazení závislé na rozhraní`<PlatformTarget>x86</PlatformTarget>`x86 ( ) povolte fond aplikací služby IIS pro 32bitové aplikace. Ve Správci služby IIS otevřete **rozšířené nastavení** fondu aplikací a nastavte **možnost Povolit 32bitové aplikace** na **hodnotu True**.

## <a name="platform-conflicts-with-rid"></a>Konflikty platformy se zásadou RID

* **Prohlížeč:** Chyba protokolu HTTP 502.5 – selhání procesu

* **Protokol aplikace:** Aplikaci "MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' s fyzickým kořenovým adresářem C:\{PATH}\' se nepodařilo spustit proces s příkazovým řádkem "C:\{PATH}{ASSEMBLY}. {exe|dll}, ErrorCode = '0x80004005 : ff.

* **ASP.NET základní modul stdout Log:** Neošetřená výjimka: System.BadImageFormatException: Nelze načíst soubor nebo sestavení {ASSEMBLY}.dll.I. Došlo k pokusu o načtení programu s nesprávným formátem.

Řešení potíží:

* Potvrďte, že aplikace běží místně na Kestrel. Selhání procesu může být důsledkem problému v rámci aplikace. Další informace naleznete v tématu <xref:test/troubleshoot-azure-iis>.

* Pokud k této výjimce dojde při nasazení aplikací Azure Apps při upgradu aplikace a nasazení novějších sestavení, ručně odstraňte všechny soubory z předchozího nasazení. Přetrvávající nekompatibilní sestavení může mít `System.BadImageFormatException` za následek výjimku při nasazování upgradované aplikace.

## <a name="uri-endpoint-wrong-or-stopped-website"></a>Koncový bod URI je nesprávný nebo zastavený web

* **Prohlížeč:** ERR_CONNECTION_REFUSED **--OR--** Nelze se připojit

* **Protokol aplikace:** Žádná položka

* **ASP.NET základní modul stdout Log:** Soubor protokolu není vytvořen.

Řešení potíží:

* Zkontrolujte, zda se pro aplikaci používá správný koncový bod IDENTIFIKÁTORURI. Zkontrolujte vázání.

* Zkontrolujte, zda web iis není ve stavu *Zastaveno.*

## <a name="corewebengine-or-w3svc-server-features-disabled"></a>Funkce serveru CoreWebEngine nebo W3SVC jsou zakázány

**Výjimka operačního es:** Chcete-li používat ASP.NET základnímodul, musí být nainstalovány funkce IIS 7.0 CoreWebEngine a W3SVC.

Řešení potíží:

Zkontrolujte, zda je povolena správná role a funkce. Viz [Konfigurace služby IIS](xref:host-and-deploy/iis/index#iis-configuration).

## <a name="incorrect-website-physical-path-or-app-missing"></a>Chybí nesprávná fyzická cesta nebo aplikace

* **Prohlížeč:** 403 Zakázáno - Přístup je odepřen **--OR--** 403.14 Zakázáno - Webový server je nakonfigurován tak, aby neuvádět obsah tohoto adresáře.

* **Protokol aplikace:** Žádná položka

* **ASP.NET základní modul stdout Log:** Soubor protokolu není vytvořen.

Řešení potíží:

Zkontrolujte základní **nastavení** webu služby IIS a složku fyzické aplikace. Zkontrolujte, zda je aplikace ve složce na webu služby IIS **Fyzická cesta**.

## <a name="incorrect-role-aspnet-core-module-not-installed-or-incorrect-permissions"></a>Nesprávná role, ASP.NET základní modul není nainstalován nebo nesprávná oprávnění

* **Prohlížeč:** 500.19 Vnitřní chyba serveru - Požadovaná stránka není přístupná, protože související konfigurační data pro stránku jsou neplatná. **...NEBO--** Tuto stránku nelze zobrazit.

* **Protokol aplikace:** Žádná položka

* **ASP.NET základní modul stdout Log:** Soubor protokolu není vytvořen.

Řešení potíží:

* Zkontrolujte, zda je povolena správná role. Viz [Konfigurace služby IIS](xref:host-and-deploy/iis/index#iis-configuration).

* **Spouštějte funkce & funkce & programy** nebo **aplikace** a ověřte, zda je nainstalován systém Windows **Server Hosting.** Pokud **systém Windows Server Hosting** není v seznamu nainstalovaných programů přítomen, stáhněte a nainstalujte sadu .NET Core Hosting Bundle.

  [Aktuální instalační program .NET Core Hosting Bundle (přímé stahování)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  Další informace naleznete [v tématu Instalace sady .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).

* Ujistěte se, že > **identita** **modelu procesu** **fondu** > aplikací je nastavena na **ApplicationPoolIdentity** nebo vlastní identita má správná oprávnění pro přístup ke složce nasazení aplikace.

* Pokud jste odinstalovali balíček ASP.NET Core Hosting a nainstalovali starší verzi hostingového balíčku, soubor *applicationHost.config* neobsahuje oddíl pro ASP.NET Core Module. Otevřete *soubor applicationHost.config* na *adrese %windir%/System32/inetsrv/config* a vyhledejte skupinu oddílů. `<configuration><configSections><sectionGroup name="system.webServer">` Pokud ve skupině oddílů chybí oddíl pro základní modul ASP.NET, přidejte prvek oddílu:

  ```xml
  <section name="aspNetCore" overrideModeDefault="Allow" />
  ```

  Případně nainstalujte nejnovější verzi ASP.NET core hostingbundle. Nejnovější verze je zpětně kompatibilní s podporovanými aplikacemi ASP.NET Core.

## <a name="incorrect-processpath-missing-path-variable-hosting-bundle-not-installed-systemiis-not-restarted-vc-redistributable-not-installed-or-dotnetexe-access-violation"></a>Nesprávná processPath, chybějící proměnná PATH, hostingový balíček není nainstalován, systém/IIS není restartován, VC++ Redistributable není nainstalován, nebo dotnet.exe narušení přístupu

* **Prohlížeč:** Chyba protokolu HTTP 502.5 – selhání procesu

* **Protokol aplikace:** Aplikace "MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' s fyzickým\{kořenovým adresářem C: PATH}\' se nepodařilo spustit proces s příkazovým řádkem "{...}" ', ErrorCode = '0x80070002 : 0.

* **ASP.NET základní modul stdout Log:** Soubor protokolu je vytvořen, ale prázdný.

Řešení potíží:

* Potvrďte, že aplikace běží místně na Kestrel. Selhání procesu může být důsledkem problému v rámci aplikace. Další informace naleznete v tématu <xref:test/troubleshoot-azure-iis>.

* Zkontrolujte atribut *processPath* `<aspNetCore>` na elementu v *web.config* a ověřte, zda se jedná `dotnet` o nasazení závislé na rámci (FDD) nebo `.\{ASSEMBLY}.exe` pro samostatné nasazení [(SCD).](/dotnet/core/deploying/#self-contained-deployments-scd)

* Pro FDD *dotnet.exe* nemusí být přístupné prostřednictvím nastavení PATH. Zkontrolujte, zda v nastavení cesty systému existuje *c:\Program Files\dotnet.\\ *

* Pro FDD *dotnet.exe* nemusí být přístupné pro identitu uživatele fondu aplikací. Zkontrolujte, zda má identita uživatele fondu aplikací přístup k adresáři *C:\Program Files\dotnet.* Zkontrolujte, zda pro identitu uživatele fondu aplikací v adresářích *C:\Program Files\dotnet* a app nejsou nakonfigurována žádná pravidla odepření.

* FDD pravděpodobně byly nasazeny a .NET Core nainstalován bez restartování služby IIS. Restartujte server nebo restartujte službu IIS spuštěním **služby net stop byl /y** následovaný **net start w3svc** z příkazového řádku.

* FDD může být nasazenbez instalace .NET Core runtime na hostitelském systému. Pokud není nainstalován a nenainstalovaný runtime .NET Core, spusťte v systému **instalační program .NET Core Hosting Bundle.**

  [Aktuální instalační program .NET Core Hosting Bundle (přímé stahování)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  Další informace naleznete [v tématu Instalace sady .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).

  Pokud je vyžadován určitý běh, stáhněte jej z [archivu stahování .NET](https://dotnet.microsoft.com/download/archives) a nainstalujte jej do systému. Dokončete instalaci restartováním systému nebo restartováním služby IIS spuštěním **služby NET Stop byl /y** následovaný **net start w3svc** z příkazového řádku.

## <a name="incorrect-arguments-of-aspnetcore-element"></a>Nesprávné argumenty \<elementu aspNetCore>

* **Prohlížeč:** Chyba protokolu HTTP 502.5 – selhání procesu

* **Protokol aplikace:** Aplikace "MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' s fyzickým\{kořenovým adresářem C: PATH}\' se nepodařilo spustit proces s příkazovým řádkem "dotnet" . \{ASSEMBLY}.dll', ErrorCode = '0x80004005 : 80008081.

* **ASP.NET základní modul stdout Log:** Aplikace, která má být spuštěna, neexistuje: "PATH\{ASSEMBLY}.dll"

Řešení potíží:

* Potvrďte, že aplikace běží místně na Kestrel. Selhání procesu může být důsledkem problému v rámci aplikace. Další informace naleznete v tématu <xref:test/troubleshoot-azure-iis>.

* Zkontrolujte atribut *argumentů* na elementu v `<aspNetCore>` *souboru web.config* `.\{ASSEMBLY}.dll` a ověřte, zda se jedná o (a) pro nasazení závislé na rámci (FDD); nebo (b) není k dispozici`arguments=""`prázdný řetězec ( ) nebo seznam`arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"`argumentů aplikace ( ) pro samostatné nasazení (SCD).

Řešení potíží:

Pro nasazení závislé na rámci (FDD) potvrďte, že správný čas runtime nainstalován v systému.

## <a name="stopped-application-pool"></a>Zastavený fond aplikací

* **Prohlížeč:** Služba 503 není k dispozici

* **Protokol aplikace:** Žádná položka

* **ASP.NET základní modul stdout Log:** Soubor protokolu není vytvořen.

Řešení potíží:

Zkontrolujte, zda fond aplikací není ve stavu *Zastaveno.*

## <a name="sub-application-includes-a-handlers-section"></a>Dílčí aplikace obsahuje \<oddíl> obslužných rutin

* **Prohlížeč:** Chyba protokolu HTTP 500.19 – vnitřní chyba serveru

* **Protokol aplikace:** Žádná položka

* **ASP.NET základní modul stdout Log:** Kořenový soubor protokolu je vytvořen a zobrazuje normální provoz. Soubor protokolu podaplikace není vytvořen.

Řešení potíží:

Zkontrolujte, zda soubor *web.config* podaplikace neobsahuje `<handlers>` oddíl.

## <a name="stdout-log-path-incorrect"></a>stdout cesta protokolu nesprávná

* **Prohlížeč:** Aplikace reaguje normálně.

* **Protokol aplikace:** Upozornění: Nelze vytvořit stdoutLogFile \\? \{PATH}\path_doesnt_exist\stdout_{ID procesu}_{TIMESTAMP}.log, ErrorCode = -2147024893.

* **ASP.NET základní modul stdout Log:** Soubor protokolu není vytvořen.

Řešení potíží:

* Cesta `stdoutLogFile` zadaná `<aspNetCore>` v prvku *web.config* neexistuje. Další informace naleznete [v tématu ASP.NET Core Module: Vytvoření protokolu a přesměrování](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection).

* Uživatel fondu aplikací nemá přístup pro zápis do cesty protokolu stdout.

## <a name="application-configuration-general-issue"></a>Obecný problém konfigurace aplikace

* **Prohlížeč:** Chyba protokolu HTTP 502.5 – selhání procesu

* **Protokol aplikace:** Aplikace "MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' s fyzickým\{kořenovým adresářem "C:\' \{PATH}\{vytvořeným procesem s příkazovým řádkem "C: PATH} ASSEMBLY}. {exe|dll}" " ale buď havarovalnebo neodpověděl nebo neposlouchal na daném portu {PORT}, ErrorCode = {ERROR CODE}'

* **ASP.NET základní modul stdout Log:** Soubor protokolu je vytvořen, ale prázdný.

Řešení potíží:

Proces se nepodařilo spustit, s největší pravděpodobností z důvodu konfigurace aplikace nebo problému s programováním.

Další informace najdete v následujících tématech:

* <xref:test/troubleshoot-azure-iis>
* <xref:test/troubleshoot>

::: moniker-end

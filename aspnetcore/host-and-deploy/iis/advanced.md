---
title: Pokročilá konfigurace
author: rick-anderson
description: Pokročilá konfigurace s modulem ASP.NET Core a Internetová informační služba (IIS).
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 5/7/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/iis/advanced
ms.openlocfilehash: ad2480faeea2f07e51585f5bc6a1c63b3a0b1668
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91755242"
---
# <a name="advanced-configuration-of-the-aspnet-core-module-and-iis"></a>Pokročilá konfigurace modulu ASP.NET Core a služby IIS

Tento článek se zabývá pokročilými možnostmi konfigurace a scénáři pro modul ASP.NET Core a službu IIS.

## <a name="modify-the-stack-size"></a>Úprava velikosti zásobníku

*Platí pouze při použití modelu hostování v rámci procesu.*

Nakonfigurujte velikost spravovaného zásobníku pomocí `stackSize` nastavení v bajtech v `web.config` souboru. Výchozí velikost je 1 048 576 bajtů (1 MB). Následující příklad změní velikost zásobníku na 2 MB (2 097 152 bajtů):

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="stackSize" value="2097152" />
  </handlerSettings>
</aspNetCore>
```

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a>Konfigurace proxy serveru používá protokol HTTP a token párování.

*Platí pouze pro hostování mimo proces.*

Proxy vytvořený mezi modulem ASP.NET Core a Kestrel používá protokol HTTP. Nehrozí riziko odposlouchávání provozu mezi modulem a Kestrel z umístění od serveru.

Párovací token se používá k zajištění, že žádosti přijaté službou Kestrel byly proxy službou IIS a nepocházejí z jiného zdroje. Token párování se vytvoří a nastaví na proměnnou prostředí ( `ASPNETCORE_TOKEN` ) modulem. Párovací token je také nastaven na hlavičku ( `MS-ASPNETCORE-TOKEN` ) pro každý požadavek proxy. Middleware služby IIS kontroluje každý požadavek, který obdrží, a potvrdí, že hodnota hlavičky párového tokenu odpovídá hodnotě proměnné prostředí. Pokud se hodnoty tokenu neshodují, požadavek se zaprotokoluje a odmítne. Proměnná prostředí párového tokenu a přenos dat mezi modulem a Kestrel nejsou přístupné z umístění mimo server. Bez znalosti hodnoty párovacího tokenu nemůže útočník odeslat žádosti, které obcházejí kontrolu v Middlewari IIS.

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a>ASP.NET Core modul se sdílenou konfigurací služby IIS

Instalační služba modulu ASP.NET Core se spouští s oprávněními `TrustedInstaller` účtu. Vzhledem k tomu, že účet místního systému nemá oprávnění k úpravám pro cestu ke sdílené složce, kterou používá sdílená konfigurace služby IIS, vyvolá Instalační program chybu odepření přístupu při pokusu o konfiguraci nastavení modulu v `applicationHost.config` souboru ve sdílené složce.

Pokud používáte konfiguraci IIS Shared na stejném počítači jako instalaci služby IIS, spusťte instalační program sady prostředků ASP.NET Core s `OPT_NO_SHARED_CONFIG_CHECK` parametrem nastaveným na `1` :

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

Pokud cesta ke sdílené konfiguraci není ve stejném počítači jako instalace služby IIS, postupujte podle těchto kroků:

1. Zakažte sdílenou konfiguraci služby IIS.
1. Spusťte instalační program.
1. Exportujte aktualizovaný `applicationHost.config` soubor do sdílené složky.
1. Znovu povolte sdílenou konfiguraci služby IIS.

## <a name="data-protection"></a>Ochrana dat

[Sada ASP.NET Core Data Protection Stack](xref:security/data-protection/introduction) je používána několika ASP.NET Core [middlewary](xref:fundamentals/middleware/index), včetně middlewaru používaného při ověřování. I v případě, že rozhraní API ochrany dat není voláno uživatelským kódem, je třeba nakonfigurovat ochranu dat pomocí skriptu nasazení nebo v uživatelském kódu, aby bylo možné vytvořit trvalé úložiště kryptografických [klíčů](xref:security/data-protection/implementation/key-management). Pokud ochrana dat není nakonfigurovaná, klíče se uchovávají v paměti a při restartování aplikace se zahodí.

Pokud je datový prstenec ochrany dat uložený v paměti při restartu aplikace:

* U cookie tokenů ověřování na základě jsou neověřené. 
* Uživatelé se musí znovu přihlásit na svůj další požadavek. 
* Data chráněná pomocí Key ringu už nebude možné dešifrovat. To může zahrnovat [CSRF tokeny](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) a [ASP.NET Core MVC TempData cookie s](xref:fundamentals/app-state#tempdata).

Pokud chcete v rámci služby IIS nakonfigurovat ochranu dat a zachovat přitom klíčového prstence, použijte **některý** z následujících přístupů:

* **Vytvoření klíčů registru ochrany dat**

  Klíče ochrany dat, které používá aplikace ASP.NET Core, jsou uložené v registru externě pro aplikace. Pokud chcete zachovat klíče pro danou aplikaci, vytvořte klíče registru pro fond aplikací.

  Pro samostatnou instalaci služby IIS, která není typu webfarma, se dá [skript Provision-AutoGenKeys.ps1 PowerShellu pro ochranu dat](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) použít pro každý fond aplikací, který se používá v aplikaci ASP.NET Core. Tento skript vytvoří klíč registru v registru HKLM, který je přístupný jenom pro účet pracovního procesu fondu aplikací aplikace. Klíče jsou v klidovém stavu zašifrované pomocí rozhraní DPAPI s klíčem celého počítače.

  Ve scénářích webové farmy může být aplikace nakonfigurovaná tak, aby používala cestu UNC k uložení svého prstence s klíčem pro ochranu dat. Ve výchozím nastavení klíče nejsou šifrovány. Ujistěte se, že oprávnění k souborům pro sdílenou síťovou složku jsou omezená na účet systému Windows, pod kterým je aplikace spuštěná. K ochraně neaktivních klíčů je možné použít certifikát x509. Vezměte v úvahu mechanismus, který uživatelům umožní nahrávat certifikáty. Dejte certifikáty do důvěryhodného úložiště certifikátů uživatele a ujistěte se, že jsou k dispozici na všech počítačích, na kterých běží aplikace uživatele. Další informace naleznete v tématu <xref:security/data-protection/configuration/overview>.

* **Konfigurace fondu aplikací služby IIS pro načtení profilu uživatele**

  Toto nastavení se nachází v části **model procesu** v části **Rozšířená nastavení** fondu aplikací. Nastavte **načíst profil uživatele** na `True` . Pokud je nastaveno na `True` , klíče jsou uloženy v adresáři profilu uživatele a chráněny pomocí rozhraní DPAPI s klíčem specifickým pro uživatelský účet. Klíče jsou uchovány do `%LOCALAPPDATA%/ASP.NET/DataProtection-Keys` složky.

  Musí být povolen i [ `setProfileEnvironment` atribut](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) ve fondu aplikací. Výchozí hodnota `setProfileEnvironment` je `true` . V některých scénářích (například operační systém Windows) `setProfileEnvironment` je nastavena na `false` . Pokud se klíče neukládají v adresáři profilu uživatele podle očekávání:

  1. Přejděte do složky `%windir%/system32/inetsrv/config`.
  1. Otevřete soubor `applicationHost.config`.
  1. Vyhledejte element `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>`.
  1. Potvrďte, že `setProfileEnvironment` atribut není přítomen, přičemž výchozí hodnota je `true` , nebo explicitně nastavte hodnotu atributu na `true` .

* **Použití systému souborů jako úložiště pro Key Ring**

  Upravte kód aplikace tak, aby [používal systém souborů jako úložiště ve službě Key Ring](xref:security/data-protection/configuration/overview). K ochraně služby Key Ring použijte certifikát x509 a ujistěte se, že certifikát je důvěryhodný certifikát. Pokud je certifikát podepsaný svým držitelem, umístěte certifikát do důvěryhodného kořenového úložiště.

  Při použití služby IIS ve webové farmě:

  * Použijte sdílenou složku, ke které mají přístup všechny počítače.
  * Nasaďte do každého počítače certifikát x509. Konfigurace [ochrany dat v kódu](xref:security/data-protection/configuration/overview).

* **Nastavení zásad pro ochranu dat na úrovni počítače**

  Systém ochrany dat má omezená podpora pro nastavení výchozích [zásad](xref:security/data-protection/configuration/machine-wide-policy) pro všechny aplikace, které využívají rozhraní API pro ochranu dat. Další informace naleznete v tématu <xref:security/data-protection/introduction>.

## <a name="iis-configuration"></a>Konfigurace služby IIS

**Operační systémy Windows Server**

Povolte roli serveru **webový server (IIS)** a vytvořte služby rolí.

1. Použijte průvodce **přidáním rolí a funkcí** z nabídky **Správa** nebo odkazu v **Správce serveru**. V kroku **role serveru** zaškrtněte políčko **webový server (IIS)**.

   ![V kroku vybrat role serveru je vybraná role Webový server IIS.](index/_static/server-roles-ws2016.png)

1. Po kroku **funkce** se krok **služby rolí** načte pro webový server (IIS). Vyberte požadované služby role IIS nebo přijměte výchozí poskytnuté služby rolí.

   ![Výchozí služby rolí se vyberou v kroku vybrat služby rolí.](index/_static/role-services-ws2016.png)

   **Ověřování systému Windows (volitelné)**  
   Chcete-li povolit ověřování systému Windows, rozbalte následující uzly: zabezpečení **webového serveru**  >  **Security**. Vyberte funkci **ověřování systému Windows** . Další informace najdete v tématu [ověřování `<windowsAuthentication>` systému Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) a [Konfigurace ověřování systému Windows](xref:security/authentication/windowsauth).

   **WebSockets (volitelné)**  
   Objekty WebSocket jsou podporované ASP.NET Core 1,1 nebo novějším. Chcete-li povolit objekty WebSockets, rozbalte následující **Web Server**uzly:  >  **vývoj aplikací**webového serveru. Vyberte funkci **protokolu WebSocket** . Další informace najdete v tématu [objekty WebSockets](xref:fundamentals/websockets).

1. Chcete-li nainstalovat roli a služby webového serveru, postupujte podle kroků pro **potvrzení** . Po instalaci role **webový server (IIS)** není nutné restartovat server nebo službu IIS.

**Desktopové operační systémy Windows**

Povolte **konzolu pro správu služby IIS** a **webové služby**.

1. Přejděte na **Ovládací panely**  >  **programy**programy  >  **a funkce**  >  **zapnout nebo vypnout funkce systému Windows** (levá strana obrazovky).

1. Otevřete uzel **Internetová informační služba** . Otevřete uzel **Nástroje webové správy** .

1. Zaškrtněte políčko pro **konzolu pro správu služby IIS**.

1. Zaškrtněte políčko u **webových služeb**.

1. Přijměte výchozí funkce pro **webové služby** nebo upravte funkce služby IIS.

   **Ověřování systému Windows (volitelné)**  
   Chcete-li povolit ověřování systému Windows, rozbalte následující uzly: zabezpečení **webové služby**  >  **Security**. Vyberte funkci **ověřování systému Windows** . Další informace najdete v tématu [ověřování `<windowsAuthentication>` systému Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) a [Konfigurace ověřování systému Windows](xref:security/authentication/windowsauth).

   **WebSockets (volitelné)**  
   Objekty WebSocket jsou podporované ASP.NET Core 1,1 nebo novějším. Chcete-li povolit objekty WebSockets, rozbalte následující uzly: funkce pro vývoj aplikací v **rámci webové služby**  >  **Application Development Features**. Vyberte funkci **protokolu WebSocket** . Další informace najdete v tématu [objekty WebSockets](xref:fundamentals/websockets).

1. Pokud instalace služby IIS vyžaduje restart, restartujte systém.

![V rámci funkcí systému Windows jsou vybrána Konzola pro správu služby IIS a webové služby.](index/_static/windows-features-win10.png)

## <a name="virtual-directories"></a>Virtuální adresáře

Aplikace ASP.NET Core nepodporují [virtuální adresáře služby IIS](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) . Aplikace může být hostována jako [dílčí aplikace](#sub-applications).

## <a name="sub-applications"></a>Dílčí aplikace

Aplikace ASP.NET Core může být hostovaná jako [podaplikace služby IIS (dílčí aplikace)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications). Cesta k dílčí aplikaci se stala součástí adresy URL kořenové aplikace.

Propojení statických prostředků v rámci dílčí aplikace by měla používat notaci vlnovku ( `~/` ). Znak tildy vlnovkou spustí [pomocnou nápovědu](xref:mvc/views/tag-helpers/intro) k předřazení pathbase dílčí aplikace na vykreslený relativní odkaz. V případě dílčí aplikace na `/subapp_path` je obrázek propojený s objektem `src="~/image.png"` vykreslen jako `src="/subapp_path/image.png"` . Middleware statických souborů kořenové aplikace nezpracovává požadavek na statický soubor. Požadavek zpracovává middleware statických souborů v dílčí aplikaci.

Pokud je atribut statického prostředku `src` nastavený na absolutní cestu (například `src="/image.png"` ), odkaz se vykreslí bez pathbase dílčí aplikace. Middleware statických souborů v kořenové aplikaci se pokusí o poskytování assetu z [kořenového adresáře webu](xref:fundamentals/index#web-root)kořenové aplikace, což má za následek *404 – nenalezené* odpovědi, pokud není k dispozici statický prostředek z kořenové aplikace.

Hostování aplikace ASP.NET Core jako dílčí aplikace v jiné aplikaci ASP.NET Core:

1. Vytvořte fond aplikací pro dílčí aplikaci. Nastavte **verzi .NET CLR** na **žádný spravovaný kód** , protože základní modul CLR (Common Language Runtime) pro .NET Core se spouští k hostování aplikace v pracovním procesu, ne CLR Desktop (.NET CLR).

1. Přidejte kořenovou lokalitu ve Správci služby IIS pomocí dílčí aplikace do složky v kořenové lokalitě.

1. Klikněte pravým tlačítkem na složku dílčí aplikace ve Správci služby IIS a vyberte **převést na aplikaci**.

1. V dialogovém okně **Přidat aplikaci** použijte pro **fond aplikací** tlačítko **Vybrat** , abyste přiřadili fond aplikací, který jste vytvořili pro dílčí aplikaci. Vyberte **OK**.

Přiřazení samostatného fondu aplikací k dílčí aplikaci je požadavek při použití modelu hostování v rámci procesu.

Další informace o modelu hostování v rámci procesu a konfiguraci modulu ASP.NET Core naleznete v tématu <xref:host-and-deploy/aspnet-core-module> .

## <a name="application-pools"></a>Fondy aplikací

Izolaci fondu aplikací určuje model hostování:

* Hostování v procesu: aplikace se vyžadují ke spuštění v samostatných fondech aplikací.
* Hostování mimo procesy: aplikace doporučujeme izolovat od sebe navzájem tak, že spustíte každou aplikaci ve svém vlastním fondu aplikací.

Dialogové okno **Přidat web** do služby IIS je ve výchozím nastavení nastaveno na jeden fond aplikací na aplikaci. Když je zadaný **název lokality** , text se automaticky přenese do textového pole **fondu aplikací** . Po přidání webu se vytvoří nový fond aplikací s názvem lokality.

## <a name="application-pool-no-locidentity"></a>Fond aplikací Identity

Účet identity fondu aplikací umožňuje aplikaci běžet v rámci jedinečného účtu, aniž by bylo nutné vytvářet a spravovat domény nebo místní účty. Ve službě IIS 8,0 nebo novější služba pracovní proces správce služby IIS (WAS) vytvoří virtuální účet s názvem nového fondu aplikací a ve výchozím nastavení spustí pracovní procesy fondu aplikací v rámci tohoto účtu. V konzole pro správu služby IIS v části **Upřesnit nastavení** fondu aplikací zajistěte, aby byl **Identity** nastaven na hodnotu použít `ApplicationPoolIdentity` :

![Dialogové okno Upřesnit nastavení fondu aplikací](index/_static/apppool-identity.png)

Proces správy služby IIS vytvoří v systému zabezpečení systému Windows zabezpečený identifikátor s názvem fondu aplikací. Prostředky je možné zabezpečit pomocí této identity. Tato identita ale není skutečným uživatelským účtem a nezobrazuje se v konzole pro správu uživatelů Windows.

Pokud pracovní proces služby IIS vyžaduje zvýšený přístup k aplikaci, upravte seznam Access Control (ACL) pro adresář obsahující aplikaci:

1. Otevřete Průzkumníka Windows a přejděte do adresáře.

1. Klikněte pravým tlačítkem na adresář a vyberte **vlastnosti**.

1. Na kartě **zabezpečení** vyberte tlačítko **Upravit** a pak klikněte na tlačítko **Přidat** .

1. Vyberte tlačítko **umístění** a ujistěte se, že je vybraný systém.

1. Zadejte `IIS AppPool\{APP POOL NAME}` formát, kde zástupný symbol `{APP POOL NAME}` je název fondu aplikací, v **Zadejte názvy objektů k výběru** oblasti. Vyberte tlačítko pro **kontrolu názvů** . U pole *DefaultAppPool* ověřte názvy pomocí `IIS AppPool\DefaultAppPool` . Když je vybráno tlačítko pro **kontrolu názvů** , hodnota `DefaultAppPool` je uvedena v oblasti názvy objektů. Není možné zadat název fondu aplikací přímo do oblasti názvy objektů. `IIS AppPool\{APP POOL NAME}`Při kontrole názvu objektu použijte formát, kde zástupný symbol `{APP POOL NAME}` je název fondu aplikací.

   ![Dialogová okna pro výběr uživatelů nebo skupin pro složku aplikace: název fondu aplikací "DefaultAppPool" se připojí k "rozhraní IIS AppPool" \" v oblasti názvy objektů před výběrem "kontrolovat jména".](index/_static/select-users-or-groups-1.png)

1. Vyberte **OK**.

   ![Dialogová okna pro výběr uživatelů nebo skupin pro složku aplikace: po výběru možnosti kontrolovat názvy se v oblasti názvy objektů zobrazí název objektu DefaultAppPool.](index/_static/select-users-or-groups-2.png)

1. &amp;Ve výchozím nastavení by měla být udělena oprávnění ke čtení. V případě potřeby zadejte další oprávnění.

Přístup se dá taky udělit na příkazovém řádku pomocí nástroje **Icacls** . Pomocí příkazu *DefaultAppPool* jako příkladu se používá následující příkaz:

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

Další informace najdete v tématu [Icacls](/windows-server/administration/windows-commands/icacls) .

## <a name="http2-support"></a>Podpora HTTP/2

[Http/2](https://httpwg.org/specs/rfc7540.html) se podporuje s ASP.NET Core v následujících scénářích nasazení služby IIS:

* V procesu
  * Windows Server 2016/Windows 10 nebo novější; IIS 10 nebo novější
  * Připojení TLS 1,2 nebo novější
* Mimo proces
  * Windows Server 2016/Windows 10 nebo novější; IIS 10 nebo novější
  * Veřejná připojení hraničních serverů používají protokol HTTP/2, ale připojení reverzního proxy [serveru Kestrel](xref:fundamentals/servers/kestrel) používá protokol HTTP/1.1.
  * Připojení TLS 1,2 nebo novější

Pro nasazení v rámci procesu, když je vytvořeno připojení HTTP/2, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) sestavy `HTTP/2` . Pro nasazení mimo procesy, když je navázáno připojení HTTP/2, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) sestavy `HTTP/1.1` .

Další informace o modelech hostování v procesu a mimoprocesové procesy naleznete v tématu <xref:host-and-deploy/aspnet-core-module> .

HTTP/2 je ve výchozím nastavení povolené. Pokud není navázáno připojení HTTP/2, připojení se vrátí k HTTP/1.1. Další informace o konfiguraci HTTP/2 v nasazeních služby IIS najdete v tématu [http/2 ve službě IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).

## <a name="cors-preflight-requests"></a>Požadavky na kontrolu před výstupem CORS

*Tato část platí jenom pro ASP.NET Core aplikace, které cílí na .NET Framework.*

Pro ASP.NET Core aplikaci, která cílí na .NET Framework, požadavky na možnosti nejsou ve výchozím nastavení ve službě IIS předány do aplikace. Informace o tom, jak nakonfigurovat obslužné rutiny služby IIS v aplikaci `web.config` k předání požadavků na možnosti, najdete v tématu [Povolení žádostí mezi zdroji v ASP.NET webovém rozhraní API 2: jak CORS funguje](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).

## <a name="application-initialization-module-and-idle-timeout"></a>Modul inicializace aplikace a časový limit nečinnosti

Při hostování ve službě IIS pomocí modulu ASP.NET Core verze 2:

* [Modul inicializace aplikace](#application-initialization-module): hostovaný [v procesu](xref:host-and-deploy/iis/in-process-hosting) nebo [mimoprocesové](xref:host-and-deploy/iis/out-of-process-hosting) aplikaci aplikace je možné nakonfigurovat tak, aby se automaticky spouštěla v pracovním procesu nebo na restartování serveru.
* [Časový limit nečinnosti](#idle-timeout): hostovaný [v procesu](xref:host-and-deploy/iis/in-process-hosting) aplikace se dá nakonfigurovat tak, aby nevypršel časový limit v období nečinnosti.

### <a name="application-initialization-module"></a>Modul inicializace aplikace

*Platí pro aplikace hostované v procesu a mimo proces.*

[Inicializace aplikace IIS](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) je funkce služby IIS, která do aplikace pošle požadavek HTTP, když se spustí nebo recykluje fond aplikací. Požadavek spustí spuštění aplikace. Služba IIS ve výchozím nastavení vydá požadavek na kořenovou adresu URL aplikace ( `/` ) k inicializaci aplikace (Další informace o konfiguraci najdete v [dalších zdrojích](#application-initialization-module-and-idle-timeout-additional-resources) ).

Potvrďte, že je povolená funkce role inicializace aplikace služby IIS:

V systémech Windows 7 nebo novějších stolních počítačích při používání služby IIS v místním prostředí:

1. Přejděte na **Ovládací panely**  >  **programy**programy  >  **a funkce**  >  **zapnout nebo vypnout funkce systému Windows** (levá strana obrazovky).
1. Otevřete **Internetová informační služba**  >  **World Wide Web Services**  >  **funkce pro vývoj aplikací**webové služby.
1. Zaškrtněte políčko pro **inicializaci aplikace**.

V systému Windows Server 2008 R2 nebo novějším:

1. Otevřete **Průvodce přidáním rolí a funkcí**.
1. Na panelu **Vybrat služby rolí** otevřete uzel **vývoj aplikací** .
1. Zaškrtněte políčko pro **inicializaci aplikace**.

Pomocí některého z následujících přístupů povolte modul inicializace aplikace pro danou lokalitu:

* Pomocí Správce služby IIS:

  1. Na panelu **připojení** vyberte **fondy aplikací** .
  1. V seznamu klikněte pravým tlačítkem na fond aplikací aplikace a vyberte **Upřesnit nastavení**.
  1. Výchozí **režim spuštění** je `OnDemand` . Nastavte **režim spuštění** na `AlwaysRunning` . Vyberte **OK**.
  1. Otevřete uzel **lokality** na panelu **připojení** .
  1. Klikněte pravým tlačítkem na aplikaci a vyberte **Spravovat**  >  **Rozšířená nastavení**webu.
  1. Výchozí nastavení pro **Povolení přednačtení** je `False` . Nastavte **přednačtení povoleno** na `True` . Vyberte **OK**.

* Pomocí `web.config` přidejte `<applicationInitialization>` element s nastavením do `doAppInitAfterRestart` `true` `<system.webServer>` prvků v `web.config` souboru aplikace:

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <applicationInitialization doAppInitAfterRestart="true" />
      </system.webServer>
    </location>
  </configuration>
  ```

### <a name="idle-timeout"></a>Časový limit nečinnosti

*Platí jenom pro aplikace hostované v procesu.*

Pokud chcete zabránit volnoběhu aplikace, nastavte časový limit nečinnosti fondu aplikací pomocí Správce služby IIS:

1. Na panelu **připojení** vyberte **fondy aplikací** .
1. V seznamu klikněte pravým tlačítkem na fond aplikací aplikace a vyberte **Upřesnit nastavení**.
1. Výchozí **časový limit nečinnosti (minuty)** je `20` minuty. Nastavte **časový limit nečinnosti (v minutách)** na `0` (nula). Vyberte **OK**.
1. Recyklujte pracovní proces.

Chcete-li zabránit aplikacím hostovaným v [procesu](xref:host-and-deploy/iis/out-of-process-hosting) vypršení časového limitu, použijte některý z následujících přístupů:

* Pomocí příkazu otestujete aplikaci z externí služby, aby byla spuštěná.
* Pokud aplikace hostuje jenom služby na pozadí, vyhněte se hostování služby IIS a použití [služby Windows k hostování aplikace ASP.NET Core](xref:host-and-deploy/windows-service).

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a>Modul inicializace aplikace a další prostředky časového limitu nečinnosti

* [Inicializace aplikace IIS 8,0](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* [Inicializace `<applicationInitialization>` aplikace ](/iis/configuration/system.webserver/applicationinitialization/)
* [Nastavení modelu procesu pro `<processModel>` fond aplikací ](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).

## <a name="module-schema-and-configuration-file-locations"></a>Umístění souboru modulu, schématu a konfiguračního souboru

### <a name="module"></a>Modul

**Služba IIS (x86/amd64)**:

* `%windir%\System32\inetsrv\aspnetcore.dll`

* `%windir%\SysWOW64\inetsrv\aspnetcore.dll`

* `%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

**IIS Express (x86/amd64)**:

* `%ProgramFiles%\IIS Express\aspnetcore.dll`

* `%ProgramFiles(x86)%\IIS Express\aspnetcore.dll`

* `%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

### <a name="schema"></a>Schéma

**IIS**

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml`

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml`

**IIS Express**

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml`

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml`

### <a name="configuration"></a>Konfigurace

**IIS**

* `%windir%\System32\inetsrv\config\applicationHost.config`

**IIS Express**

* Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`

* *iisexpress.exe* CLI `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`

Soubory lze najít hledáním `aspnetcore` v `applicationHost.config` souboru.

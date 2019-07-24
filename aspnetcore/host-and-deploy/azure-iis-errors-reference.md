---
title: Referenční informace o běžných chybách pro Azure App Service a IIS s ASP.NET Core
author: guardrex
description: Získejte Rady pro řešení běžných chyb při hostování ASP.NET Corech aplikací ve službě Azure Apps a službě IIS.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
uid: host-and-deploy/azure-iis-errors-reference
ms.openlocfilehash: 3030bc57be113d9034123c96403742442b9240bb
ms.sourcegitcommit: b40613c603d6f0cc71f3232c16df61550907f550
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2019
ms.locfileid: "68308104"
---
# <a name="common-errors-reference-for-azure-app-service-and-iis-with-aspnet-core"></a>Referenční informace o běžných chybách pro Azure App Service a IIS s ASP.NET Core

Podle [Luke Latham](https://github.com/guardrex)

Toto téma nabízí Rady pro řešení běžných chyb při hostování ASP.NET Corech aplikací ve službě Azure Apps a službě IIS.

Shromážděte následující informace:

* Chování prohlížeče (Stavový kód a chybová zpráva)
* Položky protokolu událostí aplikace
  * Azure App Service &ndash; najdete <xref:test/troubleshoot-azure-iis>v tématu.
  * IIS
    1. V nabídce **systému Windows** vyberte **Start** , zadejte příkaz *Prohlížeč událostí*a stiskněte klávesu **ENTER**.
    1. Po otevření **Prohlížeč událostí** rozbalte na postranním panelu položku **protokoly** > systému Windows.
* ASP.NET Core modul stdout a položky protokolu ladění
  * Azure App Service &ndash; najdete <xref:test/troubleshoot-azure-iis>v tématu.
  * Služba &ndash; IIS podle pokynů v částech [Vytvoření protokolu a přesměrování](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection) a [Rozšířené diagnostické protokoly](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) v tématu ASP.NET Core modulu.

Porovnejte informace o chybě s následujícími běžnými chybami. Pokud se najde shoda, postupujte podle pokynů pro řešení potíží.

Seznam chyb v tomto tématu není vyčerpávající. Pokud narazíte na chybu, která zde není uvedená, otevřete nový problém pomocí tlačítka pro **odeslání obsahu** v dolní části tohoto tématu s podrobnými pokyny pro reprodukování chyby.

[!INCLUDE[Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

## <a name="installer-unable-to-obtain-vc-redistributable"></a>Instalační program nemůže získat balíček VC + + Redistributable.

* **Výjimka instalačního programu:** 0x80072EFD **--nebo--** 0x80072f76 – neurčená chyba

* **Výjimka&#8224;protokolu instalačního programu:** Chyba 0x80072EFD **--nebo--** 0x80072f76: Nepovedlo se spustit balíček EXE.

  &#8224;Protokol se nachází na adrese *C:\Users\{uživatel} \AppData\Local\Temp\dd_DotNetCoreWinSvrHosting__{timestamp}.log*.

Řešení potíží:

Pokud systém nemá přístup k Internetu při [instalaci hostující sady .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle), k této výjimce dojde, když instalačnímu programu zabráníte v získání sady *Microsoft C++ Visual 2015 Redistributable*. Získejte instalační program z [webu Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53840). Pokud se instalační program nepovede, server nemusí získat modul runtime .NET Core potřebný k hostování [nasazení závislého na rozhraní (FDD)](/dotnet/core/deploying/#framework-dependent-deployments-fdd). Pokud hostuje FDD, zkontrolujte, že je modul runtime nainstalovaný v části **programy & funkce** nebo **aplikace & funkce**. Pokud je nutný konkrétní modul runtime, Stáhněte si modul runtime z [archivu stahování v rozhraní .NET](https://dotnet.microsoft.com/download/archives) a nainstalujte ho do systému. Po instalaci modulu runtime restartujte systém nebo restartujte službu IIS spuštěním příkazu **net stop** , který následuje po příkazu **net start w3svc** z příkazového řádku.

## <a name="os-upgrade-removed-the-32-bit-aspnet-core-module"></a>Upgrade operačního systému odebral modul 32 ASP.NET Core.

**Protokol aplikace:** Načtení knihovny DLL modulu **C:\WINDOWS\system32\inetsrv\aspnetcore.dll** se nezdařilo. Tato data jsou chybná.

Řešení potíží:

Soubory jiného typu než operační systém v adresáři **C:\Windows\SysWOW64\inetsrv** nejsou zachovány během upgradu operačního systému. Pokud je modul ASP.NET Core nainstalován před upgradem operačního systému a potom po upgradu operačního systému běží libovolný fond aplikací v 32ovém režimu, dojde k tomuto problému. Po upgradu operačního systému opravte modul ASP.NET Core. Viz [instalace hostující sady .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle). Po spuštění instalačního programu vyberte možnost **opravit** .

## <a name="missing-site-extension-32-bit-x86-and-64-bit-x64-site-extensions-installed-or-wrong-process-bitness-set"></a>Chybí rozšíření webového serveru, 32 (x86) a 64-bit (x64) rozšíření webu nebo nesprávná bitová verze sada procesů.

*Platí pro aplikace hostované v Azure App Services.*

* **Prohlížeee** Chyba protokolu HTTP 500,0 – selhání načtení obslužné rutiny ANCM v procesu

* **Protokol aplikace:** Vyvolání hostfxr k nalezení obslužné rutiny žádosti o zpracování se nezdařilo bez nalezení nativních závislostí. Nepovedlo se najít obslužnou rutinu žádosti o zpracování. Zachycený výstup z vyvolání hostfxr: Nebylo možné najít žádnou kompatibilní verzi rozhraní. Zadané rozhraní Microsoft. AspNetCore. app ', verze {Version}-Preview-\*' nebylo nalezeno. Nepodařilo se spustit aplikaci '/LM/W3SVC/1416782824/ROOT ', ErrorCode ' 0x8000FFFF '.

* **Protokol stdout modulu ASP.NET Core:** Nebylo možné najít žádnou kompatibilní verzi rozhraní. Zadané rozhraní Microsoft. AspNetCore. app ', verze {Version}-Preview-\*' nebylo nalezeno.

::: moniker range=">= aspnetcore-2.2"

* **Protokol ladění ASP.NET Coreho modulu:** Vyvolání hostfxr k nalezení obslužné rutiny žádosti o zpracování se nezdařilo bez nalezení nativních závislostí. To pravděpodobně znamená, že aplikace je nesprávně nakonfigurovaná. Zkontrolujte prosím verze Microsoft. NetCore. app a Microsoft. AspNetCore. app, na které cílí aplikace a které jsou nainstalované v počítači. Vrátila se neúspěšná hodnota HRESULT: 0x8000ffff. Nepovedlo se najít obslužnou rutinu žádosti o zpracování. Nebylo možné najít žádnou kompatibilní verzi rozhraní. Zadané rozhraní Microsoft. AspNetCore. app ', verze {Version}-Preview-\*' nebylo nalezeno.

::: moniker-end

Řešení potíží:

* Pokud spouštíte aplikaci ve verzi Preview, nainstalujte buď rozšíření 32 (x86) **nebo** 64-bit (x64), které odpovídá bitová verze aplikace a verzi modulu runtime aplikace. **Neinstalujte obě rozšíření nebo více verzí modulu runtime rozšíření.**

  * Modul runtime verze ASP.NET Core {RUNTIME} (x86)
  * Modul runtime verze ASP.NET Core {RUNTIME} (x64)

  Restartujte aplikaci. Počkejte několik sekund, než se aplikace restartuje.

* Pokud je aplikace spuštěná ve verzi Preview a nainstalují se [rozšíření lokality](xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension) 32 (x86) i 64 (x64), odinstalujte rozšíření webu, které se neshoduje s bitová verze aplikace. Po odebrání rozšíření lokality aplikaci restartujte. Počkejte několik sekund, než se aplikace restartuje.

* Pokud je aplikace spuštěná ve verzi Preview a bitová verze rozšíření lokality odpovídá této aplikaci, zkontrolujte, že *verze modulu runtime* rozšíření lokality Preview odpovídá verzi modulu runtime aplikace.

* Ověřte, že **platforma** aplikace v **nastavení aplikace** odpovídá bitová verze aplikace.

Další informace naleznete v tématu <xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension>.

## <a name="an-x86-app-is-deployed-but-the-app-pool-isnt-enabled-for-32-bit-apps"></a>Je nasazená aplikace x86, ale fond aplikací není povolený pro 32 aplikace.

* **Prohlížeee** Chyba protokolu HTTP 500,30 – Chyba spuštění ANCM v procesu

* **Protokol aplikace:** U aplikace "/LM/W3SVC/5/ROOT" s fyzickým kořenovým adresářem {PATH} se dosáhlo neočekávané spravované výjimky, kód výjimky = "0xe0434352". Další informace najdete v protokolech stderr. Aplikaci '/LM/W3SVC/5/ROOT ' s fyzickým kořenem ' {PATH} ' se nepodařilo načíst modul CLR a spravovanou aplikaci. Pracovní vlákno CLR byl předčasně ukončeno.

* **Protokol stdout modulu ASP.NET Core:** Soubor protokolu je vytvořen, ale je prázdný.

::: moniker range=">= aspnetcore-2.2"

* **Protokol ladění ASP.NET Coreho modulu:** Vrátila se neúspěšná hodnota HRESULT: 0x8007023e

::: moniker-end

Tento scénář je při publikování samostatně obsažené aplikace zachycen sadou SDK. Sada SDK vytvoří chybu, pokud identifikátor RID neodpovídá cílové platformě (například `win10-x64` `<PlatformTarget>x86</PlatformTarget>` identifikátor RID v souboru projektu).

Řešení potíží:

Pro nasazení závislé na architektuře x86 (`<PlatformTarget>x86</PlatformTarget>`) Povolte fond aplikací IIS pro 32 aplikace. Ve Správci služby IIS otevřete **Rozšířené nastavení** fondu aplikací a nastavte **možnost Povolit 32-bitové aplikace** na **hodnotu true**.

## <a name="platform-conflicts-with-rid"></a>Konflikty platformy s identifikátorem RID

* **Prohlížeee** Chyba protokolu HTTP 502,5 – chyba procesu

* **Protokol aplikace:** Nepodařilo se spustit proces pro aplikaci Machine/Webroot/apphost/{Assembly} s fyzickým\{kořenovým\' adresářem c: cesta} pomocí příkazu CommandLine '\{"C: cesta} {Assembly}. { exe | dll} "', ErrorCode = ' 0x80004005: FF.

* **Protokol stdout modulu ASP.NET Core:** Neošetřená výjimka: System.BadImageFormatException: Nepovedlo se načíst soubor nebo sestavení {ASSEMBLY}. dll. Byl proveden pokus o načtení programu v nesprávném formátu.

Řešení potíží:

* Ověřte, že se aplikace spouští místně na Kestrel. Selhání procesu může být výsledkem problému v aplikaci. Další informace naleznete v tématu <xref:test/troubleshoot-azure-iis>.

* Pokud k této výjimce dojde pro nasazení aplikací Azure při upgradu aplikace a nasazení novějších sestavení, odstraňte ručně všechny soubory z předchozího nasazení. Při zaznamenání nekompatibilních sestavení `System.BadImageFormatException` může dojít k výjimce při nasazení inovované aplikace.

## <a name="uri-endpoint-wrong-or-stopped-website"></a>Koncový bod identifikátoru URI je chybný nebo zastavený Web.

* **Prohlížeee** ERR_CONNECTION_REFUSED **--nebo--** nelze se připojit

* **Protokol aplikace:** Žádná položka

* **Protokol stdout modulu ASP.NET Core:** Soubor protokolu není vytvořen.

::: moniker range=">= aspnetcore-2.2"

* **Protokol ladění ASP.NET Coreho modulu:** Soubor protokolu není vytvořen.

::: moniker-end

Řešení potíží:

* Potvrďte, že se pro aplikaci používá správný koncový bod identifikátoru URI. Ověřte vazby.

* Ověřte, že web IIS není ve stavu *Zastaveno* .

## <a name="corewebengine-or-w3svc-server-features-disabled"></a>Funkce serveru CoreWebEngine nebo W3SVC jsou zakázané.

**Výjimka OS:** Aby bylo možné používat modul ASP.NET Core, musí být nainstalovány funkce IIS 7,0 CoreWebEngine a W3SVC.

Řešení potíží:

Zkontrolujte, jestli jsou povolené správné role a funkce. Viz [Konfigurace služby IIS](xref:host-and-deploy/iis/index#iis-configuration).

## <a name="incorrect-website-physical-path-or-app-missing"></a>Chybí nesprávná fyzická cesta nebo aplikace na webu.

* **Prohlížeee** 403 zakázán-přístup byl odepřen **--nebo--** 403,14 zakázán – webový server je nakonfigurován tak, aby neobsahoval seznam obsahu tohoto adresáře.

* **Protokol aplikace:** Žádná položka

* **Protokol stdout modulu ASP.NET Core:** Soubor protokolu není vytvořen.

::: moniker range=">= aspnetcore-2.2"

* **Protokol ladění ASP.NET Coreho modulu:** Soubor protokolu není vytvořen.

::: moniker-end

Řešení potíží:

Ověřte **základní nastavení** webu IIS a složku fyzické aplikace. Ověřte, že je aplikace ve složce na **fyzické cestě**webu IIS.

## <a name="incorrect-role-aspnet-core-module-not-installed-or-incorrect-permissions"></a>Nesprávná role, ASP.NET Core modul není nainstalovaný nebo má nesprávná oprávnění.

* **Prohlížeee** 500,19 interní chyba serveru – k požadované stránce nelze přistupovat, protože související konfigurační data stránky jsou neplatná. **--Nebo--** Tuto stránku nejde zobrazit.

* **Protokol aplikace:** Žádná položka

* **Protokol stdout modulu ASP.NET Core:** Soubor protokolu není vytvořen.

::: moniker range=">= aspnetcore-2.2"

* **Protokol ladění ASP.NET Coreho modulu:** Soubor protokolu není vytvořen.

::: moniker-end

Řešení potíží:

* Potvrďte, že je povolená správná role. Viz [Konfigurace služby IIS](xref:host-and-deploy/iis/index#iis-configuration).

* Otevřete **programy & funkce** nebo **aplikace & funkce** a potvrďte, že je nainstalované **hostování Windows serveru** . Pokud se v seznamu nainstalovaných programů nenachází **hostování Windows serveru** , Stáhněte a nainstalujte hostující sadu .NET Core.

  [Aktuální instalační program sady hostování rozhraní .NET Core (s přímým přístupem ke stažení)](https://www.microsoft.com/net/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  Další informace najdete v tématu [instalace hostující sady .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).

* Ujistěte se, že**Identita** **modelu** > procesu **fondu** > aplikací je nastavená na **ApplicationPoolIdentity** nebo že vlastní identita má správná oprávnění pro přístup ke složce pro nasazení aplikace.

* Pokud jste odinstalovali sadu hostující sadu ASP.NET Core a nainstalovali jste starší verzi hostujícího balíčku, soubor *ApplicationHost. config* neobsahuje oddíl pro modul ASP.NET Core. Otevřete soubor *ApplicationHost. config* v umístění *% windir%/system32/Inetsrv/config* `<configuration><configSections><sectionGroup name="system.webServer">` a vyhledejte skupinu oddílů. Pokud ve skupině oddílů chybí oddíl ASP.NET Core modulu, přidejte element section:

  ```xml
  <section name="aspNetCore" overrideModeDefault="Allow" />
  ```

  Případně nainstalujte nejnovější verzi sady ASP.NET Core hostující sada. Nejnovější verze je zpětně kompatibilní s podporovanými ASP.NET Core aplikacemi.

## <a name="incorrect-processpath-missing-path-variable-hosting-bundle-not-installed-systemiis-not-restarted-vc-redistributable-not-installed-or-dotnetexe-access-violation"></a>Nesprávná processPath, chybějící Proměnná cesty, hostující sada není nainstalovaná, systém/IIS se nerestartuje, VC + + Redistributable není nainstalovaný nebo došlo k narušení přístupu dotnet. exe.

::: moniker range=">= aspnetcore-2.2"

* **Prohlížeee** Chyba protokolu HTTP 500,0 – selhání načtení obslužné rutiny ANCM v procesu

* **Protokol aplikace:** Nepovedlo se spustit proces pomocí příkazového řádku {...} aplikace Machine/\{Webroot/\' apphost/{Assembly} s fyzickým kořenem C: Path}. ', ErrorCode = ' 0x80070002: 0. Aplikaci {PATH} se nepodařilo spustit. Spustitelný soubor se nenašel v cestě {PATH}. Nepodařilo se spustit aplikaci '/LM/W3SVC/2/ROOT ', ErrorCode ' 0x8007023e '.

* **Protokol stdout modulu ASP.NET Core:** Soubor protokolu není vytvořen.

* **Protokol ladění ASP.NET Coreho modulu:** Protokol událostí: Aplikaci {PATH} se nepodařilo spustit. Spustitelný soubor se nenašel v cestě {PATH}. Vrátila se neúspěšná hodnota HRESULT: 0x8007023e

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* **Prohlížeee** Chyba protokolu HTTP 502,5 – chyba procesu

* **Protokol aplikace:** Nepovedlo se spustit proces pomocí příkazového řádku {...} aplikace Machine/\{Webroot/\' apphost/{Assembly} s fyzickým kořenem C: Path}. ', ErrorCode = ' 0x80070002: 0.

* **Protokol stdout modulu ASP.NET Core:** Soubor protokolu je vytvořen, ale je prázdný.

::: moniker-end

Řešení potíží:

* Ověřte, že se aplikace spouští místně na Kestrel. Selhání procesu může být výsledkem problému v aplikaci. Další informace naleznete v tématu <xref:test/troubleshoot-azure-iis>.

* Zkontrolujte `<aspNetCore>` atribut *processPath* v elementu v *souboru Web. config* a potvrďte, že `dotnet` je pro nasazení závislé na rozhraní (FDD) nebo `.\{ASSEMBLY}.exe` pro samostatné [nasazení (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).

* V případě FDD nemusí být příkaz *dotnet. exe* přístupný prostřednictvím nastavení cesty. Ověřte, že v nastavení systémové cesty existuje *C:\Program Files\dotnet\\*  .

* Pro FDD nemusí být příkaz *dotnet. exe* dostupný pro identitu uživatele fondu aplikací. Potvrďte, že identita uživatele fondu aplikací má přístup k adresáři *C:\Program Files\dotnet* . Ověřte, že v adresáři *C:\Program Files\dotnet* a App Directory nejsou nakonfigurovaná žádná pravidla odepření pro identitu uživatele fondu aplikací.

* FDD mohla být nasazena a .NET Core nainstalována bez restartování služby IIS. Buď restartujte server, nebo restartujte službu IIS spuštěním příkazu **net stop** , který následuje po příkazu **net start w3svc** z příkazového řádku.

* FDD mohla být nasazena bez instalace modulu runtime .NET Core v hostitelském systému. Pokud modul runtime .NET Core není nainstalovaný, spusťte v systému **instalační program sady hostitelských svazků .NET Core** .

  [Aktuální instalační program sady hostování rozhraní .NET Core (s přímým přístupem ke stažení)](https://www.microsoft.com/net/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  Další informace najdete v tématu [instalace hostující sady .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).

  Pokud je nutný konkrétní modul runtime, Stáhněte si modul runtime z [archivu stahování v rozhraní .NET](https://dotnet.microsoft.com/download/archives) a nainstalujte ho do systému. Dokončete instalaci restartováním systému nebo restartováním služby IIS spuštěním příkazu **net stop** , který následuje po příkazu **net start w3svc** z příkazového řádku.

* Mohla být nasazena FDD a *Microsoft Visual C++ 2015 Redistributable (x64)* není v systému nainstalován. Získejte instalační program z [webu Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53840).

## <a name="incorrect-arguments-of-aspnetcore-element"></a>Nesprávné argumenty \<prvku aspNetCore >

::: moniker range=">= aspnetcore-2.2"

* **Prohlížeee** Chyba protokolu HTTP 500,0 – selhání načtení obslužné rutiny ANCM v procesu

* **Protokol aplikace:** Vyvolání hostfxr k nalezení obslužné rutiny žádosti o zpracování se nezdařilo bez nalezení nativních závislostí. To pravděpodobně znamená, že aplikace je nesprávně nakonfigurovaná. Zkontrolujte prosím verze Microsoft. NetCore. app a Microsoft. AspNetCore. app, na které cílí aplikace a které jsou nainstalované v počítači. Nepovedlo se najít obslužnou rutinu žádosti o zpracování. Zachycený výstup z vyvolání hostfxr: Měli jste v úmyslu spustit příkazy sady dotnet SDK? Nainstalujte prosím sadu dotnet SDK z: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 Nepodařilo se spustit aplikaci '/LM/W3SVC/3/ROOT ', ErrorCode ' 0x8000FFFF '.

* **Protokol stdout modulu ASP.NET Core:** Měli jste v úmyslu spustit příkazy sady dotnet SDK? Nainstalujte prosím sadu dotnet SDK z: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409

* **Protokol ladění ASP.NET Coreho modulu:** Vyvolání hostfxr k nalezení obslužné rutiny žádosti o zpracování se nezdařilo bez nalezení nativních závislostí. To pravděpodobně znamená, že aplikace je nesprávně nakonfigurovaná. Zkontrolujte prosím verze Microsoft. NetCore. app a Microsoft. AspNetCore. app, na které cílí aplikace a které jsou nainstalované v počítači. Vrátila se neúspěšná hodnota HRESULT: 0x8000FFFF nemůže najít obslužnou rutinu žádosti o zpracování. Zachycený výstup z vyvolání hostfxr: Měli jste v úmyslu spustit příkazy sady dotnet SDK? Nainstalujte prosím sadu dotnet SDK z: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 Vrátila se neúspěšná hodnota HRESULT: 0x8000ffff

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* **Prohlížeee** Chyba protokolu HTTP 502,5 – chyba procesu

* **Protokol aplikace:** Nepodařilo se spustit proces pomocí příkazového řádku dotnet aplikace Machine/Webroot/apphost/{Assembly\{} s\' fyzickým kořenovým adresářem C: Path}.\{ SESTAVENÍ}. dll, ErrorCode = ' 0x80004005: 80008081.

* **Protokol stdout modulu ASP.NET Core:** Aplikace, která má být spuštěna, neexistuje: Cesta\{Assembly}. dll

::: moniker-end

Řešení potíží:

* Ověřte, že se aplikace spouští místně na Kestrel. Selhání procesu může být výsledkem problému v aplikaci. Další informace naleznete v tématu <xref:test/troubleshoot-azure-iis>.

* Zkontrolujte atribut *arguments* u `<aspNetCore>` elementu v *souboru Web. config* a potvrďte tak, že je buď ( `.\{ASSEMBLY}.dll` a) pro nasazení závislé na rozhraní (FDD); nebo (b) není k dispozici, prázdný`arguments=""`řetězec () nebo seznam argumenty aplikace (`arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"`) pro samostatně uzavřené nasazení (SCD).

::: moniker range=">= aspnetcore-2.2"

## <a name="missing-net-core-shared-framework"></a>Chybějící sdílené rozhraní .NET Core

* **Prohlížeee** Chyba protokolu HTTP 500,0 – selhání načtení obslužné rutiny ANCM v procesu

* **Protokol aplikace:** Vyvolání hostfxr k nalezení obslužné rutiny žádosti o zpracování se nezdařilo bez nalezení nativních závislostí. To pravděpodobně znamená, že aplikace je nesprávně nakonfigurovaná. Zkontrolujte prosím verze Microsoft. NetCore. app a Microsoft. AspNetCore. app, na které cílí aplikace a které jsou nainstalované v počítači. Nepovedlo se najít obslužnou rutinu žádosti o zpracování. Zachycený výstup z vyvolání hostfxr: Nebylo možné najít žádnou kompatibilní verzi rozhraní. Zadané rozhraní Microsoft. AspNetCore. app, verze {VERSION} se nenašlo.

Nepodařilo se spustit aplikaci '/LM/W3SVC/5/ROOT ', ErrorCode ' 0x8000FFFF '.

* **Protokol stdout modulu ASP.NET Core:** Nebylo možné najít žádnou kompatibilní verzi rozhraní. Zadané rozhraní Microsoft. AspNetCore. app, verze {VERSION} se nenašlo.

* **Protokol ladění ASP.NET Coreho modulu:** Vrátila se neúspěšná hodnota HRESULT: 0x8000ffff

::: moniker-end

Řešení potíží:

Pro nasazení závislé na rozhraní (FDD) Zkontrolujte, že je v systému nainstalovaný správný modul runtime.

## <a name="stopped-application-pool"></a>Fond aplikací se zastavil.

* **Prohlížeee** Služba 503 není dostupná.

* **Protokol aplikace:** Žádná položka

* **Protokol stdout modulu ASP.NET Core:** Soubor protokolu není vytvořen.

::: moniker range=">= aspnetcore-2.2"

* **Protokol ladění ASP.NET Coreho modulu:** Soubor protokolu není vytvořen.

::: moniker-end

Řešení potíží:

Ověřte, že fond aplikací není v *zastaveném* stavu.

## <a name="sub-application-includes-a-handlers-section"></a>Dílčí aplikace obsahuje \<> oddíl obslužných rutin.

* **Prohlížeee** Chyba protokolu HTTP 500,19 – interní chyba serveru

* **Protokol aplikace:** Žádná položka

* **Protokol stdout modulu ASP.NET Core:** Vytvoří se soubor protokolu kořenové aplikace a zobrazí se normální operace. Soubor protokolu dílčí aplikace se nevytvoří.

::: moniker range=">= aspnetcore-2.2"

* **Protokol ladění ASP.NET Coreho modulu:** Vytvoří se soubor protokolu kořenové aplikace a zobrazí se normální operace. Soubor protokolu dílčí aplikace se nevytvoří.

::: moniker-end

Řešení potíží:

::: moniker range=">= aspnetcore-2.2"

Potvrďte, že soubor `<handlers>` *Web. config* dílčí aplikace neobsahuje oddíl nebo že dílčí aplikace nedědí obslužné rutiny nadřazené aplikace.

`<system.webServer>` Oddíl v souboru *Web. config* nadřazené aplikace je `<location>` umístěn uvnitř elementu. Vlastnost je nastavená [ \<](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) tak, aby označovala, že nastavení zadaná v rámci umístění > elementu nejsou děděna aplikacemi, které se nacházejí v podadresáři nadřazené aplikace. `false` <xref:System.Configuration.SectionInformation.InheritInChildApplications*> Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module>.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Potvrďte, že soubor *Web. config* dílčí aplikace neobsahuje `<handlers>` oddíl.

::: moniker-end

## <a name="stdout-log-path-incorrect"></a>nesprávná cesta protokolu STDOUT

* **Prohlížeee** Aplikace reaguje normálně.

::: moniker range=">= aspnetcore-2.2"

* **Protokol aplikace:** Nelze spustit přesměrování stdout v adresáři C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll. Zpráva o výjimce: HRESULT 0x80070005 vrácený v {PATH} \aspnetcoremodulev2\commonlib\fileoutputmanager.cpp: 84. Nejde zastavit přesměrování stdout v adresáři C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll. Zpráva o výjimce: HRESULT 0x80070002 vráceno v {PATH}. Nejde spustit přesměrování stdout v cestě {PATH} \aspnetcorev2_inprocess.dll.

* **Protokol stdout modulu ASP.NET Core:** Soubor protokolu není vytvořen.

* **Protokol ladění ASP.NET Coreho modulu:** Nelze spustit přesměrování stdout v adresáři C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll. Zpráva o výjimce: HRESULT 0x80070005 vrácený v {PATH} \aspnetcoremodulev2\commonlib\fileoutputmanager.cpp: 84. Nejde zastavit přesměrování stdout v adresáři C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll. Zpráva o výjimce: HRESULT 0x80070002 vráceno v {PATH}. Nejde spustit přesměrování stdout v cestě {PATH} \aspnetcorev2_inprocess.dll.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* **Protokol aplikace:** Upozornění: Nepovedlo se \\vytvořit\{ stdoutLogFile? CESTA} ID \path_doesnt_exist\stdout_{PROCESS} _ {TIMESTAMP}. log, ErrorCode =-2147024893.

* **Protokol stdout modulu ASP.NET Core:** Soubor protokolu není vytvořen.

::: moniker-end

Řešení potíží:

* Cesta zadaná v elementu *Web. config neexistuje.* `<aspNetCore>` `stdoutLogFile` Další informace najdete v tématu [ASP.NET Core modul: Vytváření a přesměrování](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection)protokolu.

* Uživatel fondu aplikací nemá přístup pro zápis do cesty protokolu STDOUT.

## <a name="application-configuration-general-issue"></a>Obecný problém konfigurace aplikace

::: moniker range=">= aspnetcore-2.2"

* **Prohlížeee** Chyba protokolu HTTP 500,0 – Chyba při načítání obslužné rutiny ANCM v procesu **--nebo--** Chyba protokolu HTTP 500,30-ANCM v procesu spuštění při selhání

* **Protokol aplikace:** Proměnná

* **Protokol stdout modulu ASP.NET Core:** Soubor protokolu se vytvoří, ale vyprázdní nebo vytvoří s normálními položkami, dokud neselže bod aplikace.

* **Protokol ladění ASP.NET Coreho modulu:** Proměnná

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* **Prohlížeee** Chyba protokolu HTTP 502,5 – chyba procesu

* **Protokol aplikace:** Aplikace ' Machine/Webroot/apphost/{Assembly} ' se vytvořeným fyzickým kořenovým '\' C:\{Path} a procesem CommandLine '\{"c\{: path} Assembly}. { exe | dll} "", ale buď došlo k chybě, nebo nereagoval nebo neposlouchal na daném portu "{PORT}", ErrorCode = "{kód chyby}"

* **Protokol stdout modulu ASP.NET Core:** Soubor protokolu je vytvořen, ale je prázdný.

::: moniker-end

Řešení potíží:

Proces se nepovedlo spustit, pravděpodobně v důsledku konfigurace aplikace nebo problému s programováním.

Další informace naleznete v následujících tématech:

* <xref:test/troubleshoot-azure-iis>
* <xref:test/troubleshoot>

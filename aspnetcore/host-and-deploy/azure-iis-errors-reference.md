---
title: Referenční informace o běžných chybách pro Azure App Service a IIS s ASP.NET Core
author: guardrex
description: Získáte pomoc při řešení potíží pro běžné chyby při hostování aplikací ASP.NET Core v Azure App Service a službu IIS.
ms.author: riande
ms.custom: mvc
ms.date: 01/11/2018
uid: host-and-deploy/azure-iis-errors-reference
ms.openlocfilehash: 887482d61ffa74bc8ffb39d0af8507fd10199eb8
ms.sourcegitcommit: 42a8164b8aba21f322ffefacb92301bdfb4d3c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54341495"
---
# <a name="common-errors-reference-for-azure-app-service-and-iis-with-aspnet-core"></a>Referenční informace o běžných chybách pro Azure App Service a IIS s ASP.NET Core

Podle [Luke Latham](https://github.com/guardrex)

Toto téma nabízí pomoc při řešení potíží pro běžné chyby při hostování aplikací ASP.NET Core v Azure App Service a službu IIS.

Shromážděte následující informace:

* Chování prohlížeče (stavový kód a chybové zprávy)
* Položky protokolu událostí aplikace
  * Azure App Service &ndash; naleznete v tématu <xref:host-and-deploy/azure-apps/troubleshoot>.
  * IIS
    1. Vyberte **Start** na **Windows** nabídky, typ *Prohlížeč událostí*a stiskněte klávesu **Enter**.
    1. Po **Prohlížeč událostí** se otevře, rozbalte **protokoly Windows** > **aplikace** na bočním panelu.
* Ladění a ASP.NET Core modulu stdout položky protokolu
  * Azure App Service &ndash; naleznete v tématu <xref:host-and-deploy/azure-apps/troubleshoot>.
  * Služba IIS &ndash; postupujte podle pokynů [vytváření a přesměrování protokolu](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection) a [rozšířené diagnostické protokoly](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) částech tématu modul ASP.NET Core.

Porovnejte následující běžné chyby informace o chybě. Pokud se najde shoda, postupujte podle pomoc při řešení potíží.

Seznam chyb v tomto tématu není vyčerpávající. Pokud narazíte na chybu tu nejsou uvedené, otevřete nový problém pomocí **obsahu zpětnou vazbu** tlačítko v dolní části tohoto tématu, s podrobnými pokyny o tom, jak chybu reprodukovat.

[!INCLUDE[Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

## <a name="installer-unable-to-obtain-vc-redistributable"></a>Instalační program nelze získat VC ++ Redistributable

* **Instalační program výjimka:** 0x80072EFD **--nebo--** 0x80072f76 - Nespecifikovaná chyba

* **Výjimky protokolu instalačního programu&#8224;:** Chyba 0x80072efd **--nebo--** 0x80072f76: Nepovedlo se spustit soubor EXE balíčku

  &#8224;V protokolu se nachází na *C:\Users\{USER}\AppData\Local\Temp\dd_DotNetCoreWinSvrHosting__{TIMESTAMP}.log*.

Řešení potíží:

Pokud systém nemá přístup k Internetu při [instalace sady .NET Core hostování](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle), tato výjimka nastane, pokud instalační program nemůže získat *Microsoft Visual C++ 2015 Redistributable*. Získat z instalačního programu [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53840). Pokud instalační program selže, server nemůže přijímat modulu runtime .NET Core vyžaduje k hostování [nasazení závisí na architektuře (chyba)](/dotnet/core/deploying/#framework-dependent-deployments-fdd). Pokud hostování disketové jednotky, ověřte, že modul runtime je nainstalován ve **programy a funkce** nebo **aplikace a funkce**. Pokud konkrétní modul runtime je vyžadována, stáhněte si modul runtime z [archivy stáhnout .NET](https://dotnet.microsoft.com/download/archives) a nainstalujte ho na systém. Po instalaci modulu runtime, restartování systému nebo restartování služby IIS pomocí provádí **net stop byla /y** následovaný **net start w3svc** z příkazového řádku.

## <a name="os-upgrade-removed-the-32-bit-aspnet-core-module"></a>Upgrade operačního systému odebrat modul ASP.NET Core 32-bit

**Protokol aplikace:** Knihovnu DLL modulu **C:\WINDOWS\system32\inetsrv\aspnetcore.dll** se nepodařilo načíst. Data jsou chyby.

Řešení potíží:

Bez operačního systému souborů v **C:\Windows\SysWOW64\inetsrv** directory nezachovají se během operační systém upgradovat. Pokud modul ASP.NET Core je nainstalovaná starší než upgrade operačního systému a pak v každém fondu aplikací běží v 32bitovém režimu po upgradu operačního systému, tento problém nastává. Po upgradu operačního systému opravit modul ASP.NET Core. Zobrazit [instalaci sady .NET Core hostování](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle). Vyberte **opravit** při spuštění Instalační služby.

## <a name="an-x86-app-is-deployed-but-the-app-pool-isnt-enabled-for-32-bit-apps"></a>X x86 byla nasazena aplikace, ale fond aplikací není povolená pro 32bitové aplikace

* **Prohlížeč:** Chyba protokolu HTTP 500.30 - ANCM selhání spuštění v procesu

* **Protokol aplikace:** Aplikace/LM/W3SVC/5/ROOT se fyzické kořenové {PATH} došlo k neočekávané výjimce spravované, kód výjimky = "0xe0434352". Další informace naleznete v protokolech stderr. Aplikace/LM/W3SVC/5/ROOT se fyzické kořenem {PATH} se nezdařilo načtení modulu clr a spravované aplikace. Pracovní vlákno CLR byl předčasně ukončen.

* **ASP.NET Core modulu stdout protokolu:** Soubor protokolu je vytvořený, ale prázdný.

::: moniker range=">= aspnetcore-2.2"

* **Modul ASP.NET Core protokol ladění:** Vrátí selhání HRESULT: 0x8007023e

::: moniker-end

Tento scénář je zachycena prostřednictvím sady SDK při publikování samostatnou aplikaci. Sada SDK způsobí chybu, pokud identifikátor RID neodpovídá cílové platformy (například `win10-x64` identifikátorů RID s `<PlatformTarget>x86</PlatformTarget>` v souboru projektu).

Řešení potíží:

Pro x x86 nasazení závisí na architektuře (`<PlatformTarget>x86</PlatformTarget>`), povolit fond aplikací služby IIS pro 32bitové aplikace. Ve Správci služby IIS otevřete fond aplikací **Upřesnit nastavení** a nastavte **povolit 32bitové aplikace** k **True**.

## <a name="platform-conflicts-with-rid"></a>Platformu je v konfliktu s identifikátorů RID

* **Prohlížeč:** Chyba protokolu HTTP 502.5 – selhání procesu

* **Protokol aplikace:** Aplikace "MACHINE/WEBROOT nebo APPHOST / {sestavení}' s kořenem fyzické ' C:\{CESTU}\' Nepodařilo se spustit proces pomocí příkazového řádku" "C:\{CESTU} {sestavení}. { soubor EXE | dll} "', kód chyby =" 0x80004005: ff.

* **ASP.NET Core modulu stdout protokolu:** Neošetřená výjimka: System.BadImageFormatException: Nelze načíst soubor nebo sestavení "{sestavení} .dll". Došlo pokusu o načtení programu v nesprávném formátu.

Řešení potíží:

* Potvrďte, že aplikace běží místně na Kestrel. Selhání procesu může být výsledkem problému v rámci aplikace. Další informace najdete v tématu [řešení (IIS)](xref:host-and-deploy/iis/troubleshoot) nebo [řešení potíží (Azure App Service)](xref:host-and-deploy/azure-apps/troubleshoot).

* Pokud tuto výjimku dojde k nasazení aplikace Azure při upgradu aplikace a nasazení novější sestavení, ručně odstraňte všechny soubory z předchozího nasazení. Přetrvávání odstraněných nekompatibilní sestavení může mít za následek `System.BadImageFormatException` při nasazení upgradovaných aplikace došlo k výjimce.

## <a name="uri-endpoint-wrong-or-stopped-website"></a>Identifikátor URI koncového bodu nesprávné nebo zastavená webu

* **Prohlížeč:** ERR_CONNECTION_REFUSED **--nebo--** nelze provést připojení

* **Protokol aplikace:** Žádná položka

* **ASP.NET Core modulu stdout protokolu:** Soubor protokolu se vytvoří.

::: moniker range=">= aspnetcore-2.2"

* **Modul ASP.NET Core protokol ladění:** Soubor protokolu se vytvoří.

::: moniker-end

Řešení potíží:

* Potvrďte, že se používá na správný koncový bod identifikátoru URI pro aplikaci. Zkontrolujte vazby.

* Potvrďte, že web služby IIS není v *Zastaveno* stavu.

## <a name="corewebengine-or-w3svc-server-features-disabled"></a>Funkce serveru CoreWebEngine nebo W3SVC zakázána

**Výjimka operačního systému:** Pokud chcete použít modul ASP.NET Core je nutné nainstalovat funkce služby IIS 7.0 CoreWebEngine a W3SVC.

Řešení potíží:

Potvrďte, že jsou povolené správné role a funkce. Zobrazit [konfiguraci služby IIS](xref:host-and-deploy/iis/index#iis-configuration).

## <a name="incorrect-website-physical-path-or-app-missing"></a>Fyzická cesta k webu nesprávná nebo chybějící aplikace

* **Prohlížeč:** 403 Zakázáno - přístup byl odepřen. **--nebo--** 403.14 – zakázáno. ve webovém serveru je nakonfigurovaný na obsah tohoto adresáře v seznamu.

* **Protokol aplikace:** Žádná položka

* **ASP.NET Core modulu stdout protokolu:** Soubor protokolu se vytvoří.

::: moniker range=">= aspnetcore-2.2"

* **Modul ASP.NET Core protokol ladění:** Soubor protokolu se vytvoří.

::: moniker-end

Řešení potíží:

Zkontrolujte web služby IIS **základní nastavení** a složky fyzické aplikace. Potvrďte, že aplikace bude ve složce na webu služby IIS **fyzická cesta**.

## <a name="incorrect-role-aspnet-core-module-not-installed-or-incorrect-permissions"></a>Nesprávný role, není nainstalovaný modul ASP.NET Core nebo nesprávná oprávnění

* **Prohlížeč:** 500.19 interní chyba serveru – požadovanou stránku nelze získat přístup, protože data související konfigurační stránky jsou neplatná. **--NEBO--** tuto stránku nelze zobrazit.

* **Protokol aplikace:** Žádná položka

* **ASP.NET Core modulu stdout protokolu:** Soubor protokolu se vytvoří.

::: moniker range=">= aspnetcore-2.2"

* **Modul ASP.NET Core protokol ladění:** Soubor protokolu se vytvoří.

::: moniker-end

Řešení potíží:

* Potvrďte, že je povoleno správné roli. Zobrazit [konfiguraci služby IIS](xref:host-and-deploy/iis/index#iis-configuration).

* Otevřít **programy a funkce** nebo **aplikace a funkce** a ujistěte se, že **Windows serveru, který hostuje** je nainstalována. Pokud **Windows serveru, který hostuje** není k dispozici v seznamu nainstalovaných programů, stahování a instalace sady .NET Core hostování.

  [Aktuální instalační program sady hostování rozhraní .NET Core (s přímým přístupem ke stažení)](https://www.microsoft.com/net/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  Další informace najdete v tématu [instalaci sady .NET Core hostování](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).

* Ujistěte se, že **fond aplikací** > **Model procesu** > **Identity** je nastavena na **ApplicationPoolIdentity** nebo vlastní identita má správná oprávnění pro přístup ke složce pro nasazení aplikace.

## <a name="incorrect-processpath-missing-path-variable-hosting-bundle-not-installed-systemiis-not-restarted-vc-redistributable-not-installed-or-dotnetexe-access-violation"></a>Nesprávné processPath, chybějící proměnné PATH, hostování sady není nainstalovaný, není restartování systému/IIS, VC ++ Redistributable není nainstalovaný nebo dotnet.exe narušení přístupu

::: moniker range=">= aspnetcore-2.2"

* **Prohlížeč:** Chyba protokolu HTTP 500.0 - ANCM v procesu selhání načtení obslužné rutiny

* **Protokol aplikace:** Aplikace "MACHINE/WEBROOT nebo APPHOST / {sestavení}' s kořenem fyzické ' C:\{CESTU}\' Nepodařilo se spustit proces pomocí příkazového řádku '"{...}" ", Kód chyby =" 0x80070002: 0. Aplikace {PATH} nebylo možné spustit. Spustitelný soubor se nenašel v {PATH}. Nepovedlo se spustit aplikaci "/ LM/W3SVC/2/ROOT", '0x8007023e' kód chyby.

* **ASP.NET Core modulu stdout protokolu:** Soubor protokolu se vytvoří.

* **Modul ASP.NET Core protokol ladění:** Protokol událostí: "Aplikace {PATH} nebylo možné spustit. Spustitelný soubor se nenašel v {PATH}. Vrátí selhání HRESULT: 0x8007023e

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* **Prohlížeč:** Chyba protokolu HTTP 502.5 – selhání procesu

* **Protokol aplikace:** Aplikace "MACHINE/WEBROOT nebo APPHOST / {sestavení}' s kořenem fyzické ' C:\{CESTU}\' Nepodařilo se spustit proces pomocí příkazového řádku '"{...}" ", Kód chyby =" 0x80070002: 0.

* **ASP.NET Core modulu stdout protokolu:** Soubor protokolu je vytvořený, ale prázdný.

::: moniker-end

Řešení potíží:

* Potvrďte, že aplikace běží místně na Kestrel. Selhání procesu může být výsledkem problému v rámci aplikace. Další informace najdete v tématu [řešení (IIS)](xref:host-and-deploy/iis/troubleshoot) nebo [řešení potíží (Azure App Service)](xref:host-and-deploy/azure-apps/troubleshoot).

* Zkontrolujte *processPath* atribut na `<aspNetCore>` element v *web.config* potvrďte, že je `dotnet` nasazení závisí na architektuře (chyba) nebo `.\{ASSEMBLY}.exe` pro [samostatná nasazení (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).

* Pro disketové jednotky *dotnet.exe* nemusí být přístupné přes nastavení cesty. Ujistěte se, že * C:\Program Files\dotnet\* existuje v systémové CESTĚ nastavení.

* Pro disketové jednotky *dotnet.exe* nemusí být dostupný pro identitu uživatele fondu aplikací. Potvrďte, že identita uživatele fondu aplikací má přístup k *C:\Program Files\dotnet* adresáře. Ověřte, zda jsou nakonfigurovaná pro identitu uživatele fondu aplikací na žádná pravidla Odepřít *C:\Program Files\dotnet* a adresáře aplikace.

* Disketové jednotky, byla nasazena a .NET Core instalaci bez nutnosti restartování služby IIS. Restartujte server nebo restartování služby IIS pomocí provádí **net stop byla /y** následovaný **net start w3svc** z příkazového řádku.

* Disketové jednotky je mohou nasadit bez nutnosti instalace modulu runtime .NET Core v hostitelském systému. Pokud nebyl nainstalován modul runtime .NET Core, spusťte **instalační program sady hostování rozhraní .NET Core** v systému.

  [Aktuální instalační program sady hostování rozhraní .NET Core (s přímým přístupem ke stažení)](https://www.microsoft.com/net/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  Další informace najdete v tématu [instalaci sady .NET Core hostování](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).

  Pokud konkrétní modul runtime je vyžadována, stáhněte si modul runtime z [archivy stáhnout .NET](https://dotnet.microsoft.com/download/archives) a nainstalujte ho na systém. Dokončete instalaci restartováním systému nebo restartování služby IIS pomocí provádí **net stop byla /y** následovaný **net start w3svc** z příkazového řádku.

* Disketové jednotky, byla nasazena a *Microsoft Visual C++ 2015 Redistributable (x64)* není nainstalovaná v systému. Získat z instalačního programu [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53840).

## <a name="incorrect-arguments-of-aspnetcore-element"></a>Nesprávné argumenty \<aspNetCore > – element

::: moniker range=">= aspnetcore-2.2"

* **Prohlížeč:** Chyba protokolu HTTP 500.0 - ANCM v procesu selhání načtení obslužné rutiny

* **Protokol aplikace:** Vyvolání hostfxr najít obslužné rutiny inprocess požadavku se nezdařilo bez hledání všechny nativní závislosti. To pravděpodobně znamená, že aplikace není správně nakonfigurovaný, Zkontrolujte prosím verze balíčky Microsoft.NetCore.App a Microsoft.AspNetCore.App, které jsou cílem aplikace a jsou nainstalovány v počítači. Nelze najít inprocess žádost o obslužnou rutinu. Vyvolání hostfxr zachycené výstup: Nechtěli jste dotnet spustit příkazy SDK? Nainstalujte dotnet SDK ze: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 Nepovedlo se spustit aplikaci "/ LM/W3SVC/3/ROOT", '0x8000ffff' kód chyby.

* **ASP.NET Core modulu stdout protokolu:** Nechtěli jste dotnet spustit příkazy SDK? Nainstalujte dotnet SDK ze: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409

* **Modul ASP.NET Core protokol ladění:** Vyvolání hostfxr najít obslužné rutiny inprocess požadavku se nezdařilo bez hledání všechny nativní závislosti. To pravděpodobně znamená, že aplikace není správně nakonfigurovaný, Zkontrolujte prosím verze balíčky Microsoft.NetCore.App a Microsoft.AspNetCore.App, které jsou cílem aplikace a jsou nainstalovány v počítači. Vrátí selhání HRESULT: 0x8000ffff nelze najít inprocess žádost o obslužnou rutinu. Vyvolání hostfxr zachycené výstup: Nechtěli jste dotnet spustit příkazy SDK? Nainstalujte dotnet SDK ze: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 Vrátí selhání HRESULT: 0x8000ffff

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* **Prohlížeč:** Chyba protokolu HTTP 502.5 – selhání procesu

* **Protokol aplikace:** Aplikace "MACHINE/WEBROOT nebo APPHOST / {sestavení}' s kořenem fyzické ' C:\{CESTU}\' Nepodařilo se spustit proces pomocí příkazového řádku" "dotnet".\{ .Dll sestavení}', kód chyby = "0x80004005: 80008081.

* **ASP.NET Core modulu stdout protokolu:** Aplikace pro spuštění neexistuje: 'PATH\{ASSEMBLY}.dll'

::: moniker-end

Řešení potíží:

* Potvrďte, že aplikace běží místně na Kestrel. Selhání procesu může být výsledkem problému v rámci aplikace. Další informace najdete v tématu [řešení (IIS)](xref:host-and-deploy/iis/troubleshoot) nebo [řešení potíží (Azure App Service)](xref:host-and-deploy/azure-apps/troubleshoot).

* Zkontrolujte *argumenty* atribut na `<aspNetCore>` element v *web.config* potvrďte, že je buď (a) `.\{ASSEMBLY}.dll` nasazení závisí na architektuře (chyba); nebo (b) není k dispozici, prázdný řetězec (`arguments=""`), nebo seznam argumentů aplikace (`arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"`) pro samostatné nasazení (SCD).

::: moniker range=">= aspnetcore-2.2"

## <a name="missing-net-core-shared-framework"></a>Chybí sdílené architektuře .NET Core

* **Prohlížeč:** Chyba protokolu HTTP 500.0 - ANCM v procesu selhání načtení obslužné rutiny

* **Protokol aplikace:** Vyvolání hostfxr najít obslužné rutiny inprocess požadavku se nezdařilo bez hledání všechny nativní závislosti. To pravděpodobně znamená, že aplikace není správně nakonfigurovaný, Zkontrolujte prosím verze balíčky Microsoft.NetCore.App a Microsoft.AspNetCore.App, které jsou cílem aplikace a jsou nainstalovány v počítači. Nelze najít inprocess žádost o obslužnou rutinu. Vyvolání hostfxr zachycené výstup: Nebylo možné najít žádné architektura kompatibilní verzi. Zadané rozhraní "Microsoft.AspNetCore.App", verze {VERSION} nebyl nalezen.

Nepovedlo se spustit aplikaci "/ LM/W3SVC/5/ROOT", '0x8000ffff' kód chyby.

* **ASP.NET Core modulu stdout protokolu:** Nebylo možné najít žádné architektura kompatibilní verzi. Zadané rozhraní "Microsoft.AspNetCore.App", verze {VERSION} nebyl nalezen.

* **Modul ASP.NET Core protokol ladění:** Vrátí selhání HRESULT: 0x8000ffff

::: moniker-end

Řešení potíží:

Nasazení závisí na architektuře (chyba) potvrďte, že v systému nainstalované správné modulu runtime.

## <a name="stopped-application-pool"></a>Zastavený fond aplikací

* **Prohlížeč:** 503 – nedostupná služba

* **Protokol aplikace:** Žádná položka

* **ASP.NET Core modulu stdout protokolu:** Soubor protokolu se vytvoří.

::: moniker range=">= aspnetcore-2.2"

* **Modul ASP.NET Core protokol ladění:** Soubor protokolu se vytvoří.

::: moniker-end

Řešení potíží:

Potvrďte, že se fond aplikací není v *Zastaveno* stavu.

## <a name="sub-application-includes-a-handlers-section"></a>Zahrnuje dílčí aplikace \<obslužné rutiny > části

* **Prohlížeč:** Chyba protokolu HTTP 500.19 – vnitřní chyba serveru

* **Protokol aplikace:** Žádná položka

* **ASP.NET Core modulu stdout protokolu:** Aplikace kořenový soubor protokolu se vytvoří a zobrazí normálního provozu. Soubor protokolu sub – aplikace se nevytvoří.

::: moniker range=">= aspnetcore-2.2"

* **Modul ASP.NET Core protokol ladění:** Aplikace kořenový soubor protokolu se vytvoří a zobrazí normálního provozu. Soubor protokolu sub – aplikace se nevytvoří.

::: moniker-end

Řešení potíží:

::: moniker range=">= aspnetcore-2.2"

Ujistěte se, že sub aplikace *web.config* soubor neobsahuje `<handlers>` části nebo že podřízeným aplikacím nedědí obslužné rutiny nadřazená aplikace.

Nadřazená aplikace `<system.webServer>` část *web.config* je umístěn uvnitř `<location>` elementu. <xref:System.Configuration.SectionInformation.InheritInChildApplications*> Je nastavena na `false` k označení, že nastavení zadané v rámci [ \<umístění >](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element nedědí od neznámých aplikací, které jsou umístěny v podadresáři nadřazená aplikace. Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module>.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Ujistěte se, že sub aplikace *web.config* soubor neobsahuje `<handlers>` oddílu.

::: moniker-end

## <a name="stdout-log-path-incorrect"></a>Nesprávná cesta protokolu STDOUT

* **Prohlížeč:** Obvykle reakce aplikace.

::: moniker range=">= aspnetcore-2.2"

* **Protokol aplikace:** Přesměrování stdout do C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll nelze spustit. Zpráva o výjimce: HRESULT 0x80070005 vrácené na {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84. Přesměrování stdout do C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll nelze zastavit. Zpráva o výjimce: Hodnota HRESULT 0x80070002 vrátil v umístění {cesta}. Nebylo možné spustit stdout přesměrování {PATH}\aspnetcorev2_inprocess.dll.

* **ASP.NET Core modulu stdout protokolu:** Soubor protokolu se vytvoří.

* **Modul ASP.NET Core protokol ladění:** Přesměrování stdout do C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll nelze spustit. Zpráva o výjimce: HRESULT 0x80070005 vrácené na {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84. Přesměrování stdout do C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll nelze zastavit. Zpráva o výjimce: Hodnota HRESULT 0x80070002 vrátil v umístění {cesta}. Nebylo možné spustit stdout přesměrování {PATH}\aspnetcorev2_inprocess.dll.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* **Protokol aplikace:** Upozornění: Nepovedlo se vytvořit stdoutLogFile \\?\{ Cesta} \path_doesnt_exist\stdout_ {ID procesu} _ {časové razítko} .log, kód chyby =-2147024893.

* **ASP.NET Core modulu stdout protokolu:** Soubor protokolu se vytvoří.

::: moniker-end

Řešení potíží:

* `stdoutLogFile` Cestě zadané v `<aspNetCore>` prvek *web.config* neexistuje. Další informace najdete v tématu [modul ASP.NET Core: Vytvoření a přesměrování protokolu](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection).

* Uživatele fondu aplikací nemá oprávnění k zápisu do stdout cesta k protokolu.

## <a name="application-configuration-general-issue"></a>Hlavní problém s konfigurací aplikace

::: moniker range=">= aspnetcore-2.2"

* **Prohlížeč:** Chyba protokolu HTTP 500.0 - ANCM v procesu obslužná rutina chyby načítání **--nebo--** Chyba protokolu HTTP 500.30 - ANCM selhání spuštění v procesu

* **Protokol aplikace:** Proměnná

* **ASP.NET Core modulu stdout protokolu:** Soubor protokolu je vytvořený, ale prázdný nebo vytvořena s normální položky až do bodu selhání aplikace.

* **Modul ASP.NET Core protokol ladění:** Proměnná

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* **Prohlížeč:** Chyba protokolu HTTP 502.5 – selhání procesu

* **Protokol aplikace:** Aplikace "MACHINE/WEBROOT nebo APPHOST / {sestavení}' s kořenem fyzické ' C:\{CESTU}\' procesu vytvořené pomocí příkazového řádku" "C:\{CESTU}\{sestavení}. { soubor EXE | knihovny dll} "" ale došlo k chybě nebo neodpověděl nebo není naslouchat na daný port: {PORT}', kód chyby = {Chyba CODE}

* **ASP.NET Core modulu stdout protokolu:** Soubor protokolu je vytvořený, ale prázdný.

::: moniker-end

Řešení potíží:

Proces se nepodařilo spustit, pravděpodobně kvůli konfiguraci aplikace nebo programovací problém.

Další informace naleznete v následujících tématech:

* <xref:host-and-deploy/iis/troubleshoot>
* <xref:host-and-deploy/azure-apps/troubleshoot>
* <xref:test/troubleshoot>

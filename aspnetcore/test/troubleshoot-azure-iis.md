---
title: Řešení potíží s ASP.NET Core v Azure App Service a IIS
author: rick-anderson
description: Naučte se diagnostikovat problémy s Azure App Service a Internetová informační služba nasazení ASP.NET Core aplikací (IIS).
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: test/troubleshoot-azure-iis
ms.openlocfilehash: 7b82947c40d8e3fb46042ee5a3a32af9126623c9
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020025"
---
# <a name="troubleshoot-aspnet-core-on-azure-app-service-and-iis"></a>Řešení potíží s ASP.NET Core v Azure App Service a IIS

Od [Justin Kotalik](https://github.com/jkotalik)

::: moniker range=">= aspnetcore-3.0"

Tento článek poskytuje informace o běžných chybách při spuštění aplikací a pokyny k diagnostice chyb při nasazení aplikace do Azure App Service nebo IIS:

[Chyby při spuštění aplikace](#app-startup-errors)  
Vysvětluje běžné spouštění scénářů stavového kódu HTTP.

[Řešení potíží na Azure App Service](#troubleshoot-on-azure-app-service)  
Poskytuje rady pro řešení potíží pro aplikace nasazené do Azure App Service.

[Odstraňování potíží ve službě IIS](#troubleshoot-on-iis)  
Poskytuje rady pro řešení potíží pro aplikace nasazené do služby IIS nebo místně spuštěné v IIS Express. Pokyny platí pro nasazení systémů Windows Server a Desktop v systému Windows.

[Vymazat mezipaměti balíčků](#clear-package-caches)  
Vysvětluje, co dělat v případě, že při provádění podstatných upgradů nebo změně verze balíčku dojde k přerušení aplikace v nesouvislých balíčcích.

[Další materiály](#additional-resources)  
Seznam dalších témat pro odstraňování potíží.

## <a name="app-startup-errors"></a>Chyby při spuštění aplikace

V aplikaci Visual Studio je výchozím nastavením projektu ASP.NET Core [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hostování během ladění. *502,5 – selhání procesu* nebo *500,30 – chyba* , ke které dojde při místním ladění, se dá diagnostikovat pomocí Rady v tomto tématu.

### <a name="40314-forbidden"></a>403,14 zakázané

Aplikaci se nepodařilo spustit. Protokoluje se následující chyba:

```
The Web server is configured to not list the contents of this directory.
```

K této chybě obvykle dochází v důsledku poškozeného nasazení v hostitelském systému, který zahrnuje kterýkoli z následujících scénářů:

* Aplikace je nasazená do chybné složky v hostitelském systému.
* Procesu nasazení se nepodařilo přesunout všechny soubory a složky aplikace do složky pro nasazení v hostitelském systému.
* V nasazení chybí soubor *web.config* , nebo je obsah souboru *web.config* poškozený.

Proveďte tyto kroky:

1. Odstraňte všechny soubory a složky ze složky pro nasazení v hostitelském systému.
1. Znovu nasaďte obsah složky pro *publikování* aplikace do hostitelského systému pomocí běžné metody nasazení, jako je například Visual Studio, PowerShell nebo ruční nasazení:
   * Potvrďte, že se soubor *web.config* v nasazení nachází a že jeho obsah je správný.
   * Při hostování na Azure App Service potvrďte, že je aplikace nasazená do `D:\home\site\wwwroot` složky.
   * Když je aplikace hostovaná službou IIS, zkontrolujte, že je aplikace nasazená na **fyzickou cestu** služby IIS, která se zobrazuje v **základním nastavení** **Správce služby IIS**.
1. Ověřte, zda jsou všechny soubory a složky aplikace nasazeny porovnáním nasazení v hostitelském systému s obsahem složky *publikování* projektu.

Další informace o rozložení publikované aplikace ASP.NET Core najdete v tématu <xref:host-and-deploy/directory-structure> . Další informace o souboru *web.config* naleznete v tématu <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig> .

### <a name="500-internal-server-error"></a>500 – Vnitřní chyba serveru

Aplikace se spustí, ale chyba brání serveru v splnění žádosti.

K této chybě dochází v kódu aplikace během spuštění nebo při vytváření odpovědi. Odpověď možná neobsahuje žádný obsah, nebo se odpověď může zobrazit jako *500 interní chyba serveru* v prohlížeči. Protokol událostí aplikace obvykle uvádí, že aplikace začala normálně. Z hlediska serveru je to správné. Aplikace se spustila, ale nemůže vygenerovat platnou odpověď. Spusťte aplikaci na příkazovém řádku na serveru nebo povolte řešení potíží pomocí protokolu stdout modulu ASP.NET Core.

### <a name="5000-in-process-handler-load-failure"></a>500,0 selhání načtení obslužné rutiny v procesu

Pracovní proces se nezdařil. Aplikace se nespustí.

Při načítání komponent [modulu ASP.NET Core](xref:host-and-deploy/aspnet-core-module) došlo k neznámé chybě. Proveďte jednu z následujících akcí:

* Kontaktujte [Podpora Microsoftu](https://support.microsoft.com/oas/default.aspx?prid=15832) (vyberte **vývojářské nástroje** potom **ASP.NET Core**).
* Položte otázku na Stack Overflow.
* Vydejte problém do našeho [úložiště GitHub](https://github.com/dotnet/AspNetCore).

### <a name="50030-in-process-startup-failure"></a>500,30 selhání při spuštění v procesu

Pracovní proces se nezdařil. Aplikace se nespustí.

[Modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) se pokusí spustit modul CLR .NET Core v procesu, ale jeho spuštění se nezdařilo. Příčinou selhání spuštění procesu může být obvykle určení z položek protokolu událostí aplikace a protokolu stdout modulu ASP.NET Core.

Běžné podmínky selhání:

* Aplikace je nesprávně nakonfigurovaná kvůli cílení na verzi ASP.NET Core sdílené rozhraní, které není k dispozici. Ověřte, které verze ASP.NET Core sdílené rozhraní jsou nainstalovány v cílovém počítači.
* Při použití Azure Key Vault chybí oprávnění Key Vault. Zkontrolujte zásady přístupu v cílovém Key Vault, abyste měli jistotu, že jsou udělená správná oprávnění.

### <a name="50031-ancm-failed-to-find-native-dependencies"></a>500,31 ANCM nepovedlo se najít nativní závislosti.

Pracovní proces se nezdařil. Aplikace se nespustí.

[Modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) se pokusí spustit modul runtime .NET Core v procesu, ale jeho spuštění se nezdařilo. Nejběžnější příčinou tohoto selhání při spuštění je, že `Microsoft.NETCore.App` `Microsoft.AspNetCore.App` modul runtime nebo není nainstalován. Pokud je aplikace nasazená na cílovou ASP.NET Core 3,0 a tato verze v počítači neexistuje, dojde k této chybě. Následuje příklad chybové zprávy:

```
The specified framework 'Microsoft.NETCore.App', version '3.0.0' was not found.
  - The following frameworks were found:
      2.2.1 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview5-27626-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27713-13 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27714-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27723-08 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
```

Chybová zpráva obsahuje seznam všech nainstalovaných verzí rozhraní .NET Core a verze, kterou aplikace požaduje. Chcete-li tuto chybu vyřešit, postupujte takto:

* Na počítač nainstalujte odpovídající verzi .NET Core.
* Změňte aplikaci na cílovou verzi .NET Core, která je na počítači přítomná.
* Publikujte aplikaci jako samostatné [nasazení](/dotnet/core/deploying/#self-contained-deployments-scd).

Při spuštění ve vývoji ( `ASPNETCORE_ENVIRONMENT` Proměnná prostředí je nastavená na hodnotu `Development` ) se konkrétní chyba zapíše do odpovědi HTTP. Příčinu selhání spuštění procesu se nachází také v protokolu událostí aplikace.

### <a name="50032-ancm-failed-to-load-dll"></a>500,32 ANCM načtení knihovny DLL se nezdařilo

Pracovní proces se nezdařil. Aplikace se nespustí.

Nejběžnější příčinou této chyby je, že aplikace je publikovaná pro nekompatibilní architekturu procesoru. Pokud je pracovní proces spuštěn jako 32 aplikace a aplikace byla publikována na cílovou 64-bit, dojde k této chybě.

Chcete-li tuto chybu vyřešit, postupujte takto:

* Znovu publikujte aplikaci pro stejnou architekturu procesoru jako pracovní proces.
* Publikování aplikace jako [nasazení závislého na rozhraní](/dotnet/core/deploying/#framework-dependent-executables-fde).

### <a name="50033-ancm-request-handler-load-failure"></a>500,33 selhání načtení obslužné rutiny žádosti ANCM

Pracovní proces se nezdařil. Aplikace se nespustí.

Aplikace neodkazovala na `Microsoft.AspNetCore.App` rozhraní. `Microsoft.AspNetCore.App` [Modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module)může hostovat jenom aplikace cílené na rozhraní.

Chcete-li tuto chybu opravit, zkontrolujte, zda je aplikace cílena na `Microsoft.AspNetCore.App` rozhraní. Zkontrolujte, `.runtimeconfig.json` jestli chcete ověřit architekturu, na kterou aplikace cílí.

### <a name="50034-ancm-mixed-hosting-models-not-supported"></a>500,34 ANCM smíšené modely hostování se nepodporují.

Pracovní proces nemůže v jednom procesu spustit jak aplikaci v rámci procesu, tak i aplikaci mimo proces.

Chcete-li tuto chybu opravit, spusťte aplikace v samostatných fondech aplikací služby IIS.

### <a name="50035-ancm-multiple-in-process-applications-in-same-process"></a>500,35 ANCM více vnitroprocesové aplikací ve stejném procesu

Pracovní proces nemůže v jednom procesu spustit více vnitroprocesové aplikací.

Chcete-li tuto chybu opravit, spusťte aplikace v samostatných fondech aplikací služby IIS.

### <a name="50036-ancm-out-of-process-handler-load-failure"></a>500,36 ANCM selhání načtení obslužné rutiny mimo proces

Obslužná rutina žádosti mimo proces, *aspnetcorev2_outofprocess.dll*, není vedle *aspnetcorev2.dllho* souboru. To označuje poškozenou instalaci [modulu ASP.NET Core](xref:host-and-deploy/aspnet-core-module).

Chcete-li tuto chybu opravit, opravte instalaci [hostující sady .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (pro IIS) nebo Visual Studio (pro IIS Express).

### <a name="50037-ancm-failed-to-start-within-startup-time-limit"></a>500,37 ANCM se nepodařilo spustit v časovém limitu spuštění.

ANCM se nepovedlo spustit v rámci časového limitu spuštění nechte. Ve výchozím nastavení je časový limit 120 sekund.

K této chybě může dojít, když ve stejném počítači spustíte velký počet aplikací. Kontrolovat špičky využití procesoru a paměti na serveru při spuštění. Možná budete muset rozložit proces spouštění více aplikací.

### <a name="50038-ancm-application-dll-not-found"></a>500,38 knihovna DLL aplikace ANCM nebyla nalezena.

ANCM se nepodařilo najít knihovnu DLL aplikace, která by měla být vedle spustitelného souboru.

K této chybě dochází, když hostuje aplikaci zabalenou jako spustitelný soubor s [jedním souborem](/dotnet/core/whats-new/dotnet-core-3-0#single-file-executables) pomocí modelu hostování v rámci procesu. Model in-Process vyžaduje, aby ANCM do existujícího procesu IIS načetl aplikaci .NET Core. Tento scénář není podporován modelem nasazení s jedním souborem. Tuto chybu můžete vyřešit pomocí **jednoho** z následujících přístupů v souboru projektu aplikace:

1. Zakažte publikování jedním souborem nastavením `PublishSingleFile` vlastnosti MSBuild na `false` .
1. Přepněte do nezpracovaného modelu hostování nastavením `AspNetCoreHostingModel` vlastnosti MSBuild na `OutOfProcess` .

### <a name="5025-process-failure"></a>502.5 – Selhání procesu

Pracovní proces se nezdařil. Aplikace se nespustí.

[Modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) se pokusí spustit pracovní proces, ale jeho spuštění se nezdařilo. Příčinou selhání spuštění procesu může být obvykle určení z položek protokolu událostí aplikace a protokolu stdout modulu ASP.NET Core.

Obvyklou chybovou podmínkou je, že aplikace je nesprávně nakonfigurovaná kvůli cílení na verzi ASP.NET Core sdílené rozhraní, které není k dispozici. Ověřte, které verze ASP.NET Core sdílené rozhraní jsou nainstalovány v cílovém počítači. *Sdílené rozhraní* je sada sestavení (soubory *. dll* ), které jsou nainstalovány na počítači a na které odkazuje Metapackage jako `Microsoft.AspNetCore.App` . Odkaz Metapackage může určovat minimální požadovanou verzi. Další informace najdete v tématu [sdílené rozhraní](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).

Chybová stránka *selhání procesu 502,5* se vrátí v případě, že při selhání hostování nebo aplikace dojde k chybě pracovního procesu:

### <a name="failed-to-start-application-errorcode-0x800700c1"></a>Nepovedlo se spustit aplikaci (ErrorCode ' 0x800700C1 ')

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

Aplikaci se nepovedlo spustit, protože se nepovedlo načíst sestavení aplikace (*. dll*).

K této chybě dochází, pokud dojde k neshodě bitová verze mezi publikovanou aplikací a procesem W3wp/IISExpress.

Zkontrolujte, jestli je správně nastavené nastavení 32 fondu aplikací:

1. Vyberte fond aplikací v **fondech aplikací**Správce služby IIS.
1. V části **Upravit fond aplikací** na panelu **Akce** vyberte **Upřesnit nastavení** .
1. Nastavte **možnost povolit 32 – bitové aplikace**:
   * Pokud nasazujete 32 (x86) aplikaci, nastavte hodnotu na `True` .
   * Pokud nasazujete 64 aplikaci (x64), nastavte hodnotu na `False` .

Ověřte, že mezi `<Platform>` vlastností MSBuild v souboru projektu a publikovaným bitová verzeem aplikace nedochází ke konfliktu.

### <a name="connection-reset"></a>Resetování připojení

Pokud dojde k chybě po odeslání hlaviček, je příliš pozdě pro server, který odešle **500 interní chybu serveru** , když dojde k chybě. K tomu často dochází, když dojde k chybě během serializace složitých objektů pro odpověď. Tento typ chyby se zobrazí jako chyba *resetování připojení* na klientovi. [Protokolování aplikace](xref:fundamentals/logging/index) může pomoct řešit tyto typy chyb.

### <a name="default-startup-limits"></a>Výchozí omezení spouštění

[ASP.NET Core modul](xref:host-and-deploy/aspnet-core-module) je nakonfigurovaný s výchozí *startupTimeLimitou* 120 sekund. Pokud necháte výchozí hodnotu, aplikace může trvat až dvě minuty, než se modul zaznamená selhání procesu. Informace o konfiguraci modulu naleznete v tématu [atributy elementu aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).

## <a name="troubleshoot-on-azure-app-service"></a>Řešení potíží na Azure App Service

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a>Protokol událostí aplikace (Azure App Service)

Chcete-li získat přístup k protokolu událostí aplikace, použijte okno **Diagnostika a řešení problémů** v Azure Portal:

1. V Azure Portal otevřete aplikaci v **App Services**.
1. Vyberte **Diagnostikovat a řešit problémy**.
1. Vyberte **diagnostické nástroje** záhlaví.
1. V nabídce **nástroje podpory**vyberte tlačítko **události aplikace** .
1. Projděte si nejnovější chybu, kterou poskytla položka *IIS AspNetCoreModule* nebo *IIS AspNetCoreModule v2* ve **zdrojovém** sloupci.

Alternativou k použití okna **diagnostikovat a řešit problémy** je kontrola souboru protokolu událostí aplikace přímo pomocí [Kudu](https://github.com/projectkudu/kudu/wiki):

1. Otevřete **Rozšířené nástroje** v oblasti **vývojové nástroje** . Vyberte tlačítko **Přejít &rarr; ** . Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.
1. Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.
1. Otevřete složku **soubory protokolů** .
1. Vyberte ikonu tužky vedle *eventlog.xml* souboru.
1. Projděte si protokol. Posuňte se do dolní části protokolu, abyste viděli nejaktuálnější události.

### <a name="run-the-app-in-the-kudu-console"></a>Spuštění aplikace v konzole Kudu

Mnoho chyb při spuštění nevytváří užitečné informace v protokolu událostí aplikace. Tuto chybu můžete zjistit spuštěním aplikace v konzole vzdáleného spuštění [Kudu](https://github.com/projectkudu/kudu/wiki) :

1. Otevřete **Rozšířené nástroje** v oblasti **vývojové nástroje** . Vyberte tlačítko **Přejít &rarr; ** . Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.
1. Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.

#### <a name="test-a-32-bit-x86-app"></a>Testování 32 (x86) aplikace

**Aktuální verze**

1. `cd d:\home\site\wwwroot`
1. Spusťte aplikaci: 
   * Pokud aplikace je [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd):

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * Pokud je aplikace samostatná, [nasazení](/dotnet/core/deploying/#self-contained-deployments-scd):

     ```console
     {ASSEMBLY NAME}.exe
     ```

Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.

**Nasazení závislé na architektuře spuštěné ve verzi Preview**

*Vyžaduje instalaci rozšíření webu ASP.NET Core {VERSION} (x86) Runtime.*

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`( `{X.Y}` je verze modulu runtime)
1. Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.

#### <a name="test-a-64-bit-x64-app"></a>Testování 64 aplikace (x64)

**Aktuální verze**

* Pokud je aplikace [nasazením závislého na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd)64 (x64):
  1. `cd D:\Program Files\dotnet`
  1. Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`
* Pokud je aplikace samostatná, [nasazení](/dotnet/core/deploying/#self-contained-deployments-scd):
  1. `cd D:\home\site\wwwroot`
  1. Spusťte aplikaci: `{ASSEMBLY NAME}.exe`

Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.

**Nasazení závislé na architektuře spuštěné ve verzi Preview**

*Vyžaduje instalaci rozšíření webu ASP.NET Core {VERSION} (x64) Runtime.*

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`( `{X.Y}` je verze modulu runtime)
1. Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a>Protokol stdout v modulu ASP.NET Core (Azure App Service)

> [!WARNING]
> Nepovedlo se zakázat protokol stdout, což může způsobit selhání aplikace nebo serveru. Velikost souboru protokolu ani počet vytvořených souborů protokolu nijak neomezuje. K řešení problémů se spouštěním aplikací použijte pouze protokolování STDOUT.
>
> Pro obecné protokolování v aplikaci ASP.NET Core po spuštění použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly. Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).

Protokol stdout modulu ASP.NET Coree často zaznamenává užitečné chybové zprávy, které se nenašly v protokolu událostí aplikace. Postup povolení a zobrazení protokolů stdout:

1. Na webu Azure Portal přejděte do webové aplikace.
1. V okně **App Service** do vyhledávacího pole zadejte **Kudu** .
1. Vyberte **Rozšířené nástroje** > **Přejít**.
1. Vyberte **ladit konzolu > cmd**.
1. Přejít na *Web/wwwroot*
1. Vyberte ikonu tužky a upravte soubor *web.config* .
1. V `<aspNetCore />` elementu nastavte `stdoutLogEnabled="true"` a vyberte **Uložit**.

Zakažte protokolování stdout, když se dokončí odstraňování potíží nastavením `stdoutLogEnabled="false"` .

Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

### <a name="aspnet-core-module-debug-log-azure-app-service"></a>Protokol ladění modulu ASP.NET Core (Azure App Service)

Protokol ladění ASP.NET Core modulu poskytuje další, hlubší protokolování z modulu ASP.NET Core. Postup povolení a zobrazení protokolů stdout:

1. Pro povolení rozšířeného diagnostického protokolu proveďte jednu z následujících akcí:
   * Postupujte podle pokynů v [rozšířených diagnostických protokolech](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) a nakonfigurujte aplikaci pro rozšířené diagnostické protokolování. Znovu nasaďte aplikaci.
   * Přidejte `<handlerSettings>` zobrazené v [rozšířených diagnostických protokolech](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) do souboru *web.config* živé aplikace pomocí konzoly Kudu:
     1. Otevřete **Rozšířené nástroje** v oblasti **vývojové nástroje** . Vyberte tlačítko **Přejít &rarr; ** . Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.
     1. Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.
     1. Otevřete složky na cestě **lokality**  >  **wwwroot**. Upravte soubor *web.config* tak, že vyberete tlačítko tužky. Přidejte `<handlerSettings>` oddíl, jak je znázorněno v [rozšířených diagnostických protokolech](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs). Vyberte tlačítko **Uložit**.
1. Otevřete **Rozšířené nástroje** v oblasti **vývojové nástroje** . Vyberte tlačítko **Přejít &rarr; ** . Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.
1. Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.
1. Otevřete složky na cestě **lokality**  >  **wwwroot**. Pokud jste nezadali cestu k souboru *aspnetcore-Debug. log* , soubor se zobrazí v seznamu. Pokud jste zadali cestu, přejděte do umístění souboru protokolu.
1. Otevřete soubor protokolu s tlačítkem tužky vedle názvu souboru.

Zakázat protokolování ladění, pokud je dokončeno odstraňování potíží:

Chcete-li zakázat rozšířený protokol ladění, proveďte jednu z následujících akcí:

* Odeberte `<handlerSettings>` soubor z *web.config* místně a aplikaci znovu nasaďte.
* Pomocí konzoly Kudu upravte soubor *web.config* a odeberte `<handlerSettings>` oddíl. Soubor uložte.

Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.

> [!WARNING]
> Nepovedlo se zakázat protokol ladění, může způsobit selhání aplikace nebo serveru. Velikost souboru protokolu není nijak omezena. K řešení problémů se spouštěním aplikace používejte pouze protokolování ladění.
>
> Pro obecné protokolování v aplikaci ASP.NET Core po spuštění použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly. Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).

### <a name="slow-or-hanging-app-azure-app-service"></a>Pomalá nebo zavěšená aplikace (Azure App Service)

Pokud aplikace reaguje pomalu nebo přestane reagovat na žádost, přečtěte si následující články:

* [Řešení potíží s výkonem pomalých webových aplikací ve službě Azure App Service](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [K zachycení výpisu problémů s přerušovanou výjimkou nebo problémy s výkonem ve webové aplikaci Azure použijte rozšíření pro diagnostiku zhroucení serveru.](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a>Okna monitorování

Okna monitorování poskytují alternativní možnosti řešení potíží pro metody popsané dříve v tématu. Pomocí těchto oken můžete diagnostikovat chyby 500-Series.

Potvrďte, že jsou nainstalovaná rozšíření ASP.NET Core. Pokud nejsou rozšíření nainstalována, nainstalujte je ručně:

1. V části okno **vývojové nástroje** vyberte okno **rozšíření** .
1. V seznamu by se měla zobrazit **rozšíření ASP.NET Core** .
1. Pokud nejsou rozšíření nainstalovaná, vyberte tlačítko **Přidat** .
1. Ze seznamu vyberte **rozšíření ASP.NET Core** .
1. Kliknutím na **OK** přijměte právní podmínky.
1. V okně **Přidat rozšíření** vyberte **OK** .
1. Informační automaticky otevíraná zpráva indikuje, že se rozšíření úspěšně nainstalovala.

Pokud není povoleno protokolování stdout, postupujte následovně:

1. V Azure Portal v oblasti **vývojové nástroje** vyberte okno **Pokročilé nástroje** . Vyberte tlačítko **Přejít &rarr; ** . Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.
1. Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.
1. Otevřete složky **pro cestu** > **wwwroot** a posuňte se dolů, aby se soubor *web.config* v dolní části seznamu.
1. Klikněte na ikonu tužky vedle *web.config* souboru.
1. Nastavte **stdoutLogEnabled** na `true` a změňte cestu **stdoutLogFile** na: `\\?\%home%\LogFiles\stdout` .
1. Výběrem **Uložit** Uložte aktualizovaný soubor *web.config* .

Pokračovat k aktivaci protokolování diagnostiky:

1. V Azure Portal vyberte okno **diagnostické protokoly** .
1. Vyberte přepínač **zapnuto** pro **protokolování aplikace (systém souborů)** a **podrobné chybové zprávy**. V horní části okna vyberte tlačítko **Uložit** .
1. Chcete-li zahrnout trasování chybných požadavků, označované také jako protokolování událostí neúspěšných požadavků na FREB, vyberte přepínač **zapnuto** pro **trasování chybných požadavků**.
1. Vyberte okno **log Stream** , které je uvedeno hned pod oknem **diagnostické protokoly** na portálu.
1. Vytvořte žádost do aplikace.
1. V datech streamu protokolu se označuje příčina chyby.

Nezapomeňte zakázat protokolování stdout po dokončení řešení potíží.

Zobrazení protokolů pro trasování chybných požadavků (protokoly FREB):

1. Přejděte do okna **diagnostikovat a řešit problémy** v Azure Portal.
1. V oblasti **nástroje podpory** v postranním panelu vyberte **protokoly pro trasování chybných požadavků** .

V [části Povolení protokolování diagnostiky pro webové aplikace v Azure App Service tématu](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) a [Nejčastější dotazy týkající se výkonu aplikace pro Web Apps v Azure: návody zapnout trasování chybných požadavků?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) Další informace.

Další informace najdete v tématu [Povolení protokolování diagnostiky pro webové aplikace v Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).

> [!WARNING]
> Nepovedlo se zakázat protokol stdout, což může způsobit selhání aplikace nebo serveru. Velikost souboru protokolu ani počet vytvořených souborů protokolu nijak neomezuje.
>
> Pro rutinu protokolování v aplikaci ASP.NET Core použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly. Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).

## <a name="troubleshoot-on-iis"></a>Odstraňování potíží ve službě IIS

### <a name="application-event-log-iis"></a>Protokol událostí aplikace (IIS)

Přístup k protokolu událostí aplikace:

1. Otevřete nabídku Start, vyhledejte *Prohlížeč událostí*a vyberte aplikaci **Prohlížeč událostí** .
1. V **Prohlížeč událostí**otevřete uzel **protokoly systému Windows** .
1. Výběrem **aplikace** otevřete protokol událostí aplikace.
1. Vyhledá chyby spojené s chybnou aplikací. Chyby mají ve *zdrojovém* sloupci hodnotu *modulu IIS AspNetCore* nebo *modul IIS Express AspNetCore* .

### <a name="run-the-app-at-a-command-prompt"></a>Spuštění aplikace na příkazovém řádku

Mnoho chyb při spuštění nevytváří užitečné informace v protokolu událostí aplikace. Příčinu některých chyb můžete najít spuštěním aplikace na příkazovém řádku v hostitelském systému.

#### <a name="framework-dependent-deployment"></a>Nasazení závislé na rozhraní

Pokud aplikace je [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd):

1. Na příkazovém řádku přejděte do složky pro nasazení a spusťte aplikaci spuštěním sestavení aplikace pomocí *dotnet.exe*. V následujícím příkazu nahraďte název sestavení aplikace pro \<assembly_name> : `dotnet .\<assembly_name>.dll` .
1. Výstup konzoly z aplikace, který zobrazuje všechny chyby, je zapsán do okna konzoly.
1. Pokud dojde k chybám při podání žádosti do aplikace, vytvořte žádost na hostitele a port, kde Kestrel naslouchá. Pomocí výchozího hostitele a příspěvku vytvořte žádost `http://localhost:5000/` . Pokud aplikace reaguje normálně na adrese koncového bodu Kestrel, problém je pravděpodobnější v souvislosti s konfigurací hostování a méně pravděpodobným v rámci aplikace.

#### <a name="self-contained-deployment"></a>Samostatně zahrnuté nasazení

Pokud je aplikace samostatná, [nasazení](/dotnet/core/deploying/#self-contained-deployments-scd):

1. Na příkazovém řádku přejděte do složky pro nasazení a spusťte spustitelný soubor aplikace. V následujícím příkazu nahraďte název sestavení aplikace pro \<assembly_name> : `<assembly_name>.exe` .
1. Výstup konzoly z aplikace, který zobrazuje všechny chyby, je zapsán do okna konzoly.
1. Pokud dojde k chybám při podání žádosti do aplikace, vytvořte žádost na hostitele a port, kde Kestrel naslouchá. Pomocí výchozího hostitele a příspěvku vytvořte žádost `http://localhost:5000/` . Pokud aplikace reaguje normálně na adrese koncového bodu Kestrel, problém je pravděpodobnější v souvislosti s konfigurací hostování a méně pravděpodobným v rámci aplikace.

### <a name="aspnet-core-module-stdout-log-iis"></a>Protokol stdout v modulu ASP.NET Core (IIS)

Postup povolení a zobrazení protokolů stdout:

1. Přejděte do složky pro nasazení webu v hostitelském systému.
1. Pokud složka *logs* není k dispozici, vytvořte složku. Pokyny k tomu, jak povolit MSBuild pro vytvoření složky *logs* v nasazení automaticky, najdete v tématu [Struktura adresáře](xref:host-and-deploy/directory-structure) .
1. Upravte soubor *web.config* . Nastavte **stdoutLogEnabled** na `true` a změňte cestu **stdoutLogFile** tak, aby odkazovala na složku *logs* (například `.\logs\stdout` ). `stdout`v cestě je předpona názvu souboru protokolu. Časové razítko, ID procesu a Přípona souboru se automaticky přidají při vytvoření protokolu. `stdout`Jako předpona názvu souboru se používá typický soubor protokolu s názvem *stdout_20180205184032_5412. log*.
1. Zajistěte, aby identita fondu aplikací měla oprávnění k zápisu do složky *logs* .
1. Uložte aktualizovaný soubor *web.config* .
1. Vytvořte žádost do aplikace.
1. Přejděte do složky *logs* . Vyhledejte a otevřete nejnovější protokol STDOUT.
1. Prostudujte si protokol o chybách.

Zakázat protokolování stdout při odstraňování potíží:

1. Upravte soubor *web.config* .
1. Nastavte **stdoutLogEnabled** na `false` .
1. Soubor uložte.

Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

> [!WARNING]
> Nepovedlo se zakázat protokol stdout, což může způsobit selhání aplikace nebo serveru. Velikost souboru protokolu ani počet vytvořených souborů protokolu nijak neomezuje.
>
> Pro rutinu protokolování v aplikaci ASP.NET Core použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly. Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).

### <a name="aspnet-core-module-debug-log-iis"></a>Protokol ladění modulu ASP.NET Core (IIS)

Do souboru *web.config* aplikace přidejte následující nastavení obslužných rutin, aby se povolil protokol ladění ASP.NET Core modulu:

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

Potvrďte, že zadaná cesta k protokolu existuje a že má identita fondu aplikací oprávnění k zápisu do tohoto umístění.

Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.

### <a name="enable-the-developer-exception-page"></a>Povolit stránku výjimky pro vývojáře

`ASPNETCORE_ENVIRONMENT` [Proměnnou prostředí lze přidat do web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) ke spuštění aplikace ve vývojovém prostředí. Pokud se prostředí nepřepisuje při spuštění aplikace v `UseEnvironment` tvůrci hostitele, nastavení proměnné prostředí umožní, aby se při spuštění aplikace zobrazila [Stránka s výjimkou vývojářů](xref:fundamentals/error-handling) .

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

Nastavení proměnné prostředí pro `ASPNETCORE_ENVIRONMENT` se doporučuje jenom pro použití na pracovních a testovacích serverech, které nejsou přístupné pro Internet. Po vyřešení potíží odeberte proměnnou prostředí ze souboru *web.config* . Informace o nastavení proměnných prostředí v *web.config*naleznete v tématu [environmentVariables Child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).

### <a name="obtain-data-from-an-app"></a>Získání dat z aplikace

Pokud aplikace dokáže reagovat na žádosti, získat požadavek, připojení a další data z aplikace pomocí vloženého middlewaru terminálu. Další informace a ukázka kódu naleznete v tématu <xref:test/troubleshoot#obtain-data-from-an-app> .

### <a name="slow-or-hanging-app-iis"></a>Pomalá nebo zavěšená aplikace (IIS)

*Výpis stavu* systému je snímek paměti systému a může vám pomůže určit příčinu selhání aplikace, selhání při spuštění nebo pomalé aplikace.

#### <a name="app-crashes-or-encounters-an-exception"></a>Aplikace selže nebo dojde k výjimce.

Získat a analyzovat výpis z [zasílání zpráv o chybách systému Windows (WER)](/windows/desktop/wer/windows-error-reporting):

1. Vytvořte složku uchovávající soubory s výpisem stavu systému `c:\dumps` . Fond aplikací musí mít ke složce přístup pro zápis.
1. Spusťte [skript prostředí PowerShell pro EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):
   * Pokud aplikace používá [model hostování v rámci procesu](xref:host-and-deploy/iis/index#in-process-hosting-model), spusťte skript pro *w3wp.exe*:

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * Pokud aplikace používá [model hostování mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model), spusťte skript pro *dotnet.exe*:

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. Spusťte aplikaci za podmínek, které způsobí, že dojde k chybě.
1. Po chybě spusťte [skript PowerShellu DisableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):
   * Pokud aplikace používá [model hostování v rámci procesu](xref:host-and-deploy/iis/index#in-process-hosting-model), spusťte skript pro *w3wp.exe*:

     ```console
     .\DisableDumps w3wp.exe
     ```

   * Pokud aplikace používá [model hostování mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model), spusťte skript pro *dotnet.exe*:

     ```console
     .\DisableDumps dotnet.exe
     ```

Po selhání aplikace a dokončení shromažďování výpisu je možné aplikaci ukončit normálně. Skript PowerShellu nakonfiguruje WER a shromáždí až pět výpisů paměti pro každou aplikaci.

> [!WARNING]
> Výpisy stavu systému můžou zabírat velké množství místa na disku (každý až několik gigabajtů).

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>Aplikace přestane reagovat, dojde k chybě při spuštění nebo se spustí normálně.

Když *aplikace přestane reagovat (zastaví* se, ale nejedná se o chybu), selže během spuštění nebo se spustí normálně, podívejte [se na soubory výpisu paměti v uživatelském režimu: zvolením nejlepšího](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) nástroje vyberte vhodný nástroj pro vytváření výpisu.

#### <a name="analyze-the-dump"></a>Analýza výpisu paměti

Výpis paměti lze analyzovat pomocí několika přístupů. Další informace najdete v tématu [Analýza souboru s výpisem stavu v uživatelském režimu](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).

## <a name="clear-package-caches"></a>Vymazat mezipaměti balíčků

Funkční aplikace může po upgradu .NET Core SDK ve vývojovém počítači nebo změně verzí balíčku v rámci aplikace selhat okamžitě. V některých případech můžou nesouvislé balíčky při provádění hlavních upgradů poškodit aplikaci. Většinu těchto problémů můžete vyřešit pomocí následujících pokynů:

1. Odstraňte složky *bin* a *obj* .
1. Pomocí příkazu [dotnet All--Clear](/dotnet/core/tools/dotnet-nuget-locals) z příkazového prostředí vymažte mezipaměť balíčku.

   Mazání mezipamětí balíčků lze také provést pomocí nástroje [nuget.exe](https://www.nuget.org/downloads) a provedení příkazu `nuget locals all -clear` . *nuget.exe* není sada odinstalována s desktopovým operačním systémem Windows a je nutné ji získat samostatně z [webu NuGet](https://www.nuget.org/downloads).

1. Obnovte a znovu sestavte projekt.
1. Před opětovným nasazením aplikace odstraňte všechny soubory ve složce pro nasazení na serveru.

## <a name="additional-resources"></a>Další zdroje

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a>Dokumentace k Azure

* [Application Insights pro ASP.NET Core](/azure/application-insights/app-insights-asp-net-core)
* [Část vzdálené ladění webových aplikací řešení potíží s webovou aplikací v Azure App Service pomocí sady Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [Přehled diagnostiky Azure App Service](/azure/app-service/app-service-diagnostics)
* [Postup: Monitorování aplikací ve službě Azure App Service](/azure/app-service/web-sites-monitor)
* [Řešení potíží s webovou aplikací v Azure App Service pomocí sady Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [Řešení chyb HTTP z "502 špatné brány" a "nedostupné služby 503" v Azure Web Apps](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [Řešení potíží s výkonem pomalých webových aplikací ve službě Azure App Service](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [Nejčastější dotazy týkající se výkonu aplikací pro Web Apps v Azure](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [Sandbox webové aplikace Azure (omezení spuštění App Service Runtime)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [Azure pátek: Azure App Service diagnostické prostředí a řešení potíží (12 minut video)](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a>Dokumentace sady Visual Studio

* [Vzdálené ladění ASP.NET Core ve službě IIS v Azure v aplikaci Visual Studio 2017](/visualstudio/debugger/remote-debugging-azure)
* [Vzdálené ladění ASP.NET Core na vzdáleném počítači IIS v aplikaci Visual Studio 2017](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [Informace o ladění pomocí sady Visual Studio](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a>Dokumentace k Visual Studio Code

* [Ladění pomocí Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Tento článek poskytuje informace o běžných chybách při spuštění aplikací a pokyny k diagnostice chyb při nasazení aplikace do Azure App Service nebo IIS:

[Chyby při spuštění aplikace](#app-startup-errors)  
Vysvětluje běžné spouštění scénářů stavového kódu HTTP.

[Řešení potíží na Azure App Service](#troubleshoot-on-azure-app-service)  
Poskytuje rady pro řešení potíží pro aplikace nasazené do Azure App Service.

[Odstraňování potíží ve službě IIS](#troubleshoot-on-iis)  
Poskytuje rady pro řešení potíží pro aplikace nasazené do služby IIS nebo místně spuštěné v IIS Express. Pokyny platí pro nasazení systémů Windows Server a Desktop v systému Windows.

[Vymazat mezipaměti balíčků](#clear-package-caches)  
Vysvětluje, co dělat v případě, že při provádění podstatných upgradů nebo změně verze balíčku dojde k přerušení aplikace v nesouvislých balíčcích.

[Další materiály](#additional-resources)  
Seznam dalších témat pro odstraňování potíží.

## <a name="app-startup-errors"></a>Chyby při spuštění aplikace

V aplikaci Visual Studio je výchozím nastavením projektu ASP.NET Core [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hostování během ladění. *502,5 – selhání procesu* nebo *500,30 – chyba* , ke které dojde při místním ladění, se dá diagnostikovat pomocí Rady v tomto tématu.

### <a name="40314-forbidden"></a>403,14 zakázané

Aplikaci se nepodařilo spustit. Protokoluje se následující chyba:

```
The Web server is configured to not list the contents of this directory.
```

K této chybě obvykle dochází v důsledku poškozeného nasazení v hostitelském systému, který zahrnuje kterýkoli z následujících scénářů:

* Aplikace je nasazená do chybné složky v hostitelském systému.
* Procesu nasazení se nepodařilo přesunout všechny soubory a složky aplikace do složky pro nasazení v hostitelském systému.
* V nasazení chybí soubor *web.config* , nebo je obsah souboru *web.config* poškozený.

Proveďte tyto kroky:

1. Odstraňte všechny soubory a složky ze složky pro nasazení v hostitelském systému.
1. Znovu nasaďte obsah složky pro *publikování* aplikace do hostitelského systému pomocí běžné metody nasazení, jako je například Visual Studio, PowerShell nebo ruční nasazení:
   * Potvrďte, že se soubor *web.config* v nasazení nachází a že jeho obsah je správný.
   * Při hostování na Azure App Service potvrďte, že je aplikace nasazená do `D:\home\site\wwwroot` složky.
   * Když je aplikace hostovaná službou IIS, zkontrolujte, že je aplikace nasazená na **fyzickou cestu** služby IIS, která se zobrazuje v **základním nastavení** **Správce služby IIS**.
1. Ověřte, zda jsou všechny soubory a složky aplikace nasazeny porovnáním nasazení v hostitelském systému s obsahem složky *publikování* projektu.

Další informace o rozložení publikované aplikace ASP.NET Core najdete v tématu <xref:host-and-deploy/directory-structure> . Další informace o souboru *web.config* naleznete v tématu <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig> .

### <a name="500-internal-server-error"></a>500 – Vnitřní chyba serveru

Aplikace se spustí, ale chyba brání serveru v splnění žádosti.

K této chybě dochází v kódu aplikace během spuštění nebo při vytváření odpovědi. Odpověď možná neobsahuje žádný obsah, nebo se odpověď může zobrazit jako *500 interní chyba serveru* v prohlížeči. Protokol událostí aplikace obvykle uvádí, že aplikace začala normálně. Z hlediska serveru je to správné. Aplikace se spustila, ale nemůže vygenerovat platnou odpověď. Spusťte aplikaci na příkazovém řádku na serveru nebo povolte řešení potíží pomocí protokolu stdout modulu ASP.NET Core.

### <a name="5000-in-process-handler-load-failure"></a>500,0 selhání načtení obslužné rutiny v procesu

Pracovní proces se nezdařil. Aplikace se nespustí.

[Modulu ASP.NET Core](xref:host-and-deploy/aspnet-core-module) se nepovedlo najít modul CLR .NET Core a najít obslužnou rutinu žádosti v procesu (*aspnetcorev2_inprocess.dll*). Ověřte, že:

* Aplikace cílí na balíček NuGet [Microsoft. AspNetCore. Server. IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) nebo na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).
* Verze ASP.NET Core sdílené architektury, na které je aplikace cílena, je nainstalována na cílovém počítači.

### <a name="5000-out-of-process-handler-load-failure"></a>500,0 selhání načtení obslužné rutiny mimo proces

Pracovní proces se nezdařil. Aplikace se nespustí.

[Modulu ASP.NET Core](xref:host-and-deploy/aspnet-core-module) se nepodařilo najít obslužnou rutinu žádosti mimo proces hostování. Ujistěte se, že *aspnetcorev2_outofprocess.dll* k dispozici v podsložce vedle *aspnetcorev2.dll*.

### <a name="5025-process-failure"></a>502.5 – Selhání procesu

Pracovní proces se nezdařil. Aplikace se nespustí.

[Modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) se pokusí spustit pracovní proces, ale jeho spuštění se nezdařilo. Příčinou selhání spuštění procesu může být obvykle určení z položek protokolu událostí aplikace a protokolu stdout modulu ASP.NET Core.

Obvyklou chybovou podmínkou je, že aplikace je nesprávně nakonfigurovaná kvůli cílení na verzi ASP.NET Core sdílené rozhraní, které není k dispozici. Ověřte, které verze ASP.NET Core sdílené rozhraní jsou nainstalovány v cílovém počítači. *Sdílené rozhraní* je sada sestavení (soubory *. dll* ), které jsou nainstalovány na počítači a na které odkazuje Metapackage jako `Microsoft.AspNetCore.App` . Odkaz Metapackage může určovat minimální požadovanou verzi. Další informace najdete v tématu [sdílené rozhraní](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).

Chybová stránka *selhání procesu 502,5* se vrátí v případě, že při selhání hostování nebo aplikace dojde k chybě pracovního procesu:

### <a name="failed-to-start-application-errorcode-0x800700c1"></a>Nepovedlo se spustit aplikaci (ErrorCode ' 0x800700C1 ')

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

Aplikaci se nepovedlo spustit, protože se nepovedlo načíst sestavení aplikace (*. dll*).

K této chybě dochází, pokud dojde k neshodě bitová verze mezi publikovanou aplikací a procesem W3wp/IISExpress.

Zkontrolujte, jestli je správně nastavené nastavení 32 fondu aplikací:

1. Vyberte fond aplikací v **fondech aplikací**Správce služby IIS.
1. V části **Upravit fond aplikací** na panelu **Akce** vyberte **Upřesnit nastavení** .
1. Nastavte **možnost povolit 32 – bitové aplikace**:
   * Pokud nasazujete 32 (x86) aplikaci, nastavte hodnotu na `True` .
   * Pokud nasazujete 64 aplikaci (x64), nastavte hodnotu na `False` .

Ověřte, že mezi `<Platform>` vlastností MSBuild v souboru projektu a publikovaným bitová verzeem aplikace nedochází ke konfliktu.

### <a name="connection-reset"></a>Resetování připojení

Pokud dojde k chybě po odeslání hlaviček, je příliš pozdě pro server, který odešle **500 interní chybu serveru** , když dojde k chybě. K tomu často dochází, když dojde k chybě během serializace složitých objektů pro odpověď. Tento typ chyby se zobrazí jako chyba *resetování připojení* na klientovi. [Protokolování aplikace](xref:fundamentals/logging/index) může pomoct řešit tyto typy chyb.

### <a name="default-startup-limits"></a>Výchozí omezení spouštění

[ASP.NET Core modul](xref:host-and-deploy/aspnet-core-module) je nakonfigurovaný s výchozí *startupTimeLimitou* 120 sekund. Pokud necháte výchozí hodnotu, aplikace může trvat až dvě minuty, než se modul zaznamená selhání procesu. Informace o konfiguraci modulu naleznete v tématu [atributy elementu aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).

## <a name="troubleshoot-on-azure-app-service"></a>Řešení potíží na Azure App Service

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a>Protokol událostí aplikace (Azure App Service)

Chcete-li získat přístup k protokolu událostí aplikace, použijte okno **Diagnostika a řešení problémů** v Azure Portal:

1. V Azure Portal otevřete aplikaci v **App Services**.
1. Vyberte **Diagnostikovat a řešit problémy**.
1. Vyberte **diagnostické nástroje** záhlaví.
1. V nabídce **nástroje podpory**vyberte tlačítko **události aplikace** .
1. Projděte si nejnovější chybu, kterou poskytla položka *IIS AspNetCoreModule* nebo *IIS AspNetCoreModule v2* ve **zdrojovém** sloupci.

Alternativou k použití okna **diagnostikovat a řešit problémy** je kontrola souboru protokolu událostí aplikace přímo pomocí [Kudu](https://github.com/projectkudu/kudu/wiki):

1. Otevřete **Rozšířené nástroje** v oblasti **vývojové nástroje** . Vyberte tlačítko **Přejít &rarr; ** . Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.
1. Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.
1. Otevřete složku **soubory protokolů** .
1. Vyberte ikonu tužky vedle *eventlog.xml* souboru.
1. Projděte si protokol. Posuňte se do dolní části protokolu, abyste viděli nejaktuálnější události.

### <a name="run-the-app-in-the-kudu-console"></a>Spuštění aplikace v konzole Kudu

Mnoho chyb při spuštění nevytváří užitečné informace v protokolu událostí aplikace. Tuto chybu můžete zjistit spuštěním aplikace v konzole vzdáleného spuštění [Kudu](https://github.com/projectkudu/kudu/wiki) :

1. Otevřete **Rozšířené nástroje** v oblasti **vývojové nástroje** . Vyberte tlačítko **Přejít &rarr; ** . Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.
1. Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.

#### <a name="test-a-32-bit-x86-app"></a>Testování 32 (x86) aplikace

**Aktuální verze**

1. `cd d:\home\site\wwwroot`
1. Spusťte aplikaci: 
   * Pokud aplikace je [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd):

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * Pokud je aplikace samostatná, [nasazení](/dotnet/core/deploying/#self-contained-deployments-scd):

     ```console
     {ASSEMBLY NAME}.exe
     ```

Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.

**Nasazení závislé na architektuře spuštěné ve verzi Preview**

*Vyžaduje instalaci rozšíření webu ASP.NET Core {VERSION} (x86) Runtime.*

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`( `{X.Y}` je verze modulu runtime)
1. Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.

#### <a name="test-a-64-bit-x64-app"></a>Testování 64 aplikace (x64)

**Aktuální verze**

* Pokud je aplikace [nasazením závislého na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd)64 (x64):
  1. `cd D:\Program Files\dotnet`
  1. Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`
* Pokud je aplikace samostatná, [nasazení](/dotnet/core/deploying/#self-contained-deployments-scd):
  1. `cd D:\home\site\wwwroot`
  1. Spusťte aplikaci: `{ASSEMBLY NAME}.exe`

Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.

**Nasazení závislé na architektuře spuštěné ve verzi Preview**

*Vyžaduje instalaci rozšíření webu ASP.NET Core {VERSION} (x64) Runtime.*

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`( `{X.Y}` je verze modulu runtime)
1. Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a>Protokol stdout v modulu ASP.NET Core (Azure App Service)

Protokol stdout modulu ASP.NET Coree často zaznamenává užitečné chybové zprávy, které se nenašly v protokolu událostí aplikace. Postup povolení a zobrazení protokolů stdout:

1. Přejděte do okna **diagnostikovat a řešit problémy** v Azure Portal.
1. V části **Vybrat kategorii problému**vyberte tlačítko pro **webovou aplikaci** .
1. V části **navrhovaná řešení** > **povolte přesměrování protokolu stdout**a výběrem tlačítka **otevřete konzolu Kudu a upravte Web.Config**.
1. V konzole pro **diagnostiku**Kudu otevřete složky v lokalitě s cestou **site**  >  **wwwroot**. Posuňte se dolů, aby se soubor *web.config* v dolní části seznamu.
1. Klikněte na ikonu tužky vedle *web.config* souboru.
1. Nastavte **stdoutLogEnabled** na `true` a změňte cestu **stdoutLogFile** na: `\\?\%home%\LogFiles\stdout` .
1. Výběrem **Uložit** Uložte aktualizovaný soubor *web.config* .
1. Vytvořte žádost do aplikace.
1. Vraťte se na Azure Portal. V oblasti **vývojové nástroje** vyberte okno **Pokročilé nástroje** . Vyberte tlačítko **Přejít &rarr; ** . Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.
1. Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.
1. Vyberte složku **soubory protokolů** .
1. Zkontrolujte **upravený** sloupec a vyberte ikonu tužky a upravte protokol stdout s nejnovějším datem úpravy.
1. Po otevření souboru protokolu se zobrazí chyba.

Zakázat protokolování stdout při odstraňování potíží:

1. V **diagnostické konzole**Kudu se vraťte do umístění **site**  >  **wwwroot** lokality a odhalte tak soubor *web.config* . Znovu otevřete soubor **web.config** tím, že vyberete ikonu tužky.
1. Nastavte **stdoutLogEnabled** na `false` .
1. Vyberte **Uložit** a soubor uložte.

Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

> [!WARNING]
> Nepovedlo se zakázat protokol stdout, což může způsobit selhání aplikace nebo serveru. Velikost souboru protokolu ani počet vytvořených souborů protokolu nijak neomezuje. K řešení problémů se spouštěním aplikací použijte pouze protokolování STDOUT.
>
> Pro obecné protokolování v aplikaci ASP.NET Core po spuštění použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly. Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).

### <a name="aspnet-core-module-debug-log-azure-app-service"></a>Protokol ladění modulu ASP.NET Core (Azure App Service)

Protokol ladění ASP.NET Core modulu poskytuje další, hlubší protokolování z modulu ASP.NET Core. Postup povolení a zobrazení protokolů stdout:

1. Pro povolení rozšířeného diagnostického protokolu proveďte jednu z následujících akcí:
   * Postupujte podle pokynů v [rozšířených diagnostických protokolech](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) a nakonfigurujte aplikaci pro rozšířené diagnostické protokolování. Znovu nasaďte aplikaci.
   * Přidejte `<handlerSettings>` zobrazené v [rozšířených diagnostických protokolech](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) do souboru *web.config* živé aplikace pomocí konzoly Kudu:
     1. Otevřete **Rozšířené nástroje** v oblasti **vývojové nástroje** . Vyberte tlačítko **Přejít &rarr; ** . Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.
     1. Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.
     1. Otevřete složky na cestě **lokality**  >  **wwwroot**. Upravte soubor *web.config* tak, že vyberete tlačítko tužky. Přidejte `<handlerSettings>` oddíl, jak je znázorněno v [rozšířených diagnostických protokolech](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs). Vyberte tlačítko **Uložit**.
1. Otevřete **Rozšířené nástroje** v oblasti **vývojové nástroje** . Vyberte tlačítko **Přejít &rarr; ** . Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.
1. Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.
1. Otevřete složky na cestě **lokality**  >  **wwwroot**. Pokud jste nezadali cestu k souboru *aspnetcore-Debug. log* , soubor se zobrazí v seznamu. Pokud jste zadali cestu, přejděte do umístění souboru protokolu.
1. Otevřete soubor protokolu s tlačítkem tužky vedle názvu souboru.

Zakázat protokolování ladění, pokud je dokončeno odstraňování potíží:

Chcete-li zakázat rozšířený protokol ladění, proveďte jednu z následujících akcí:

* Odeberte `<handlerSettings>` soubor z *web.config* místně a aplikaci znovu nasaďte.
* Pomocí konzoly Kudu upravte soubor *web.config* a odeberte `<handlerSettings>` oddíl. Soubor uložte.

Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.

> [!WARNING]
> Nepovedlo se zakázat protokol ladění, může způsobit selhání aplikace nebo serveru. Velikost souboru protokolu není nijak omezena. K řešení problémů se spouštěním aplikace používejte pouze protokolování ladění.
>
> Pro obecné protokolování v aplikaci ASP.NET Core po spuštění použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly. Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).

### <a name="slow-or-hanging-app-azure-app-service"></a>Pomalá nebo zavěšená aplikace (Azure App Service)

Pokud aplikace reaguje pomalu nebo přestane reagovat na žádost, přečtěte si následující články:

* [Řešení potíží s výkonem pomalých webových aplikací ve službě Azure App Service](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [K zachycení výpisu problémů s přerušovanou výjimkou nebo problémy s výkonem ve webové aplikaci Azure použijte rozšíření pro diagnostiku zhroucení serveru.](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a>Okna monitorování

Okna monitorování poskytují alternativní možnosti řešení potíží pro metody popsané dříve v tématu. Pomocí těchto oken můžete diagnostikovat chyby 500-Series.

Potvrďte, že jsou nainstalovaná rozšíření ASP.NET Core. Pokud nejsou rozšíření nainstalována, nainstalujte je ručně:

1. V části okno **vývojové nástroje** vyberte okno **rozšíření** .
1. V seznamu by se měla zobrazit **rozšíření ASP.NET Core** .
1. Pokud nejsou rozšíření nainstalovaná, vyberte tlačítko **Přidat** .
1. Ze seznamu vyberte **rozšíření ASP.NET Core** .
1. Kliknutím na **OK** přijměte právní podmínky.
1. V okně **Přidat rozšíření** vyberte **OK** .
1. Informační automaticky otevíraná zpráva indikuje, že se rozšíření úspěšně nainstalovala.

Pokud není povoleno protokolování stdout, postupujte následovně:

1. V Azure Portal v oblasti **vývojové nástroje** vyberte okno **Pokročilé nástroje** . Vyberte tlačítko **Přejít &rarr; ** . Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.
1. Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.
1. Otevřete složky **pro cestu** > **wwwroot** a posuňte se dolů, aby se soubor *web.config* v dolní části seznamu.
1. Klikněte na ikonu tužky vedle *web.config* souboru.
1. Nastavte **stdoutLogEnabled** na `true` a změňte cestu **stdoutLogFile** na: `\\?\%home%\LogFiles\stdout` .
1. Výběrem **Uložit** Uložte aktualizovaný soubor *web.config* .

Pokračovat k aktivaci protokolování diagnostiky:

1. V Azure Portal vyberte okno **diagnostické protokoly** .
1. Vyberte přepínač **zapnuto** pro **protokolování aplikace (systém souborů)** a **podrobné chybové zprávy**. V horní části okna vyberte tlačítko **Uložit** .
1. Chcete-li zahrnout trasování chybných požadavků, označované také jako protokolování událostí neúspěšných požadavků na FREB, vyberte přepínač **zapnuto** pro **trasování chybných požadavků**.
1. Vyberte okno **log Stream** , které je uvedeno hned pod oknem **diagnostické protokoly** na portálu.
1. Vytvořte žádost do aplikace.
1. V datech streamu protokolu se označuje příčina chyby.

Nezapomeňte zakázat protokolování stdout po dokončení řešení potíží.

Zobrazení protokolů pro trasování chybných požadavků (protokoly FREB):

1. Přejděte do okna **diagnostikovat a řešit problémy** v Azure Portal.
1. V oblasti **nástroje podpory** v postranním panelu vyberte **protokoly pro trasování chybných požadavků** .

V [části Povolení protokolování diagnostiky pro webové aplikace v Azure App Service tématu](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) a [Nejčastější dotazy týkající se výkonu aplikace pro Web Apps v Azure: návody zapnout trasování chybných požadavků?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) Další informace.

Další informace najdete v tématu [Povolení protokolování diagnostiky pro webové aplikace v Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).

> [!WARNING]
> Nepovedlo se zakázat protokol stdout, což může způsobit selhání aplikace nebo serveru. Velikost souboru protokolu ani počet vytvořených souborů protokolu nijak neomezuje.
>
> Pro rutinu protokolování v aplikaci ASP.NET Core použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly. Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).

## <a name="troubleshoot-on-iis"></a>Odstraňování potíží ve službě IIS

### <a name="application-event-log-iis"></a>Protokol událostí aplikace (IIS)

Přístup k protokolu událostí aplikace:

1. Otevřete nabídku Start, vyhledejte *Prohlížeč událostí*a vyberte aplikaci **Prohlížeč událostí** .
1. V **Prohlížeč událostí**otevřete uzel **protokoly systému Windows** .
1. Výběrem **aplikace** otevřete protokol událostí aplikace.
1. Vyhledá chyby spojené s chybnou aplikací. Chyby mají ve *zdrojovém* sloupci hodnotu *modulu IIS AspNetCore* nebo *modul IIS Express AspNetCore* .

### <a name="run-the-app-at-a-command-prompt"></a>Spuštění aplikace na příkazovém řádku

Mnoho chyb při spuštění nevytváří užitečné informace v protokolu událostí aplikace. Příčinu některých chyb můžete najít spuštěním aplikace na příkazovém řádku v hostitelském systému.

#### <a name="framework-dependent-deployment"></a>Nasazení závislé na rozhraní

Pokud aplikace je [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd):

1. Na příkazovém řádku přejděte do složky pro nasazení a spusťte aplikaci spuštěním sestavení aplikace pomocí *dotnet.exe*. V následujícím příkazu nahraďte název sestavení aplikace pro \<assembly_name> : `dotnet .\<assembly_name>.dll` .
1. Výstup konzoly z aplikace, který zobrazuje všechny chyby, je zapsán do okna konzoly.
1. Pokud dojde k chybám při podání žádosti do aplikace, vytvořte žádost na hostitele a port, kde Kestrel naslouchá. Pomocí výchozího hostitele a příspěvku vytvořte žádost `http://localhost:5000/` . Pokud aplikace reaguje normálně na adrese koncového bodu Kestrel, problém je pravděpodobnější v souvislosti s konfigurací hostování a méně pravděpodobným v rámci aplikace.

#### <a name="self-contained-deployment"></a>Samostatně zahrnuté nasazení

Pokud je aplikace samostatná, [nasazení](/dotnet/core/deploying/#self-contained-deployments-scd):

1. Na příkazovém řádku přejděte do složky pro nasazení a spusťte spustitelný soubor aplikace. V následujícím příkazu nahraďte název sestavení aplikace pro \<assembly_name> : `<assembly_name>.exe` .
1. Výstup konzoly z aplikace, který zobrazuje všechny chyby, je zapsán do okna konzoly.
1. Pokud dojde k chybám při podání žádosti do aplikace, vytvořte žádost na hostitele a port, kde Kestrel naslouchá. Pomocí výchozího hostitele a příspěvku vytvořte žádost `http://localhost:5000/` . Pokud aplikace reaguje normálně na adrese koncového bodu Kestrel, problém je pravděpodobnější v souvislosti s konfigurací hostování a méně pravděpodobným v rámci aplikace.

### <a name="aspnet-core-module-stdout-log-iis"></a>Protokol stdout v modulu ASP.NET Core (IIS)

Postup povolení a zobrazení protokolů stdout:

1. Přejděte do složky pro nasazení webu v hostitelském systému.
1. Pokud složka *logs* není k dispozici, vytvořte složku. Pokyny k tomu, jak povolit MSBuild pro vytvoření složky *logs* v nasazení automaticky, najdete v tématu [Struktura adresáře](xref:host-and-deploy/directory-structure) .
1. Upravte soubor *web.config* . Nastavte **stdoutLogEnabled** na `true` a změňte cestu **stdoutLogFile** tak, aby odkazovala na složku *logs* (například `.\logs\stdout` ). `stdout`v cestě je předpona názvu souboru protokolu. Časové razítko, ID procesu a Přípona souboru se automaticky přidají při vytvoření protokolu. `stdout`Jako předpona názvu souboru se používá typický soubor protokolu s názvem *stdout_20180205184032_5412. log*.
1. Zajistěte, aby identita fondu aplikací měla oprávnění k zápisu do složky *logs* .
1. Uložte aktualizovaný soubor *web.config* .
1. Vytvořte žádost do aplikace.
1. Přejděte do složky *logs* . Vyhledejte a otevřete nejnovější protokol STDOUT.
1. Prostudujte si protokol o chybách.

Zakázat protokolování stdout při odstraňování potíží:

1. Upravte soubor *web.config* .
1. Nastavte **stdoutLogEnabled** na `false` .
1. Soubor uložte.

Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

> [!WARNING]
> Nepovedlo se zakázat protokol stdout, což může způsobit selhání aplikace nebo serveru. Velikost souboru protokolu ani počet vytvořených souborů protokolu nijak neomezuje.
>
> Pro rutinu protokolování v aplikaci ASP.NET Core použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly. Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).

### <a name="aspnet-core-module-debug-log-iis"></a>Protokol ladění modulu ASP.NET Core (IIS)

Do souboru *web.config* aplikace přidejte následující nastavení obslužných rutin, aby se povolil protokol ladění ASP.NET Core modulu:

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

Potvrďte, že zadaná cesta k protokolu existuje a že má identita fondu aplikací oprávnění k zápisu do tohoto umístění.

Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.

### <a name="enable-the-developer-exception-page"></a>Povolit stránku výjimky pro vývojáře

`ASPNETCORE_ENVIRONMENT` [Proměnnou prostředí lze přidat do web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) ke spuštění aplikace ve vývojovém prostředí. Pokud se prostředí nepřepisuje při spuštění aplikace v `UseEnvironment` tvůrci hostitele, nastavení proměnné prostředí umožní, aby se při spuštění aplikace zobrazila [Stránka s výjimkou vývojářů](xref:fundamentals/error-handling) .

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

Nastavení proměnné prostředí pro `ASPNETCORE_ENVIRONMENT` se doporučuje jenom pro použití na pracovních a testovacích serverech, které nejsou přístupné pro Internet. Po vyřešení potíží odeberte proměnnou prostředí ze souboru *web.config* . Informace o nastavení proměnných prostředí v *web.config*naleznete v tématu [environmentVariables Child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).

### <a name="obtain-data-from-an-app"></a>Získání dat z aplikace

Pokud aplikace dokáže reagovat na žádosti, získat požadavek, připojení a další data z aplikace pomocí vloženého middlewaru terminálu. Další informace a ukázka kódu naleznete v tématu <xref:test/troubleshoot#obtain-data-from-an-app> .

### <a name="slow-or-hanging-app-iis"></a>Pomalá nebo zavěšená aplikace (IIS)

*Výpis stavu* systému je snímek paměti systému a může vám pomůže určit příčinu selhání aplikace, selhání při spuštění nebo pomalé aplikace.

#### <a name="app-crashes-or-encounters-an-exception"></a>Aplikace selže nebo dojde k výjimce.

Získat a analyzovat výpis z [zasílání zpráv o chybách systému Windows (WER)](/windows/desktop/wer/windows-error-reporting):

1. Vytvořte složku uchovávající soubory s výpisem stavu systému `c:\dumps` . Fond aplikací musí mít ke složce přístup pro zápis.
1. Spusťte [skript prostředí PowerShell pro EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):
   * Pokud aplikace používá [model hostování v rámci procesu](xref:host-and-deploy/iis/index#in-process-hosting-model), spusťte skript pro *w3wp.exe*:

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * Pokud aplikace používá [model hostování mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model), spusťte skript pro *dotnet.exe*:

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. Spusťte aplikaci za podmínek, které způsobí, že dojde k chybě.
1. Po chybě spusťte [skript PowerShellu DisableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):
   * Pokud aplikace používá [model hostování v rámci procesu](xref:host-and-deploy/iis/index#in-process-hosting-model), spusťte skript pro *w3wp.exe*:

     ```console
     .\DisableDumps w3wp.exe
     ```

   * Pokud aplikace používá [model hostování mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model), spusťte skript pro *dotnet.exe*:

     ```console
     .\DisableDumps dotnet.exe
     ```

Po selhání aplikace a dokončení shromažďování výpisu je možné aplikaci ukončit normálně. Skript PowerShellu nakonfiguruje WER a shromáždí až pět výpisů paměti pro každou aplikaci.

> [!WARNING]
> Výpisy stavu systému můžou zabírat velké množství místa na disku (každý až několik gigabajtů).

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>Aplikace přestane reagovat, dojde k chybě při spuštění nebo se spustí normálně.

Když *aplikace přestane reagovat (zastaví* se, ale nejedná se o chybu), selže během spuštění nebo se spustí normálně, podívejte [se na soubory výpisu paměti v uživatelském režimu: zvolením nejlepšího](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) nástroje vyberte vhodný nástroj pro vytváření výpisu.

#### <a name="analyze-the-dump"></a>Analýza výpisu paměti

Výpis paměti lze analyzovat pomocí několika přístupů. Další informace najdete v tématu [Analýza souboru s výpisem stavu v uživatelském režimu](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).

## <a name="clear-package-caches"></a>Vymazat mezipaměti balíčků

Funkční aplikace může po upgradu .NET Core SDK ve vývojovém počítači nebo změně verzí balíčku v rámci aplikace selhat okamžitě. V některých případech můžou nesouvislé balíčky při provádění hlavních upgradů poškodit aplikaci. Většinu těchto problémů můžete vyřešit pomocí následujících pokynů:

1. Odstraňte složky *bin* a *obj* .
1. Pomocí příkazu [dotnet All--Clear](/dotnet/core/tools/dotnet-nuget-locals) z příkazového prostředí vymažte mezipaměť balíčku.

   Mazání mezipamětí balíčků lze také provést pomocí nástroje [nuget.exe](https://www.nuget.org/downloads) a provedení příkazu `nuget locals all -clear` . *nuget.exe* není sada odinstalována s desktopovým operačním systémem Windows a je nutné ji získat samostatně z [webu NuGet](https://www.nuget.org/downloads).

1. Obnovte a znovu sestavte projekt.
1. Před opětovným nasazením aplikace odstraňte všechny soubory ve složce pro nasazení na serveru.

## <a name="additional-resources"></a>Další zdroje

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a>Dokumentace k Azure

* [Application Insights pro ASP.NET Core](/azure/application-insights/app-insights-asp-net-core)
* [Část vzdálené ladění webových aplikací řešení potíží s webovou aplikací v Azure App Service pomocí sady Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [Přehled diagnostiky Azure App Service](/azure/app-service/app-service-diagnostics)
* [Postup: Monitorování aplikací ve službě Azure App Service](/azure/app-service/web-sites-monitor)
* [Řešení potíží s webovou aplikací v Azure App Service pomocí sady Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [Řešení chyb HTTP z "502 špatné brány" a "nedostupné služby 503" v Azure Web Apps](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [Řešení potíží s výkonem pomalých webových aplikací ve službě Azure App Service](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [Nejčastější dotazy týkající se výkonu aplikací pro Web Apps v Azure](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [Sandbox webové aplikace Azure (omezení spuštění App Service Runtime)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [Azure pátek: Azure App Service diagnostické prostředí a řešení potíží (12 minut video)](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a>Dokumentace sady Visual Studio

* [Vzdálené ladění ASP.NET Core ve službě IIS v Azure v aplikaci Visual Studio 2017](/visualstudio/debugger/remote-debugging-azure)
* [Vzdálené ladění ASP.NET Core na vzdáleném počítači IIS v aplikaci Visual Studio 2017](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [Informace o ladění pomocí sady Visual Studio](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a>Dokumentace k Visual Studio Code

* [Ladění pomocí Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Tento článek poskytuje informace o běžných chybách při spuštění aplikací a pokyny k diagnostice chyb při nasazení aplikace do Azure App Service nebo IIS:

[Chyby při spuštění aplikace](#app-startup-errors)  
Vysvětluje běžné spouštění scénářů stavového kódu HTTP.

[Řešení potíží na Azure App Service](#troubleshoot-on-azure-app-service)  
Poskytuje rady pro řešení potíží pro aplikace nasazené do Azure App Service.

[Odstraňování potíží ve službě IIS](#troubleshoot-on-iis)  
Poskytuje rady pro řešení potíží pro aplikace nasazené do služby IIS nebo místně spuštěné v IIS Express. Pokyny platí pro nasazení systémů Windows Server a Desktop v systému Windows.

[Vymazat mezipaměti balíčků](#clear-package-caches)  
Vysvětluje, co dělat v případě, že při provádění podstatných upgradů nebo změně verze balíčku dojde k přerušení aplikace v nesouvislých balíčcích.

[Další materiály](#additional-resources)  
Seznam dalších témat pro odstraňování potíží.

## <a name="app-startup-errors"></a>Chyby při spuštění aplikace

V aplikaci Visual Studio je výchozím nastavením projektu ASP.NET Core [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hostování během ladění. *Selhání procesu 502,5* , ke kterému dochází, když se místně ladění dá diagnostikovat pomocí Rady v tomto tématu.

### <a name="40314-forbidden"></a>403,14 zakázané

Aplikaci se nepodařilo spustit. Protokoluje se následující chyba:

```
The Web server is configured to not list the contents of this directory.
```

K této chybě obvykle dochází v důsledku poškozeného nasazení v hostitelském systému, který zahrnuje kterýkoli z následujících scénářů:

* Aplikace je nasazená do chybné složky v hostitelském systému.
* Procesu nasazení se nepodařilo přesunout všechny soubory a složky aplikace do složky pro nasazení v hostitelském systému.
* V nasazení chybí soubor *web.config* , nebo je obsah souboru *web.config* poškozený.

Proveďte tyto kroky:

1. Odstraňte všechny soubory a složky ze složky pro nasazení v hostitelském systému.
1. Znovu nasaďte obsah složky pro *publikování* aplikace do hostitelského systému pomocí běžné metody nasazení, jako je například Visual Studio, PowerShell nebo ruční nasazení:
   * Potvrďte, že se soubor *web.config* v nasazení nachází a že jeho obsah je správný.
   * Při hostování na Azure App Service potvrďte, že je aplikace nasazená do `D:\home\site\wwwroot` složky.
   * Když je aplikace hostovaná službou IIS, zkontrolujte, že je aplikace nasazená na **fyzickou cestu** služby IIS, která se zobrazuje v **základním nastavení** **Správce služby IIS**.
1. Ověřte, zda jsou všechny soubory a složky aplikace nasazeny porovnáním nasazení v hostitelském systému s obsahem složky *publikování* projektu.

Další informace o rozložení publikované aplikace ASP.NET Core najdete v tématu <xref:host-and-deploy/directory-structure> . Další informace o souboru *web.config* naleznete v tématu <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig> .

### <a name="500-internal-server-error"></a>500 – Vnitřní chyba serveru

Aplikace se spustí, ale chyba brání serveru v splnění žádosti.

K této chybě dochází v kódu aplikace během spuštění nebo při vytváření odpovědi. Odpověď možná neobsahuje žádný obsah, nebo se odpověď může zobrazit jako *500 interní chyba serveru* v prohlížeči. Protokol událostí aplikace obvykle uvádí, že aplikace začala normálně. Z hlediska serveru je to správné. Aplikace se spustila, ale nemůže vygenerovat platnou odpověď. Spusťte aplikaci na příkazovém řádku na serveru nebo povolte řešení potíží pomocí protokolu stdout modulu ASP.NET Core.

### <a name="5025-process-failure"></a>502.5 – Selhání procesu

Pracovní proces se nezdařil. Aplikace se nespustí.

[Modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) se pokusí spustit pracovní proces, ale jeho spuštění se nezdařilo. Příčinou selhání spuštění procesu může být obvykle určení z položek protokolu událostí aplikace a protokolu stdout modulu ASP.NET Core.

Obvyklou chybovou podmínkou je, že aplikace je nesprávně nakonfigurovaná kvůli cílení na verzi ASP.NET Core sdílené rozhraní, které není k dispozici. Ověřte, které verze ASP.NET Core sdílené rozhraní jsou nainstalovány v cílovém počítači. *Sdílené rozhraní* je sada sestavení (soubory *. dll* ), které jsou nainstalovány na počítači a na které odkazuje Metapackage jako `Microsoft.AspNetCore.App` . Odkaz Metapackage může určovat minimální požadovanou verzi. Další informace najdete v tématu [sdílené rozhraní](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).

Chybová stránka *selhání procesu 502,5* se vrátí v případě, že při selhání hostování nebo aplikace dojde k chybě pracovního procesu:

### <a name="failed-to-start-application-errorcode-0x800700c1"></a>Nepovedlo se spustit aplikaci (ErrorCode ' 0x800700C1 ')

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

Aplikaci se nepovedlo spustit, protože se nepovedlo načíst sestavení aplikace (*. dll*).

K této chybě dochází, pokud dojde k neshodě bitová verze mezi publikovanou aplikací a procesem W3wp/IISExpress.

Zkontrolujte, jestli je správně nastavené nastavení 32 fondu aplikací:

1. Vyberte fond aplikací v **fondech aplikací**Správce služby IIS.
1. V části **Upravit fond aplikací** na panelu **Akce** vyberte **Upřesnit nastavení** .
1. Nastavte **možnost povolit 32 – bitové aplikace**:
   * Pokud nasazujete 32 (x86) aplikaci, nastavte hodnotu na `True` .
   * Pokud nasazujete 64 aplikaci (x64), nastavte hodnotu na `False` .

Ověřte, že mezi `<Platform>` vlastností MSBuild v souboru projektu a publikovaným bitová verzeem aplikace nedochází ke konfliktu.

### <a name="connection-reset"></a>Resetování připojení

Pokud dojde k chybě po odeslání hlaviček, je příliš pozdě pro server, který odešle **500 interní chybu serveru** , když dojde k chybě. K tomu často dochází, když dojde k chybě během serializace složitých objektů pro odpověď. Tento typ chyby se zobrazí jako chyba *resetování připojení* na klientovi. [Protokolování aplikace](xref:fundamentals/logging/index) může pomoct řešit tyto typy chyb.

### <a name="default-startup-limits"></a>Výchozí omezení spouštění

[ASP.NET Core modul](xref:host-and-deploy/aspnet-core-module) je nakonfigurovaný s výchozí *startupTimeLimitou* 120 sekund. Pokud necháte výchozí hodnotu, aplikace může trvat až dvě minuty, než se modul zaznamená selhání procesu. Informace o konfiguraci modulu naleznete v tématu [atributy elementu aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).

## <a name="troubleshoot-on-azure-app-service"></a>Řešení potíží na Azure App Service

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a>Protokol událostí aplikace (Azure App Service)

Chcete-li získat přístup k protokolu událostí aplikace, použijte okno **Diagnostika a řešení problémů** v Azure Portal:

1. V Azure Portal otevřete aplikaci v **App Services**.
1. Vyberte **Diagnostikovat a řešit problémy**.
1. Vyberte **diagnostické nástroje** záhlaví.
1. V nabídce **nástroje podpory**vyberte tlačítko **události aplikace** .
1. Projděte si nejnovější chybu, kterou poskytla položka *IIS AspNetCoreModule* nebo *IIS AspNetCoreModule v2* ve **zdrojovém** sloupci.

Alternativou k použití okna **diagnostikovat a řešit problémy** je kontrola souboru protokolu událostí aplikace přímo pomocí [Kudu](https://github.com/projectkudu/kudu/wiki):

1. Otevřete **Rozšířené nástroje** v oblasti **vývojové nástroje** . Vyberte tlačítko **Přejít &rarr; ** . Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.
1. Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.
1. Otevřete složku **soubory protokolů** .
1. Vyberte ikonu tužky vedle *eventlog.xml* souboru.
1. Projděte si protokol. Posuňte se do dolní části protokolu, abyste viděli nejaktuálnější události.

### <a name="run-the-app-in-the-kudu-console"></a>Spuštění aplikace v konzole Kudu

Mnoho chyb při spuštění nevytváří užitečné informace v protokolu událostí aplikace. Tuto chybu můžete zjistit spuštěním aplikace v konzole vzdáleného spuštění [Kudu](https://github.com/projectkudu/kudu/wiki) :

1. Otevřete **Rozšířené nástroje** v oblasti **vývojové nástroje** . Vyberte tlačítko **Přejít &rarr; ** . Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.
1. Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.

#### <a name="test-a-32-bit-x86-app"></a>Testování 32 (x86) aplikace

**Aktuální verze**

1. `cd d:\home\site\wwwroot`
1. Spusťte aplikaci: 
   * Pokud aplikace je [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd):

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * Pokud je aplikace samostatná, [nasazení](/dotnet/core/deploying/#self-contained-deployments-scd):

     ```console
     {ASSEMBLY NAME}.exe
     ```

Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.

**Nasazení závislé na architektuře spuštěné ve verzi Preview**

*Vyžaduje instalaci rozšíření webu ASP.NET Core {VERSION} (x86) Runtime.*

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`( `{X.Y}` je verze modulu runtime)
1. Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.

#### <a name="test-a-64-bit-x64-app"></a>Testování 64 aplikace (x64)

**Aktuální verze**

* Pokud je aplikace [nasazením závislého na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd)64 (x64):
  1. `cd D:\Program Files\dotnet`
  1. Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`
* Pokud je aplikace samostatná, [nasazení](/dotnet/core/deploying/#self-contained-deployments-scd):
  1. `cd D:\home\site\wwwroot`
  1. Spusťte aplikaci: `{ASSEMBLY NAME}.exe`

Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.

**Nasazení závislé na architektuře spuštěné ve verzi Preview**

*Vyžaduje instalaci rozšíření webu ASP.NET Core {VERSION} (x64) Runtime.*

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`( `{X.Y}` je verze modulu runtime)
1. Spusťte aplikaci: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

Výstup konzoly z aplikace, v němž se zobrazují všechny chyby, je předán do konzoly Kudu.

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a>Protokol stdout v modulu ASP.NET Core (Azure App Service)

Protokol stdout modulu ASP.NET Coree často zaznamenává užitečné chybové zprávy, které se nenašly v protokolu událostí aplikace. Postup povolení a zobrazení protokolů stdout:

1. Přejděte do okna **diagnostikovat a řešit problémy** v Azure Portal.
1. V části **Vybrat kategorii problému**vyberte tlačítko pro **webovou aplikaci** .
1. V části **navrhovaná řešení** > **povolte přesměrování protokolu stdout**a výběrem tlačítka **otevřete konzolu Kudu a upravte Web.Config**.
1. V konzole pro **diagnostiku**Kudu otevřete složky v lokalitě s cestou **site**  >  **wwwroot**. Posuňte se dolů, aby se soubor *web.config* v dolní části seznamu.
1. Klikněte na ikonu tužky vedle *web.config* souboru.
1. Nastavte **stdoutLogEnabled** na `true` a změňte cestu **stdoutLogFile** na: `\\?\%home%\LogFiles\stdout` .
1. Výběrem **Uložit** Uložte aktualizovaný soubor *web.config* .
1. Vytvořte žádost do aplikace.
1. Vraťte se na Azure Portal. V oblasti **vývojové nástroje** vyberte okno **Pokročilé nástroje** . Vyberte tlačítko **Přejít &rarr; ** . Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.
1. Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.
1. Vyberte složku **soubory protokolů** .
1. Zkontrolujte **upravený** sloupec a vyberte ikonu tužky a upravte protokol stdout s nejnovějším datem úpravy.
1. Po otevření souboru protokolu se zobrazí chyba.

Zakázat protokolování stdout při odstraňování potíží:

1. V **diagnostické konzole**Kudu se vraťte do umístění **site**  >  **wwwroot** lokality a odhalte tak soubor *web.config* . Znovu otevřete soubor **web.config** tím, že vyberete ikonu tužky.
1. Nastavte **stdoutLogEnabled** na `false` .
1. Vyberte **Uložit** a soubor uložte.

Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

> [!WARNING]
> Nepovedlo se zakázat protokol stdout, což může způsobit selhání aplikace nebo serveru. Velikost souboru protokolu ani počet vytvořených souborů protokolu nijak neomezuje. K řešení problémů se spouštěním aplikací použijte pouze protokolování STDOUT.
>
> Pro obecné protokolování v aplikaci ASP.NET Core po spuštění použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly. Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).

### <a name="slow-or-hanging-app-azure-app-service"></a>Pomalá nebo zavěšená aplikace (Azure App Service)

Pokud aplikace reaguje pomalu nebo přestane reagovat na žádost, přečtěte si následující články:

* [Řešení potíží s výkonem pomalých webových aplikací ve službě Azure App Service](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [K zachycení výpisu problémů s přerušovanou výjimkou nebo problémy s výkonem ve webové aplikaci Azure použijte rozšíření pro diagnostiku zhroucení serveru.](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a>Okna monitorování

Okna monitorování poskytují alternativní možnosti řešení potíží pro metody popsané dříve v tématu. Pomocí těchto oken můžete diagnostikovat chyby 500-Series.

Potvrďte, že jsou nainstalovaná rozšíření ASP.NET Core. Pokud nejsou rozšíření nainstalována, nainstalujte je ručně:

1. V části okno **vývojové nástroje** vyberte okno **rozšíření** .
1. V seznamu by se měla zobrazit **rozšíření ASP.NET Core** .
1. Pokud nejsou rozšíření nainstalovaná, vyberte tlačítko **Přidat** .
1. Ze seznamu vyberte **rozšíření ASP.NET Core** .
1. Kliknutím na **OK** přijměte právní podmínky.
1. V okně **Přidat rozšíření** vyberte **OK** .
1. Informační automaticky otevíraná zpráva indikuje, že se rozšíření úspěšně nainstalovala.

Pokud není povoleno protokolování stdout, postupujte následovně:

1. V Azure Portal v oblasti **vývojové nástroje** vyberte okno **Pokročilé nástroje** . Vyberte tlačítko **Přejít &rarr; ** . Konzola Kudu se otevře v novém okně nebo záložce prohlížeče.
1. Pomocí navigačního panelu v horní části stránky otevřete **konzolu ladění** a vyberte **cmd**.
1. Otevřete složky **pro cestu** > **wwwroot** a posuňte se dolů, aby se soubor *web.config* v dolní části seznamu.
1. Klikněte na ikonu tužky vedle *web.config* souboru.
1. Nastavte **stdoutLogEnabled** na `true` a změňte cestu **stdoutLogFile** na: `\\?\%home%\LogFiles\stdout` .
1. Výběrem **Uložit** Uložte aktualizovaný soubor *web.config* .

Pokračovat k aktivaci protokolování diagnostiky:

1. V Azure Portal vyberte okno **diagnostické protokoly** .
1. Vyberte přepínač **zapnuto** pro **protokolování aplikace (systém souborů)** a **podrobné chybové zprávy**. V horní části okna vyberte tlačítko **Uložit** .
1. Chcete-li zahrnout trasování chybných požadavků, označované také jako protokolování událostí neúspěšných požadavků na FREB, vyberte přepínač **zapnuto** pro **trasování chybných požadavků**.
1. Vyberte okno **log Stream** , které je uvedeno hned pod oknem **diagnostické protokoly** na portálu.
1. Vytvořte žádost do aplikace.
1. V datech streamu protokolu se označuje příčina chyby.

Nezapomeňte zakázat protokolování stdout po dokončení řešení potíží.

Zobrazení protokolů pro trasování chybných požadavků (protokoly FREB):

1. Přejděte do okna **diagnostikovat a řešit problémy** v Azure Portal.
1. V oblasti **nástroje podpory** v postranním panelu vyberte **protokoly pro trasování chybných požadavků** .

V [části Povolení protokolování diagnostiky pro webové aplikace v Azure App Service tématu](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) a [Nejčastější dotazy týkající se výkonu aplikace pro Web Apps v Azure: návody zapnout trasování chybných požadavků?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) Další informace.

Další informace najdete v tématu [Povolení protokolování diagnostiky pro webové aplikace v Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).

> [!WARNING]
> Nepovedlo se zakázat protokol stdout, což může způsobit selhání aplikace nebo serveru. Velikost souboru protokolu ani počet vytvořených souborů protokolu nijak neomezuje.
>
> Pro rutinu protokolování v aplikaci ASP.NET Core použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly. Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).

## <a name="troubleshoot-on-iis"></a>Odstraňování potíží ve službě IIS

### <a name="application-event-log-iis"></a>Protokol událostí aplikace (IIS)

Přístup k protokolu událostí aplikace:

1. Otevřete nabídku Start, vyhledejte *Prohlížeč událostí*a vyberte aplikaci **Prohlížeč událostí** .
1. V **Prohlížeč událostí**otevřete uzel **protokoly systému Windows** .
1. Výběrem **aplikace** otevřete protokol událostí aplikace.
1. Vyhledá chyby spojené s chybnou aplikací. Chyby mají ve *zdrojovém* sloupci hodnotu *modulu IIS AspNetCore* nebo *modul IIS Express AspNetCore* .

### <a name="run-the-app-at-a-command-prompt"></a>Spuštění aplikace na příkazovém řádku

Mnoho chyb při spuštění nevytváří užitečné informace v protokolu událostí aplikace. Příčinu některých chyb můžete najít spuštěním aplikace na příkazovém řádku v hostitelském systému.

#### <a name="framework-dependent-deployment"></a>Nasazení závislé na rozhraní

Pokud aplikace je [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd):

1. Na příkazovém řádku přejděte do složky pro nasazení a spusťte aplikaci spuštěním sestavení aplikace pomocí *dotnet.exe*. V následujícím příkazu nahraďte název sestavení aplikace pro \<assembly_name> : `dotnet .\<assembly_name>.dll` .
1. Výstup konzoly z aplikace, který zobrazuje všechny chyby, je zapsán do okna konzoly.
1. Pokud dojde k chybám při podání žádosti do aplikace, vytvořte žádost na hostitele a port, kde Kestrel naslouchá. Pomocí výchozího hostitele a příspěvku vytvořte žádost `http://localhost:5000/` . Pokud aplikace reaguje normálně na adrese koncového bodu Kestrel, problém je pravděpodobnější v souvislosti s konfigurací hostování a méně pravděpodobným v rámci aplikace.

#### <a name="self-contained-deployment"></a>Samostatně zahrnuté nasazení

Pokud je aplikace samostatná, [nasazení](/dotnet/core/deploying/#self-contained-deployments-scd):

1. Na příkazovém řádku přejděte do složky pro nasazení a spusťte spustitelný soubor aplikace. V následujícím příkazu nahraďte název sestavení aplikace pro \<assembly_name> : `<assembly_name>.exe` .
1. Výstup konzoly z aplikace, který zobrazuje všechny chyby, je zapsán do okna konzoly.
1. Pokud dojde k chybám při podání žádosti do aplikace, vytvořte žádost na hostitele a port, kde Kestrel naslouchá. Pomocí výchozího hostitele a příspěvku vytvořte žádost `http://localhost:5000/` . Pokud aplikace reaguje normálně na adrese koncového bodu Kestrel, problém je pravděpodobnější v souvislosti s konfigurací hostování a méně pravděpodobným v rámci aplikace.

### <a name="aspnet-core-module-stdout-log-iis"></a>Protokol stdout v modulu ASP.NET Core (IIS)

Postup povolení a zobrazení protokolů stdout:

1. Přejděte do složky pro nasazení webu v hostitelském systému.
1. Pokud složka *logs* není k dispozici, vytvořte složku. Pokyny k tomu, jak povolit MSBuild pro vytvoření složky *logs* v nasazení automaticky, najdete v tématu [Struktura adresáře](xref:host-and-deploy/directory-structure) .
1. Upravte soubor *web.config* . Nastavte **stdoutLogEnabled** na `true` a změňte cestu **stdoutLogFile** tak, aby odkazovala na složku *logs* (například `.\logs\stdout` ). `stdout`v cestě je předpona názvu souboru protokolu. Časové razítko, ID procesu a Přípona souboru se automaticky přidají při vytvoření protokolu. `stdout`Jako předpona názvu souboru se používá typický soubor protokolu s názvem *stdout_20180205184032_5412. log*.
1. Zajistěte, aby identita fondu aplikací měla oprávnění k zápisu do složky *logs* .
1. Uložte aktualizovaný soubor *web.config* .
1. Vytvořte žádost do aplikace.
1. Přejděte do složky *logs* . Vyhledejte a otevřete nejnovější protokol STDOUT.
1. Prostudujte si protokol o chybách.

Zakázat protokolování stdout při odstraňování potíží:

1. Upravte soubor *web.config* .
1. Nastavte **stdoutLogEnabled** na `false` .
1. Soubor uložte.

Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

> [!WARNING]
> Nepovedlo se zakázat protokol stdout, což může způsobit selhání aplikace nebo serveru. Velikost souboru protokolu ani počet vytvořených souborů protokolu nijak neomezuje.
>
> Pro rutinu protokolování v aplikaci ASP.NET Core použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly. Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).

### <a name="enable-the-developer-exception-page"></a>Povolit stránku výjimky pro vývojáře

`ASPNETCORE_ENVIRONMENT` [Proměnnou prostředí lze přidat do web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) ke spuštění aplikace ve vývojovém prostředí. Pokud se prostředí nepřepisuje při spuštění aplikace v `UseEnvironment` tvůrci hostitele, nastavení proměnné prostředí umožní, aby se při spuštění aplikace zobrazila [Stránka s výjimkou vývojářů](xref:fundamentals/error-handling) .

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

Nastavení proměnné prostředí pro `ASPNETCORE_ENVIRONMENT` se doporučuje jenom pro použití na pracovních a testovacích serverech, které nejsou přístupné pro Internet. Po vyřešení potíží odeberte proměnnou prostředí ze souboru *web.config* . Informace o nastavení proměnných prostředí v *web.config*naleznete v tématu [environmentVariables Child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).

### <a name="obtain-data-from-an-app"></a>Získání dat z aplikace

Pokud aplikace dokáže reagovat na žádosti, získat požadavek, připojení a další data z aplikace pomocí vloženého middlewaru terminálu. Další informace a ukázka kódu naleznete v tématu <xref:test/troubleshoot#obtain-data-from-an-app> .

### <a name="slow-or-hanging-app-iis"></a>Pomalá nebo zavěšená aplikace (IIS)

*Výpis stavu* systému je snímek paměti systému a může vám pomůže určit příčinu selhání aplikace, selhání při spuštění nebo pomalé aplikace.

#### <a name="app-crashes-or-encounters-an-exception"></a>Aplikace selže nebo dojde k výjimce.

Získat a analyzovat výpis z [zasílání zpráv o chybách systému Windows (WER)](/windows/desktop/wer/windows-error-reporting):

1. Vytvořte složku uchovávající soubory s výpisem stavu systému `c:\dumps` . Fond aplikací musí mít ke složce přístup pro zápis.
1. Spusťte [skript prostředí PowerShell pro EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):
   * Pokud aplikace používá [model hostování v rámci procesu](xref:host-and-deploy/iis/index#in-process-hosting-model), spusťte skript pro *w3wp.exe*:

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * Pokud aplikace používá [model hostování mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model), spusťte skript pro *dotnet.exe*:

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. Spusťte aplikaci za podmínek, které způsobí, že dojde k chybě.
1. Po chybě spusťte [skript PowerShellu DisableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):
   * Pokud aplikace používá [model hostování v rámci procesu](xref:host-and-deploy/iis/index#in-process-hosting-model), spusťte skript pro *w3wp.exe*:

     ```console
     .\DisableDumps w3wp.exe
     ```

   * Pokud aplikace používá [model hostování mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model), spusťte skript pro *dotnet.exe*:

     ```console
     .\DisableDumps dotnet.exe
     ```

Po selhání aplikace a dokončení shromažďování výpisu je možné aplikaci ukončit normálně. Skript PowerShellu nakonfiguruje WER a shromáždí až pět výpisů paměti pro každou aplikaci.

> [!WARNING]
> Výpisy stavu systému můžou zabírat velké množství místa na disku (každý až několik gigabajtů).

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>Aplikace přestane reagovat, dojde k chybě při spuštění nebo se spustí normálně.

Když *aplikace přestane reagovat (zastaví* se, ale nejedná se o chybu), selže během spuštění nebo se spustí normálně, podívejte [se na soubory výpisu paměti v uživatelském režimu: zvolením nejlepšího](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) nástroje vyberte vhodný nástroj pro vytváření výpisu.

#### <a name="analyze-the-dump"></a>Analýza výpisu paměti

Výpis paměti lze analyzovat pomocí několika přístupů. Další informace najdete v tématu [Analýza souboru s výpisem stavu v uživatelském režimu](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).

## <a name="clear-package-caches"></a>Vymazat mezipaměti balíčků

Funkční aplikace může po upgradu .NET Core SDK ve vývojovém počítači nebo změně verzí balíčku v rámci aplikace selhat okamžitě. V některých případech můžou nesouvislé balíčky při provádění hlavních upgradů poškodit aplikaci. Většinu těchto problémů můžete vyřešit pomocí následujících pokynů:

1. Odstraňte složky *bin* a *obj* .
1. Pomocí příkazu [dotnet All--Clear](/dotnet/core/tools/dotnet-nuget-locals) z příkazového prostředí vymažte mezipaměť balíčku.

   Mazání mezipamětí balíčků lze také provést pomocí nástroje [nuget.exe](https://www.nuget.org/downloads) a provedení příkazu `nuget locals all -clear` . *nuget.exe* není sada odinstalována s desktopovým operačním systémem Windows a je nutné ji získat samostatně z [webu NuGet](https://www.nuget.org/downloads).

1. Obnovte a znovu sestavte projekt.
1. Před opětovným nasazením aplikace odstraňte všechny soubory ve složce pro nasazení na serveru.

## <a name="additional-resources"></a>Další zdroje

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a>Dokumentace k Azure

* [Application Insights pro ASP.NET Core](/azure/application-insights/app-insights-asp-net-core)
* [Část vzdálené ladění webových aplikací řešení potíží s webovou aplikací v Azure App Service pomocí sady Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [Přehled diagnostiky Azure App Service](/azure/app-service/app-service-diagnostics)
* [Postup: Monitorování aplikací ve službě Azure App Service](/azure/app-service/web-sites-monitor)
* [Řešení potíží s webovou aplikací v Azure App Service pomocí sady Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [Řešení chyb HTTP z "502 špatné brány" a "nedostupné služby 503" v Azure Web Apps](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [Řešení potíží s výkonem pomalých webových aplikací ve službě Azure App Service](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [Nejčastější dotazy týkající se výkonu aplikací pro Web Apps v Azure](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [Sandbox webové aplikace Azure (omezení spuštění App Service Runtime)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [Azure pátek: Azure App Service diagnostické prostředí a řešení potíží (12 minut video)](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a>Dokumentace sady Visual Studio

* [Vzdálené ladění ASP.NET Core ve službě IIS v Azure v aplikaci Visual Studio 2017](/visualstudio/debugger/remote-debugging-azure)
* [Vzdálené ladění ASP.NET Core na vzdáleném počítači IIS v aplikaci Visual Studio 2017](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [Informace o ladění pomocí sady Visual Studio](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a>Dokumentace k Visual Studio Code

* [Ladění pomocí Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end

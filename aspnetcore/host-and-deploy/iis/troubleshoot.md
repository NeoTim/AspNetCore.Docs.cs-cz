---
title: Řešení potíží s ASP.NET Core ve službě IIS
author: guardrex
description: Zjistěte, jak diagnostikovat problémy s nasazením aplikací ASP.NET Core Internetové informační služby (IIS).
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/12/2019
uid: host-and-deploy/iis/troubleshoot
ms.openlocfilehash: e4c93459f2030c7c0a55ea90e0cc8c8d30b76c51
ms.sourcegitcommit: a04eb20e81243930ec829a9db5dd5de49f669450
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/03/2019
ms.locfileid: "66470457"
---
# <a name="troubleshoot-aspnet-core-on-iis"></a>Řešení potíží s ASP.NET Core ve službě IIS

Podle [Luke Latham](https://github.com/guardrex)

Tento článek obsahuje pokyny o tom, jak Diagnostika ASP.NET Core problém při spuštění aplikace při hostování za nástrojem s [Internetové informační služby (IIS)](/iis). Informace v tomto článku se vztahují k hostování ve službě IIS na serveru systému Windows a Windows Desktop.

::: moniker range=">= aspnetcore-2.2"

V sadě Visual Studio projekt ASP.NET Core výchozí hodnota je [služby IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hostování během ladění. A *502.5 – selhání procesu* nebo *500.30 - Start selhání* , která nastane, pokud ladění místně může být troubleshooted pomocí doporučení v tomto tématu.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

V sadě Visual Studio projekt ASP.NET Core výchozí hodnota je [služby IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hostování během ladění. A *502.5 selhání procesu* , která nastane, pokud ladění místně může být troubleshooted pomocí doporučení v tomto tématu.

::: moniker-end

Další témata pro řešení potíží:

<xref:host-and-deploy/azure-apps/troubleshoot> I když služba App Service používá [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) a služby IIS pro hostování aplikací, v tématu vyhrazené pro pokyny, které jsou specifické pro App Service.

<xref:fundamentals/error-handling> Objevte, jak zpracovávat chyby v aplikacích ASP.NET Core během vývoje v místním systému.

[Zjistěte, jak ladit pomocí sady Visual Studio](/visualstudio/debugger/getting-started-with-the-debugger) Toto téma popisuje funkce ladicího programu sady Visual Studio.

[Ladění pomocí Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging) přečtěte si víc o podporu ladění, které jsou součástí Visual Studio Code.

## <a name="app-startup-errors"></a>Chyby při spuštění aplikace

### <a name="5025-process-failure"></a>502.5 zpracovat selhání

Pracovní proces se nezdaří. Aplikace se nespustí.

Modul ASP.NET Core se pokusí spustit proces dotnet back-endu, ale že ji nebude možné spustit. Příčinu selhání spuštění procesu lze určit obvykle položky [protokolu událostí aplikace](#application-event-log) a [protokolů stdout modul ASP.NET Core](#aspnet-core-module-stdout-log).

Běžné chyby je, že aplikace je špatně nakonfigurovaný. kvůli cílení na určitou verzi rozhraní framework sdílené ASP.NET Core, který není k dispozici. Zkontrolujte, jaké verze rozhraní framework ASP.NET Core sdílené jsou nainstalovány v cílovém počítači. *Sdílené architektuře* je sada sestavení ( *.dll* soubory), které jsou na počítači nainstalovaný a odkazuje Microsoft.aspnetcore.all například `Microsoft.AspNetCore.App`. Odkaz na Microsoft.aspnetcore.all můžete určit minimální požadovaná verze. Další informace najdete v tématu [sdílené architektuře](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).

*502.5 selhání procesu* při hostování nebo aplikace chybná konfigurace způsobí, že se pracovní proces selže, vrátí se chybová stránka:

![Okno prohlížeče zobrazující stránku 502.5 selhání procesu](troubleshoot/_static/process-failure-page.png)

::: moniker range="= aspnetcore-2.2"

### <a name="50030-in-process-startup-failure"></a>500.30 v procesu selhání spuštění

Pracovní proces se nezdaří. Aplikace se nespustí.

Modul ASP.NET Core se pokusí spustit .NET Core CLR v procesu, ale že ji nebude možné spustit. Příčinu selhání spuštění procesu lze určit obvykle položky [protokolu událostí aplikace](#application-event-log) a [protokolů stdout modul ASP.NET Core](#aspnet-core-module-stdout-log).

Běžné chyby je, že aplikace je špatně nakonfigurovaný. kvůli cílení na určitou verzi rozhraní framework sdílené ASP.NET Core, který není k dispozici. Zkontrolujte, jaké verze rozhraní framework ASP.NET Core sdílené jsou nainstalovány v cílovém počítači.

### <a name="5000-in-process-handler-load-failure"></a>500.0 v procesu selhání načtení obslužné rutiny

Pracovní proces se nezdaří. Aplikace se nespustí.

Modul ASP.NET Core se nepodařilo najít .NET Core CLR a najít obslužnou rutinu požadavků v procesu (*aspnetcorev2_inprocess.dll*). Zkontrolujte, jestli:

* Aplikace cílí na buď [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) balíček NuGet nebo [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).
* Verze rozhraní framework ASP.NET Core sdílené cíle, které aplikace nainstalované v cílovém počítači.

### <a name="5000-out-of-process-handler-load-failure"></a>500.0 Chyba načtení out-Of-Process obslužné rutiny

Pracovní proces se nezdaří. Aplikace se nespustí.

Modul ASP.NET Core nenajde žádné obslužné rutiny hostování požadavku na více instancí procesu. Ujistěte se, *aspnetcorev2_outofprocess.dll* je k dispozici v podsložce vedle *aspnetcorev2.dll*.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="50031-ancm-failed-to-find-native-dependencies"></a>500.31 nepovedlo se najít nativní závislosti ANCM

Pracovní proces se nezdaří. Aplikace se nespustí.

Modul ASP.NET Core se pokusí spustit .NET Core runtime v procesu, ale že ji nebude možné spustit. Nejčastější příčinou této chyby při spuštění je, když `Microsoft.NETCore.App` nebo `Microsoft.AspNetCore.App` není nainstalován modul CLR. Pokud nasazení aplikace do cíle ASP.NET Core 3.0 a tuto verzi neexistuje na počítači, dojde k této chybě. Následuje příklad chybová zpráva:

```
The specified framework 'Microsoft.NETCore.App', version '3.0.0' was not found.
  - The following frameworks were found:
      2.2.1 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview5-27626-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27713-13 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27714-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27723-08 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
```

Chybová zpráva obsahuje seznam všech nainstalovaných verzí rozhraní .NET Core a verze požadované aplikace. Chcete-li tuto chybu opravit, buď:

* Nainstalujte odpovídající verzi .NET Core na počítači.
* Změna aplikace pro cílení na verzi .NET Core, který je k dispozici na počítači.
* Publikování aplikací jako [samostatná nasazení](/dotnet/core/deploying/#self-contained-deployments-scd).

Při spuštění ve vývoji ( `ASPNETCORE_ENVIRONMENT` proměnná prostředí je nastavená na `Development`), konkrétní chyba zapsána do odpovědi HTTP. Příčinu selhání spuštění procesu je také součástí [protokolu událostí aplikace](#application-event-log).

### <a name="50032-ancm-failed-to-load-dll"></a>500.32 ANCM nepovedlo se načíst knihovnu dll

Pracovní proces se nezdaří. Aplikace se nespustí.

Nejčastější příčinou této chyby je, že aplikace je publikována pro architekturu nekompatibilní procesor. Pokud pracovní proces běží jako 32bitová aplikace a aplikace byla publikována na cíl 64-bit, dojde k této chybě.

Chcete-li tuto chybu opravit, buď:

* Znovu publikujte aplikaci pro stejnou architekturu procesorů jako pracovní proces.
* Publikování aplikací jako [nasazení závisí na architektuře](/dotnet/core/deploying/#framework-dependent-executables-fde).

### <a name="50033-ancm-request-handler-load-failure"></a>500.33 Chyba načtení obslužná rutina požadavku ANCM

Pracovní proces se nezdaří. Aplikace se nespustí.

Aplikace nebyla odkazovat `Microsoft.AspNetCore.App` rozhraní framework. Jenom aplikace, jejichž cílem `Microsoft.AspNetCore.App` framework mohou být hostovány systémem modul ASP.NET Core.

Chcete-li tuto chybu opravit, zkontrolujte, že aplikace je cílen na verzi `Microsoft.AspNetCore.App` rozhraní framework. Zkontrolujte `.runtimeconfig.json` ověření rozhraní framework aplikace cílí.

### <a name="50034-ancm-mixed-hosting-models-not-supported"></a>500.34 ANCM smíšené modelech hostování není podporován

Pracovní proces nelze spustit aplikaci v rámci procesu a aplikace na více instancí procesu ve stejném procesu.

Chcete-li vyřešit tuto chybu, spouštění aplikací v samostatné fondy aplikací IIS.

### <a name="50035-ancm-multiple-in-process-applications-in-same-process"></a>500.35 ANCM několik aplikací v rámci procesu ve stejném procesu

Pracovní proces nelze spustit aplikaci v rámci procesu a aplikace na více instancí procesu ve stejném procesu.

Chcete-li vyřešit tuto chybu, spouštění aplikací v samostatné fondy aplikací IIS.

### <a name="50036-ancm-out-of-process-handler-load-failure"></a>500.36 Chyba načtení ANCM obslužné rutiny na více instancí procesu

Obslužné rutiny požadavku na více instancí procesu *aspnetcorev2_outofprocess.dll*, vedle položky není *aspnetcorev2.dll* souboru. To znamená, že modul ASP.NET Core poškozená instalace.

Chcete-li vyřešit tuto chybu, opravte instalaci [sady hostování rozhraní .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (pro IIS) nebo Visual Studio (pro službu IIS Express).

### <a name="50037-ancm-failed-to-start-within-startup-time-limit"></a>500.37 nepovedlo se spustit v rámci časový Limit spuštění ANCM

Spuštění v rámci časový limit spuštění poskytují ANCM se nezdařilo. Výchozí časový limit je 120 sekund.

Této chybě může dojít při spuštění velkého počtu aplikací ve stejném počítači. Zkontrolujte provozní špičky využití procesoru nebo paměti na serveru při spuštění. Budete muset rozvrhnout procesu spuštění více aplikací.

### <a name="50030-in-process-startup-failure"></a>500.30 v procesu selhání spuštění

Pracovní proces se nezdaří. Aplikace se nespustí.

Modul ASP.NET Core se pokusí spustit .NET Core runtime v procesu, ale že ji nebude možné spustit. Příčinu selhání spuštění procesu se obvykle určí na základě položky [protokolu událostí aplikace](#application-event-log) a [protokolů stdout modul ASP.NET Core](#aspnet-core-module-stdout-log).

### <a name="5000-in-process-handler-load-failure"></a>500.0 v procesu selhání načtení obslužné rutiny

Pracovní proces se nezdaří. Aplikace se nespustí.

Načítání modul ASP.NET Core komponenty došlo k neznámé chybě. Proveďte jednu z následujících akcí:

* Kontakt [Microsoft Support](https://support.microsoft.com/oas/default.aspx?prid=15832) (vyberte **vývojářské nástroje** pak **ASP.NET Core**).
* Položte dotaz na Stack Overflow.
* Založte problém na našem [úložiště GitHub](https://github.com/aspnet/AspNetCore).

::: moniker-end

### <a name="500-internal-server-error"></a>Chyba 500 interní Server

Spuštění aplikace, ale chybu brání splnění žádosti. na serveru.

Při spuštění nebo při vytváření odpovědi, k této chybě dochází v kódu aplikace. Odpověď může obsahovat žádný obsah nebo se může zobrazit odpovědi *500 – Interní chyba serveru* v prohlížeči. V protokolu událostí aplikace obvykle hlásí, že aplikace se normálně spustit. Z pohledu serveru, který je správný. Aplikace začal, ale nemůže generovat platnou odpověď. [Spuštění aplikace příkazového řádku](#run-the-app-at-a-command-prompt) na serveru nebo [povolit protokol stdout modul ASP.NET Core](#aspnet-core-module-stdout-log) k vyřešení tohoto problému.

### <a name="failed-to-start-application-errorcode-0x800700c1"></a>Nepovedlo se spustit aplikaci (kód chyby "0x800700c1")

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

Aplikaci se nepodařilo spustit, protože sestavení aplikace ( *.dll*) nelze načíst.

Tato chyba nastane, pokud došlo k neshodě bitové verze mezi publikované aplikace a proces w3wp/iisexpress.

Ověřte správnost nastavení 32-bit fondu aplikací:

1. Vyberte fond aplikací ve Správci služby IIS na **fondy aplikací**.
1. Vyberte **Upřesnit nastavení** pod **upravit fond aplikací** v **akce** panelu.
1. Nastavte **povolit 32bitové aplikace**:
   * Pokud nasazení (x86) 32bitové aplikace, nastavte hodnotu na `True`.
   * Pokud nasazení (x64) 64bitové aplikace, nastavte hodnotu na `False`.

### <a name="connection-reset"></a>Obnovení připojení

Pokud dojde k chybě po odeslání hlavičky, bude příliš pozdě pro server k odeslání **500 – Interní chyba serveru** , když dojde k chybě. Často se to stane, když dojde k chybě při serializaci složitých objektů pro odpověď. Tento typ chyby se zobrazí jako *obnovení připojení* chyba na straně klienta. [Protokolování aplikací](xref:fundamentals/logging/index) mohou pomoci při řešení těchto typů chyb.

## <a name="default-startup-limits"></a>Výchozí omezení při spuštění

Modul ASP.NET Core je nakonfigurovaná s výchozí *startupTimeLimit* 120 sekund. Když necháte na výchozí hodnotu, aplikace může trvat až dvě minuty, spusťte před modulu protokoly selhání procesu. Informace o konfiguraci modulu najdete v tématu [atributy elementu aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).

## <a name="troubleshoot-app-startup-errors"></a>Řešení chyb při spuštění aplikace

### <a name="enable-the-aspnet-core-module-debug-log"></a>Povolit protokol ladění modul ASP.NET Core

Přidáním následujícího nastavení obslužné rutiny na aplikaci *web.config* souboru povolení protokolů ladění modul ASP.NET Core:

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

Potvrďte, že cesta zadaná pro protokol existuje a že identita fondu aplikací má oprávnění k zápisu do umístění.

### <a name="application-event-log"></a>Protokol událostí aplikace

Přístup k protokolu událostí aplikace:

1. Otevření nabídky Start, vyhledejte **Prohlížeč událostí**a pak vyberte **Prohlížeč událostí** aplikace.
1. V **Prohlížeč událostí**, otevřete **protokoly Windows** uzlu.
1. Vyberte **aplikace** otevřít protokol událostí aplikace.
1. Vyhledejte chyby související s selhání aplikace. Chyby mají hodnotu *modulu IIS AspNetCore* nebo *služby IIS Express AspNetCore modulu* v *zdroj* sloupce.

### <a name="run-the-app-at-a-command-prompt"></a>Spuštění aplikace příkazového řádku

Mnoho chyb při spuštění nevytvářejí užitečné informace v protokolu událostí aplikace. Příčin některých chyb můžete najít spuštěním aplikace v příkazovém řádku v hostitelském systému.

#### <a name="framework-dependent-deployment"></a>Nasazení závisí na architektuře

Pokud je aplikace [nasazení závisí na architektuře](/dotnet/core/deploying/#framework-dependent-deployments-fdd):

1. Na příkazovém řádku přejděte do složky pro nasazení a spuštění aplikace spuštěním sestavení aplikace s *dotnet.exe*. V následujícím příkazu nahraďte název sestavení aplikace pro \<název_sestavení >: `dotnet .\<assembly_name>.dll`.
1. Výstup z aplikace zobrazuje všechny chyby konzoly je zapsán do okna konzoly.
1. Je-li této chybě dojde při požadavku na aplikaci, vytvořte žádost na hostitele a port, kde Kestrel naslouchá. Pomocí výchozího hostitele a post, vytvořit žádost o `http://localhost:5000/`. Aplikace reaguje, obvykle na adrese Kestrel koncový bod, tím je pravděpodobnější týkající se konfigurace hostování a méně pravděpodobné, že v rámci aplikace.

#### <a name="self-contained-deployment"></a>Samostatná nasazení

Pokud je aplikace [samostatná nasazení](/dotnet/core/deploying/#self-contained-deployments-scd):

1. Na příkazovém řádku přejděte do složky pro nasazení a spuštění spustitelného souboru aplikace. V následujícím příkazu nahraďte název sestavení aplikace pro \<název_sestavení >: `<assembly_name>.exe`.
1. Výstup z aplikace zobrazuje všechny chyby konzoly je zapsán do okna konzoly.
1. Je-li této chybě dojde při požadavku na aplikaci, vytvořte žádost na hostitele a port, kde Kestrel naslouchá. Pomocí výchozího hostitele a post, vytvořit žádost o `http://localhost:5000/`. Aplikace reaguje, obvykle na adrese Kestrel koncový bod, tím je pravděpodobnější týkající se konfigurace hostování a méně pravděpodobné, že v rámci aplikace.

### <a name="aspnet-core-module-stdout-log"></a>ASP.NET Core modulu stdout protokolu

Povolení a zobrazení protokolů stdout:

1. Přejděte do složky pro nasazení webu v hostitelském systému.
1. Pokud *protokoly* složka není k dispozici, vytvořte složku. Pokyny o tom, jak povolit MSBuild k vytvoření *protokoly* složky v nasazení automaticky, zobrazí [adresářovou strukturu](xref:host-and-deploy/directory-structure) tématu.
1. Upravit *web.config* souboru. Nastavte **stdoutLogEnabled** k `true` a změnit **stdoutLogFile** tak, aby odkazoval na cestu *protokoly* složky (například `.\logs\stdout`). `stdout` v cestě je předpona názvu souboru protokolu. Časové razítko, id procesu a příponu souboru jsou přidány automaticky při vytvoření protokolu. Pomocí `stdout` jako předpona názvu souboru, má název souboru typické protokolu *stdout_20180205184032_5412.log*.
1. Ověřte identitu fondu aplikací má oprávnění k zápisu *protokoly* složky.
1. Uložte aktualizovaný *web.config* souboru.
1. Vytvořte žádost do aplikace.
1. Přejděte *protokoly* složky. Vyhledání a otevření protokolu nejnovější stdout.
1. Studie v protokolu chyb.

> [!IMPORTANT]
> Zakážete protokolování stdout po dokončení odstraňování potíží.

1. Upravit *web.config* souboru.
1. Nastavte **stdoutLogEnabled** k `false`.
1. Uložte soubor.

> [!WARNING]
> Nepodařilo se zakázat protokol stdout může vést k selhání aplikace nebo serveru. Neexistuje žádné omezení velikosti souboru protokolu nebo počet souborů protokolů, které jsou vytvořeny.
>
> Pro rutiny protokolování v aplikaci ASP.NET Core, použijte protokolování knihovnu, která omezuje velikost souboru protokolu a otočí protokoly. Další informace najdete v tématu [zprostředkovatele přihlášení třetí strany](xref:fundamentals/logging/index#third-party-logging-providers).

## <a name="enable-the-developer-exception-page"></a>Povolit na stránce výjimek pro vývojáře

`ASPNETCORE_ENVIRONMENT` [Proměnnou prostředí lze přidat do souboru web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) ke spuštění aplikace ve vývojovém prostředí. Tak dlouho, dokud není prostředí přepsána ve spuštění aplikace podle `UseEnvironment` na tvůrce hostitele nastavení proměnné prostředí umožňuje [stránku výjimek pro vývojáře](xref:fundamentals/error-handling) se zobrazí při spuštění aplikace.

::: moniker range=">= aspnetcore-2.2"

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

::: moniker-end

::: moniker range="< aspnetcore-2.2"

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

::: moniker-end

Nastavení proměnné prostředí pro `ASPNETCORE_ENVIRONMENT` se doporučuje jenom pro použití v přípravy a testování serverů, které nejsou vystaveny v Internetu. Odebrat z proměnné prostředí *web.config* soubor po vyřešení potíží. Informace o nastavení proměnných prostředí *web.config*, naleznete v tématu [environmentVariables podřízený prvek aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).

## <a name="common-startup-errors"></a>Běžné chyby spuštění

Viz <xref:host-and-deploy/azure-iis-errors-reference>. Většina běžných problémů, které brání spuštění aplikace jsou zahrnuté v referenčním tématu.

## <a name="obtain-data-from-an-app"></a>Získání dat z aplikace

Pokud aplikace je schopná reagovat na požadavky, získáte žádost o připojení a další data z aplikace pomocí terminálu vložené middlewaru. Další informace a ukázky kódu najdete v tématu <xref:test/troubleshoot#obtain-data-from-an-app>.

## <a name="create-a-dump"></a>Vytvoření výpisu

A *s výpisem paměti* snímek paměti v systému a vám může pomoct zjistit příčinu selhání aplikace, spuštění selhání nebo pomalé aplikace.

### <a name="app-crashes-or-encounters-an-exception"></a>Aplikace dojde k chybě nebo dojde k výjimce

Získání a analýza výpisu paměti z [hlášení chyb Windows (zasílání)](/windows/desktop/wer/windows-error-reporting):

1. Vytvořte složku pro uložení souborů se stavem systému na `c:\dumps`. Fond aplikací musí mít oprávnění k zápisu do složky.
1. Spustit [EnableDumps Powershellový skript](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/EnableDumps.ps1):
   * Pokud aplikace využívá [model hostingu v procesu](xref:fundamentals/servers/index#in-process-hosting-model), spusťte skript pro *w3wp.exe*:

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * Pokud aplikace využívá [model hostingu mimo proces](xref:fundamentals/servers/index#out-of-process-hosting-model), spusťte skript pro *dotnet.exe*:

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. Spusťte aplikaci v rámci podmínky, které způsobily selhání dojde k.
1. Po došlo k selhání, spusťte [DisableDumps Powershellový skript](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/DisableDumps.ps1):
   * Pokud aplikace využívá [model hostingu v procesu](xref:fundamentals/servers/index#in-process-hosting-model), spusťte skript pro *w3wp.exe*:

     ```console
     .\DisableDumps w3wp.exe
     ```

   * Pokud aplikace využívá [model hostingu mimo proces](xref:fundamentals/servers/index#out-of-process-hosting-model), spusťte skript pro *dotnet.exe*:

     ```console
     .\DisableDumps dotnet.exe
     ```

Po dokončení shromažďování výpisu stavu systému dojde k chybě aplikace a aplikace je povoleno jej měla ukončit normálně. Skript Powershellu nakonfiguruje zasílání shromažďovat až o pěti výpisů paměti na aplikaci.

> [!WARNING]
> Výpisy stavu systému může trvat až velké množství místa na disku (až několik gigabajtů každý).

### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>Aplikace přestane reagovat, při spuštění selže nebo běží normálně

Když aplikaci *přestane reagovat* (přestane reagovat, ale nedojde k chybě), selže při spuštění nebo spuštění za normálních okolností viz [soubory výpisu paměti uživatelského režimu: Výběr vždycky ten nejlepší nástroj](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) vybrat vhodného nástroje k vytvoření výpisu paměti.

### <a name="analyze-the-dump"></a>Analýza výpisu paměti

Výpis paměti mohou být analyzovány pomocí několik přístupů. Další informace najdete v tématu [analýzy souboru výpisu paměti uživatelského režimu](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).

## <a name="remote-debugging"></a>Vzdálené ladění

Zobrazit [vzdálené ladění ASP.NET Core ve vzdáleném počítači služby IIS v sadě Visual Studio 2017](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer) v dokumentaci k sadě Visual Studio.

## <a name="application-insights"></a>Application Insights

[Application Insights](/azure/application-insights/) poskytuje telemetrická data z aplikací hostovaných službou IIS, včetně protokolování a generování sestav funkce chyb. Application Insights může jenom nahlásit to o chybách, ke kterým dochází po spuštění aplikace, když funkce protokolování aplikace budou k dispozici. Další informace najdete v tématu [Application Insights pro ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).

## <a name="additional-advice"></a>Další Rady

Někdy funkční aplikace selže okamžitě po provedení upgradu buď .NET Core SDK na vývojové počítače nebo balíček verze v rámci aplikace. V některých případech osamocené balíčky mohou narušit funkce aplikace při provádění hlavní upgrady. Většina těchto problémů můžete opravit podle těchto pokynů:

1. Odstranit *bin* a *obj* složek.
1. Vymazat balíček ukládá do mezipaměti na *% UserProfile %\\.nuget\\balíčky* a *% LocalAppData %\\Nuget\\v3-cache*.
1. Obnovit a znovu sestavte projekt.
1. Potvrďte, že předchozího nasazení na serveru byl zcela odstraněn před opětovným nasazením aplikace.

> [!TIP]
> Pohodlný způsob, jak Vymazat mezipaměti balíčku je ke spuštění `dotnet nuget locals all --clear` z příkazového řádku.
>
> Vymazání mezipaměti balíčku provést taky pomocí [nuget.exe](https://www.nuget.org/downloads) nástroj a provádění příkazu `nuget locals all -clear`. *nuget.exe* není připojené instalace s operačním systémem klasické pracovní plochy Windows a je potřeba pořídit samostatně z [webu NuGet](https://www.nuget.org/downloads).

## <a name="additional-resources"></a>Další zdroje

* <xref:test/troubleshoot>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/azure-apps/troubleshoot>

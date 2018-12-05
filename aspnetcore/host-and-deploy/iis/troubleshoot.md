---
title: Řešení potíží s ASP.NET Core ve službě IIS
author: guardrex
description: Zjistěte, jak diagnostikovat problémy s nasazením aplikací ASP.NET Core Internetové informační služby (IIS).
ms.author: riande
ms.custom: mvc
ms.date: 11/30/2018
uid: host-and-deploy/iis/troubleshoot
ms.openlocfilehash: 10c40746ffca1343c84f6a7388f3b2d7ab77ab02
ms.sourcegitcommit: 9bb58d7c8dad4bbd03419bcc183d027667fefa20
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52861573"
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

<xref:host-and-deploy/azure-apps/troubleshoot>  
I když služba App Service používá [modul ASP.NET Core](xref:fundamentals/servers/aspnet-core-module) a služby IIS pro hostování aplikací, v tématu vyhrazené pro pokyny, které jsou specifické pro App Service.

<xref:fundamentals/error-handling>  
Objevte, jak zpracovávat chyby v aplikacích ASP.NET Core během vývoje v místním systému.

[Další informace k ladění pomocí sady Visual Studio](/visualstudio/debugger/getting-started-with-the-debugger)  
Toto téma popisuje funkce ladicího programu sady Visual Studio.

[Ladění ve Visual Studiu Code](https://code.visualstudio.com/docs/editor/debugging)  
Další informace o podporu ladění, které jsou součástí Visual Studio Code.

## <a name="app-startup-errors"></a>Chyby při spuštění aplikace

### <a name="5025-process-failure"></a>502.5 zpracovat selhání

Pracovní proces se nezdaří. Aplikace se nespustí.

Modul ASP.NET Core se pokusí spustit proces dotnet back-endu, ale že ji nebude možné spustit. Příčinu selhání spuštění procesu lze určit obvykle položky [protokolu událostí aplikace](#application-event-log) a [protokolů stdout modul ASP.NET Core](#aspnet-core-module-stdout-log). 

Běžné chyby je, že aplikace je špatně nakonfigurovaný. kvůli cílení na určitou verzi rozhraní framework sdílené ASP.NET Core, který není k dispozici. Zkontrolujte, jaké verze rozhraní framework ASP.NET Core sdílené jsou nainstalovány v cílovém počítači.

*502.5 selhání procesu* při hostování nebo aplikace chybná konfigurace způsobí, že se pracovní proces selže, vrátí se chybová stránka:

![Okno prohlížeče zobrazující stránku 502.5 selhání procesu](troubleshoot/_static/process-failure-page.png)

::: moniker range=">= aspnetcore-2.2"

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

### <a name="500-internal-server-error"></a>Chyba 500 interní Server

Spuštění aplikace, ale chybu brání splnění žádosti. na serveru.

Při spuštění nebo při vytváření odpovědi, k této chybě dochází v kódu aplikace. Odpověď může obsahovat žádný obsah nebo se může zobrazit odpovědi *500 – Interní chyba serveru* v prohlížeči. V protokolu událostí aplikace obvykle hlásí, že aplikace se normálně spustit. Z pohledu serveru, který je správný. Aplikace začal, ale nemůže generovat platnou odpověď. [Spuštění aplikace příkazového řádku](#run-the-app-at-a-command-prompt) na serveru nebo [povolit protokol stdout modul ASP.NET Core](#aspnet-core-module-stdout-log) k vyřešení tohoto problému.

### <a name="connection-reset"></a>Obnovení připojení

Pokud dojde k chybě po odeslání hlavičky, bude příliš pozdě pro server k odeslání **500 – Interní chyba serveru** , když dojde k chybě. Často se to stane, když dojde k chybě při serializaci složitých objektů pro odpověď. Tento typ chyby se zobrazí jako *obnovení připojení* chyba na straně klienta. [Protokolování aplikací](xref:fundamentals/logging/index) mohou pomoci při řešení těchto typů chyb.

## <a name="default-startup-limits"></a>Výchozí omezení při spuštění

Modul ASP.NET Core je nakonfigurovaná s výchozí *startupTimeLimit* 120 sekund. Když necháte na výchozí hodnotu, aplikace může trvat až dvě minuty, spusťte před modulu protokoly selhání procesu. Informace o konfiguraci modulu najdete v tématu [atributy elementu aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).

## <a name="troubleshoot-app-startup-errors"></a>Řešení chyb při spuštění aplikace

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
      hostingModel="inprocess">
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

## <a name="slow-or-hanging-app"></a>Pomalá nebo Změ aplikace

Když aplikace reaguje pomalu nebo přestane reagovat na vyžádání, získání a analyzovat [soubor s výpisem paměti](/visualstudio/debugger/using-dump-files). Soubory s výpisem paměti se dají získat pomocí kteréhokoli z následujících nástrojů:

* [ProcDump](/sysinternals/downloads/procdump)
* [DebugDiag](https://www.microsoft.com/download/details.aspx?id=49924)
* WinDbg: [stáhnout Debugging tools for Windows](https://developer.microsoft.com/windows/hardware/download-windbg), [ladění pomocí WinDbg](/windows-hardware/drivers/debugger/debugging-using-windbg)

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

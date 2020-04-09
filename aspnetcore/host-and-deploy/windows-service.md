---
title: Hostování ASP.NET jádra ve službě Windows
author: rick-anderson
description: Přečtěte si, jak ve službě Windows service hostovat aplikaci ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: host-and-deploy/windows-service
ms.openlocfilehash: 5cb61d330df7e15fbd54396207792596ae018fd3
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80417576"
---
# <a name="host-aspnet-core-in-a-windows-service"></a>Hostování ASP.NET jádra ve službě Windows

::: moniker range=">= aspnetcore-3.0"

Aplikaci ASP.NET Core lze hostovat ve Windows jako [službu Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) bez použití služby IIS. Při hostování jako služba Windows se aplikace automaticky spustí po restartování serveru.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="prerequisites"></a>Požadavky

* [ASP.NET jádra SDK 2.1 nebo novější](https://dotnet.microsoft.com/download)
* [PowerShell 6.2 nebo novější](https://github.com/PowerShell/PowerShell)

## <a name="worker-service-template"></a>Šablona služby pracovního procesu

Šablona ASP.NET core worker service poskytuje výchozí bod pro zápis dlouho běžících aplikací služby. Použití šablony jako základu pro aplikaci služby Windows:

1. Vytvořte aplikaci pracovní služby ze šablony .NET Core.
1. Podle pokynů v části [Konfigurace aplikace](#app-configuration) aktualizujte aplikaci Pracovní služba tak, aby mohla fungovat jako služba Windows.

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="app-configuration"></a>Konfigurace aplikací

Aplikace vyžaduje odkaz na balíček pro [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).

`IHostBuilder.UseWindowsService`se nazývá při vytváření hostitele. Pokud je aplikace spuštěná jako služba Windows, metoda:

* Nastaví životnost `WindowsServiceLifetime`hostitele na .
* Nastaví [kořenový obsah](xref:fundamentals/index#content-root) na [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory). Další informace naleznete v části [Aktuální adresář a kořenový obsah.](#current-directory-and-content-root)
* Umožňuje protokolování do protokolu událostí:
  * Název aplikace se používá jako výchozí zdrojový název.
  * Výchozí úroveň protokolu je *Upozornění* nebo vyšší pro aplikaci `CreateDefaultBuilder` založenou na šabloně ASP.NET Core, která volá k sestavení hostitele.
  * Přepište výchozí úroveň protokolu `Logging:EventLog:LogLevel:Default` pomocí klíče v nastavení aplikace *appsettings.json*/*appsettings.{ Prostředí}.json* nebo jiný zprostředkovatel konfigurace.
  * Nové zdroje událostí mohou vytvářet pouze správci. Pokud nelze vytvořit zdroj událostí pomocí názvu aplikace, je do zdroje *aplikace* zaznamenáno upozornění a protokoly událostí jsou zakázány.

V `CreateHostBuilder` *Program.cs*Program.cs :

```csharp
Host.CreateDefaultBuilder(args)
    .UseWindowsService()
    ...
```

K tomuto tématu se přivázala následující ukázková aplikace:

* Ukázka &ndash; pracovní služby na pozadí Ukázka newebové aplikace založená na [šabloně pracovní služby,](#worker-service-template) která používá [hostované služby](xref:fundamentals/host/hosted-services) pro úlohy na pozadí.
* Ukázka ukázkové &ndash; ukázky webové aplikace Razor Pages, která běží jako služba Windows s [hostovanými službami](xref:fundamentals/host/hosted-services) pro úlohy na pozadí.

Pokyny k MVC naleznete <xref:mvc/overview> v <xref:migration/22-to-30>článcích pod a .

## <a name="deployment-type"></a>Typ nasazení

Informace a rady ohledně scénářů nasazení naleznete v tématu [nasazení aplikace .NET Core](/dotnet/core/deploying/).

### <a name="sdk"></a>Sada SDK

Pro službu založenou na webových aplikacích, která používá razor pages nebo mvc architektury, zadejte web ovou sadu SDK v souboru projektu:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Pokud služba provádí pouze úlohy na pozadí (například [hostované služby](xref:fundamentals/host/hosted-services)), zadejte pracovní sdk v souboru projektu:

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a>Nasazení závislé na rámci (FDD)

Nasazení závislé na rozhraní (FDD) závisí na přítomnosti sdílené verze rozhraní .NET Core pro celý systém v cílovém systému. Při přijetí scénáře FDD podle pokynů v tomto článku sada SDK vytvoří spustitelný soubor (*.exe*), nazývaný *spustitelný soubor závislý na rozhraní*.

Pokud používáte [webovou sadku SDK](#sdk), není pro aplikaci služeb windows nutný soubor *web.config,* který se obvykle vytváří při publikování aplikace ASP.NET Core. Chcete-li zakázat vytváření souboru *web.config,* přidejte `<IsTransformWebConfigDisabled>` vlastnost nastavenou na . `true`

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a>Samostatné nasazení (SCD)

Samostatné nasazení (SCD) nespoléhá na přítomnost sdíleného rozhraní v hostitelském systému. Runtime a závislosti aplikace se nasazují s aplikací.

Identifikátor [rid (Windows Runtime)](/dotnet/core/rid-catalog) je `<PropertyGroup>` součástí cílového rozhraní:

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

Publikování pro více idů:

* Zadejte ridy v seznamu odděleného středníkem.
* Použijte název [ \<vlastnosti RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (množné číslo).

Další informace naleznete v tématu [.NET Core RID Catalog](/dotnet/core/rid-catalog).

## <a name="service-user-account"></a>Uživatelský účet služby

Chcete-li vytvořit uživatelský účet pro službu, použijte rutinu [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) z příkazového prostředí PowerShell 6 pro správu.

V aktualizaci Windows 10 říjen 2018 (verze 1809/build 10.0.17763) nebo novější:

```powershell
New-LocalUser -Name {SERVICE NAME}
```

V os windows starší než Windows 10 říjen 2018 Update (verze 1809/build 10.0.17763):

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

Po zobrazení výzvy zadejte [silné heslo.](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements)

Pokud `-AccountExpires` parametr není dodán rutině [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) s <xref:System.DateTime>vypršením platnosti , účet nevyprší.

Další informace naleznete v tématu [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).

Alternativním přístupem ke správě uživatelů při používání služby Active Directory je použití účtů spravovaných služeb. Další informace naleznete v [tématu Přehled účtů spravovaných služeb skupiny](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).

## <a name="log-on-as-a-service-rights"></a>Přihlásit se jako servisní práva

Chcete-li vytvořit *přihlásit jako* práva služby pro účet uživatele služby:

1. Spuštěním souboru *secpol.msc*otevřete editor místních zásad zabezpečení.
1. Rozbalte uzel **Místní zásady** a vyberte **přiřazení uživatelských práv**.
1. Otevřete **zásadu Přihlášení jako zásadu služby.**
1. Vyberte **Přidat uživatele nebo skupinu**.
1. Zadejte název objektu (uživatelský účet) pomocí některého z následujících přístupů:
   1. Do pole názvu`{DOMAIN OR COMPUTER NAME\USER}`objektu zadejte uživatelský účet ( ) a výběrem **možnosti OK** přidejte uživatele do zásady.
   1. Vyberte **Upřesnit**. Vyberte **Najít .** Vyberte uživatelský účet ze seznamu. Vyberte **OK**. Chcete-li přidat uživatele do zásady, vyberte znovu **OK.**
1. Chcete-li změny přijmout, vyberte **ok** nebo **Použít.**

## <a name="create-and-manage-the-windows-service"></a>Vytvoření a správa služby Systému Windows

### <a name="create-a-service"></a>Vytvoření služby

K registraci služby použijte příkazy prostředí PowerShell. Z řídicího prostředí powershellu 6 pro správu proveďte následující příkazy:

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* `{EXE PATH}`&ndash; Cesta ke složce aplikace na hostiteli `d:\myservice`(například). Nezahrnejte spustitelný soubor aplikace do cesty. Koncové lomítko není nutné.
* `{DOMAIN OR COMPUTER NAME\USER}`&ndash; Uživatelský účet služby `Contoso\ServiceUser`(například ).
* `{SERVICE NAME}`&ndash; Název služby (například `MyService`).
* `{EXE FILE PATH}`&ndash; Cesta spustitelného souboru aplikace `d:\myservice\myservice.exe`(například). Zahrňte název souboru spustitelného souboru s příponou.
* `{DESCRIPTION}`&ndash; Popis služby (například `My sample service`).
* `{DISPLAY NAME}`&ndash; Zobrazovaný název služby `My Service`(například).

### <a name="start-a-service"></a>Spuštění služby

Spusťte službu pomocí následujícího příkazu PowerShellu 6:

```powershell
Start-Service -Name {SERVICE NAME}
```

Spuštění služby trvá několik sekund.

### <a name="determine-a-services-status"></a>Určení stavu služby

Chcete-li zkontrolovat stav služby, použijte následující příkaz PowerShellu 6:

```powershell
Get-Service -Name {SERVICE NAME}
```

Stav je vykázán jako jedna z následujících hodnot:

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a>Zastavení služby

Zastavení služby pomocí následujícího příkazu Powershellu 6:

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a>Odebrání služby

Po krátké prodlevě pro zastavení služby odeberte službu pomocí následujícího příkazu Powershellu 6:

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="proxy-server-and-load-balancer-scenarios"></a>Scénáře proxy serveru a vyrovnávání zatížení

Služby, které interagují s požadavky z Internetu nebo podnikové sítě a jsou za proxy serverem nebo nástrojem pro vyrovnávání zatížení, mohou vyžadovat další konfiguraci. Další informace naleznete v tématu <xref:host-and-deploy/proxy-load-balancer>.

## <a name="configure-endpoints"></a>Konfigurace koncových bodů

Ve výchozím nastavení se `http://localhost:5000`ASP.NET jádro váže na . Nakonfigurujte adresu `ASPNETCORE_URLS` URL a port nastavením proměnné prostředí.

Další přístupy ke konfiguraci adres URL a portů naleznete v příslušném článku serveru:

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

Předchozí pokyny zahrnuje podporu pro koncové body HTTPS. Nakonfigurujte například aplikaci pro protokol HTTPS při použití ověřování se službou systému Windows.

> [!NOTE]
> Použití ASP.NET certifikátu pro vývoj jádra HTTPS k zabezpečení koncového bodu služby není podporováno.

## <a name="current-directory-and-content-root"></a>Aktuální adresář a kořenový adresář obsahu

Aktuální pracovní adresář vrácený voláním <xref:System.IO.Directory.GetCurrentDirectory*> služby Systému Windows je složka *C:\\Windows\\system32.* Složka *system32* není vhodné umístění pro ukládání souborů služby (například soubory nastavení). Pomocí jednoho z následujících přístupů můžete udržovat a přistupovat k datovým zdrojům a souborům nastavení služby.

### <a name="use-contentrootpath-or-contentrootfileprovider"></a>Použití ContentRootPath nebo ContentRootFileProvider

Použijte [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) nebo <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> vyhledejte prostředky aplikace.

Když aplikace běží jako <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> služba, <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> nastaví na [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).

Výchozí soubory nastavení aplikace, *soubor appsettings.json* a *nastavení aplikace.{ Environment}.json*, se načítají z kořenového adresáře obsahu aplikace voláním [CreateDefaultBuilder během výstavby hostitele](xref:fundamentals/host/generic-host#set-up-a-host).

U dalších souborů nastavení <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>načtených vývojářským kódem <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>v oblasti není třeba volat . V následujícím příkladu *custom_settings.json* soubor existuje v kořenovém adresáři obsahu aplikace a je načten bez explicitního nastavení základní cesty:

[!code-csharp[](windows-service/samples_snapshot/CustomSettingsExample.cs?highlight=13)]

Nepokoušejte se <xref:System.IO.Directory.GetCurrentDirectory*> získat cestu k prostředku, protože aplikace služby Windows vrátí jako aktuální adresář složku *C:\\Windows\\system32.*

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a>Uložení souborů služby na vhodném místě na disku

Při použití <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> složky obsahující soubory určete absolutní cestu.

## <a name="troubleshoot"></a>Řešení potíží

Informace o řešení potíží <xref:test/troubleshoot>s aplikací služby Windows naleznete v tématu .

### <a name="common-errors"></a>Běžné chyby

* Stará nebo předběžná verze prostředí PowerShell se používá.
* Registrovaná služba nepoužívá **publikovaný** výstup aplikace z příkazu [dotnet publish.](/dotnet/core/tools/dotnet-publish) Výstup příkazu [dotnet build](/dotnet/core/tools/dotnet-build) není pro nasazení aplikace podporován. Publikované datové zdroje se nacházejí v jedné z následujících složek v závislosti na typu nasazení:
  * *bin/Release/{TARGET FRAMEWORK}/publish* (FDD)
  * *bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)
* Služba není ve stavu SPUŠTĚNO.
* Cesty k prostředkům, které aplikace používá (například certifikáty) jsou nesprávné. Základní cesta služby systému Windows je *c:\\Windows\\System32*.
* Uživatel nemá *Přihlásit jako servisní* práva.
* Heslo uživatele vypršelo nebo nesprávně předáno při `New-Service` provádění příkazu PowerShell.
* Aplikace vyžaduje ASP.NET ověřování jádra, ale není nakonfigurovánpro zabezpečená připojení (HTTPS).
* Port url požadavku je v aplikaci nesprávný nebo není správně nakonfigurován.

### <a name="system-and-application-event-logs"></a>Protokoly událostí systému a aplikací

Přístup k protokolům událostí systému a aplikací:

1. Otevřete nabídku Start, vyhledejte *Prohlížeč událostí*a vyberte aplikaci **Prohlížeč událostí.**
1. V **Prohlížeči událostí**otevřete uzel **Protokoly systému Windows.**
1. Výběrem **možnosti Systém** otevřete protokol systémových událostí. Výběrem **možnosti Aplikace** otevřete protokol událostí aplikace.
1. Vyhledejte chyby spojené s aplikací se selháním.

### <a name="run-the-app-at-a-command-prompt"></a>Spuštění aplikace na příkazovém řádku

Mnoho chyb při spuštění nevytváří užitečné informace v protokolech událostí. Příčinu některých chyb můžete najít spuštěním aplikace na příkazovém řádku v hostitelském systému. Chcete-li protokolovat další podrobnosti z aplikace, snižte [úroveň protokolu](xref:fundamentals/logging/index#log-level) nebo spusťte aplikaci ve [vývojovém prostředí](xref:fundamentals/environments).

### <a name="clear-package-caches"></a>Vymazat mezipaměti balíčků

Funkční aplikace může selhat ihned po upgradu sady .NET Core SDK ve vývojovém počítači nebo změně verze balíčku v rámci aplikace. V některých případech nesouvislé balíčky může přerušit aplikaci při provádění hlavních upgradů. Většinu těchto problémů lze opravit podle následujících pokynů:

1. Odstraňte složky *bin* a *obj.*
1. Vymazat mezipaměti balíčku spuštěním [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) z příkazového prostředí.

   Vymazání mezipaměti balíčků lze také provést pomocí nástroje [nuget.exe](https://www.nuget.org/downloads) a spuštěnípříkazu `nuget locals all -clear`. *nuget.exe* není přibalená instalace s operačním systémem Windows desktop a musí být získány odděleně od [webu NuGet](https://www.nuget.org/downloads).

1. Obnovte a znovu vytvořte projekt.
1. Před opětovnou nasazením aplikace odstraňte všechny soubory ve složce nasazení na serveru.

### <a name="slow-or-hanging-app"></a>Pomalá nebo zavěšená aplikace

*Výpis stavu systému* je snímek paměti systému a může pomoci určit příčinu selhání aplikace, selhání při spuštění nebo pomalé aplikace.

#### <a name="app-crashes-or-encounters-an-exception"></a>Aplikace dojde k chybě nebo narazí na výjimku

Získání a analýza výpisu z [programu Zasílání zpráv o chybách systému Windows (WER)](/windows/desktop/wer/windows-error-reporting):

1. Vytvořte složku, ve které `c:\dumps`budou uloženy soubory s výpisem stavu systému .
1. Spusťte [skript EnableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) s názvem spustitelného souboru aplikace:

   ```powershell
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. Spusťte aplikaci za podmínek, které způsobují selhání dojít.
1. Po selhání spusťte [skript DisableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1):

   ```powershell
   .\DisableDumps {APPLICATION EXE}
   ```

Po zhroucení aplikace a výpis kolekce je kompletní, aplikace je povoleno ukončit normálně. Skript PowerShell udává WER tak, aby shromažďoval až pět výpisů v aplikaci.

> [!WARNING]
> Výpisy stavu systému může zabírají velké množství místa na disku (až několik gigabajtů každý).

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>Aplikace přestane reagovat, selže při spuštění nebo běží normálně

Když aplikace *přestane reagovat* (přestane reagovat, ale nezhroutí se), selže při spuštění nebo běží normálně, najdete v [tématu Soubory výpisu uživatelského režimu: Výběr nejvhodnějšího nástroje](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) pro výběr vhodného nástroje pro vytvoření výpisu.

#### <a name="analyze-the-dump"></a>Analyzovat výpis

Výpis lze analyzovat pomocí několika přístupů. Další informace naleznete [v tématu Analýza souboru s výpisem stavu uživatelského režimu](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).

## <a name="additional-resources"></a>Další zdroje

* [Konfigurace koncového bodu Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (zahrnuje konfiguraci HTTPS a podporu SNI)
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Aplikaci ASP.NET Core lze hostovat ve Windows jako [službu Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) bez použití služby IIS. Při hostování jako služba Windows se aplikace automaticky spustí po restartování serveru.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="prerequisites"></a>Požadavky

* [ASP.NET jádra SDK 2.1 nebo novější](https://dotnet.microsoft.com/download)
* [PowerShell 6.2 nebo novější](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a>Konfigurace aplikací

Aplikace vyžaduje odkazy na balíčky pro [microsoft.aspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) a [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).

Chcete-li testovat a ladit při spuštění mimo službu, přidejte kód, který určí, jestli je aplikace spuštěná jako služba nebo konzolová aplikace. Zkontrolujte, zda je připojen ladicí program nebo zda je k dispozici `--console` přepínač. Pokud je některá podmínka pravdivá (aplikace není <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>spuštěna jako služba), volejte . Pokud jsou podmínky nepravdivé (aplikace je spuštěna jako služba):

* Zavolejte <xref:System.IO.Directory.SetCurrentDirectory*> a použijte cestu k publikovanému místu aplikace. Nevolejte <xref:System.IO.Directory.GetCurrentDirectory*> získat cestu, protože aplikace služby Windows vrátí *c:\\windows\\system32* složku, když <xref:System.IO.Directory.GetCurrentDirectory*> je volána. Další informace naleznete v části [Aktuální adresář a kořenový obsah.](#current-directory-and-content-root) Tento krok se provádí před `CreateWebHostBuilder`konfigurací aplikace v aplikaci .
* Volání <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> pro spuštění aplikace jako služby.

Vzhledem k tomu, [že zprostředkovatel konfigurace příkazového řádku](xref:fundamentals/configuration/index#command-line-configuration-provider) vyžaduje `--console` dvojice název-hodnota pro <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> argumenty příkazového řádku, přepínač je odebrán z argumentů před přijetím argumentů.

Chcete-li zapisovat do protokolu událostí <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>systému Windows, přidejte do aplikace zprostředkovatele eventlogu . Nastavte úroveň protokolování `Logging:LogLevel:Default` pomocí klíče v *nastavení aplikace. Soubor Production.json.*

V následujícím příkladu z `RunAsCustomService` ukázkové aplikace <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> se volá místo pro zpracování událostí životnosti v rámci aplikace. Další informace naleznete v části [Zpracování událostí spuštění a zastavení.](#handle-starting-and-stopping-events)

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a>Typ nasazení

Informace a rady ohledně scénářů nasazení naleznete v tématu [nasazení aplikace .NET Core](/dotnet/core/deploying/).

### <a name="sdk"></a>Sada SDK

Pro službu založenou na webových aplikacích, která používá razor pages nebo mvc architektury, zadejte web ovou sadu SDK v souboru projektu:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Pokud služba provádí pouze úlohy na pozadí (například [hostované služby](xref:fundamentals/host/hosted-services)), zadejte pracovní sdk v souboru projektu:

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a>Nasazení závislé na rámci (FDD)

Nasazení závislé na rozhraní (FDD) závisí na přítomnosti sdílené verze rozhraní .NET Core pro celý systém v cílovém systému. Při přijetí scénáře FDD podle pokynů v tomto článku sada SDK vytvoří spustitelný soubor (*.exe*), nazývaný *spustitelný soubor závislý na rozhraní*.

Identifikátor [prostředí Windows Runtime (RID)](/dotnet/core/rid-catalog) [\<(RuntimeIdentifier>) ](/dotnet/core/tools/csproj#runtimeidentifier)obsahuje cílovou architekturu. V následujícím příkladu je rid `win7-x64`nastaven na . Vlastnost `<SelfContained>` je nastavena na `false`. Tyto vlastnosti instruují sadu SDK ke generování spustitelného souboru (*EXE*) pro Systém Windows a aplikace, která závisí na sdíleném rozhraní .NET Core framework.

Soubor *web.config,* který se obvykle vytváří při publikování aplikace ASP.NET Core, není pro aplikaci služeb Windows nutný. Chcete-li zakázat vytváření souboru *web.config,* přidejte `<IsTransformWebConfigDisabled>` vlastnost nastavenou na . `true`

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a>Samostatné nasazení (SCD)

Samostatné nasazení (SCD) nespoléhá na přítomnost sdíleného rozhraní v hostitelském systému. Runtime a závislosti aplikace se nasazují s aplikací.

Identifikátor [rid (Windows Runtime)](/dotnet/core/rid-catalog) je `<PropertyGroup>` součástí cílového rozhraní:

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

Publikování pro více idů:

* Zadejte ridy v seznamu odděleného středníkem.
* Použijte název [ \<vlastnosti RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (množné číslo).

Další informace naleznete v tématu [.NET Core RID Catalog](/dotnet/core/rid-catalog).

Vlastnost `<SelfContained>` je nastavena na `true`:

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a>Uživatelský účet služby

Chcete-li vytvořit uživatelský účet pro službu, použijte rutinu [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) z příkazového prostředí PowerShell 6 pro správu.

V aktualizaci Windows 10 říjen 2018 (verze 1809/build 10.0.17763) nebo novější:

```powershell
New-LocalUser -Name {SERVICE NAME}
```

V os windows starší než Windows 10 říjen 2018 Update (verze 1809/build 10.0.17763):

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

Po zobrazení výzvy zadejte [silné heslo.](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements)

Pokud `-AccountExpires` parametr není dodán rutině [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) s <xref:System.DateTime>vypršením platnosti , účet nevyprší.

Další informace naleznete v tématu [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).

Alternativním přístupem ke správě uživatelů při používání služby Active Directory je použití účtů spravovaných služeb. Další informace naleznete v [tématu Přehled účtů spravovaných služeb skupiny](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).

## <a name="log-on-as-a-service-rights"></a>Přihlásit se jako servisní práva

Chcete-li vytvořit *přihlásit jako* práva služby pro účet uživatele služby:

1. Spuštěním souboru *secpol.msc*otevřete editor místních zásad zabezpečení.
1. Rozbalte uzel **Místní zásady** a vyberte **přiřazení uživatelských práv**.
1. Otevřete **zásadu Přihlášení jako zásadu služby.**
1. Vyberte **Přidat uživatele nebo skupinu**.
1. Zadejte název objektu (uživatelský účet) pomocí některého z následujících přístupů:
   1. Do pole názvu`{DOMAIN OR COMPUTER NAME\USER}`objektu zadejte uživatelský účet ( ) a výběrem **možnosti OK** přidejte uživatele do zásady.
   1. Vyberte **Upřesnit**. Vyberte **Najít .** Vyberte uživatelský účet ze seznamu. Vyberte **OK**. Chcete-li přidat uživatele do zásady, vyberte znovu **OK.**
1. Chcete-li změny přijmout, vyberte **ok** nebo **Použít.**

## <a name="create-and-manage-the-windows-service"></a>Vytvoření a správa služby Systému Windows

### <a name="create-a-service"></a>Vytvoření služby

K registraci služby použijte příkazy prostředí PowerShell. Z řídicího prostředí powershellu 6 pro správu proveďte následující příkazy:

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* `{EXE PATH}`&ndash; Cesta ke složce aplikace na hostiteli `d:\myservice`(například). Nezahrnejte spustitelný soubor aplikace do cesty. Koncové lomítko není nutné.
* `{DOMAIN OR COMPUTER NAME\USER}`&ndash; Uživatelský účet služby `Contoso\ServiceUser`(například ).
* `{SERVICE NAME}`&ndash; Název služby (například `MyService`).
* `{EXE FILE PATH}`&ndash; Cesta spustitelného souboru aplikace `d:\myservice\myservice.exe`(například). Zahrňte název souboru spustitelného souboru s příponou.
* `{DESCRIPTION}`&ndash; Popis služby (například `My sample service`).
* `{DISPLAY NAME}`&ndash; Zobrazovaný název služby `My Service`(například).

### <a name="start-a-service"></a>Spuštění služby

Spusťte službu pomocí následujícího příkazu PowerShellu 6:

```powershell
Start-Service -Name {SERVICE NAME}
```

Spuštění služby trvá několik sekund.

### <a name="determine-a-services-status"></a>Určení stavu služby

Chcete-li zkontrolovat stav služby, použijte následující příkaz PowerShellu 6:

```powershell
Get-Service -Name {SERVICE NAME}
```

Stav je vykázán jako jedna z následujících hodnot:

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a>Zastavení služby

Zastavení služby pomocí následujícího příkazu Powershellu 6:

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a>Odebrání služby

Po krátké prodlevě pro zastavení služby odeberte službu pomocí následujícího příkazu Powershellu 6:

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a>Zpracování událostí spuštění a zastavení

Zpracování <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>a <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> události:

1. Vytvořte třídu, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> která `OnStarting`je `OnStarted`odvozena z , a `OnStopping` metody:

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. Vytvořte metodu <xref:Microsoft.AspNetCore.Hosting.IWebHost> rozšíření, `CustomWebHostService` <xref:System.ServiceProcess.ServiceBase.Run*>která předá do :

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. V `Program.Main`, `RunAsCustomService` volání metody <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>rozšíření namísto :

   ```csharp
   host.RunAsCustomService();
   ```

   Chcete-li zobrazit <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> `Program.Main`umístění v , naleznete v ukázce kódu uvedené v části [Typ nasazení.](#deployment-type)

## <a name="proxy-server-and-load-balancer-scenarios"></a>Scénáře proxy serveru a vyrovnávání zatížení

Služby, které interagují s požadavky z Internetu nebo podnikové sítě a jsou za proxy serverem nebo nástrojem pro vyrovnávání zatížení, mohou vyžadovat další konfiguraci. Další informace naleznete v tématu <xref:host-and-deploy/proxy-load-balancer>.

## <a name="configure-endpoints"></a>Konfigurace koncových bodů

Ve výchozím nastavení se `http://localhost:5000`ASP.NET jádro váže na . Nakonfigurujte adresu `ASPNETCORE_URLS` URL a port nastavením proměnné prostředí.

Další přístupy ke konfiguraci adres URL a portů naleznete v příslušném článku serveru:

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

Předchozí pokyny zahrnuje podporu pro koncové body HTTPS. Nakonfigurujte například aplikaci pro protokol HTTPS při použití ověřování se službou systému Windows.

> [!NOTE]
> Použití ASP.NET certifikátu pro vývoj jádra HTTPS k zabezpečení koncového bodu služby není podporováno.

## <a name="current-directory-and-content-root"></a>Aktuální adresář a kořenový adresář obsahu

Aktuální pracovní adresář vrácený voláním <xref:System.IO.Directory.GetCurrentDirectory*> služby Systému Windows je složka *C:\\Windows\\system32.* Složka *system32* není vhodné umístění pro ukládání souborů služby (například soubory nastavení). Pomocí jednoho z následujících přístupů můžete udržovat a přistupovat k datovým zdrojům a souborům nastavení služby.

### <a name="set-the-content-root-path-to-the-apps-folder"></a>Nastavení kořenové cesty obsahu ke složce aplikace

Je <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> stejná cesta k `binPath` dispozici argument při vytvoření služby. Místo volání `GetCurrentDirectory` k vytvoření cest k <xref:System.IO.Directory.SetCurrentDirectory*> souborům nastavení volejte s cestou ke [kořenu obsahu](xref:fundamentals/index#content-root)aplikace .

V `Program.Main`aplikaci určete cestu ke složce spustitelného souboru služby a pomocí této cesty vytvořte kořen obsahu aplikace:

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a>Uložení souborů služby na vhodném místě na disku

Při použití <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> složky obsahující soubory určete absolutní cestu.

## <a name="troubleshoot"></a>Řešení potíží

Informace o řešení potíží <xref:test/troubleshoot>s aplikací služby Windows naleznete v tématu .

### <a name="common-errors"></a>Běžné chyby

* Stará nebo předběžná verze prostředí PowerShell se používá.
* Registrovaná služba nepoužívá **publikovaný** výstup aplikace z příkazu [dotnet publish.](/dotnet/core/tools/dotnet-publish) Výstup příkazu [dotnet build](/dotnet/core/tools/dotnet-build) není pro nasazení aplikace podporován. Publikované datové zdroje se nacházejí v jedné z následujících složek v závislosti na typu nasazení:
  * *bin/Release/{TARGET FRAMEWORK}/publish* (FDD)
  * *bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)
* Služba není ve stavu SPUŠTĚNO.
* Cesty k prostředkům, které aplikace používá (například certifikáty) jsou nesprávné. Základní cesta služby systému Windows je *c:\\Windows\\System32*.
* Uživatel nemá *Přihlásit jako servisní* práva.
* Heslo uživatele vypršelo nebo nesprávně předáno při `New-Service` provádění příkazu PowerShell.
* Aplikace vyžaduje ASP.NET ověřování jádra, ale není nakonfigurovánpro zabezpečená připojení (HTTPS).
* Port url požadavku je v aplikaci nesprávný nebo není správně nakonfigurován.

### <a name="system-and-application-event-logs"></a>Protokoly událostí systému a aplikací

Přístup k protokolům událostí systému a aplikací:

1. Otevřete nabídku Start, vyhledejte *Prohlížeč událostí*a vyberte aplikaci **Prohlížeč událostí.**
1. V **Prohlížeči událostí**otevřete uzel **Protokoly systému Windows.**
1. Výběrem **možnosti Systém** otevřete protokol systémových událostí. Výběrem **možnosti Aplikace** otevřete protokol událostí aplikace.
1. Vyhledejte chyby spojené s aplikací se selháním.

### <a name="run-the-app-at-a-command-prompt"></a>Spuštění aplikace na příkazovém řádku

Mnoho chyb při spuštění nevytváří užitečné informace v protokolech událostí. Příčinu některých chyb můžete najít spuštěním aplikace na příkazovém řádku v hostitelském systému. Chcete-li protokolovat další podrobnosti z aplikace, snižte [úroveň protokolu](xref:fundamentals/logging/index#log-level) nebo spusťte aplikaci ve [vývojovém prostředí](xref:fundamentals/environments).

### <a name="clear-package-caches"></a>Vymazat mezipaměti balíčků

Funkční aplikace může selhat ihned po upgradu sady .NET Core SDK ve vývojovém počítači nebo změně verze balíčku v rámci aplikace. V některých případech nesouvislé balíčky může přerušit aplikaci při provádění hlavních upgradů. Většinu těchto problémů lze opravit podle následujících pokynů:

1. Odstraňte složky *bin* a *obj.*
1. Vymazat mezipaměti balíčku spuštěním [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) z příkazového prostředí.

   Vymazání mezipaměti balíčků lze také provést pomocí nástroje [nuget.exe](https://www.nuget.org/downloads) a spuštěnípříkazu `nuget locals all -clear`. *nuget.exe* není přibalená instalace s operačním systémem Windows desktop a musí být získány odděleně od [webu NuGet](https://www.nuget.org/downloads).

1. Obnovte a znovu vytvořte projekt.
1. Před opětovnou nasazením aplikace odstraňte všechny soubory ve složce nasazení na serveru.

### <a name="slow-or-hanging-app"></a>Pomalá nebo zavěšená aplikace

*Výpis stavu systému* je snímek paměti systému a může pomoci určit příčinu selhání aplikace, selhání při spuštění nebo pomalé aplikace.

#### <a name="app-crashes-or-encounters-an-exception"></a>Aplikace dojde k chybě nebo narazí na výjimku

Získání a analýza výpisu z [programu Zasílání zpráv o chybách systému Windows (WER)](/windows/desktop/wer/windows-error-reporting):

1. Vytvořte složku, ve které `c:\dumps`budou uloženy soubory s výpisem stavu systému .
1. Spusťte [skript EnableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) s názvem spustitelného souboru aplikace:

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. Spusťte aplikaci za podmínek, které způsobují selhání dojít.
1. Po selhání spusťte [skript DisableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

Po zhroucení aplikace a výpis kolekce je kompletní, aplikace je povoleno ukončit normálně. Skript PowerShell udává WER tak, aby shromažďoval až pět výpisů v aplikaci.

> [!WARNING]
> Výpisy stavu systému může zabírají velké množství místa na disku (až několik gigabajtů každý).

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>Aplikace přestane reagovat, selže při spuštění nebo běží normálně

Když aplikace *přestane reagovat* (přestane reagovat, ale nezhroutí se), selže při spuštění nebo běží normálně, najdete v [tématu Soubory výpisu uživatelského režimu: Výběr nejvhodnějšího nástroje](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) pro výběr vhodného nástroje pro vytvoření výpisu.

#### <a name="analyze-the-dump"></a>Analyzovat výpis

Výpis lze analyzovat pomocí několika přístupů. Další informace naleznete [v tématu Analýza souboru s výpisem stavu uživatelského režimu](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).

## <a name="additional-resources"></a>Další zdroje

* [Konfigurace koncového bodu Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (zahrnuje konfiguraci HTTPS a podporu SNI)
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Aplikaci ASP.NET Core lze hostovat ve Windows jako [službu Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) bez použití služby IIS. Při hostování jako služba Windows se aplikace automaticky spustí po restartování serveru.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="prerequisites"></a>Požadavky

* [ASP.NET jádra SDK 2.1 nebo novější](https://dotnet.microsoft.com/download)
* [PowerShell 6.2 nebo novější](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a>Konfigurace aplikací

Aplikace vyžaduje odkazy na balíčky pro [microsoft.aspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) a [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).

Chcete-li testovat a ladit při spuštění mimo službu, přidejte kód, který určí, jestli je aplikace spuštěná jako služba nebo konzolová aplikace. Zkontrolujte, zda je připojen ladicí program nebo zda je k dispozici `--console` přepínač. Pokud je některá podmínka pravdivá (aplikace není <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>spuštěna jako služba), volejte . Pokud jsou podmínky nepravdivé (aplikace je spuštěna jako služba):

* Zavolejte <xref:System.IO.Directory.SetCurrentDirectory*> a použijte cestu k publikovanému místu aplikace. Nevolejte <xref:System.IO.Directory.GetCurrentDirectory*> získat cestu, protože aplikace služby Windows vrátí *c:\\windows\\system32* složku, když <xref:System.IO.Directory.GetCurrentDirectory*> je volána. Další informace naleznete v části [Aktuální adresář a kořenový obsah.](#current-directory-and-content-root) Tento krok se provádí před `CreateWebHostBuilder`konfigurací aplikace v aplikaci .
* Volání <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> pro spuštění aplikace jako služby.

Vzhledem k tomu, [že zprostředkovatel konfigurace příkazového řádku](xref:fundamentals/configuration/index#command-line-configuration-provider) vyžaduje `--console` dvojice název-hodnota pro <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> argumenty příkazového řádku, přepínač je odebrán z argumentů před přijetím argumentů.

Chcete-li zapisovat do protokolu událostí <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>systému Windows, přidejte do aplikace zprostředkovatele eventlogu . Nastavte úroveň protokolování `Logging:LogLevel:Default` pomocí klíče v *nastavení aplikace. Soubor Production.json.*

V následujícím příkladu z `RunAsCustomService` ukázkové aplikace <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> se volá místo pro zpracování událostí životnosti v rámci aplikace. Další informace naleznete v části [Zpracování událostí spuštění a zastavení.](#handle-starting-and-stopping-events)

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a>Typ nasazení

Informace a rady ohledně scénářů nasazení naleznete v tématu [nasazení aplikace .NET Core](/dotnet/core/deploying/).

### <a name="sdk"></a>Sada SDK

Pro službu založenou na webových aplikacích, která používá razor pages nebo mvc architektury, zadejte web ovou sadu SDK v souboru projektu:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Pokud služba provádí pouze úlohy na pozadí (například [hostované služby](xref:fundamentals/host/hosted-services)), zadejte pracovní sdk v souboru projektu:

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a>Nasazení závislé na rámci (FDD)

Nasazení závislé na rozhraní (FDD) závisí na přítomnosti sdílené verze rozhraní .NET Core pro celý systém v cílovém systému. Při přijetí scénáře FDD podle pokynů v tomto článku sada SDK vytvoří spustitelný soubor (*.exe*), nazývaný *spustitelný soubor závislý na rozhraní*.

Identifikátor [prostředí Windows Runtime (RID)](/dotnet/core/rid-catalog) [\<(RuntimeIdentifier>) ](/dotnet/core/tools/csproj#runtimeidentifier)obsahuje cílovou architekturu. V následujícím příkladu je rid `win7-x64`nastaven na . Vlastnost `<SelfContained>` je nastavena na `false`. Tyto vlastnosti instruují sadu SDK ke generování spustitelného souboru (*EXE*) pro Systém Windows a aplikace, která závisí na sdíleném rozhraní .NET Core framework.

Vlastnost `<UseAppHost>` je nastavena na `true`. Tato vlastnost poskytuje službě aktivační cestu (spustitelný *soubor, .exe)* pro FDD.

Soubor *web.config,* který se obvykle vytváří při publikování aplikace ASP.NET Core, není pro aplikaci služeb Windows nutný. Chcete-li zakázat vytváření souboru *web.config,* přidejte `<IsTransformWebConfigDisabled>` vlastnost nastavenou na . `true`

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a>Samostatné nasazení (SCD)

Samostatné nasazení (SCD) nespoléhá na přítomnost sdíleného rozhraní v hostitelském systému. Runtime a závislosti aplikace se nasazují s aplikací.

Identifikátor [rid (Windows Runtime)](/dotnet/core/rid-catalog) je `<PropertyGroup>` součástí cílového rozhraní:

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

Publikování pro více idů:

* Zadejte ridy v seznamu odděleného středníkem.
* Použijte název [ \<vlastnosti RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (množné číslo).

Další informace naleznete v tématu [.NET Core RID Catalog](/dotnet/core/rid-catalog).

Vlastnost `<SelfContained>` je nastavena na `true`:

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a>Uživatelský účet služby

Chcete-li vytvořit uživatelský účet pro službu, použijte rutinu [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) z příkazového prostředí PowerShell 6 pro správu.

V aktualizaci Windows 10 říjen 2018 (verze 1809/build 10.0.17763) nebo novější:

```powershell
New-LocalUser -Name {SERVICE NAME}
```

V os windows starší než Windows 10 říjen 2018 Update (verze 1809/build 10.0.17763):

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

Po zobrazení výzvy zadejte [silné heslo.](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements)

Pokud `-AccountExpires` parametr není dodán rutině [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) s <xref:System.DateTime>vypršením platnosti , účet nevyprší.

Další informace naleznete v tématu [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).

Alternativním přístupem ke správě uživatelů při používání služby Active Directory je použití účtů spravovaných služeb. Další informace naleznete v [tématu Přehled účtů spravovaných služeb skupiny](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).

## <a name="log-on-as-a-service-rights"></a>Přihlásit se jako servisní práva

Chcete-li vytvořit *přihlásit jako* práva služby pro účet uživatele služby:

1. Spuštěním souboru *secpol.msc*otevřete editor místních zásad zabezpečení.
1. Rozbalte uzel **Místní zásady** a vyberte **přiřazení uživatelských práv**.
1. Otevřete **zásadu Přihlášení jako zásadu služby.**
1. Vyberte **Přidat uživatele nebo skupinu**.
1. Zadejte název objektu (uživatelský účet) pomocí některého z následujících přístupů:
   1. Do pole názvu`{DOMAIN OR COMPUTER NAME\USER}`objektu zadejte uživatelský účet ( ) a výběrem **možnosti OK** přidejte uživatele do zásady.
   1. Vyberte **Upřesnit**. Vyberte **Najít .** Vyberte uživatelský účet ze seznamu. Vyberte **OK**. Chcete-li přidat uživatele do zásady, vyberte znovu **OK.**
1. Chcete-li změny přijmout, vyberte **ok** nebo **Použít.**

## <a name="create-and-manage-the-windows-service"></a>Vytvoření a správa služby Systému Windows

### <a name="create-a-service"></a>Vytvoření služby

K registraci služby použijte příkazy prostředí PowerShell. Z řídicího prostředí powershellu 6 pro správu proveďte následující příkazy:

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* `{EXE PATH}`&ndash; Cesta ke složce aplikace na hostiteli `d:\myservice`(například). Nezahrnejte spustitelný soubor aplikace do cesty. Koncové lomítko není nutné.
* `{DOMAIN OR COMPUTER NAME\USER}`&ndash; Uživatelský účet služby `Contoso\ServiceUser`(například ).
* `{SERVICE NAME}`&ndash; Název služby (například `MyService`).
* `{EXE FILE PATH}`&ndash; Cesta spustitelného souboru aplikace `d:\myservice\myservice.exe`(například). Zahrňte název souboru spustitelného souboru s příponou.
* `{DESCRIPTION}`&ndash; Popis služby (například `My sample service`).
* `{DISPLAY NAME}`&ndash; Zobrazovaný název služby `My Service`(například).

### <a name="start-a-service"></a>Spuštění služby

Spusťte službu pomocí následujícího příkazu PowerShellu 6:

```powershell
Start-Service -Name {SERVICE NAME}
```

Spuštění služby trvá několik sekund.

### <a name="determine-a-services-status"></a>Určení stavu služby

Chcete-li zkontrolovat stav služby, použijte následující příkaz PowerShellu 6:

```powershell
Get-Service -Name {SERVICE NAME}
```

Stav je vykázán jako jedna z následujících hodnot:

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a>Zastavení služby

Zastavení služby pomocí následujícího příkazu Powershellu 6:

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a>Odebrání služby

Po krátké prodlevě pro zastavení služby odeberte službu pomocí následujícího příkazu Powershellu 6:

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a>Zpracování událostí spuštění a zastavení

Zpracování <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>a <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> události:

1. Vytvořte třídu, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> která `OnStarting`je `OnStarted`odvozena z , a `OnStopping` metody:

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. Vytvořte metodu <xref:Microsoft.AspNetCore.Hosting.IWebHost> rozšíření, `CustomWebHostService` <xref:System.ServiceProcess.ServiceBase.Run*>která předá do :

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. V `Program.Main`, `RunAsCustomService` volání metody <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>rozšíření namísto :

   ```csharp
   host.RunAsCustomService();
   ```

   Chcete-li zobrazit <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> `Program.Main`umístění v , naleznete v ukázce kódu uvedené v části [Typ nasazení.](#deployment-type)

## <a name="proxy-server-and-load-balancer-scenarios"></a>Scénáře proxy serveru a vyrovnávání zatížení

Služby, které interagují s požadavky z Internetu nebo podnikové sítě a jsou za proxy serverem nebo nástrojem pro vyrovnávání zatížení, mohou vyžadovat další konfiguraci. Další informace naleznete v tématu <xref:host-and-deploy/proxy-load-balancer>.

## <a name="configure-endpoints"></a>Konfigurace koncových bodů

Ve výchozím nastavení se `http://localhost:5000`ASP.NET jádro váže na . Nakonfigurujte adresu `ASPNETCORE_URLS` URL a port nastavením proměnné prostředí.

Další přístupy ke konfiguraci adres URL a portů naleznete v příslušném článku serveru:

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

Předchozí pokyny zahrnuje podporu pro koncové body HTTPS. Nakonfigurujte například aplikaci pro protokol HTTPS při použití ověřování se službou systému Windows.

> [!NOTE]
> Použití ASP.NET certifikátu pro vývoj jádra HTTPS k zabezpečení koncového bodu služby není podporováno.

## <a name="current-directory-and-content-root"></a>Aktuální adresář a kořenový adresář obsahu

Aktuální pracovní adresář vrácený voláním <xref:System.IO.Directory.GetCurrentDirectory*> služby Systému Windows je složka *C:\\Windows\\system32.* Složka *system32* není vhodné umístění pro ukládání souborů služby (například soubory nastavení). Pomocí jednoho z následujících přístupů můžete udržovat a přistupovat k datovým zdrojům a souborům nastavení služby.

### <a name="set-the-content-root-path-to-the-apps-folder"></a>Nastavení kořenové cesty obsahu ke složce aplikace

Je <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> stejná cesta k `binPath` dispozici argument při vytvoření služby. Místo volání `GetCurrentDirectory` k vytvoření cest k <xref:System.IO.Directory.SetCurrentDirectory*> souborům nastavení volejte s cestou ke [kořenu obsahu](xref:fundamentals/index#content-root)aplikace .

V `Program.Main`aplikaci určete cestu ke složce spustitelného souboru služby a pomocí této cesty vytvořte kořen obsahu aplikace:

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a>Uložení souborů služby na vhodném místě na disku

Při použití <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> složky obsahující soubory určete absolutní cestu.

## <a name="troubleshoot"></a>Řešení potíží

Informace o řešení potíží <xref:test/troubleshoot>s aplikací služby Windows naleznete v tématu .

### <a name="common-errors"></a>Běžné chyby

* Stará nebo předběžná verze prostředí PowerShell se používá.
* Registrovaná služba nepoužívá **publikovaný** výstup aplikace z příkazu [dotnet publish.](/dotnet/core/tools/dotnet-publish) Výstup příkazu [dotnet build](/dotnet/core/tools/dotnet-build) není pro nasazení aplikace podporován. Publikované datové zdroje se nacházejí v jedné z následujících složek v závislosti na typu nasazení:
  * *bin/Release/{TARGET FRAMEWORK}/publish* (FDD)
  * *bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)
* Služba není ve stavu SPUŠTĚNO.
* Cesty k prostředkům, které aplikace používá (například certifikáty) jsou nesprávné. Základní cesta služby systému Windows je *c:\\Windows\\System32*.
* Uživatel nemá *Přihlásit jako servisní* práva.
* Heslo uživatele vypršelo nebo nesprávně předáno při `New-Service` provádění příkazu PowerShell.
* Aplikace vyžaduje ASP.NET ověřování jádra, ale není nakonfigurovánpro zabezpečená připojení (HTTPS).
* Port url požadavku je v aplikaci nesprávný nebo není správně nakonfigurován.

### <a name="system-and-application-event-logs"></a>Protokoly událostí systému a aplikací

Přístup k protokolům událostí systému a aplikací:

1. Otevřete nabídku Start, vyhledejte *Prohlížeč událostí*a vyberte aplikaci **Prohlížeč událostí.**
1. V **Prohlížeči událostí**otevřete uzel **Protokoly systému Windows.**
1. Výběrem **možnosti Systém** otevřete protokol systémových událostí. Výběrem **možnosti Aplikace** otevřete protokol událostí aplikace.
1. Vyhledejte chyby spojené s aplikací se selháním.

### <a name="run-the-app-at-a-command-prompt"></a>Spuštění aplikace na příkazovém řádku

Mnoho chyb při spuštění nevytváří užitečné informace v protokolech událostí. Příčinu některých chyb můžete najít spuštěním aplikace na příkazovém řádku v hostitelském systému. Chcete-li protokolovat další podrobnosti z aplikace, snižte [úroveň protokolu](xref:fundamentals/logging/index#log-level) nebo spusťte aplikaci ve [vývojovém prostředí](xref:fundamentals/environments).

### <a name="clear-package-caches"></a>Vymazat mezipaměti balíčků

Funkční aplikace může selhat ihned po upgradu sady .NET Core SDK ve vývojovém počítači nebo změně verze balíčku v rámci aplikace. V některých případech nesouvislé balíčky může přerušit aplikaci při provádění hlavních upgradů. Většinu těchto problémů lze opravit podle následujících pokynů:

1. Odstraňte složky *bin* a *obj.*
1. Vymazat mezipaměti balíčku spuštěním [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) z příkazového prostředí.

   Vymazání mezipaměti balíčků lze také provést pomocí nástroje [nuget.exe](https://www.nuget.org/downloads) a spuštěnípříkazu `nuget locals all -clear`. *nuget.exe* není přibalená instalace s operačním systémem Windows desktop a musí být získány odděleně od [webu NuGet](https://www.nuget.org/downloads).

1. Obnovte a znovu vytvořte projekt.
1. Před opětovnou nasazením aplikace odstraňte všechny soubory ve složce nasazení na serveru.

### <a name="slow-or-hanging-app"></a>Pomalá nebo zavěšená aplikace

*Výpis stavu systému* je snímek paměti systému a může pomoci určit příčinu selhání aplikace, selhání při spuštění nebo pomalé aplikace.

#### <a name="app-crashes-or-encounters-an-exception"></a>Aplikace dojde k chybě nebo narazí na výjimku

Získání a analýza výpisu z [programu Zasílání zpráv o chybách systému Windows (WER)](/windows/desktop/wer/windows-error-reporting):

1. Vytvořte složku, ve které `c:\dumps`budou uloženy soubory s výpisem stavu systému .
1. Spusťte [skript EnableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) s názvem spustitelného souboru aplikace:

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. Spusťte aplikaci za podmínek, které způsobují selhání dojít.
1. Po selhání spusťte [skript DisableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

Po zhroucení aplikace a výpis kolekce je kompletní, aplikace je povoleno ukončit normálně. Skript PowerShell udává WER tak, aby shromažďoval až pět výpisů v aplikaci.

> [!WARNING]
> Výpisy stavu systému může zabírají velké množství místa na disku (až několik gigabajtů každý).

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>Aplikace přestane reagovat, selže při spuštění nebo běží normálně

Když aplikace *přestane reagovat* (přestane reagovat, ale nezhroutí se), selže při spuštění nebo běží normálně, najdete v [tématu Soubory výpisu uživatelského režimu: Výběr nejvhodnějšího nástroje](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) pro výběr vhodného nástroje pro vytvoření výpisu.

#### <a name="analyze-the-dump"></a>Analyzovat výpis

Výpis lze analyzovat pomocí několika přístupů. Další informace naleznete [v tématu Analýza souboru s výpisem stavu uživatelského režimu](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).

## <a name="additional-resources"></a>Další zdroje

* [Konfigurace koncového bodu Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (zahrnuje konfiguraci HTTPS a podporu SNI)
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end

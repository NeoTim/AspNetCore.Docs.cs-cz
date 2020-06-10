---
title: ASP.NET Core hostitele ve službě systému Windows
author: rick-anderson
description: Naučte se hostovat aplikaci ASP.NET Core ve službě systému Windows.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/windows-service
ms.openlocfilehash: af0c3836362233e41a79e72bd28b4a331e9763bc
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/09/2020
ms.locfileid: "84106478"
---
# <a name="host-aspnet-core-in-a-windows-service"></a>ASP.NET Core hostitele ve službě systému Windows

::: moniker range=">= aspnetcore-3.0"

Aplikace ASP.NET Core může být hostována ve Windows jako [služba systému Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) bez použití služby IIS. Po hostování jako služby systému Windows se aplikace automaticky spustí po restartování serveru.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Požadavky

* [ASP.NET Core SDK 2,1 nebo novější](https://dotnet.microsoft.com/download)
* [PowerShell 6,2 nebo novější](https://github.com/PowerShell/PowerShell)

## <a name="worker-service-template"></a>Šablona služby pracovní proces

Šablona služby ASP.NET Core Worker poskytuje výchozí bod pro psaní dlouhotrvajících aplikací služeb. Použití šablony jako základu pro aplikaci služby systému Windows:

1. Vytvořte aplikaci pracovní služby ze šablony .NET Core.
1. Podle pokynů v části [Konfigurace aplikace](#app-configuration) aktualizujte aplikaci pracovní služby pracovních procesů tak, aby mohla běžet jako služba systému Windows.

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="app-configuration"></a>Konfigurace aplikací

Aplikace vyžaduje odkaz na balíček pro [Microsoft. Extensions. Hosting. WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).

`IHostBuilder.UseWindowsService`je volána při sestavování hostitele. Pokud je aplikace spuštěná jako služba systému Windows, metoda:

* Nastaví dobu života hostitele na `WindowsServiceLifetime` .
* Nastaví [kořen obsahu](xref:fundamentals/index#content-root) na [AppContext. BaseDirectory](xref:System.AppContext.BaseDirectory). Další informace najdete v oddílu [aktuální adresář a kořenový adresář obsahu](#current-directory-and-content-root) .
* Povolí protokolování do protokolu událostí:
  * Název aplikace se používá jako výchozí název zdroje.
  * Výchozí úroveň protokolování je *Upozornění* nebo vyšší pro aplikaci na základě šablony ASP.NET Core, která volá `CreateDefaultBuilder` k sestavení hostitele.
  * Přepište výchozí úroveň protokolu pomocí `Logging:EventLog:LogLevel:Default` klíče v souboru *appSettings. JSON* / *appSettings. { Environment}. JSON* nebo jiný poskytovatel konfigurace.
  * Pouze správci mohou vytvářet nové zdroje událostí. Když zdroj události nejde vytvořit pomocí názvu aplikace, zaprotokoluje se upozornění na zdroj *aplikace* a protokoly událostí jsou zakázané.

V `CreateHostBuilder` *program.cs*:

```csharp
Host.CreateDefaultBuilder(args)
    .UseWindowsService()
    ...
```

Toto téma doprovází následující ukázkové aplikace:

* Ukázka služby na pozadí Worker: Ukázka newebové aplikace založená na [šabloně pracovní služby pracovního procesu](#worker-service-template) , která používá [hostované služby](xref:fundamentals/host/hosted-services) pro úlohy na pozadí.
* Ukázka Web App Service: Razor Webová aplikace stránky, která se spouští jako služba systému Windows s [hostovanými službami](xref:fundamentals/host/hosted-services) pro úlohy na pozadí.

Pokyny pro MVC najdete v článcích v tématu <xref:mvc/overview> a <xref:migration/22-to-30> .

## <a name="deployment-type"></a>Typ nasazení

Informace a Rady týkající se scénářů nasazení najdete v tématu [nasazení aplikace .NET Core](/dotnet/core/deploying/).

### <a name="sdk"></a>Sada SDK

U služby založené na webové aplikaci, která používá Razor stránky nebo architektury MVC, zadejte webovou sadu SDK v souboru projektu:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Pokud služba spouští pouze úlohy na pozadí (například [hostované služby](xref:fundamentals/host/hosted-services)), zadejte sadu pracovních procesů sady SDK v souboru projektu:

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a>Nasazení závislé na rozhraní (FDD)

Nasazení závislé na rozhraní (FDD) spoléhá na přítomnost sdílené verze .NET Core v rámci systému v cílovém systému. Pokud je FDD scénář přijatý podle pokynů v tomto článku, sada SDK vytvoří spustitelný soubor (*. exe*), který se nazývá *spustitelný soubor závislý na rozhraní*.

Pokud používáte [webovou sadu SDK](#sdk), soubor *Web. config* , který je obvykle vytvořen při publikování aplikace ASP.NET Core, není pro aplikaci služby systému Windows zapotřebí. Chcete-li zakázat vytvoření souboru *Web. config* , přidejte `<IsTransformWebConfigDisabled>` vlastnost nastavenou na `true` .

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a>Samostatně uzavřené nasazení (SCD)

Samostatně zahrnuté nasazení (SCD) se nespoléhá na přítomnost sdílené architektury v hostitelském systému. Modul runtime a závislosti aplikace se nasazují s aplikací.

Identifikátor prostředí Windows [runtime (RID)](/dotnet/core/rid-catalog) je součástí `<PropertyGroup>` , který obsahuje cílovou architekturu:

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

Publikování pro několik identifikátorů RID:

* Zadejte identifikátorů RID do seznamu středníkem oddělených.
* Použijte název vlastnosti [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).

Další informace najdete v tématu [katalog identifikátorů RID .NET Core](/dotnet/core/rid-catalog).

## <a name="service-user-account"></a>Uživatelský účet služby

Chcete-li vytvořit uživatelský účet pro službu, použijte rutinu [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) z příkazového prostředí PowerShellu pro správu 6.

Ve Windows 10 říjen 2018 Update (verze 1809/Build 10.0.17763) nebo novější:

```powershell
New-LocalUser -Name {SERVICE NAME}
```

V operačním systému Windows starším než Windows 10 říjen 2018 Update (verze 1809/Build 10.0.17763):

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

Po zobrazení výzvy zadejte [silné heslo](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) .

Pokud `-AccountExpires` není parametr zadán rutině [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) s vypršenou platností <xref:System.DateTime> , platnost účtu nekončí.

Další informace najdete v tématu [uživatelské účty](/windows/desktop/services/service-user-accounts) [Microsoft. PowerShell. LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) a Service.

Alternativním přístupem ke správě uživatelů při používání služby Active Directory je použití účtů spravované služby. Další informace najdete v tématu [Přehled skupinových účtů spravované služby](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).

## <a name="log-on-as-a-service-rights"></a>Přihlášení jako práva služby

Chcete-li vytvořit oprávnění *Přihlásit se jako služba* pro uživatelský účet služby:

1. Spuštěním nástroje *secpol. msc*otevřete Editor místních zásad zabezpečení.
1. Rozbalte uzel **místní zásady** a vyberte **přiřazení uživatelských práv**.
1. Otevřete zásadu **Přihlásit se jako služba** .
1. Vyberte **Přidat uživatele nebo skupinu**.
1. Zadejte název objektu (uživatelský účet) pomocí některého z následujících přístupů:
   1. Do pole název objektu zadejte uživatelský účet ( `{DOMAIN OR COMPUTER NAME\USER}` ) a výběrem **OK** přidejte uživatele do zásad.
   1. Vyberte **Upřesnit**. Vyberte **Najít hned**. V seznamu vyberte uživatelský účet. Vyberte **OK**. Znovu vyberte **OK** a přidejte uživatele k zásadám.
1. Potvrďte změny kliknutím na **tlačítko OK** nebo **použít** .

## <a name="create-and-manage-the-windows-service"></a>Vytvoření a Správa služby systému Windows

### <a name="create-a-service"></a>Vytvoření služby

K registraci služby použijte příkazy prostředí PowerShell. V příkazovém prostředí PowerShellu pro správu 6 spusťte následující příkazy:

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* `{EXE PATH}`: Cesta ke složce aplikace na hostiteli (například `d:\myservice` ). Do cesty nezahrnujte spustitelný soubor aplikace. Koncové lomítko není vyžadováno.
* `{DOMAIN OR COMPUTER NAME\USER}`: Uživatelský účet služby (například `Contoso\ServiceUser` ).
* `{SERVICE NAME}`: Název služby (například `MyService` ).
* `{EXE FILE PATH}`: Cesta ke spustitelnému souboru aplikace (například `d:\myservice\myservice.exe` ). Zahrňte název spustitelného souboru s příponou.
* `{DESCRIPTION}`: Popis služby (například `My sample service` ).
* `{DISPLAY NAME}`: Zobrazovaný název služby (například `My Service` ).

### <a name="start-a-service"></a>Spuštění služby

Spusťte službu s následujícím příkazem prostředí PowerShell 6:

```powershell
Start-Service -Name {SERVICE NAME}
```

Spuštění služby trvá několik sekund.

### <a name="determine-a-services-status"></a>Určení stavu služby

Chcete-li zjistit stav služby, použijte následující příkaz prostředí PowerShell 6:

```powershell
Get-Service -Name {SERVICE NAME}
```

Stav je hlášen jako jedna z následujících hodnot:

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a>Zastavení služby

Zastavte službu pomocí následujícího příkazu PowerShellu 6:

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a>Odebrání služby

Po krátké prodlevě pro zastavení služby odeberte službu pomocí následujícího příkazu PowerShellu 6:

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="proxy-server-and-load-balancer-scenarios"></a>Scénáře proxy serveru a nástroje pro vyrovnávání zatížení

Služby, které komunikují s požadavky z Internetu nebo podnikové sítě a jsou za proxy serverem nebo nástrojem pro vyrovnávání zatížení, můžou vyžadovat další konfiguraci. Další informace naleznete v tématu <xref:host-and-deploy/proxy-load-balancer>.

## <a name="configure-endpoints"></a>Konfigurace koncových bodů

Ve výchozím nastavení ASP.NET Core váže `http://localhost:5000` . Nakonfigurujte adresu URL a port nastavením `ASPNETCORE_URLS` proměnné prostředí.

Další přístupy k adresám URL a konfiguraci portů najdete v příslušném článku na serveru:

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

Předchozí doprovodné materiály zahrnují podporu koncových bodů HTTPS. Například můžete nakonfigurovat aplikaci pro protokol HTTPS při použití ověřování u služby systému Windows.

> [!NOTE]
> Použití vývojového certifikátu HTTPS ASP.NET Core k zabezpečení koncového bodu služby se nepodporuje.

## <a name="current-directory-and-content-root"></a>Aktuální adresář a kořen obsahu

Aktuální pracovní adresář vrácený voláním <xref:System.IO.Directory.GetCurrentDirectory*> pro službu systému Windows je složka *C: \\ Windows \\ system32* . Složka *system32* není vhodným umístěním pro ukládání souborů služby (například souborů nastavení). Pomocí jednoho z následujících přístupů můžete spravovat a přistupovat k souborům assetů a nastavení služby.

### <a name="use-contentrootpath-or-contentrootfileprovider"></a>Použití ContentRootPath nebo ContentRootFileProvider

K vyhledání prostředků aplikace použijte [IHostEnvironment. ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) nebo <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> .

Když aplikace běží jako služba, nastaví na <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> [AppContext. BaseDirectory](xref:System.AppContext.BaseDirectory).

Soubory výchozích nastavení aplikace, *appSettings. JSON* a *appSettings. { Environment}. JSON*jsou načteny z kořenu obsahu aplikace voláním [CreateDefaultBuilder během vytváření hostitele](xref:fundamentals/host/generic-host#set-up-a-host).

Pro jiné soubory nastavení načtené vývojářským kódem v nástroji <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> není nutné volat <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> . V následujícím příkladu soubor *custom_settings. JSON* existuje v kořenu obsahu aplikace a je načtený bez explicitního nastavení základní cesty:

[!code-csharp[](windows-service/samples_snapshot/CustomSettingsExample.cs?highlight=13)]

Nepokoušejte se použít <xref:System.IO.Directory.GetCurrentDirectory*> k získání cesty prostředku, protože aplikace služby systému Windows vrátí složku *C: \\ Windows \\ system32* jako svůj aktuální adresář.

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a>Uložení souborů služby do vhodného umístění na disku

<xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>Při použití <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> do složky obsahující soubory zadejte absolutní cestu.

## <a name="troubleshoot"></a>Řešení potíží

Řešení potíží s aplikací služby systému Windows najdete v tématu <xref:test/troubleshoot> .

### <a name="common-errors"></a>Běžné chyby

* Stará nebo předběžná verze PowerShellu se používá.
* Registrovaná služba nepoužívá **publikovaný** výstup aplikace z příkazu [dotnet Publish](/dotnet/core/tools/dotnet-publish) . Výstup příkazu [dotnet Build](/dotnet/core/tools/dotnet-build) není pro nasazení aplikace podporovaný. Publikované assety se nacházejí v některé z následujících složek v závislosti na typu nasazení:
  * *bin/verze/{Target Framework}/Publish* (FDD)
  * *bin/verze/{Target Framework} identifikátor/{runtime}/Publish* (SCD)
* Služba není ve stavu SPUŠTĚNo.
* Cesty k prostředkům, které aplikace používá (například certifikáty), jsou nesprávné. Základní cesta služby systému Windows je *c: \\ Windows \\ system32*.
* Uživatel nemá oprávnění *Přihlásit se jako služba* .
* Při provádění příkazu PowerShellu vypršela platnost hesla uživatele nebo se předala nesprávně `New-Service` .
* Aplikace vyžaduje ASP.NET Core ověřování, ale není nakonfigurovaná pro zabezpečená připojení (HTTPS).
* Port adresy URL požadavku není správný nebo v aplikaci není správně nakonfigurovaný.

### <a name="system-and-application-event-logs"></a>Protokoly událostí systému a aplikace

Přístup k protokolům událostí systému a aplikace:

1. Otevřete nabídku Start, vyhledejte *Prohlížeč událostí*a vyberte aplikaci **Prohlížeč událostí** .
1. V **Prohlížeč událostí**otevřete uzel **protokoly systému Windows** .
1. Vyberte **systém** a otevřete protokol událostí systému. Výběrem **aplikace** otevřete protokol událostí aplikace.
1. Vyhledá chyby spojené s chybnou aplikací.

### <a name="run-the-app-at-a-command-prompt"></a>Spuštění aplikace na příkazovém řádku

Mnoho chyb při spuštění nevytváří užitečné informace v protokolech událostí. Příčinu některých chyb můžete najít spuštěním aplikace na příkazovém řádku v hostitelském systému. Pokud chcete protokolovat další podrobnosti z aplikace, snižte [úroveň protokolu](xref:fundamentals/logging/index#log-level) nebo spusťte aplikaci ve [vývojovém prostředí](xref:fundamentals/environments).

### <a name="clear-package-caches"></a>Vymazat mezipaměti balíčků

Funkční aplikace může po upgradu .NET Core SDK ve vývojovém počítači nebo změně verzí balíčku v rámci aplikace selhat okamžitě. V některých případech můžou nesouvislé balíčky při provádění hlavních upgradů poškodit aplikaci. Většinu těchto problémů můžete vyřešit pomocí následujících pokynů:

1. Odstraňte složky *bin* a *obj* .
1. Pomocí příkazu [dotnet All--Clear](/dotnet/core/tools/dotnet-nuget-locals) z příkazového prostředí vymažte mezipaměť balíčku.

   Mazání mezipamětí balíčků lze také provést pomocí nástroje [NuGet. exe](https://www.nuget.org/downloads) a provedením příkazu `nuget locals all -clear` . *NuGet. exe* není sada instalovaná instalace s desktopovým operačním systémem Windows a musí se získat samostatně z [webu NuGet](https://www.nuget.org/downloads).

1. Obnovte a znovu sestavte projekt.
1. Před opětovným nasazením aplikace odstraňte všechny soubory ve složce pro nasazení na serveru.

### <a name="slow-or-hanging-app"></a>Pomalá nebo zavěšená aplikace

*Výpis stavu* systému je snímek paměti systému a může vám pomůže určit příčinu selhání aplikace, selhání při spuštění nebo pomalé aplikace.

#### <a name="app-crashes-or-encounters-an-exception"></a>Aplikace selže nebo dojde k výjimce.

Získat a analyzovat výpis z [zasílání zpráv o chybách systému Windows (WER)](/windows/desktop/wer/windows-error-reporting):

1. Vytvořte složku uchovávající soubory s výpisem stavu systému `c:\dumps` .
1. Spusťte [skript PowerShellu EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) s názvem spustitelného souboru aplikace:

   ```powershell
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. Spusťte aplikaci za podmínek, které způsobí, že dojde k chybě.
1. Po chybě spusťte [skript PowerShellu DisableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1):

   ```powershell
   .\DisableDumps {APPLICATION EXE}
   ```

Po selhání aplikace a dokončení shromažďování výpisu je možné aplikaci ukončit normálně. Skript PowerShellu nakonfiguruje WER a shromáždí až pět výpisů paměti pro každou aplikaci.

> [!WARNING]
> Výpisy stavu systému můžou zabírat velké množství místa na disku (každý až několik gigabajtů).

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>Aplikace přestane reagovat, dojde k chybě při spuštění nebo se spustí normálně.

Když *aplikace přestane reagovat (zastaví* se, ale nejedná se o chybu), selže během spuštění nebo se spustí normálně, podívejte [se na soubory výpisu paměti v uživatelském režimu: zvolením nejlepšího](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) nástroje vyberte vhodný nástroj pro vytváření výpisu.

#### <a name="analyze-the-dump"></a>Analýza výpisu paměti

Výpis paměti lze analyzovat pomocí několika přístupů. Další informace najdete v tématu [Analýza souboru s výpisem stavu v uživatelském režimu](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).

## <a name="additional-resources"></a>Další zdroje

* [Konfigurace koncového bodu Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (zahrnuje konfiguraci HTTPS a podporu sni)
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Aplikace ASP.NET Core může být hostována ve Windows jako [služba systému Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) bez použití služby IIS. Po hostování jako služby systému Windows se aplikace automaticky spustí po restartování serveru.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Požadavky

* [ASP.NET Core SDK 2,1 nebo novější](https://dotnet.microsoft.com/download)
* [PowerShell 6,2 nebo novější](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a>Konfigurace aplikací

Aplikace vyžaduje odkazy na balíčky [Microsoft. AspNetCore. Hosting. WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) a [Microsoft. Extensions. Logging. EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).

Pokud chcete testovat a ladit spouštění mimo službu, přidejte kód, který určí, jestli je aplikace spuštěná jako služba nebo Konzolová aplikace. Zkontrolujte, zda je ladicí program připojen nebo zda `--console` je k dispozici přepínač. Pokud je jedna podmínka pravdivá (aplikace není spuštěna jako služba), zavolejte <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> . Pokud jsou podmínky nepravdivé (aplikace je spuštěná jako služba):

* Zavolejte <xref:System.IO.Directory.SetCurrentDirectory*> a použijte cestu k umístění publikované aplikace. Nevolejte <xref:System.IO.Directory.GetCurrentDirectory*> pro získání cesty, protože aplikace služby systému Windows vrátí složku *C: \\ Windows \\ system32* , když <xref:System.IO.Directory.GetCurrentDirectory*> je volána. Další informace najdete v oddílu [aktuální adresář a kořenový adresář obsahu](#current-directory-and-content-root) . Tento krok se provádí před konfigurací aplikace v nástroji `CreateWebHostBuilder` .
* Zavolejte <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> ke spuštění aplikace jako služby.

Vzhledem k tomu, že [zprostředkovatel konfigurace příkazového řádku](xref:fundamentals/configuration/index#command-line-configuration-provider) vyžaduje páry název-hodnota pro argumenty příkazového řádku, `--console` přepínač je odebrán z argumentů před <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> přijetím argumentů.

Chcete-li zapisovat do protokolu událostí systému Windows, přidejte zprostředkovatele EventLog do <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*> . Nastavte úroveň protokolování pomocí `Logging:LogLevel:Default` klíče v *appSettings. Soubor produkčního. JSON* .

V následujícím příkladu z ukázkové aplikace `RunAsCustomService` je volána místo <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> pro zpracování událostí životního cyklu v aplikaci. Další informace naleznete v části [popisovač spouštění a zastavování událostí](#handle-starting-and-stopping-events) .

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a>Typ nasazení

Informace a Rady týkající se scénářů nasazení najdete v tématu [nasazení aplikace .NET Core](/dotnet/core/deploying/).

### <a name="sdk"></a>Sada SDK

U služby založené na webové aplikaci, která používá Razor stránky nebo architektury MVC, zadejte webovou sadu SDK v souboru projektu:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Pokud služba spouští pouze úlohy na pozadí (například [hostované služby](xref:fundamentals/host/hosted-services)), zadejte sadu pracovních procesů sady SDK v souboru projektu:

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a>Nasazení závislé na rozhraní (FDD)

Nasazení závislé na rozhraní (FDD) spoléhá na přítomnost sdílené verze .NET Core v rámci systému v cílovém systému. Pokud je FDD scénář přijatý podle pokynů v tomto článku, sada SDK vytvoří spustitelný soubor (*. exe*), který se nazývá *spustitelný soubor závislý na rozhraní*.

Identifikátor prostředí Windows [runtime (RID)](/dotnet/core/rid-catalog) ( [\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier) ) obsahuje cílovou architekturu. V následujícím příkladu je identifikátor RID nastaven na `win7-x64` . `<SelfContained>`Vlastnost je nastavena na hodnotu `false` . Tyto vlastnosti instruují sadu SDK, aby vygenerovala spustitelný soubor (*. exe*) pro Windows a aplikaci, která závisí na sdílené platformě .NET Core.

Soubor *Web. config* , který je obvykle vytvořen při publikování aplikace ASP.NET Core, není pro aplikaci pro Windows nezbytný. Chcete-li zakázat vytvoření souboru *Web. config* , přidejte `<IsTransformWebConfigDisabled>` vlastnost nastavenou na `true` .

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a>Samostatně uzavřené nasazení (SCD)

Samostatně zahrnuté nasazení (SCD) se nespoléhá na přítomnost sdílené architektury v hostitelském systému. Modul runtime a závislosti aplikace se nasazují s aplikací.

Identifikátor prostředí Windows [runtime (RID)](/dotnet/core/rid-catalog) je součástí `<PropertyGroup>` , který obsahuje cílovou architekturu:

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

Publikování pro několik identifikátorů RID:

* Zadejte identifikátorů RID do seznamu středníkem oddělených.
* Použijte název vlastnosti [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).

Další informace najdete v tématu [katalog identifikátorů RID .NET Core](/dotnet/core/rid-catalog).

`<SelfContained>`Vlastnost je nastavena na hodnotu `true` :

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a>Uživatelský účet služby

Chcete-li vytvořit uživatelský účet pro službu, použijte rutinu [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) z příkazového prostředí PowerShellu pro správu 6.

Ve Windows 10 říjen 2018 Update (verze 1809/Build 10.0.17763) nebo novější:

```powershell
New-LocalUser -Name {SERVICE NAME}
```

V operačním systému Windows starším než Windows 10 říjen 2018 Update (verze 1809/Build 10.0.17763):

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

Po zobrazení výzvy zadejte [silné heslo](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) .

Pokud `-AccountExpires` není parametr zadán rutině [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) s vypršenou platností <xref:System.DateTime> , platnost účtu nekončí.

Další informace najdete v tématu [uživatelské účty](/windows/desktop/services/service-user-accounts) [Microsoft. PowerShell. LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) a Service.

Alternativním přístupem ke správě uživatelů při používání služby Active Directory je použití účtů spravované služby. Další informace najdete v tématu [Přehled skupinových účtů spravované služby](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).

## <a name="log-on-as-a-service-rights"></a>Přihlášení jako práva služby

Chcete-li vytvořit oprávnění *Přihlásit se jako služba* pro uživatelský účet služby:

1. Spuštěním nástroje *secpol. msc*otevřete Editor místních zásad zabezpečení.
1. Rozbalte uzel **místní zásady** a vyberte **přiřazení uživatelských práv**.
1. Otevřete zásadu **Přihlásit se jako služba** .
1. Vyberte **Přidat uživatele nebo skupinu**.
1. Zadejte název objektu (uživatelský účet) pomocí některého z následujících přístupů:
   1. Do pole název objektu zadejte uživatelský účet ( `{DOMAIN OR COMPUTER NAME\USER}` ) a výběrem **OK** přidejte uživatele do zásad.
   1. Vyberte **Upřesnit**. Vyberte **Najít hned**. V seznamu vyberte uživatelský účet. Vyberte **OK**. Znovu vyberte **OK** a přidejte uživatele k zásadám.
1. Potvrďte změny kliknutím na **tlačítko OK** nebo **použít** .

## <a name="create-and-manage-the-windows-service"></a>Vytvoření a Správa služby systému Windows

### <a name="create-a-service"></a>Vytvoření služby

K registraci služby použijte příkazy prostředí PowerShell. V příkazovém prostředí PowerShellu pro správu 6 spusťte následující příkazy:

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* `{EXE PATH}`: Cesta ke složce aplikace na hostiteli (například `d:\myservice` ). Do cesty nezahrnujte spustitelný soubor aplikace. Koncové lomítko není vyžadováno.
* `{DOMAIN OR COMPUTER NAME\USER}`: Uživatelský účet služby (například `Contoso\ServiceUser` ).
* `{SERVICE NAME}`: Název služby (například `MyService` ).
* `{EXE FILE PATH}`: Cesta ke spustitelnému souboru aplikace (například `d:\myservice\myservice.exe` ). Zahrňte název spustitelného souboru s příponou.
* `{DESCRIPTION}`: Popis služby (například `My sample service` ).
* `{DISPLAY NAME}`: Zobrazovaný název služby (například `My Service` ).

### <a name="start-a-service"></a>Spuštění služby

Spusťte službu s následujícím příkazem prostředí PowerShell 6:

```powershell
Start-Service -Name {SERVICE NAME}
```

Spuštění služby trvá několik sekund.

### <a name="determine-a-services-status"></a>Určení stavu služby

Chcete-li zjistit stav služby, použijte následující příkaz prostředí PowerShell 6:

```powershell
Get-Service -Name {SERVICE NAME}
```

Stav je hlášen jako jedna z následujících hodnot:

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a>Zastavení služby

Zastavte službu pomocí následujícího příkazu PowerShellu 6:

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a>Odebrání služby

Po krátké prodlevě pro zastavení služby odeberte službu pomocí následujícího příkazu PowerShellu 6:

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a>Zpracování událostí spouštění a zastavování

Pro zpracování <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*> <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> událostí, a <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> :

1. Vytvořte třídu, která je odvozena z <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> s `OnStarting` `OnStarted` `OnStopping` metodami, a:

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. Vytvořte metodu rozšíření pro <xref:Microsoft.AspNetCore.Hosting.IWebHost> , která předá `CustomWebHostService` <xref:System.ServiceProcess.ServiceBase.Run*> :

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. V `Program.Main` volejte `RunAsCustomService` metodu rozšíření místo <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> :

   ```csharp
   host.RunAsCustomService();
   ```

   Chcete-li zobrazit umístění <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> v nástroji `Program.Main` , podívejte se na ukázku kódu zobrazenou v části [typ nasazení](#deployment-type) .

## <a name="proxy-server-and-load-balancer-scenarios"></a>Scénáře proxy serveru a nástroje pro vyrovnávání zatížení

Služby, které komunikují s požadavky z Internetu nebo podnikové sítě a jsou za proxy serverem nebo nástrojem pro vyrovnávání zatížení, můžou vyžadovat další konfiguraci. Další informace naleznete v tématu <xref:host-and-deploy/proxy-load-balancer>.

## <a name="configure-endpoints"></a>Konfigurace koncových bodů

Ve výchozím nastavení ASP.NET Core váže `http://localhost:5000` . Nakonfigurujte adresu URL a port nastavením `ASPNETCORE_URLS` proměnné prostředí.

Další přístupy k adresám URL a konfiguraci portů najdete v příslušném článku na serveru:

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

Předchozí doprovodné materiály zahrnují podporu koncových bodů HTTPS. Například můžete nakonfigurovat aplikaci pro protokol HTTPS při použití ověřování u služby systému Windows.

> [!NOTE]
> Použití vývojového certifikátu HTTPS ASP.NET Core k zabezpečení koncového bodu služby se nepodporuje.

## <a name="current-directory-and-content-root"></a>Aktuální adresář a kořen obsahu

Aktuální pracovní adresář vrácený voláním <xref:System.IO.Directory.GetCurrentDirectory*> pro službu systému Windows je složka *C: \\ Windows \\ system32* . Složka *system32* není vhodným umístěním pro ukládání souborů služby (například souborů nastavení). Pomocí jednoho z následujících přístupů můžete spravovat a přistupovat k souborům assetů a nastavení služby.

### <a name="set-the-content-root-path-to-the-apps-folder"></a>Nastavení kořenové cesty obsahu ke složce aplikace

<xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*>Je stejná Cesta zadaná `binPath` argumentu při vytvoření služby. Namísto volání `GetCurrentDirectory` k vytváření cest k souborům nastavení volejte <xref:System.IO.Directory.SetCurrentDirectory*> s cestou k [kořenu obsahu](xref:fundamentals/index#content-root)aplikace.

V nástroji `Program.Main` Určete cestu ke složce spustitelného souboru služby a použijte cestu k vytvoření kořene obsahu aplikace:

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a>Uložení souborů služby do vhodného umístění na disku

<xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>Při použití <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> do složky obsahující soubory zadejte absolutní cestu.

## <a name="troubleshoot"></a>Řešení potíží

Řešení potíží s aplikací služby systému Windows najdete v tématu <xref:test/troubleshoot> .

### <a name="common-errors"></a>Běžné chyby

* Stará nebo předběžná verze PowerShellu se používá.
* Registrovaná služba nepoužívá **publikovaný** výstup aplikace z příkazu [dotnet Publish](/dotnet/core/tools/dotnet-publish) . Výstup příkazu [dotnet Build](/dotnet/core/tools/dotnet-build) není pro nasazení aplikace podporovaný. Publikované assety se nacházejí v některé z následujících složek v závislosti na typu nasazení:
  * *bin/verze/{Target Framework}/Publish* (FDD)
  * *bin/verze/{Target Framework} identifikátor/{runtime}/Publish* (SCD)
* Služba není ve stavu SPUŠTĚNo.
* Cesty k prostředkům, které aplikace používá (například certifikáty), jsou nesprávné. Základní cesta služby systému Windows je *c: \\ Windows \\ system32*.
* Uživatel nemá oprávnění *Přihlásit se jako služba* .
* Při provádění příkazu PowerShellu vypršela platnost hesla uživatele nebo se předala nesprávně `New-Service` .
* Aplikace vyžaduje ASP.NET Core ověřování, ale není nakonfigurovaná pro zabezpečená připojení (HTTPS).
* Port adresy URL požadavku není správný nebo v aplikaci není správně nakonfigurovaný.

### <a name="system-and-application-event-logs"></a>Protokoly událostí systému a aplikace

Přístup k protokolům událostí systému a aplikace:

1. Otevřete nabídku Start, vyhledejte *Prohlížeč událostí*a vyberte aplikaci **Prohlížeč událostí** .
1. V **Prohlížeč událostí**otevřete uzel **protokoly systému Windows** .
1. Vyberte **systém** a otevřete protokol událostí systému. Výběrem **aplikace** otevřete protokol událostí aplikace.
1. Vyhledá chyby spojené s chybnou aplikací.

### <a name="run-the-app-at-a-command-prompt"></a>Spuštění aplikace na příkazovém řádku

Mnoho chyb při spuštění nevytváří užitečné informace v protokolech událostí. Příčinu některých chyb můžete najít spuštěním aplikace na příkazovém řádku v hostitelském systému. Pokud chcete protokolovat další podrobnosti z aplikace, snižte [úroveň protokolu](xref:fundamentals/logging/index#log-level) nebo spusťte aplikaci ve [vývojovém prostředí](xref:fundamentals/environments).

### <a name="clear-package-caches"></a>Vymazat mezipaměti balíčků

Funkční aplikace může po upgradu .NET Core SDK ve vývojovém počítači nebo změně verzí balíčku v rámci aplikace selhat okamžitě. V některých případech můžou nesouvislé balíčky při provádění hlavních upgradů poškodit aplikaci. Většinu těchto problémů můžete vyřešit pomocí následujících pokynů:

1. Odstraňte složky *bin* a *obj* .
1. Pomocí příkazu [dotnet All--Clear](/dotnet/core/tools/dotnet-nuget-locals) z příkazového prostředí vymažte mezipaměť balíčku.

   Mazání mezipamětí balíčků lze také provést pomocí nástroje [NuGet. exe](https://www.nuget.org/downloads) a provedením příkazu `nuget locals all -clear` . *NuGet. exe* není sada instalovaná instalace s desktopovým operačním systémem Windows a musí se získat samostatně z [webu NuGet](https://www.nuget.org/downloads).

1. Obnovte a znovu sestavte projekt.
1. Před opětovným nasazením aplikace odstraňte všechny soubory ve složce pro nasazení na serveru.

### <a name="slow-or-hanging-app"></a>Pomalá nebo zavěšená aplikace

*Výpis stavu* systému je snímek paměti systému a může vám pomůže určit příčinu selhání aplikace, selhání při spuštění nebo pomalé aplikace.

#### <a name="app-crashes-or-encounters-an-exception"></a>Aplikace selže nebo dojde k výjimce.

Získat a analyzovat výpis z [zasílání zpráv o chybách systému Windows (WER)](/windows/desktop/wer/windows-error-reporting):

1. Vytvořte složku uchovávající soubory s výpisem stavu systému `c:\dumps` .
1. Spusťte [skript PowerShellu EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) s názvem spustitelného souboru aplikace:

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. Spusťte aplikaci za podmínek, které způsobí, že dojde k chybě.
1. Po chybě spusťte [skript PowerShellu DisableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

Po selhání aplikace a dokončení shromažďování výpisu je možné aplikaci ukončit normálně. Skript PowerShellu nakonfiguruje WER a shromáždí až pět výpisů paměti pro každou aplikaci.

> [!WARNING]
> Výpisy stavu systému můžou zabírat velké množství místa na disku (každý až několik gigabajtů).

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>Aplikace přestane reagovat, dojde k chybě při spuštění nebo se spustí normálně.

Když *aplikace přestane reagovat (zastaví* se, ale nejedná se o chybu), selže během spuštění nebo se spustí normálně, podívejte [se na soubory výpisu paměti v uživatelském režimu: zvolením nejlepšího](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) nástroje vyberte vhodný nástroj pro vytváření výpisu.

#### <a name="analyze-the-dump"></a>Analýza výpisu paměti

Výpis paměti lze analyzovat pomocí několika přístupů. Další informace najdete v tématu [Analýza souboru s výpisem stavu v uživatelském režimu](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).

## <a name="additional-resources"></a>Další zdroje

* [Konfigurace koncového bodu Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (zahrnuje konfiguraci HTTPS a podporu sni)
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Aplikace ASP.NET Core může být hostována ve Windows jako [služba systému Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) bez použití služby IIS. Po hostování jako služby systému Windows se aplikace automaticky spustí po restartování serveru.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Požadavky

* [ASP.NET Core SDK 2,1 nebo novější](https://dotnet.microsoft.com/download)
* [PowerShell 6,2 nebo novější](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a>Konfigurace aplikací

Aplikace vyžaduje odkazy na balíčky [Microsoft. AspNetCore. Hosting. WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) a [Microsoft. Extensions. Logging. EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).

Pokud chcete testovat a ladit spouštění mimo službu, přidejte kód, který určí, jestli je aplikace spuštěná jako služba nebo Konzolová aplikace. Zkontrolujte, zda je ladicí program připojen nebo zda `--console` je k dispozici přepínač. Pokud je jedna podmínka pravdivá (aplikace není spuštěna jako služba), zavolejte <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> . Pokud jsou podmínky nepravdivé (aplikace je spuštěná jako služba):

* Zavolejte <xref:System.IO.Directory.SetCurrentDirectory*> a použijte cestu k umístění publikované aplikace. Nevolejte <xref:System.IO.Directory.GetCurrentDirectory*> pro získání cesty, protože aplikace služby systému Windows vrátí složku *C: \\ Windows \\ system32* , když <xref:System.IO.Directory.GetCurrentDirectory*> je volána. Další informace najdete v oddílu [aktuální adresář a kořenový adresář obsahu](#current-directory-and-content-root) . Tento krok se provádí před konfigurací aplikace v nástroji `CreateWebHostBuilder` .
* Zavolejte <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> ke spuštění aplikace jako služby.

Vzhledem k tomu, že [zprostředkovatel konfigurace příkazového řádku](xref:fundamentals/configuration/index#command-line-configuration-provider) vyžaduje páry název-hodnota pro argumenty příkazového řádku, `--console` přepínač je odebrán z argumentů před <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> přijetím argumentů.

Chcete-li zapisovat do protokolu událostí systému Windows, přidejte zprostředkovatele EventLog do <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*> . Nastavte úroveň protokolování pomocí `Logging:LogLevel:Default` klíče v *appSettings. Soubor produkčního. JSON* .

V následujícím příkladu z ukázkové aplikace `RunAsCustomService` je volána místo <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> pro zpracování událostí životního cyklu v aplikaci. Další informace naleznete v části [popisovač spouštění a zastavování událostí](#handle-starting-and-stopping-events) .

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a>Typ nasazení

Informace a Rady týkající se scénářů nasazení najdete v tématu [nasazení aplikace .NET Core](/dotnet/core/deploying/).

### <a name="sdk"></a>Sada SDK

U služby založené na webové aplikaci, která používá Razor stránky nebo architektury MVC, zadejte webovou sadu SDK v souboru projektu:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Pokud služba spouští pouze úlohy na pozadí (například [hostované služby](xref:fundamentals/host/hosted-services)), zadejte sadu pracovních procesů sady SDK v souboru projektu:

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a>Nasazení závislé na rozhraní (FDD)

Nasazení závislé na rozhraní (FDD) spoléhá na přítomnost sdílené verze .NET Core v rámci systému v cílovém systému. Pokud je FDD scénář přijatý podle pokynů v tomto článku, sada SDK vytvoří spustitelný soubor (*. exe*), který se nazývá *spustitelný soubor závislý na rozhraní*.

Identifikátor prostředí Windows [runtime (RID)](/dotnet/core/rid-catalog) ( [\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier) ) obsahuje cílovou architekturu. V následujícím příkladu je identifikátor RID nastaven na `win7-x64` . `<SelfContained>`Vlastnost je nastavena na hodnotu `false` . Tyto vlastnosti instruují sadu SDK, aby vygenerovala spustitelný soubor (*. exe*) pro Windows a aplikaci, která závisí na sdílené platformě .NET Core.

`<UseAppHost>`Vlastnost je nastavena na hodnotu `true` . Tato vlastnost poskytuje službě aktivační cestu (spustitelný soubor *. exe*) pro FDD.

Soubor *Web. config* , který je obvykle vytvořen při publikování aplikace ASP.NET Core, není pro aplikaci pro Windows nezbytný. Chcete-li zakázat vytvoření souboru *Web. config* , přidejte `<IsTransformWebConfigDisabled>` vlastnost nastavenou na `true` .

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a>Samostatně uzavřené nasazení (SCD)

Samostatně zahrnuté nasazení (SCD) se nespoléhá na přítomnost sdílené architektury v hostitelském systému. Modul runtime a závislosti aplikace se nasazují s aplikací.

Identifikátor prostředí Windows [runtime (RID)](/dotnet/core/rid-catalog) je součástí `<PropertyGroup>` , který obsahuje cílovou architekturu:

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

Publikování pro několik identifikátorů RID:

* Zadejte identifikátorů RID do seznamu středníkem oddělených.
* Použijte název vlastnosti [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).

Další informace najdete v tématu [katalog identifikátorů RID .NET Core](/dotnet/core/rid-catalog).

`<SelfContained>`Vlastnost je nastavena na hodnotu `true` :

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a>Uživatelský účet služby

Chcete-li vytvořit uživatelský účet pro službu, použijte rutinu [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) z příkazového prostředí PowerShellu pro správu 6.

Ve Windows 10 říjen 2018 Update (verze 1809/Build 10.0.17763) nebo novější:

```powershell
New-LocalUser -Name {SERVICE NAME}
```

V operačním systému Windows starším než Windows 10 říjen 2018 Update (verze 1809/Build 10.0.17763):

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

Po zobrazení výzvy zadejte [silné heslo](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) .

Pokud `-AccountExpires` není parametr zadán rutině [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) s vypršenou platností <xref:System.DateTime> , platnost účtu nekončí.

Další informace najdete v tématu [uživatelské účty](/windows/desktop/services/service-user-accounts) [Microsoft. PowerShell. LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) a Service.

Alternativním přístupem ke správě uživatelů při používání služby Active Directory je použití účtů spravované služby. Další informace najdete v tématu [Přehled skupinových účtů spravované služby](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).

## <a name="log-on-as-a-service-rights"></a>Přihlášení jako práva služby

Chcete-li vytvořit oprávnění *Přihlásit se jako služba* pro uživatelský účet služby:

1. Spuštěním nástroje *secpol. msc*otevřete Editor místních zásad zabezpečení.
1. Rozbalte uzel **místní zásady** a vyberte **přiřazení uživatelských práv**.
1. Otevřete zásadu **Přihlásit se jako služba** .
1. Vyberte **Přidat uživatele nebo skupinu**.
1. Zadejte název objektu (uživatelský účet) pomocí některého z následujících přístupů:
   1. Do pole název objektu zadejte uživatelský účet ( `{DOMAIN OR COMPUTER NAME\USER}` ) a výběrem **OK** přidejte uživatele do zásad.
   1. Vyberte **Upřesnit**. Vyberte **Najít hned**. V seznamu vyberte uživatelský účet. Vyberte **OK**. Znovu vyberte **OK** a přidejte uživatele k zásadám.
1. Potvrďte změny kliknutím na **tlačítko OK** nebo **použít** .

## <a name="create-and-manage-the-windows-service"></a>Vytvoření a Správa služby systému Windows

### <a name="create-a-service"></a>Vytvoření služby

K registraci služby použijte příkazy prostředí PowerShell. V příkazovém prostředí PowerShellu pro správu 6 spusťte následující příkazy:

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* `{EXE PATH}`: Cesta ke složce aplikace na hostiteli (například `d:\myservice` ). Do cesty nezahrnujte spustitelný soubor aplikace. Koncové lomítko není vyžadováno.
* `{DOMAIN OR COMPUTER NAME\USER}`: Uživatelský účet služby (například `Contoso\ServiceUser` ).
* `{SERVICE NAME}`: Název služby (například `MyService` ).
* `{EXE FILE PATH}`: Cesta ke spustitelnému souboru aplikace (například `d:\myservice\myservice.exe` ). Zahrňte název spustitelného souboru s příponou.
* `{DESCRIPTION}`: Popis služby (například `My sample service` ).
* `{DISPLAY NAME}`: Zobrazovaný název služby (například `My Service` ).

### <a name="start-a-service"></a>Spuštění služby

Spusťte službu s následujícím příkazem prostředí PowerShell 6:

```powershell
Start-Service -Name {SERVICE NAME}
```

Spuštění služby trvá několik sekund.

### <a name="determine-a-services-status"></a>Určení stavu služby

Chcete-li zjistit stav služby, použijte následující příkaz prostředí PowerShell 6:

```powershell
Get-Service -Name {SERVICE NAME}
```

Stav je hlášen jako jedna z následujících hodnot:

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a>Zastavení služby

Zastavte službu pomocí následujícího příkazu PowerShellu 6:

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a>Odebrání služby

Po krátké prodlevě pro zastavení služby odeberte službu pomocí následujícího příkazu PowerShellu 6:

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a>Zpracování událostí spouštění a zastavování

Pro zpracování <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*> <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> událostí, a <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> :

1. Vytvořte třídu, která je odvozena z <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> s `OnStarting` `OnStarted` `OnStopping` metodami, a:

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. Vytvořte metodu rozšíření pro <xref:Microsoft.AspNetCore.Hosting.IWebHost> , která předá `CustomWebHostService` <xref:System.ServiceProcess.ServiceBase.Run*> :

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. V `Program.Main` volejte `RunAsCustomService` metodu rozšíření místo <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> :

   ```csharp
   host.RunAsCustomService();
   ```

   Chcete-li zobrazit umístění <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> v nástroji `Program.Main` , podívejte se na ukázku kódu zobrazenou v části [typ nasazení](#deployment-type) .

## <a name="proxy-server-and-load-balancer-scenarios"></a>Scénáře proxy serveru a nástroje pro vyrovnávání zatížení

Služby, které komunikují s požadavky z Internetu nebo podnikové sítě a jsou za proxy serverem nebo nástrojem pro vyrovnávání zatížení, můžou vyžadovat další konfiguraci. Další informace naleznete v tématu <xref:host-and-deploy/proxy-load-balancer>.

## <a name="configure-endpoints"></a>Konfigurace koncových bodů

Ve výchozím nastavení ASP.NET Core váže `http://localhost:5000` . Nakonfigurujte adresu URL a port nastavením `ASPNETCORE_URLS` proměnné prostředí.

Další přístupy k adresám URL a konfiguraci portů najdete v příslušném článku na serveru:

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

Předchozí doprovodné materiály zahrnují podporu koncových bodů HTTPS. Například můžete nakonfigurovat aplikaci pro protokol HTTPS při použití ověřování u služby systému Windows.

> [!NOTE]
> Použití vývojového certifikátu HTTPS ASP.NET Core k zabezpečení koncového bodu služby se nepodporuje.

## <a name="current-directory-and-content-root"></a>Aktuální adresář a kořen obsahu

Aktuální pracovní adresář vrácený voláním <xref:System.IO.Directory.GetCurrentDirectory*> pro službu systému Windows je složka *C: \\ Windows \\ system32* . Složka *system32* není vhodným umístěním pro ukládání souborů služby (například souborů nastavení). Pomocí jednoho z následujících přístupů můžete spravovat a přistupovat k souborům assetů a nastavení služby.

### <a name="set-the-content-root-path-to-the-apps-folder"></a>Nastavení kořenové cesty obsahu ke složce aplikace

<xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*>Je stejná Cesta zadaná `binPath` argumentu při vytvoření služby. Namísto volání `GetCurrentDirectory` k vytváření cest k souborům nastavení volejte <xref:System.IO.Directory.SetCurrentDirectory*> s cestou k [kořenu obsahu](xref:fundamentals/index#content-root)aplikace.

V nástroji `Program.Main` Určete cestu ke složce spustitelného souboru služby a použijte cestu k vytvoření kořene obsahu aplikace:

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a>Uložení souborů služby do vhodného umístění na disku

<xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>Při použití <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> do složky obsahující soubory zadejte absolutní cestu.

## <a name="troubleshoot"></a>Řešení potíží

Řešení potíží s aplikací služby systému Windows najdete v tématu <xref:test/troubleshoot> .

### <a name="common-errors"></a>Běžné chyby

* Stará nebo předběžná verze PowerShellu se používá.
* Registrovaná služba nepoužívá **publikovaný** výstup aplikace z příkazu [dotnet Publish](/dotnet/core/tools/dotnet-publish) . Výstup příkazu [dotnet Build](/dotnet/core/tools/dotnet-build) není pro nasazení aplikace podporovaný. Publikované assety se nacházejí v některé z následujících složek v závislosti na typu nasazení:
  * *bin/verze/{Target Framework}/Publish* (FDD)
  * *bin/verze/{Target Framework} identifikátor/{runtime}/Publish* (SCD)
* Služba není ve stavu SPUŠTĚNo.
* Cesty k prostředkům, které aplikace používá (například certifikáty), jsou nesprávné. Základní cesta služby systému Windows je *c: \\ Windows \\ system32*.
* Uživatel nemá oprávnění *Přihlásit se jako služba* .
* Při provádění příkazu PowerShellu vypršela platnost hesla uživatele nebo se předala nesprávně `New-Service` .
* Aplikace vyžaduje ASP.NET Core ověřování, ale není nakonfigurovaná pro zabezpečená připojení (HTTPS).
* Port adresy URL požadavku není správný nebo v aplikaci není správně nakonfigurovaný.

### <a name="system-and-application-event-logs"></a>Protokoly událostí systému a aplikace

Přístup k protokolům událostí systému a aplikace:

1. Otevřete nabídku Start, vyhledejte *Prohlížeč událostí*a vyberte aplikaci **Prohlížeč událostí** .
1. V **Prohlížeč událostí**otevřete uzel **protokoly systému Windows** .
1. Vyberte **systém** a otevřete protokol událostí systému. Výběrem **aplikace** otevřete protokol událostí aplikace.
1. Vyhledá chyby spojené s chybnou aplikací.

### <a name="run-the-app-at-a-command-prompt"></a>Spuštění aplikace na příkazovém řádku

Mnoho chyb při spuštění nevytváří užitečné informace v protokolech událostí. Příčinu některých chyb můžete najít spuštěním aplikace na příkazovém řádku v hostitelském systému. Pokud chcete protokolovat další podrobnosti z aplikace, snižte [úroveň protokolu](xref:fundamentals/logging/index#log-level) nebo spusťte aplikaci ve [vývojovém prostředí](xref:fundamentals/environments).

### <a name="clear-package-caches"></a>Vymazat mezipaměti balíčků

Funkční aplikace může po upgradu .NET Core SDK ve vývojovém počítači nebo změně verzí balíčku v rámci aplikace selhat okamžitě. V některých případech můžou nesouvislé balíčky při provádění hlavních upgradů poškodit aplikaci. Většinu těchto problémů můžete vyřešit pomocí následujících pokynů:

1. Odstraňte složky *bin* a *obj* .
1. Pomocí příkazu [dotnet All--Clear](/dotnet/core/tools/dotnet-nuget-locals) z příkazového prostředí vymažte mezipaměť balíčku.

   Mazání mezipamětí balíčků lze také provést pomocí nástroje [NuGet. exe](https://www.nuget.org/downloads) a provedením příkazu `nuget locals all -clear` . *NuGet. exe* není sada instalovaná instalace s desktopovým operačním systémem Windows a musí se získat samostatně z [webu NuGet](https://www.nuget.org/downloads).

1. Obnovte a znovu sestavte projekt.
1. Před opětovným nasazením aplikace odstraňte všechny soubory ve složce pro nasazení na serveru.

### <a name="slow-or-hanging-app"></a>Pomalá nebo zavěšená aplikace

*Výpis stavu* systému je snímek paměti systému a může vám pomůže určit příčinu selhání aplikace, selhání při spuštění nebo pomalé aplikace.

#### <a name="app-crashes-or-encounters-an-exception"></a>Aplikace selže nebo dojde k výjimce.

Získat a analyzovat výpis z [zasílání zpráv o chybách systému Windows (WER)](/windows/desktop/wer/windows-error-reporting):

1. Vytvořte složku uchovávající soubory s výpisem stavu systému `c:\dumps` .
1. Spusťte [skript PowerShellu EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) s názvem spustitelného souboru aplikace:

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. Spusťte aplikaci za podmínek, které způsobí, že dojde k chybě.
1. Po chybě spusťte [skript PowerShellu DisableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

Po selhání aplikace a dokončení shromažďování výpisu je možné aplikaci ukončit normálně. Skript PowerShellu nakonfiguruje WER a shromáždí až pět výpisů paměti pro každou aplikaci.

> [!WARNING]
> Výpisy stavu systému můžou zabírat velké množství místa na disku (každý až několik gigabajtů).

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>Aplikace přestane reagovat, dojde k chybě při spuštění nebo se spustí normálně.

Když *aplikace přestane reagovat (zastaví* se, ale nejedná se o chybu), selže během spuštění nebo se spustí normálně, podívejte [se na soubory výpisu paměti v uživatelském režimu: zvolením nejlepšího](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) nástroje vyberte vhodný nástroj pro vytváření výpisu.

#### <a name="analyze-the-dump"></a>Analýza výpisu paměti

Výpis paměti lze analyzovat pomocí několika přístupů. Další informace najdete v tématu [Analýza souboru s výpisem stavu v uživatelském režimu](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).

## <a name="additional-resources"></a>Další zdroje

* [Konfigurace koncového bodu Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (zahrnuje konfiguraci HTTPS a podporu sni)
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end

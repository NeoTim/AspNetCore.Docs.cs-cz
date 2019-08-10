---
title: ASP.NET Core hostitele ve službě systému Windows
author: guardrex
description: Naučte se hostovat aplikaci ASP.NET Core ve službě systému Windows.
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 06/03/2019
uid: host-and-deploy/windows-service
ms.openlocfilehash: 703edff19d82d10415bedb9b92d83709c275100f
ms.sourcegitcommit: 776367717e990bdd600cb3c9148ffb905d56862d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68913921"
---
# <a name="host-aspnet-core-in-a-windows-service"></a>ASP.NET Core hostitele ve službě systému Windows

[Luke Latham](https://github.com/guardrex) a [Dykstra](https://github.com/tdykstra)

Aplikace ASP.NET Core může být hostována ve Windows jako [služba systému Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) bez použití služby IIS. Po hostování jako služby systému Windows se aplikace automaticky spustí po restartování serveru.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/) ([stažení](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Požadavky

* [ASP.NET Core SDK 2,1 nebo novější](https://dotnet.microsoft.com/download)
* [PowerShell 6,2 nebo novější](https://github.com/PowerShell/PowerShell)

::: moniker range=">= aspnetcore-3.0"

## <a name="worker-service-template"></a>Šablona služby pracovní proces

Šablona služby ASP.NET Core Worker poskytuje výchozí bod pro psaní dlouhotrvajících aplikací služeb. Použití šablony jako základu pro aplikaci služby systému Windows:

1. Vytvořte aplikaci pracovní služby ze šablony .NET Core.
1. Podle pokynů v části [Konfigurace aplikace](#app-configuration) aktualizujte aplikaci pracovní služby pracovních procesů tak, aby mohla běžet jako služba systému Windows.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Vytvořte nový projekt.
1. Vyberte **webová aplikace ASP.NET Core**. Vyberte **Další**.
1. Do pole **název projektu** zadejte název projektu nebo přijměte výchozí název projektu. Vyberte **Vytvořit**.
1. V dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** potvrďte, že je vybrána možnost **.net Core** a **ASP.NET Core 3,0** .
1. Vyberte šablonu **služby pracovního procesu** . Vyberte **Vytvořit**.

# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

Použijte šablonu Worker Service`worker`() s příkazem [dotnet New](/dotnet/core/tools/dotnet-new) z příkazového prostředí. V následujícím příkladu se vytvoří aplikace pracovní služby s názvem `ContosoWorkerService`. Složka pro `ContosoWorkerService` aplikaci se vytvoří automaticky při spuštění příkazu.

```console
dotnet new worker -o ContosoWorkerService
```

---

::: moniker-end

## <a name="app-configuration"></a>Konfigurace aplikace

::: moniker range=">= aspnetcore-3.0"

`IHostBuilder.UseWindowsService`, poskytnutý balíčkem [Microsoft. Extensions. Hosting. WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices) , se volá při sestavování hostitele. Pokud je aplikace spuštěná jako služba systému Windows, metoda:

* Nastaví dobu života hostitele na `WindowsServiceLifetime`.
* Nastaví kořen obsahu.
* Povolí protokolování do protokolu událostí s názvem aplikace jako s výchozím názvem zdroje.
  * Úroveň protokolu se dá nakonfigurovat pomocí `Logging:LogLevel:Default` klíče v *appSettings. Soubor produkčního. JSON* .
  * Pouze správci mohou vytvářet nové zdroje událostí. Když zdroj události nejde vytvořit pomocí názvu aplikace, zaprotokoluje se upozornění na zdroj *aplikace* a protokoly událostí jsou zakázané.

[!code-csharp[](windows-service/samples/3.x/AspNetCoreService/Program.cs?name=snippet_Program)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Aplikace vyžaduje odkazy na balíčky [Microsoft. AspNetCore. Hosting. WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) a [Microsoft. Extensions. Logging. EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).

Pokud chcete testovat a ladit spouštění mimo službu, přidejte kód, který určí, jestli je aplikace spuštěná jako služba nebo Konzolová aplikace. Zkontrolujte, zda je ladicí program připojen nebo `--console` zda je k dispozici přepínač. Pokud je jedna podmínka pravdivá (aplikace není spuštěna jako služba), zavolejte <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>. Pokud jsou podmínky nepravdivé (aplikace je spuštěná jako služba):

* Zavolejte <xref:System.IO.Directory.SetCurrentDirectory*> a použijte cestu k umístění publikované aplikace. Nevolejte <xref:System.IO.Directory.GetCurrentDirectory*> pro získání cesty, protože aplikace služby systému Windows vrátí složku *C:\\Windows\\system32* , když <xref:System.IO.Directory.GetCurrentDirectory*> je volána. Další informace najdete v oddílu [aktuální adresář a kořenový adresář obsahu](#current-directory-and-content-root) . Tento krok se provádí před konfigurací aplikace v `CreateWebHostBuilder`nástroji.
* Zavolejte <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> ke spuštění aplikace jako služby.

Vzhledem k tomu, že [zprostředkovatel konfigurace příkazového řádku](xref:fundamentals/configuration/index#command-line-configuration-provider) vyžaduje páry název-hodnota pro argumenty příkazového `--console` řádku, přepínač je odebrán z argumentů <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> před přijetím argumentů.

Chcete-li zapisovat do protokolu událostí systému Windows, přidejte zprostředkovatele EventLog <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>do. Nastavte úroveň protokolování pomocí `Logging:LogLevel:Default` klíče v *appSettings. Soubor produkčního. JSON* .

V následujícím příkladu z ukázkové aplikace `RunAsCustomService` je volána <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> místo pro zpracování událostí životního cyklu v aplikaci. Další informace naleznete v části [popisovač spouštění a zastavování událostí](#handle-starting-and-stopping-events) .

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

::: moniker-end

## <a name="deployment-type"></a>Typ nasazení

Informace a Rady týkající se scénářů nasazení najdete v tématu [nasazení aplikace .NET Core](/dotnet/core/deploying/).

### <a name="framework-dependent-deployment-fdd"></a>Nasazení závislé na rozhraní (FDD)

Nasazení závislé na rozhraní (FDD) spoléhá na přítomnost sdílené verze .NET Core v rámci systému v cílovém systému. Pokud je FDD scénář přijatý podle pokynů v tomto článku, sada SDK vytvoří spustitelný soubor ( *. exe*), který se nazývá *spustitelný soubor závislý na rozhraní*.

::: moniker range=">= aspnetcore-3.0"

Do souboru projektu přidejte následující prvky vlastností:

* `<OutputType>`Typ výstupu aplikace (`Exe` pro spustitelný soubor). &ndash;
* `<LangVersion>`&ndash; Verze C# jazyka(`latest` nebo`preview`).

Soubor *Web. config* , který je obvykle vytvořen při publikování aplikace ASP.NET Core, není pro aplikaci pro Windows nezbytný. Chcete-li zakázat vytvoření souboru *Web. config* , přidejte `<IsTransformWebConfigDisabled>` vlastnost nastavenou na `true`.

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <OutputType>Exe</OutputType>
  <LangVersion>preview</LangVersion>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Identifikátor prostředí Windows [runtime (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier >](/dotnet/core/tools/csproj#runtimeidentifier)) obsahuje cílovou architekturu. V následujícím příkladu je identifikátor RID nastaven na `win7-x64`. Vlastnost je nastavena na `false`hodnotu. `<SelfContained>` Tyto vlastnosti instruují sadu SDK, aby vygenerovala spustitelný soubor ( *. exe*) pro Windows a aplikaci, která závisí na sdílené platformě .NET Core.

Soubor *Web. config* , který je obvykle vytvořen při publikování aplikace ASP.NET Core, není pro aplikaci pro Windows nezbytný. Chcete-li zakázat vytvoření souboru *Web. config* , přidejte `<IsTransformWebConfigDisabled>` vlastnost nastavenou na `true`.

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

::: moniker-end

::: moniker range="= aspnetcore-2.1"

Identifikátor prostředí Windows [runtime (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier >](/dotnet/core/tools/csproj#runtimeidentifier)) obsahuje cílovou architekturu. V následujícím příkladu je identifikátor RID nastaven na `win7-x64`. Vlastnost je nastavena na `false`hodnotu. `<SelfContained>` Tyto vlastnosti instruují sadu SDK, aby vygenerovala spustitelný soubor ( *. exe*) pro Windows a aplikaci, která závisí na sdílené platformě .NET Core.

Vlastnost je nastavena na `true`hodnotu. `<UseAppHost>` Tato vlastnost poskytuje službě aktivační cestu (spustitelný soubor *. exe*) pro FDD.

Soubor *Web. config* , který je obvykle vytvořen při publikování aplikace ASP.NET Core, není pro aplikaci pro Windows nezbytný. Chcete-li zakázat vytvoření souboru *Web. config* , přidejte `<IsTransformWebConfigDisabled>` vlastnost nastavenou na `true`.

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.1</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

::: moniker-end

### <a name="self-contained-deployment-scd"></a>Samostatně uzavřené nasazení (SCD)

Samostatně zahrnuté nasazení (SCD) se nespoléhá na přítomnost sdílené architektury v hostitelském systému. Modul runtime a závislosti aplikace se nasazují s aplikací.

Identifikátor prostředí Windows [runtime (RID)](/dotnet/core/rid-catalog) je součástí `<PropertyGroup>` , který obsahuje cílovou architekturu:

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

Publikování pro několik identifikátorů RID:

* Zadejte identifikátorů RID do seznamu středníkem oddělených.
* Použijte název [ \<vlastnosti RuntimeIdentifiers >](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).

Další informace najdete v tématu [katalog identifikátorů RID .NET Core](/dotnet/core/rid-catalog).

::: moniker range="< aspnetcore-3.0"

Vlastnost je nastavena na `true`hodnotu: `<SelfContained>`

```xml
<SelfContained>true</SelfContained>
```

::: moniker-end

## <a name="service-user-account"></a>Uživatelský účet služby

Chcete-li vytvořit uživatelský účet pro službu, použijte rutinu [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) z příkazového prostředí PowerShellu pro správu 6.

Ve Windows 10 říjen 2018 Update (verze 1809/Build 10.0.17763) nebo novější:

```PowerShell
New-LocalUser -Name {NAME}
```

V operačním systému Windows starším než Windows 10 říjen 2018 Update (verze 1809/Build 10.0.17763):

```console
powershell -Command "New-LocalUser -Name {NAME}"
```

Po zobrazení výzvy zadejte [silné heslo](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) .

Pokud není <xref:System.DateTime>parametr zadán rutině [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) s vypršenou platností, platnost účtu nekončí. `-AccountExpires`

Další informace najdete v tématu [uživatelské účty](/windows/desktop/services/service-user-accounts) [Microsoft. PowerShell. LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) a Service.

Alternativním přístupem ke správě uživatelů při používání služby Active Directory je použití účtů spravované služby. Další informace najdete v tématu [Přehled skupinových účtů spravované služby](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).

## <a name="log-on-as-a-service-rights"></a>Přihlášení jako práva služby

Chcete-li vytvořit oprávnění *Přihlásit se jako služba* pro uživatelský účet služby:

1. Spuštěním nástroje *secpol. msc*otevřete Editor místních zásad zabezpečení.
1. Rozbalte uzel **místní zásady** a vyberte **přiřazení uživatelských práv**.
1. Otevřete zásadu **Přihlásit se jako služba** .
1. Vyberte **Přidat uživatele nebo skupinu**.
1. Zadejte název objektu (uživatelský účet) pomocí některého z následujících přístupů:
   1. Do pole název objektu zadejte`{DOMAIN OR COMPUTER NAME\USER}`uživatelský účet () a výběrem **OK** přidejte uživatele do zásad.
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

New-Service -Name {NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* `{EXE PATH}`Cesta ke složce aplikace na hostiteli ( `d:\myservice`například). &ndash; Do cesty nezahrnujte spustitelný soubor aplikace. Koncové lomítko není vyžadováno.
* `{DOMAIN OR COMPUTER NAME\USER}`Uživatelský účet služby ( `Contoso\ServiceUser`například). &ndash;
* `{NAME}`Název služby ( `MyService`například). &ndash;
* `{EXE FILE PATH}`Cesta ke spustitelnému souboru aplikace ( `d:\myservice\myservice.exe`například). &ndash; Zahrňte název spustitelného souboru s příponou.
* `{DESCRIPTION}`Popis služby ( `My sample service`například). &ndash;
* `{DISPLAY NAME}`Zobrazovaný název služby ( `My Service`například). &ndash;

### <a name="start-a-service"></a>Spustit službu

Spusťte službu s následujícím příkazem prostředí PowerShell 6:

```powershell
Start-Service -Name {NAME}
```

Spuštění služby trvá několik sekund.

### <a name="determine-a-services-status"></a>Určení stavu služby

Chcete-li zjistit stav služby, použijte následující příkaz prostředí PowerShell 6:

```powershell
Get-Service -Name {NAME}
```

Stav je hlášen jako jedna z následujících hodnot:

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a>Zastavení služby

Zastavte službu pomocí následujícího příkazu PowerShellu 6:

```powershell
Stop-Service -Name {NAME}
```

### <a name="remove-a-service"></a>Odebrání služby

Po krátké prodlevě pro zastavení služby odeberte službu pomocí následujícího příkazu PowerShellu 6:

```powershell
Remove-Service -Name {NAME}
```

::: moniker range="< aspnetcore-3.0"

## <a name="handle-starting-and-stopping-events"></a>Zpracování událostí spouštění a zastavování

Pro zpracování <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>událostí <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, a <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> :

1. Vytvořte třídu, která je odvozena <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> z `OnStarting`s metodami, `OnStopping` `OnStarted`a:

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. Vytvořte metodu rozšíření pro <xref:Microsoft.AspNetCore.Hosting.IWebHost> , která `CustomWebHostService` předá <xref:System.ServiceProcess.ServiceBase.Run*>:

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. V `Program.Main` <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>volejte metodu rozšíření místo: `RunAsCustomService`

   ```csharp
   host.RunAsCustomService();
   ```

   Chcete-li zobrazit umístění <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> v `Program.Main`nástroji, podívejte se na ukázku kódu zobrazenou v části [typ nasazení](#deployment-type) .

::: moniker-end

## <a name="proxy-server-and-load-balancer-scenarios"></a>Proxy server a scénáře pro nástroj pro vyrovnávání zatížení

Služby, které komunikují s požadavky z Internetu nebo podnikové sítě a jsou za proxy serverem nebo nástrojem pro vyrovnávání zatížení, můžou vyžadovat další konfiguraci. Další informace naleznete v tématu <xref:host-and-deploy/proxy-load-balancer>.

## <a name="configure-https"></a>Konfigurace HTTPS

Konfigurace služby pomocí zabezpečeného koncového bodu:

1. Vytvořte certifikát X. 509 pro hostující systém pomocí mechanismů pro získání a nasazení certifikátů vaší platformy.

1. Zadejte [konfiguraci koncového bodu HTTPS serveru Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) pro použití certifikátu.

Použití vývojového certifikátu HTTPS ASP.NET Core k zabezpečení koncového bodu služby se nepodporuje.

## <a name="current-directory-and-content-root"></a>Aktuální adresář a kořen obsahu

Aktuální pracovní adresář vrácený voláním <xref:System.IO.Directory.GetCurrentDirectory*> pro službu systému Windows je složka *C:\\Windows\\system32* . Složka *system32* není vhodným umístěním pro ukládání souborů služby (například souborů nastavení). Pomocí jednoho z následujících přístupů můžete spravovat a přistupovat k souborům assetů a nastavení služby.

::: moniker range=">= aspnetcore-3.0"

### <a name="use-contentrootpath-or-contentrootfileprovider"></a>Použití ContentRootPath nebo ContentRootFileProvider

K vyhledání prostředků aplikace použijte <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> [IHostEnvironment. ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) nebo.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

### <a name="set-the-content-root-path-to-the-apps-folder"></a>Nastavení kořenové cesty obsahu ke složce aplikace

Je stejná Cesta zadaná `binPath` argumentu při vytvoření služby. <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> Namísto volání `GetCurrentDirectory` k vytváření cest k souborům nastavení volejte <xref:System.IO.Directory.SetCurrentDirectory*> s cestou k kořenu obsahu aplikace.

V `Program.Main`nástroji Určete cestu ke složce spustitelného souboru služby a použijte cestu k vytvoření kořene obsahu aplikace:

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

::: moniker-end

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a>Uložení souborů služby do vhodného umístění na disku

<xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> Při<xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> použití do složky obsahující soubory zadejte absolutní cestu.

## <a name="additional-resources"></a>Další zdroje

::: moniker range=">= aspnetcore-3.0"

* [Konfigurace koncového bodu Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (zahrnuje konfiguraci HTTPS a podporu SNI)
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [Konfigurace koncového bodu Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (zahrnuje konfiguraci HTTPS a podporu SNI)
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end

---
title: ASP.NET Core hostitele ve službě systému Windows
author: guardrex
description: Naučte se hostovat aplikaci ASP.NET Core ve službě systému Windows.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/30/2019
uid: host-and-deploy/windows-service
ms.openlocfilehash: 014585cd1e170fc94f7f577e11ec19824e54572f
ms.sourcegitcommit: 6628cd23793b66e4ce88788db641a5bbf470c3c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73659853"
---
# <a name="host-aspnet-core-in-a-windows-service"></a>ASP.NET Core hostitele ve službě systému Windows

Od [Luke Latham](https://github.com/guardrex)

Aplikace ASP.NET Core může být hostována ve Windows jako [služba systému Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) bez použití služby IIS. Po hostování jako služby systému Windows se aplikace automaticky spustí po restartování serveru.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Požadavky

* [ASP.NET Core SDK 2,1 nebo novější](https://dotnet.microsoft.com/download)
* [PowerShell 6,2 nebo novější](https://github.com/PowerShell/PowerShell)

::: moniker range=">= aspnetcore-3.0"

## <a name="worker-service-template"></a>Šablona služby pracovní proces

Šablona služby ASP.NET Core Worker poskytuje výchozí bod pro psaní dlouhotrvajících aplikací služeb. Použití šablony jako základu pro aplikaci služby systému Windows:

1. Vytvořte aplikaci pracovní služby ze šablony .NET Core.
1. Podle pokynů v části [Konfigurace aplikace](#app-configuration) aktualizujte aplikaci pracovní služby pracovních procesů tak, aby mohla běžet jako služba systému Windows.

[!INCLUDE[](~/includes/worker-template-instructions.md)]

::: moniker-end

## <a name="app-configuration"></a>Konfigurace aplikace

::: moniker range=">= aspnetcore-3.0"

Aplikace vyžaduje odkaz na balíček pro [Microsoft. Extensions. Hosting. WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).

`IHostBuilder.UseWindowsService` se volá při sestavování hostitele. Pokud je aplikace spuštěná jako služba systému Windows, metoda:

* Nastaví dobu života hostitele na hodnotu `WindowsServiceLifetime`.
* Nastaví [kořen obsahu](xref:fundamentals/index#content-root).
* Povolí protokolování do protokolu událostí s názvem aplikace jako s výchozím názvem zdroje.
  * Úroveň protokolu lze konfigurovat pomocí klíče `Logging:LogLevel:Default` v souboru *appSettings. Soubor produkčního. JSON* .
  * Pouze správci mohou vytvářet nové zdroje událostí. Když zdroj události nejde vytvořit pomocí názvu aplikace, zaprotokoluje se upozornění na zdroj *aplikace* a protokoly událostí jsou zakázané.

V `CreateHostBuilder` *program.cs*:

```csharp
Host.CreateDefaultBuilder(args)
    .UseWindowsService()
    ...
```

Toto téma doprovází následující ukázkové aplikace:

* Ukázka služby pracovní proces na pozadí &ndash; ukázka newebové aplikace v závislosti na [šabloně pracovní služby](#worker-service-template) , která používá [hostované služby](xref:fundamentals/host/hosted-services) pro úlohy na pozadí.
* Ukázka webového App Service &ndash; ukázkovou webovou aplikaci Razor Pages, která se spouští jako služba systému Windows s [hostovanými službami](xref:fundamentals/host/hosted-services) pro úlohy na pozadí.

Pokyny pro MVC najdete v článcích <xref:mvc/overview> a <xref:migration/22-to-30>.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Aplikace vyžaduje odkazy na balíčky [Microsoft. AspNetCore. Hosting. WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) a [Microsoft. Extensions. Logging. EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).

Pokud chcete testovat a ladit spouštění mimo službu, přidejte kód, který určí, jestli je aplikace spuštěná jako služba nebo Konzolová aplikace. Zkontrolujte, zda je ladicí program připojen nebo zda je k dispozici přepínač `--console`. Pokud má kterákoli podmínka hodnotu true (aplikace není spuštěná jako služba), zavolejte <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>. Pokud jsou podmínky nepravdivé (aplikace je spuštěná jako služba):

* Zavolejte <xref:System.IO.Directory.SetCurrentDirectory*> a použijte cestu k umístění publikované aplikace. Nevolejte <xref:System.IO.Directory.GetCurrentDirectory*> pro získání cesty, protože aplikace služby systému Windows vrátí složku *C:\\Windows\\system32* při volání <xref:System.IO.Directory.GetCurrentDirectory*>. Další informace najdete v oddílu [aktuální adresář a kořenový adresář obsahu](#current-directory-and-content-root) . Tento krok se provádí před konfigurací aplikace v `CreateWebHostBuilder`.
* Pokud chcete aplikaci spustit jako službu, zavolejte <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>.

Vzhledem k tomu, že [zprostředkovatel konfigurace příkazového řádku](xref:fundamentals/configuration/index#command-line-configuration-provider) vyžaduje páry název-hodnota pro argumenty příkazového řádku, přepínač `--console` je z argumentů odebraný, než <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> obdrží argumenty.

Chcete-li zapisovat do protokolu událostí systému Windows, přidejte zprostředkovatele EventLog do <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>. Nastavte úroveň protokolování pomocí klíče `Logging:LogLevel:Default` v souboru *appSettings. Soubor produkčního. JSON* .

V následujícím příkladu z ukázkové aplikace se volá `RunAsCustomService` místo <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>, aby bylo možné zpracovávat události životního cyklu v rámci aplikace. Další informace naleznete v části [popisovač spouštění a zastavování událostí](#handle-starting-and-stopping-events) .

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

::: moniker-end

## <a name="deployment-type"></a>Typ nasazení

Informace a Rady týkající se scénářů nasazení najdete v tématu [nasazení aplikace .NET Core](/dotnet/core/deploying/).

### <a name="sdk"></a>Sada SDK

U služby založené na webové aplikaci, která používá Razor Pages nebo architektury MVC, zadejte webovou sadu SDK v souboru projektu:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Pokud služba spouští pouze úlohy na pozadí (například [hostované služby](xref:fundamentals/host/hosted-services)), zadejte sadu pracovních procesů sady SDK v souboru projektu:

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a>Nasazení závislé na rozhraní (FDD)

Nasazení závislé na rozhraní (FDD) spoléhá na přítomnost sdílené verze .NET Core v rámci systému v cílovém systému. Pokud je FDD scénář přijatý podle pokynů v tomto článku, sada SDK vytvoří spustitelný soubor ( *. exe*), který se nazývá *spustitelný soubor závislý na rozhraní*.

::: moniker range=">= aspnetcore-3.0"

Pokud používáte [webovou sadu SDK](#sdk), soubor *Web. config* , který je obvykle vytvořen při publikování aplikace ASP.NET Core, není pro aplikaci služby systému Windows zapotřebí. Chcete-li zakázat vytvoření souboru *Web. config* , přidejte vlastnost `<IsTransformWebConfigDisabled>` nastavenou na `true`.

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Identifikátor prostředí Windows [runtime (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier >](/dotnet/core/tools/csproj#runtimeidentifier)) obsahuje cílovou architekturu. V následujícím příkladu je identifikátor RID nastaven na `win7-x64`. Vlastnost `<SelfContained>` je nastavena na hodnotu `false`. Tyto vlastnosti instruují sadu SDK, aby vygenerovala spustitelný soubor ( *. exe*) pro Windows a aplikaci, která závisí na sdílené platformě .NET Core.

Soubor *Web. config* , který je obvykle vytvořen při publikování aplikace ASP.NET Core, není pro aplikaci pro Windows nezbytný. Chcete-li zakázat vytvoření souboru *Web. config* , přidejte vlastnost `<IsTransformWebConfigDisabled>` nastavenou na `true`.

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

Identifikátor prostředí Windows [runtime (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier >](/dotnet/core/tools/csproj#runtimeidentifier)) obsahuje cílovou architekturu. V následujícím příkladu je identifikátor RID nastaven na `win7-x64`. Vlastnost `<SelfContained>` je nastavena na hodnotu `false`. Tyto vlastnosti instruují sadu SDK, aby vygenerovala spustitelný soubor ( *. exe*) pro Windows a aplikaci, která závisí na sdílené platformě .NET Core.

Vlastnost `<UseAppHost>` je nastavena na hodnotu `true`. Tato vlastnost poskytuje službě aktivační cestu (spustitelný soubor *. exe*) pro FDD.

Soubor *Web. config* , který je obvykle vytvořen při publikování aplikace ASP.NET Core, není pro aplikaci pro Windows nezbytný. Chcete-li zakázat vytvoření souboru *Web. config* , přidejte vlastnost `<IsTransformWebConfigDisabled>` nastavenou na `true`.

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

::: moniker-end

### <a name="self-contained-deployment-scd"></a>Samostatně uzavřené nasazení (SCD)

Samostatně zahrnuté nasazení (SCD) se nespoléhá na přítomnost sdílené architektury v hostitelském systému. Modul runtime a závislosti aplikace se nasazují s aplikací.

Identifikátor prostředí Windows [runtime (RID)](/dotnet/core/rid-catalog) je součástí `<PropertyGroup>` obsahujícího cílové rozhraní:

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

Publikování pro několik identifikátorů RID:

* Zadejte identifikátorů RID do seznamu středníkem oddělených.
* Použijte název vlastnosti [\<RuntimeIdentifiers >](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).

Další informace najdete v tématu [katalog identifikátorů RID .NET Core](/dotnet/core/rid-catalog).

::: moniker range="< aspnetcore-3.0"

Vlastnost `<SelfContained>` je nastavena na hodnotu `true`:

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

Pokud rutina [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) nezadá parametr `-AccountExpires` s vypršením <xref:System.DateTime>, platnost účtu nekončí.

Další informace najdete v tématu [uživatelské účty](/windows/desktop/services/service-user-accounts) [Microsoft. PowerShell. LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) a Service.

Alternativním přístupem ke správě uživatelů při používání služby Active Directory je použití účtů spravované služby. Další informace najdete v tématu [Přehled skupinových účtů spravované služby](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).

## <a name="log-on-as-a-service-rights"></a>Přihlášení jako práva služby

Chcete-li vytvořit oprávnění *Přihlásit se jako služba* pro uživatelský účet služby:

1. Spuštěním nástroje *secpol. msc*otevřete Editor místních zásad zabezpečení.
1. Rozbalte uzel **místní zásady** a vyberte **přiřazení uživatelských práv**.
1. Otevřete zásadu **Přihlásit se jako služba** .
1. Vyberte **Přidat uživatele nebo skupinu**.
1. Zadejte název objektu (uživatelský účet) pomocí některého z následujících přístupů:
   1. Zadejte uživatelský účet (`{DOMAIN OR COMPUTER NAME\USER}`) do pole název objektu a výběrem **OK** přidejte uživatele do zásad.
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

* `{EXE PATH}` &ndash; cesta k složce aplikace na hostiteli (například `d:\myservice`). Do cesty nezahrnujte spustitelný soubor aplikace. Koncové lomítko není vyžadováno.
* uživatelský účet služby `{DOMAIN OR COMPUTER NAME\USER}` &ndash; (například `Contoso\ServiceUser`).
* `{NAME}` název služby &ndash; (například `MyService`).
* `{EXE FILE PATH}` &ndash; cesta ke spustitelnému souboru aplikace (například `d:\myservice\myservice.exe`). Zahrňte název spustitelného souboru s příponou.
* `{DESCRIPTION}` Popis služby &ndash; (například `My sample service`).
* `{DISPLAY NAME}` zobrazovaný název služby &ndash; (například `My Service`).

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

Zpracování událostí <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> a <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*>:

1. Vytvořte třídu, která je odvozena z <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> pomocí metod `OnStarting`, `OnStarted` a `OnStopping`:

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. Vytvořte metodu rozšíření pro <xref:Microsoft.AspNetCore.Hosting.IWebHost>, která předá `CustomWebHostService` do <xref:System.ServiceProcess.ServiceBase.Run*>:

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. V `Program.Main` zavolejte metodu rozšíření `RunAsCustomService` namísto <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:

   ```csharp
   host.RunAsCustomService();
   ```

   Chcete-li zobrazit umístění <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> v `Program.Main`, přečtěte si ukázku kódu zobrazenou v části [typ nasazení](#deployment-type) .

::: moniker-end

## <a name="proxy-server-and-load-balancer-scenarios"></a>Scénáře proxy serveru a nástroje pro vyrovnávání zatížení

Služby, které komunikují s požadavky z Internetu nebo podnikové sítě a jsou za proxy serverem nebo nástrojem pro vyrovnávání zatížení, můžou vyžadovat další konfiguraci. Další informace najdete v tématu <xref:host-and-deploy/proxy-load-balancer>.

## <a name="configure-endpoints"></a>Konfigurace koncových bodů

Ve výchozím nastavení ASP.NET Core váže `http://localhost:5000`. Nakonfigurujte adresu URL a port nastavením proměnné prostředí `ASPNETCORE_URLS`.

Další přístupy k adresám URL a konfiguraci portů najdete v příslušném článku na serveru:

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

Předchozí doprovodné materiály zahrnují podporu koncových bodů HTTPS. Například můžete nakonfigurovat aplikaci pro protokol HTTPS při použití ověřování u služby systému Windows.

> [!NOTE]
> Použití vývojového certifikátu HTTPS ASP.NET Core k zabezpečení koncového bodu služby se nepodporuje.

## <a name="current-directory-and-content-root"></a>Aktuální adresář a kořen obsahu

Aktuální pracovní adresář vrácený voláním <xref:System.IO.Directory.GetCurrentDirectory*> pro službu systému Windows je složka *C:\\Windows\\system32* . Složka *system32* není vhodným umístěním pro ukládání souborů služby (například souborů nastavení). Pomocí jednoho z následujících přístupů můžete spravovat a přistupovat k souborům assetů a nastavení služby.

::: moniker range=">= aspnetcore-3.0"

### <a name="use-contentrootpath-or-contentrootfileprovider"></a>Použití ContentRootPath nebo ContentRootFileProvider

Vyhledejte prostředky aplikace pomocí [IHostEnvironment. ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) nebo <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider>.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

### <a name="set-the-content-root-path-to-the-apps-folder"></a>Nastavení kořenové cesty obsahu ke složce aplikace

<xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> je při vytvoření služby stejná cesta k argumentu `binPath`. Namísto volání `GetCurrentDirectory` pro vytváření cest k souborům nastavení volejte <xref:System.IO.Directory.SetCurrentDirectory*> s cestou k [kořenu obsahu](xref:fundamentals/index#content-root)aplikace.

V `Program.Main` Určete cestu ke složce spustitelného souboru služby a použijte cestu k vytvoření kořene obsahu aplikace:

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

Zadejte absolutní cestu s <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> při použití <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> do složky obsahující soubory.

## <a name="additional-resources"></a>Další zdroje

::: moniker range=">= aspnetcore-3.0"

* [Konfigurace koncového bodu Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (zahrnuje konfiguraci HTTPS a podporu sni)
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [Konfigurace koncového bodu Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (zahrnuje konfiguraci HTTPS a podporu sni)
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end

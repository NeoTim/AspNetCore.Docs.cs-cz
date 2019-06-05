---
title: Hostitele ASP.NET Core ve službě Windows
author: guardrex
description: Zjistěte, jak hostovat aplikace ASP.NET Core ve službě Windows.
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 06/03/2019
uid: host-and-deploy/windows-service
ms.openlocfilehash: 4cfca4b38543ff073bb98dc09b483d96096928ae
ms.sourcegitcommit: 5dd2ce9709c9e41142771e652d1a4bd0b5248cec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2019
ms.locfileid: "66692570"
---
# <a name="host-aspnet-core-in-a-windows-service"></a>Hostitele ASP.NET Core ve službě Windows

Podle [Luke Latham](https://github.com/guardrex) a [Petr Dykstra](https://github.com/tdykstra)

Na Windows, jako je možné hostovat aplikace ASP.NET Core [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) bez použití služby IIS. Pokud hostovaný jako služba Windows, aplikace se automaticky spustí po restartování serveru.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/) ([stažení](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Požadavky

* [ASP.NET Core SDK 2.1 nebo novější](https://dotnet.microsoft.com/download)
* [Prostředí PowerShell 6.2 nebo novější](https://github.com/PowerShell/PowerShell)

::: moniker range=">= aspnetcore-3.0"

## <a name="worker-service-template"></a>Šablona služby pracovního procesu

Šablony ASP.NET Core pracovního procesu služby poskytuje výchozí bod pro psaní dlouho běžící aplikace služby. Použití šablony jako základ pro aplikace Windows Service:

1. Vytvoření aplikace služby pracovního procesu v šabloně .NET Core.
1. Postupujte podle pokynů v [konfigurace aplikace](#app-configuration) část aktualizace aplikace služby pracovního procesu, tak, aby mohly běžet jako služba Windows.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Vytvořte nový projekt.
1. Vyberte **webová aplikace ASP.NET Core**. Vyberte **Další**.
1. Zadejte název projektu **název projektu** pole nebo přijměte výchozí název projektu. Vyberte **Vytvořit**.
1. V **vytvořit novou webovou aplikaci ASP.NET Core** dialogového okna, ujistěte se, že **.NET Core** a **ASP.NET Core 3.0** jsou vybrány.
1. Vyberte **služby pracovního procesu** šablony. Vyberte **Vytvořit**.

# <a name="visual-studio-code--net-core-clitabvisual-studio-codenetcore-cli"></a>[Visual Studio Code / .NET Core CLI](#tab/visual-studio-code+netcore-cli)

Použití služby pracovního procesu (`worker`) šablony s [dotnet nové](/dotnet/core/tools/dotnet-new) z příkazové okno. V následujícím příkladu se vytvoří aplikace služby pracovního procesu pojmenované `ContosoWorkerService`. Složka pro `ContosoWorkerService` aplikace se vytvoří automaticky při spuštění příkazu.

```console
dotnet new worker -o ContosoWorkerService
```

---

::: moniker-end

## <a name="app-configuration"></a>Konfigurace aplikace

::: moniker range=">= aspnetcore-3.0"

`IHostBuilder.UseWindowsService`, poskytuje [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices) balíček, je volána, když vytváření hostitele. Pokud aplikace běží jako služba Windows, metodu:

* Nastaví životnost hostitele `WindowsServiceLifetime`.
* Nastaví kořenové obsahu.
* Povolí protokolování do protokolu událostí s názvem aplikace jako výchozí název zdroje.
  * Úroveň protokolu můžete konfigurovat pomocí `Logging:LogLevel:Default` klíče v *appsettings. Production.JSON* souboru.
  * Pouze správci mohou vytvářet nové zdroje událostí. Když zdroj událostí nelze vytvořit pomocí názvu aplikace, bude zaznamenána upozornění *aplikace* zdroje a protokoly událostí jsou zakázané.

[!code-csharp[](windows-service/samples/3.x/AspNetCoreService/Program.cs?name=snippet_Program)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Aplikace vyžaduje odkazy na balíček pro [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) a [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).

K testování a ladění, když se provozují mimo službu, přidání kódu k určení, jestli aplikace běží jako službu nebo konzolové aplikace. Kontrola, pokud je připojen ladicí program nebo `--console` přepínači je k dispozici. Pokud je některá podmínka pravdivá (aplikace není spuštěna jako služba), volání <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>. Pokud jsou podmínky hodnotu false (aplikace je spuštěn jako služba):

* Volání <xref:System.IO.Directory.SetCurrentDirectory*> a použijte cestu k umístění publikované aplikace. Nevolejte <xref:System.IO.Directory.GetCurrentDirectory*> získat cestu, protože aplikace Windows Service vrátí *C:\\WINDOWS\\system32* složky při <xref:System.IO.Directory.GetCurrentDirectory*> je volána. Další informace najdete v tématu [aktuálního adresáře a kořenový adresář obsahu](#current-directory-and-content-root) oddílu. Tento krok se provádí před aplikace podle konfigurace v `CreateWebHostBuilder`.
* Volání <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> a spusťte tak aplikaci jako službu.

Protože [poskytovatele konfigurace příkazového řádku](xref:fundamentals/configuration/index#command-line-configuration-provider) vyžaduje páry název hodnota pro argumenty příkazového řádku, `--console` přepínače se odebere z argumentů před <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> přijímá argumenty.

Zapsat do protokolu událostí Windows, přidejte zprostředkovatele protokolu událostí na <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>. Nastavení úrovně protokolování s `Logging:LogLevel:Default` klíče v *appsettings. Production.JSON* souboru.

V následujícím příkladu z ukázkové aplikace `RunAsCustomService` se nazývá místo <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> ke zpracování událostí životního cyklu v rámci aplikace. Další informace najdete v tématu [zpracování, spouštění a zastavování události](#handle-starting-and-stopping-events) oddílu.

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

::: moniker-end

## <a name="deployment-type"></a>Typ nasazení

Informace a Rady, scénáře nasazení najdete v tématu [nasazení aplikace .NET Core](/dotnet/core/deploying/).

### <a name="framework-dependent-deployment-fdd"></a>Nasazení závisí na architektuře (chyba)

Nasazení závisí na architektuře (chyba) spoléhá na přítomnost sdílené systémová verzi .NET Core v cílovém systému. Přijatá chyba scénář je následující pokyny v tomto článku, sady SDK vytvoří spustitelný soubor ( *.exe*), označované jako *spustitelného souboru závisí na architektuře*.

::: moniker range=">= aspnetcore-3.0"

Přidejte následující prvky vlastnosti do souboru projektu:

* `<OutputType>` &ndash; Typ výstupní aplikace (`Exe` pro spustitelný soubor).
* `<LangVersion>` &ndash; C# Jazykovou verzi (`latest` nebo `preview`).

A *web.config* soubor, který je obvykle vytvořen při publikování aplikace ASP.NET Core, není nutné pro aplikaci služby Windows. Chcete-li zakázat vytváření *web.config* přidejte `<IsTransformWebConfigDisabled>` nastavenou na `true`.

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

Windows [identifikátor modulu Runtime (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier >](/dotnet/core/tools/csproj#runtimeidentifier)) obsahuje cílovou architekturu. V následujícím příkladu RID nastavena `win7-x64`. `<SelfContained>` Je nastavena na `false`. Tyto vlastnosti dáte pokyn, aby sada SDK pro generování spustitelného souboru ( *.exe*) soubor pro Windows a aplikace, která závisí na sdílené .NET Core framework.

A *web.config* soubor, který je obvykle vytvořen při publikování aplikace ASP.NET Core, není nutné pro aplikaci služby Windows. Chcete-li zakázat vytváření *web.config* přidejte `<IsTransformWebConfigDisabled>` nastavenou na `true`.

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

Windows [identifikátor modulu Runtime (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier >](/dotnet/core/tools/csproj#runtimeidentifier)) obsahuje cílovou architekturu. V následujícím příkladu RID nastavena `win7-x64`. `<SelfContained>` Je nastavena na `false`. Tyto vlastnosti dáte pokyn, aby sada SDK pro generování spustitelného souboru ( *.exe*) soubor pro Windows a aplikace, která závisí na sdílené .NET Core framework.

`<UseAppHost>` Je nastavena na `true`. Tato vlastnost poskytuje službu s cestou aktivace (spustitelný soubor, *.exe*) pro disketové jednotky.

A *web.config* soubor, který je obvykle vytvořen při publikování aplikace ASP.NET Core, není nutné pro aplikaci služby Windows. Chcete-li zakázat vytváření *web.config* přidejte `<IsTransformWebConfigDisabled>` nastavenou na `true`.

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

### <a name="self-contained-deployment-scd"></a>Samostatná nasazení (SCD)

Samostatná nasazení (SCD) nemusí spoléhat na přítomnost sdílené architektuře v hostitelském systému. Modul runtime a závislostí aplikace se nasadí s aplikací.

Windows [identifikátor modulu Runtime (RID)](/dotnet/core/rid-catalog) je součástí `<PropertyGroup>` , který obsahuje Cílová architektura:

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

Chcete-li publikovat pro více identifikátorů RID:

* Zadejte identifikátory RID v seznam oddělený středníkem.
* Použijte název vlastnosti [ \<RuntimeIdentifiers >](/dotnet/core/tools/csproj#runtimeidentifiers) (množné číslo).

Další informace najdete v tématu [katalog identifikátorů RID .NET Core](/dotnet/core/rid-catalog).

::: moniker range="< aspnetcore-3.0"

A `<SelfContained>` je nastavena na `true`:

```xml
<SelfContained>true</SelfContained>
```

::: moniker-end

## <a name="service-user-account"></a>Uživatelský účet služby

Chcete-li vytvořit uživatelský účet pro službu, použijte [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) rutiny z správu příkazové okno Powershellu 6.

Ve Windows 10. října 2018 Update (verze 1809/sestavení 10.0.17763) nebo novější:

```PowerShell
New-LocalUser -Name {NAME}
```

V operačním systému Windows starší než Windows 10. října 2018 Update (verze 1809/sestavení 10.0.17763):

```console
powershell -Command "New-LocalUser -Name {NAME}"
```

Zadejte [silné heslo](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) po zobrazení výzvy.

Pokud `-AccountExpires` parametr zadaný [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) rutinu s vypršení <xref:System.DateTime>, účet platnost pasu nevyprší.

Další informace najdete v tématu [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) a [uživatelské účty služby](/windows/desktop/services/service-user-accounts).

Alternativní způsob správy uživatelů při používání služby Active Directory je použití účtů spravované služby. Další informace najdete v tématu [přehled účtů spravované služby skupiny](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).

## <a name="log-on-as-a-service-rights"></a>Přihlaste se jako práva služby

K navázání *přihlásit jako službu* práva pro uživatelský účet služby:

1. Otevřete editor místních zásad zabezpečení, že spustíte *secpool.msc*.
1. Rozbalte **místní zásady** uzel a vyberte možnost **přiřazení uživatelských práv**.
1. Otevřít **přihlásit jako službu** zásad.
1. Vyberte **přidat uživatele nebo skupinu**.
1. Zadejte název objektu (uživatelský účet) některou z následujících postupů:
   1. Zadejte uživatelský účet (`{DOMAIN OR COMPUTER NAME\USER}`) v objektu název pole a vyberte **OK** přidejte uživatele k zásadám.
   1. Vyberte **Upřesnit**. Vyberte **najít**. Vyberte uživatelský účet ze seznamu. Vyberte **OK**. Vyberte **OK** opakujte k přidání uživatele do zásad.
1. Vyberte **OK** nebo **použít** změny uložte.

## <a name="create-and-manage-the-windows-service"></a>Vytvořit a spravovat službu Windows

### <a name="create-a-service"></a>Vytvoření služby

Použijte příkazy prostředí PowerShell pro registraci služby. Z pro správu příkazové okno Powershellu 6 spusťte následující příkazy:

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* `{EXE PATH}` &ndash; Cesta ke složce aplikace na hostiteli (například `d:\myservice`). V cestě nezahrnují spustitelný soubor aplikace. Koncové lomítko není povinné.
* `{DOMAIN OR COMPUTER NAME\USER}` &ndash; Uživatelský účet služby (například `Contoso\ServiceUser`).
* `{NAME}` &ndash; Název služby (například `MyService`).
* `{EXE FILE PATH}` &ndash; Cesta ke spustitelnému souboru aplikace (například `d:\myservice\myservice.exe`). Zahrnovat název souboru spustitelný soubor s příponou.
* `{DESCRIPTION}` &ndash; Popis služby (například `My sample service`).
* `{DISPLAY NAME}` &ndash; Zobrazovaný název služby (například `My Service`).

### <a name="start-a-service"></a>Spuštění služby

Spuštění služby pomocí následujícího příkazu Powershellu 6:

```powershell
Start-Service -Name {NAME}
```

Příkaz trvá několik sekund se spustit službu.

### <a name="determine-a-services-status"></a>Zjistit stav služby

Pokud chcete zkontrolovat stav služby, použijte následující příkaz Powershellu 6:

```powershell
Get-Service -Name {NAME}
```

Stav je uveden jako jeden z následujících hodnot:

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a>Zastavit službu

Zastavte službu pomocí následujícího příkazu Powershellu 6:

```powershell
Stop-Service -Name {NAME}
```

### <a name="remove-a-service"></a>Odebrat službu

Po krátké prodlevě zastavit službu odeberte službu pomocí následujícího příkazu Powershellu 6:

```powershell
Remove-Service -Name {NAME}
```

::: moniker range="< aspnetcore-3.0"

## <a name="handle-starting-and-stopping-events"></a>Zpracování spuštění a zastavení událostí

Pro zpracování <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, a <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> události:

1. Vytvořte třídu, která je odvozena z <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> s `OnStarting`, `OnStarted`, a `OnStopping` metody:

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. Vytvořit metodu rozšíření pro <xref:Microsoft.AspNetCore.Hosting.IWebHost> , který předá `CustomWebHostService` k <xref:System.ServiceProcess.ServiceBase.Run*>:

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. V `Program.Main`, zavolejte `RunAsCustomService` místo rozšiřující metoda <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:

   ```csharp
   host.RunAsCustomService();
   ```

   Pokud chcete zobrazit umístění <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> v `Program.Main`, najdete vzorový kód uvedený v [typ nasazení](#deployment-type) oddílu.

::: moniker-end

## <a name="proxy-server-and-load-balancer-scenarios"></a>Proxy server a scénáře pro nástroj pro vyrovnávání zatížení

Služby, které interakci s žádostí z Internetu nebo podnikové síti a jsou za proxy nebo nástroj pro vyrovnávání zatížení může vyžadovat dodatečnou konfiguraci. Další informace naleznete v tématu <xref:host-and-deploy/proxy-load-balancer>.

## <a name="configure-https"></a>Konfigurace HTTPS

Nakonfigurujte službu s koncovým bodem zabezpečení:

1. Vytvořte certifikát X.509, který pro hostování systému získání certifikátů vaší platformě a mechanismy nasazení.

1. Zadejte [konfigurace koncového bodu HTTPS serveru Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) mohl certifikát používat.

Použití certifikátu vývoj pro ASP.NET Core HTTPS k zabezpečení koncového bodu služby se nepodporuje.

## <a name="current-directory-and-content-root"></a>Aktuální adresář a obsahu root

Aktuální pracovní adresář vrátit voláním <xref:System.IO.Directory.GetCurrentDirectory*> pro službu Windows je *C:\\WINDOWS\\system32* složky. *System32* složka není vhodné umístění pro ukládání souborů služby (například soubory nastavení). Použijte jednu z následujících dvou přístupů k zajištění údržby a přístup k prostředků a souborů s nastavením služby.

::: moniker range=">= aspnetcore-3.0"

### <a name="use-contentrootpath-or-contentrootfileprovider"></a>Použití ContentRootPath nebo ContentRootFileProvider

Použití [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) nebo <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> při vyhledávání prostředků vaší aplikace.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

### <a name="set-the-content-root-path-to-the-apps-folder"></a>Nastavení obsahu kořenovou cestu ke složce aplikace

<xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> Se stejnou cestu k dispozici na `binPath` argument při vytváření služby. Namísto volání metody `GetCurrentDirectory` cest k souborům nastavení vytvoříte volání <xref:System.IO.Directory.SetCurrentDirectory*> cestu ke kořenové obsahu aplikace.

V `Program.Main`, určit cestu ke složce spustitelný soubor služby a použijte cestu k vytvoření obsahu kořenové aplikace:

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

::: moniker-end

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a>Store soubory služby na vhodné místo na disku

Zadejte absolutní cestu s <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> při použití <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> do složky obsahující soubory.

## <a name="additional-resources"></a>Další zdroje

::: moniker range=">= aspnetcore-3.0"

* [Konfigurace koncového bodu kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (včetně konfigurace protokolu HTTPS a podporu SNI)
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [Konfigurace koncového bodu kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (včetně konfigurace protokolu HTTPS a podporu SNI)
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end

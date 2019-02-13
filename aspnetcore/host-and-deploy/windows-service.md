---
title: Hostitele ASP.NET Core ve službě Windows
author: guardrex
description: Zjistěte, jak hostovat aplikace ASP.NET Core ve službě Windows.
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 02/07/2019
uid: host-and-deploy/windows-service
ms.openlocfilehash: 5393dcec4f5e2eb37ec9cac2435bf15eedb8e361
ms.sourcegitcommit: af8a6eb5375ef547a52ffae22465e265837aa82b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56159366"
---
# <a name="host-aspnet-core-in-a-windows-service"></a>Hostitele ASP.NET Core ve službě Windows

Podle [Luke Latham](https://github.com/guardrex) a [Petr Dykstra](https://github.com/tdykstra)

Na Windows, jako je možné hostovat aplikace ASP.NET Core [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) bez použití služby IIS. Pokud hostovaný jako služba Windows, aplikace se automaticky spustí po restartování počítače.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([stažení](xref:index#how-to-download-a-sample))

## <a name="deployment-type"></a>Typ nasazení

Můžete vytvořit buď závisí na architektuře nebo samostatná Windows nasazení služby. Informace a Rady, scénáře nasazení najdete v tématu [nasazení aplikace .NET Core](/dotnet/core/deploying/).

### <a name="framework-dependent-deployment"></a>Nasazení závisí na architektuře

Nasazení závisí na architektuře (chyba) spoléhá na přítomnost sdílené systémová verzi .NET Core v cílovém systému. Při použití scénář disketové jednotky s aplikací ASP.NET Core Windows Service SDK vytvoří spustitelný soubor (*\*.exe*), označované jako *spustitelného souboru závisí na architektuře*.

### <a name="self-contained-deployment"></a>Samostatná nasazení

Samostatná nasazení (SCD) nemusí spoléhat na přítomnost sdílené komponenty v cílovém systému. Modul runtime a závislostí aplikace jsou nasazené v aplikaci k hostování systému.

## <a name="convert-a-project-into-a-windows-service"></a>Převést projekt do služby Windows

Do existujícího projektu ASP.NET Core a spusťte tak aplikaci jako službu, proveďte následující změny:

### <a name="project-file-updates"></a>Aktualizace souboru projektu

Podle podle vaší volby [typ nasazení](#deployment-type), aktualizujte soubor projektu:

#### <a name="framework-dependent-deployment-fdd"></a>Nasazení závisí na architektuře (chyba)

Přidat Windows [identifikátor modulu Runtime (RID)](/dotnet/core/rid-catalog) k `<PropertyGroup>` , která obsahuje cílové rozhraní. V následujícím příkladu RID nastavena `win7-x64`. Přidat `<SelfContained>` nastavenou na `false`. Tyto vlastnosti dáte pokyn, aby sada SDK pro generování spustitelného souboru (*.exe*) souborů pro Windows.

A *web.config* soubor, který je obvykle vytvořen při publikování aplikace ASP.NET Core, není nutné pro aplikaci služby Windows. Chcete-li zakázat vytváření *web.config* přidejte `<IsTransformWebConfigDisabled>` nastavenou na `true`.

::: moniker range=">= aspnetcore-2.2"

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

Přidat `<UseAppHost>` nastavenou na `true`. Tato vlastnost poskytuje službu s cestou aktivace (spustitelný soubor, *.exe*) pro disketové jednotky.

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

#### <a name="self-contained-deployment-scd"></a>Samostatná nasazení (SCD)

Ověřte existenci Windows [identifikátor modulu Runtime (RID)](/dotnet/core/rid-catalog) nebo přidání identifikátorů RID pro `<PropertyGroup>` , která obsahuje cílové rozhraní. Zakázat vytváření *web.config* souboru tak, že přidáte `<IsTransformWebConfigDisabled>` nastavenou na `true`.

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

Chcete-li publikovat pro více identifikátorů RID:

* Zadejte identifikátory RID v seznam oddělený středníkem.
* Použijte název vlastnosti `<RuntimeIdentifiers>` (množné číslo).

  Další informace najdete v tématu [katalog identifikátorů RID .NET Core](/dotnet/core/rid-catalog).

Přidat odkaz na balíček pro [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices).

Pokud chcete povolit protokolování protokolu událostí Windows, přidejte odkaz na balíček pro [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).

Další informace najdete v tématu [zpracování, spouštění a zastavování události](#handle-starting-and-stopping-events) oddílu.

### <a name="programmain-updates"></a>Aktualizace Program.Main

Proveďte následující změny v `Program.Main`:

* K testování a ladění, když se provozují mimo službu, přidání kódu k určení, jestli aplikace běží jako službu nebo konzolové aplikace. Kontrola, pokud je připojen ladicí program nebo `--console` argument příkazového řádku je k dispozici.

  Pokud je některá podmínka pravdivá (aplikace není spuštěna jako služba), volání <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> na webového hostitele.

  Pokud jsou podmínky hodnotu false (aplikace je spuštěn jako služba):

  * Volání <xref:System.IO.Directory.SetCurrentDirectory*> a použijte cestu k umístění publikované aplikace. Nevolejte <xref:System.IO.Directory.GetCurrentDirectory*> získat cestu, protože aplikace Windows Service vrátí *C:\\WINDOWS\\system32* složky při `GetCurrentDirectory` je volána. Další informace najdete v tématu [aktuálního adresáře a kořenový adresář obsahu](#current-directory-and-content-root) oddílu.
  * Volání <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> a spusťte tak aplikaci jako službu.

  Protože [poskytovatele konfigurace příkazového řádku](xref:fundamentals/configuration/index#command-line-configuration-provider) vyžaduje páry název hodnota pro argumenty příkazového řádku, `--console` přepínače se odebere z argumentů před <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> je obdrží.

* Zapsat do protokolu událostí Windows, přidejte zprostředkovatele protokolu událostí na <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>. Nastavení úrovně protokolování s `Logging:LogLevel:Default` klíče v *appsettings. Production.JSON* souboru. Soubor nastavení produkčním prostředí ukázkovou aplikaci pro demonstrační účely a testování, nastaví úroveň protokolování na `Information`. V produkčním prostředí, hodnota se obvykle nastavuje na `Error`. Další informace naleznete v tématu <xref:fundamentals/logging/index#windows-eventlog-provider>.

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

### <a name="publish-the-app"></a>Publikování aplikace

Publikování aplikace pomocí [dotnet publikovat](/dotnet/articles/core/tools/dotnet-publish), [profil publikování pro Visual Studio](xref:host-and-deploy/visual-studio-publish-profiles), nebo Visual Studio Code. Když pomocí sady Visual Studio, vyberte **FolderProfile** a nakonfigurovat **cílové umístění** před výběrem **publikovat** tlačítko.

Chcete-li publikovat ukázkovou aplikaci pomocí nástrojů rozhraní příkazového řádku (CLI), spusťte [dotnet publikovat](/dotnet/core/tools/dotnet-publish) příkazu na příkazovém řádku ve složce projektu s předat konfiguraci vydané verze [- c |--konfigurace](/dotnet/core/tools/dotnet-publish#options)možnost. Použití [-o |--výstup](/dotnet/core/tools/dotnet-publish#options) možnost s cestou k publikování do složky mimo aplikaci.

#### <a name="publish-a-framework-dependent-deployment-fdd"></a>Publikování nasazení závisí na architektuře (chyba)

V následujícím příkladu je aplikace publikována na *c:\\svc* složky:

```console
dotnet publish --configuration Release --output c:\svc
```

#### <a name="publish-a-self-contained-deployment-scd"></a>Publikování samostatná nasazení (SCD)

V musí být zadán identifikátor RID `<RuntimeIdenfifier>` (nebo `<RuntimeIdentifiers>`) vlastnost souboru projektu. Zadejte modul runtime [- r |--runtime](/dotnet/core/tools/dotnet-publish#options) možnost `dotnet publish` příkazu.

V následujícím příkladu je aplikace publikována pro `win7-x64` modulu runtime *c:\\svc* složky:

```console
dotnet publish --configuration Release --runtime win7-x64 --output c:\svc
```

### <a name="create-a-user-account"></a>Vytvoření uživatelského účtu

Vytvoření uživatelského účtu pro službu pomocí `net user` příkaz správu příkazové prostředí:

```console
net user {USER ACCOUNT} {PASSWORD} /add
```

Vypršení platnosti hesla výchozí je šest týdnů.

Pro ukázkovou aplikaci, vytvořte uživatelský účet s názvem `ServiceUser` a heslo. V následujícím příkazu nahraďte `{PASSWORD}` s [silné heslo](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements).

```console
net user ServiceUser {PASSWORD} /add
```

Pokud potřebujete přidat uživatele do skupiny, použijte `net localgroup` příkaz, kde `{GROUP}` je název skupiny:

```console
net localgroup {GROUP} {USER ACCOUNT} /add
```

Další informace najdete v tématu [uživatelské účty služby](/windows/desktop/services/service-user-accounts).

Alternativní způsob správy uživatelů při používání služby Active Directory je použití účtů spravované služby. Další informace najdete v tématu [přehled účtů spravované služby skupiny](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).

### <a name="set-permissions"></a>Nastavení oprávnění

#### <a name="access-to-the-app-folder"></a>Přístup ke složce aplikace

Udělit přístup, zápis a čtení a spouštění do složky aplikace pomocí [icacls](/windows-server/administration/windows-commands/icacls) příkaz správu příkazové prostředí:

```console
icacls "{PATH}" /grant {USER ACCOUNT}:(OI)(CI){PERMISSION FLAGS} /t
```

* `{PATH}` &ndash; Cesta ke složce aplikace.
* `{USER ACCOUNT}` &ndash; Uživatelský účet (SID).
* `(OI)` &ndash; Příznak objekt dědit šíří oprávnění na podřízené soubory.
* `(CI)` &ndash; Příznak kontejneru dědit šíří oprávnění na podřízené složky.
* `{PERMISSION FLAGS}` &ndash; Nastaví její oprávnění přístupu.
  * Zápis (`W`)
  * Čtení (`R`)
  * Spuštění (`X`)
  * Úplné (`F`)
  * Upravit (`M`)
* `/t` &ndash; Rekurzivně se vztahují na existující podřízené složky a soubory.

Pro publikování ukázkové aplikace *c:\\svc* složky a `ServiceUser` účet s oprávněními pro zápis a čtení a spouštění, použijte následující příkaz:

```console
icacls "c:\svc" /grant ServiceUser:(OI)(CI)WRX /t
```

Další informace najdete v tématu [icacls](/windows-server/administration/windows-commands/icacls).

#### <a name="log-on-as-a-service"></a>Přihlaste se jako služba

Udělit [přihlásit jako službu](/windows/security/threat-protection/security-policy-settings/log-on-as-a-service) oprávnění pro uživatelský účet:

1. Vyhledejte **přiřazení uživatelských práv** zásad v konzole místní zásady zabezpečení nebo konzolu Editor místních zásad skupiny. Pokyny najdete v tématu: [Konfigurovat nastavení zásad zabezpečení](/windows/security/threat-protection/security-policy-settings/how-to-configure-security-policy-settings).
1. Vyhledejte `Log on as a service` zásad. Dvakrát klikněte na zásady tak, aby ho otevřete.
1. Vyberte **přidat uživatele nebo skupinu**.
1. Vyberte **Upřesnit** a vyberte **najít**.
1. Vyberte uživatelský účet vytvořený v [vytvoření uživatelského účtu](#create-a-user-account) výše v části. Vyberte **OK** potvrďte výběr.
1. Vyberte **OK** po potvrzení, že je správný název objektu.
1. Vyberte **Použít**. Vyberte **OK** zavřete okno zásady.

## <a name="manage-the-service"></a>Spravovat službu

### <a name="create-the-service"></a>Vytvoření služby

Použití [sc.exe](https://technet.microsoft.com/library/bb490995) vytvoříte službu z správu příkazové okno nástroje příkazového řádku. `binPath` Hodnota je cesta ke spustitelnému souboru aplikace, která zahrnuje název spustitelného souboru. **Mezera mezi znaménko rovná se a znak pro uvození každého parametru a hodnota je povinný.**

```console
sc create {SERVICE NAME} binPath= "{PATH}" obj= "{DOMAIN}\{USER ACCOUNT}" password= "{PASSWORD}"
```

* `{SERVICE NAME}` &ndash; Název, který chcete přiřadit ke službě v [správce řízení služeb](/windows/desktop/services/service-control-manager).
* `{PATH}` &ndash; Cesta ke spustitelnému souboru služby.
* `{DOMAIN}` &ndash; Doména počítače připojené k doméně. Pokud počítač není připojený k doméně, použijte název místního počítače.
* `{USER ACCOUNT}` &ndash; Uživatelský účet, pod kterým je služba spuštěna.
* `{PASSWORD}` &ndash; Heslo k uživatelskému účtu.

> [!WARNING]
> Proveďte **není** vynechat, nechte `obj` parametru. Výchozí hodnota pro `obj` je [účet LocalSystem](/windows/desktop/services/localsystem-account) účtu. Spuštěná služba v rámci `LocalSystem` účet představuje významné bezpečnostní riziko. Vždy spuštění služby pomocí uživatelského účtu, který má omezená oprávnění.

V následujícím příkladu pro ukázkovou aplikaci:

* Služba má název **Moje_služba**.
* Publikované služba se nachází v *c:\\svc* složky. Je název spustitelné aplikace *SampleApp.exe*. Uzavřete `binPath` hodnotu do dvojitých uvozovek (").
* Je služba spuštěna pod `ServiceUser` účtu. Nahraďte `{DOMAIN}` s účtem uživatele domény nebo názvu místního počítače. Uzavřete `obj` hodnotu do dvojitých uvozovek ("). Příklad: Pokud je hostující systém místní počítač s názvem `MairaPC`, nastavte `obj` k `"MairaPC\ServiceUser"`.
* Nahraďte `{PASSWORD}` s heslem uživatelského účtu. Uzavřete `password` hodnotu do dvojitých uvozovek (").

```console
sc create MyService binPath= "c:\svc\sampleapp.exe" obj= "{DOMAIN}\ServiceUser" password= "{PASSWORD}"
```

> [!IMPORTANT]
> Ujistěte se, že mezery mezi symboly rovná parametrů a hodnot parametrů jsou k dispozici.

### <a name="start-the-service"></a>Spustit službu

Spusťte službu pomocí `sc start {SERVICE NAME}` příkazu.

Spustit službu ukázkové aplikace, použijte následující příkaz:

```console
sc start MyService
```

Příkaz trvá několik sekund se spustit službu.

### <a name="determine-the-service-status"></a>Zjistit stav služby

Chcete-li zkontrolovat stav služby, použijte `sc query {SERVICE NAME}` příkazu. Stav je uveden jako jeden z následujících hodnot:

* `START_PENDING`
* `RUNNING`
* `STOP_PENDING`
* `STOPPED`

Použijte následující příkaz a zkontrolujte stav služby app service vzorku:

```console
sc query MyService
```

### <a name="browse-a-web-app-service"></a>Procházet služba webové aplikace

Pokud je služba v `RUNNING` stavu a pokud je služba webové aplikace, procházet aplikace, její cesta (ve výchozím nastavení, `http://localhost:5000`, který přesměruje `https://localhost:5001` při použití [HTTPS přesměrování Middleware](xref:security/enforcing-ssl)).

Aplikační služba ukázkového procházet aplikace na adrese `http://localhost:5000`.

### <a name="stop-the-service"></a>Zastavit službu

Zastavit službu s `sc stop {SERVICE NAME}` příkazu.

Následující příkaz zastaví aplikační služba ukázkového:

```console
sc stop MyService
```

### <a name="delete-the-service"></a>Odstranit službu

Po krátké prodlevě zastavit službu, odinstalujte službu s `sc delete {SERVICE NAME}` příkazu.

Postup kontroly stavu aplikační služba ukázkového:

```console
sc query MyService
```

Pokud je aplikační služba ukázkového v `STOPPED` stavu, použijte následující příkaz pro odinstalaci aplikační služba ukázkového:

```console
sc delete MyService
```

## <a name="handle-starting-and-stopping-events"></a>Zpracování spuštění a zastavení událostí

Pro zpracování <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, a <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> události, proveďte následující další změny:

1. Vytvořte třídu, která je odvozena z <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> s `OnStarting`, `OnStarted`, a `OnStopping` metody:

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. Vytvořit metodu rozšíření pro <xref:Microsoft.AspNetCore.Hosting.IWebHost> , který předá `CustomWebHostService` k <xref:System.ServiceProcess.ServiceBase.Run*>:

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. V `Program.Main`, zavolejte `RunAsCustomService` místo rozšiřující metoda <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:

   ```csharp
   host.RunAsCustomService();
   ```

   Pokud chcete zobrazit umístění <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> v `Program.Main`, najdete vzorový kód uvedený v [převést projekt do služby Windows](#convert-a-project-into-a-windows-service) oddílu.

## <a name="proxy-server-and-load-balancer-scenarios"></a>Proxy server a scénáře pro nástroj pro vyrovnávání zatížení

Služby, které interakci s žádostí z Internetu nebo podnikové síti a jsou za proxy nebo nástroj pro vyrovnávání zatížení může vyžadovat dodatečnou konfiguraci. Další informace naleznete v tématu <xref:host-and-deploy/proxy-load-balancer>.

## <a name="configure-https"></a>Konfigurace HTTPS

Jak nakonfigurovat službu s koncovým bodem zabezpečení:

1. Vytvořte certifikát X.509, který pro hostování systému získání certifikátů vaší platformě a mechanismy nasazení.

1. Zadejte [konfigurace koncového bodu HTTPS serveru Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) mohl certifikát používat.

Použití certifikátu vývoj pro ASP.NET Core HTTPS k zabezpečení koncového bodu služby se nepodporuje.

## <a name="current-directory-and-content-root"></a>Aktuální adresář a obsahu root

Aktuální pracovní adresář vrátit voláním <xref:System.IO.Directory.GetCurrentDirectory*> pro službu Windows je *C:\\WINDOWS\\system32* složky. *System32* složka není vhodné umístění pro ukládání souborů služby (například soubory nastavení). Použijte jednu z následujících dvou přístupů k zajištění údržby a přístup k prostředků a souborů s nastavením služby.

### <a name="set-the-content-root-path-to-the-apps-folder"></a>Nastavení obsahu kořenovou cestu ke složce aplikace

<xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> Se stejnou cestu k dispozici na `binPath` argument při vytvoření služby. Namísto volání metody `GetCurrentDirectory` cest k souborům nastavení vytvoříte volání <xref:System.IO.Directory.SetCurrentDirectory*> cestu ke kořenové obsahu aplikace.

V `Program.Main`, určit cestu ke složce spustitelný soubor služby a použijte cestu k vytvoření obsahu kořenové aplikace:

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-the-services-files-in-a-suitable-location-on-disk"></a>Soubory služby Store na vhodné místo na disku

Zadejte absolutní cestu s <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> při použití <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> do složky obsahující soubory.

## <a name="additional-resources"></a>Další zdroje

* [Konfigurace koncového bodu kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (včetně konfigurace protokolu HTTPS a podporu SNI)
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

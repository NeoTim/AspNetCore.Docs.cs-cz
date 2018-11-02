---
title: Hostitele ASP.NET Core ve službě Windows
author: guardrex
description: Zjistěte, jak hostovat aplikace ASP.NET Core ve službě Windows.
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 10/30/2018
uid: host-and-deploy/windows-service
ms.openlocfilehash: 11913019bfe5d06c259b806fce9cc580a8280ad5
ms.sourcegitcommit: fc2486ddbeb15ab4969168d99b3fe0fbe91e8661
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2018
ms.locfileid: "50758190"
---
# <a name="host-aspnet-core-in-a-windows-service"></a>Hostitele ASP.NET Core ve službě Windows

Podle [Luke Latham](https://github.com/guardrex) a [Petr Dykstra](https://github.com/tdykstra)

Aplikace ASP.NET Core je možné hostovat na Windows bez použití služby IIS jako [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications). Pokud hostovaný jako služba Windows, aplikace se automaticky spustí po restartování počítače.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([stažení](xref:index#how-to-download-a-sample))

## <a name="convert-a-project-into-a-windows-service"></a>Převést projekt do služby Windows

Následující minimální změny je potřeba nastavit stávající projekt ASP.NET Core pro spouštěn jako služba:

1. V souboru projektu:

   * Ověřte existenci Windows [identifikátor modulu Runtime (RID)](/dotnet/core/rid-catalog) nebo ho přidat do `<PropertyGroup>` , který obsahuje Cílová architektura:

      ```xml
      <PropertyGroup>
        <TargetFramework>netcoreapp2.2</TargetFramework>
        <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
      </PropertyGroup>
      ```

      Chcete-li publikovat pro více identifikátorů RID:

      * Zadejte identifikátory RID v seznam oddělený středníkem.
      * Použijte název vlastnosti `<RuntimeIdentifiers>` (množné číslo).

      Další informace najdete v tématu [katalog identifikátorů RID .NET Core](/dotnet/core/rid-catalog).

   * Přidat odkaz na balíček pro [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices).

1. Proveďte následující změny v `Program.Main`:

   * Volání [hostitele. RunAsService](/dotnet/api/microsoft.aspnetcore.hosting.windowsservices.webhostwindowsserviceextensions.runasservice) místo `host.Run`.

   * Volání [UseContentRoot](xref:fundamentals/host/web-host#content-root) a cesta k aplikaci pro publikování umístění, namísto použití `Directory.GetCurrentDirectory()`.

     [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=ServiceOnly&highlight=8-9,16)]

1. Publikování aplikace pomocí [dotnet publikovat](/dotnet/articles/core/tools/dotnet-publish), [profil publikování pro Visual Studio](xref:host-and-deploy/visual-studio-publish-profiles), nebo Visual Studio Code. Když pomocí sady Visual Studio, vyberte **FolderProfile** a nakonfigurovat **cílové umístění** před výběrem **publikovat** tlačítko.

   Chcete-li publikovat ukázkovou aplikaci pomocí nástrojů rozhraní příkazového řádku (CLI), spusťte [dotnet publikovat](/dotnet/core/tools/dotnet-publish) příkazu na příkazovém řádku ve složce projektu. V musí být zadán identifikátor RID `<RuntimeIdenfifier>` (nebo `<RuntimeIdentifiers>`) vlastnost souboru projektu. V následujícím příkladu, publikování aplikace v rámci konfigurace verze pro `win7-x64` runtime do složky vytvořené v *c:\\svc*:

   ```console
   dotnet publish --configuration Release --runtime win7-x64 --output c:\svc
   ```

1. Vytvořte účet uživatele pro používání služby `net user` příkaz:

   ```console
   net user {USER ACCOUNT} {PASSWORD} /add
   ```

   Pro ukázkovou aplikaci, vytvořte uživatelský účet s názvem `ServiceUser` a heslo. V následujícím příkazu nahraďte `{PASSWORD}` s [silné heslo](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements).

   ```console
   net user ServiceUser {PASSWORD} /add
   ```

   Pokud potřebujete přidat uživatele do skupiny, použijte `net localgroup` příkaz, kde `{GROUP}` je název skupiny:

   ```console
   net localgroup {GROUP} {USER ACCOUNT} /add
   ```

   Další informace najdete v tématu [uživatelské účty služby](/windows/desktop/services/service-user-accounts).

1. Udělit přístup, zápis a čtení a spouštění do složky aplikace pomocí [icacls](/windows-server/administration/windows-commands/icacls) příkaz:

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

1. Použití [sc.exe](https://technet.microsoft.com/library/bb490995) nástroj příkazového řádku vytvořte službu. `binPath` Hodnota je cesta ke spustitelnému souboru aplikace, která zahrnuje název spustitelného souboru. **Mezera mezi znaménko rovná se a znak pro uvození každého parametru a hodnota je povinný.**

   ```console
   sc create {SERVICE NAME} binPath= "{PATH}" obj= "{DOMAIN}\{USER ACCOUNT}" password= "{PASSWORD}"
   ```

   * `{SERVICE NAME}` &ndash; Název, který chcete přiřadit ke službě v [správce řízení služeb](/windows/desktop/services/service-control-manager).
   * `{PATH}` &ndash; Cesta ke spustitelnému souboru služby.
   * `{DOMAIN}` (nebo pokud počítač není domény připojené, názvu místního počítače) a `{USER ACCOUNT}` &ndash; doménu (nebo názvu místního počítače) a uživatelský účet, pod kterým je služba spuštěna. Proveďte **není** vynechat, nechte `obj` parametru. Výchozí hodnota pro `obj` je [účet LocalSystem](/windows/desktop/services/localsystem-account) účtu. Spuštěná služba v rámci `LocalSystem` účet představuje významné bezpečnostní riziko. Vždy spustit službu pod uživatelským účtem s omezenými oprávněními na serveru.
   * `{PASSWORD}` &ndash; Heslo k uživatelskému účtu.

   V následujícím příkladu:

   * Služba má název **Moje_služba**.
   * Publikované služba se nachází v *c:\\svc* složky. Je název spustitelné aplikace *AspNetCoreService.exe*. `binPath` Je hodnota uzavřena do uvozovek (").
   * Je služba spuštěna pod `ServiceUser` účtu. Nahraďte `{DOMAIN}` s účtem uživatele domény nebo názvu místního počítače. Uzavřete `obj` hodnotu v rovné uvozovky ("). Příklad: Pokud je hostující systém místní počítač s názvem `MairaPC`, nastavte `obj` k `"MairaPC\ServiceUser"`.
   * Nahraďte `{PASSWORD}` s heslem uživatelského účtu. `password` Je hodnota uzavřena do uvozovek (").

   ```console
   sc create MyService binPath= "c:\svc\aspnetcoreservice.exe" obj= "{DOMAIN}\ServiceUser" password= "{PASSWORD}"
   ```

   > [!IMPORTANT]
   > Ujistěte se, že mezery mezi symboly rovná parametrů a hodnot parametrů jsou k dispozici.

1. Spusťte službu pomocí `sc start {SERVICE NAME}` příkazu.

   Spustit službu ukázkové aplikace, použijte následující příkaz:

   ```console
   sc start MyService
   ```

   Příkaz trvá několik sekund se spustit službu.

1. Chcete-li zkontrolovat stav služby, použijte `sc query {SERVICE NAME}` příkazu. Stav je uveden jako jeden z následujících hodnot:

   * `START_PENDING`
   * `RUNNING`
   * `STOP_PENDING`
   * `STOPPED`

   Použijte následující příkaz a zkontrolujte stav služby app service vzorku:

   ```console
   sc query MyService
   ```

1. Pokud je služba v `RUNNING` stavu a pokud je služba webové aplikace, procházet aplikace, její cesta (ve výchozím nastavení, `http://localhost:5000`, který přesměruje `https://localhost:5001` při použití [HTTPS přesměrování Middleware](xref:security/enforcing-ssl)).

   Aplikační služba ukázkového procházet aplikace na adrese `http://localhost:5000`.

1. Zastavit službu s `sc stop {SERVICE NAME}` příkazu.

   Následující příkaz zastaví aplikační služba ukázkového:

   ```console
   sc stop MyService
   ```

1. Po krátké prodlevě zastavit službu, odinstalujte službu s `sc delete {SERVICE NAME}` příkazu.

   Postup kontroly stavu aplikační služba ukázkového:

   ```console
   sc query MyService
   ```

   Pokud je aplikační služba ukázkového v `STOPPED` stavu, použijte následující příkaz pro odinstalaci aplikační služba ukázkového:

   ```console
   sc delete MyService
   ```

## <a name="run-the-app-outside-of-a-service"></a>Spuštění aplikace mimo službu

Usnadňuje testování a ladění, když se provozují mimo službu, takže je obvyklý přidáte kód, který volá `RunAsService` pouze za určitých podmínek. Například aplikace může běžet jako aplikace konzoly v jazyce `--console` argument příkazového řádku nebo pokud je připojen ladicí program:

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=ServiceOrConsole)]

Vzhledem k tomu, že konfigurace ASP.NET Core vyžaduje páry název hodnota pro argumenty příkazového řádku, `--console` přepínač byl předtím, než jsou předány argumenty [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder).

> [!NOTE]
> `isService` nebude zadán, využije z `Main` do `CreateWebHostBuilder` protože podpis `CreateWebHostBuilder` musí být `CreateWebHostBuilder(string[])` mohl [testování integrace](xref:test/integration-tests) fungovala správně.

## <a name="handle-stopping-and-starting-events"></a>Zpracování, spouštění a zastavování události

Pro zpracování [OnStarting](/dotnet/api/microsoft.aspnetcore.hosting.windowsservices.webhostservice.onstarting), [OnStarted](/dotnet/api/microsoft.aspnetcore.hosting.windowsservices.webhostservice.onstarted), a [OnStopping](/dotnet/api/microsoft.aspnetcore.hosting.windowsservices.webhostservice.onstopping) události, proveďte následující další změny:

1. Vytvořte třídu, která je odvozena z [WebHostService](/dotnet/api/microsoft.aspnetcore.hosting.windowsservices.webhostservice):

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=NoLogging)]

2. Vytvořit metodu rozšíření pro [IWebHost](/dotnet/api/microsoft.aspnetcore.hosting.iwebhost) , který předá vlastní `WebHostService` k [ServiceBase.Run](/dotnet/api/system.serviceprocess.servicebase.run):

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. V `Program.Main`, zavolat novou metodu rozšíření `RunAsCustomService`, namísto [RunAsService](/dotnet/api/microsoft.aspnetcore.hosting.windowsservices.webhostwindowsserviceextensions.runasservice):

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=HandleStopStart&highlight=17)]

   > [!NOTE]
   > `isService` nebude zadán, využije z `Main` do `CreateWebHostBuilder` protože podpis `CreateWebHostBuilder` musí být `CreateWebHostBuilder(string[])` mohl [testování integrace](xref:test/integration-tests) fungovala správně.

Pokud vlastní `WebHostService` kód vyžaduje službu z injektáž závislostí (jako je například protokolování), získat ze [IWebHost.Services](/dotnet/api/microsoft.aspnetcore.hosting.iwebhost.services) vlastnost:

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=Logging&highlight=7-8)]

## <a name="proxy-server-and-load-balancer-scenarios"></a>Proxy server a scénáře pro nástroj pro vyrovnávání zatížení

Služby, které interakci s žádostí z Internetu nebo podnikové síti a jsou za proxy nebo nástroj pro vyrovnávání zatížení může vyžadovat dodatečnou konfiguraci. Další informace naleznete v tématu <xref:host-and-deploy/proxy-load-balancer>.

## <a name="configure-https"></a>Konfigurace HTTPS

Jak nakonfigurovat službu s koncovým bodem zabezpečení:

1. Vytvořte certifikát X.509, který pro hostování systému získání certifikátů vaší platformě a mechanismy nasazení.
1. Zadejte [konfigurace koncového bodu HTTPS serveru Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) mohl certifikát používat.

Použití certifikátu vývoj pro ASP.NET Core HTTPS k zabezpečení koncového bodu služby se nepodporuje.

## <a name="current-directory-and-content-root"></a>Aktuální adresář a obsahu root

Aktuální pracovní adresář vrátit voláním `Directory.GetCurrentDirectory()` pro službu Windows je *C:\\WINDOWS\\system32* složky. *System32* složka není vhodné umístění pro ukládání souborů služby (například soubory nastavení). Použijte jednu z následujících dvou přístupů k zajištění údržby a přístup k prostředky a nastavení souborů pomocí služby [FileConfigurationExtensions.SetBasePath](/dotnet/api/microsoft.extensions.configuration.fileconfigurationextensions.setbasepath) při použití [IConfigurationBuilder](/dotnet/api/microsoft.extensions.configuration.iconfigurationbuilder):

* Pomocí obsahu kořenovou cestu. `IHostingEnvironment.ContentRootPath` Se stejnou cestu k dispozici na `binPath` argument při vytvoření služby. Namísto použití `Directory.GetCurrentDirectory()` k vytvoření cesty k souborům nastavení, použijte obsah kořenové cestě a udržovat soubory v kořenové obsahu aplikace.
* Store soubory na vhodné místo na disku. Zadejte absolutní cestu s `SetBasePath` do složky obsahující soubory.

## <a name="additional-resources"></a>Další zdroje

* [Konfigurace koncového bodu kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (včetně konfigurace protokolu HTTPS a podporu SNI)
* <xref:fundamentals/host/web-host>

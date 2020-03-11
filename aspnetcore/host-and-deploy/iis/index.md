---
title: Hostitele ASP.NET Core ve Windows se službou IIS
author: rick-anderson
description: Zjistěte, jak hostovat aplikace ASP.NET Core na Windows serveru Internetové informační služby (IIS).
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: host-and-deploy/iis/index
ms.openlocfilehash: c2ca867e3eebdc3fcf512cc5d457ff3c1967f9b1
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78657981"
---
# <a name="host-aspnet-core-on-windows-with-iis"></a>Hostitele ASP.NET Core ve Windows se službou IIS

::: moniker range=">= aspnetcore-3.0"

Kurz týkající se publikování ASP.NET Core aplikace na server služby IIS najdete v tématu <xref:tutorials/publish-to-iis>.

[Instalace hostující sady .NET Core](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a>Podporované operační systémy

Podporovány jsou následující operační systémy:

* Windows 7 nebo novější
* Windows Server 2012 R2 nebo novější

[Http. sys Server](xref:fundamentals/servers/httpsys) (dříve označovaný jako weblisten) nefunguje v konfiguraci reverzního proxy serveru se službou IIS. Použijte [Server Kestrel](xref:fundamentals/servers/kestrel).

Informace o hostování v Azure najdete v tématu <xref:host-and-deploy/azure-apps/index>.

Pokyny k řešení potíží najdete v tématu <xref:test/troubleshoot>.

## <a name="supported-platforms"></a>Podporované platformy

Podporují se aplikace publikované pro nasazení 32 (x86) nebo 64-bit (x64). Nasazení 32 aplikace s 32 (x86) .NET Core SDK, pokud aplikace:

* Vyžaduje větší dostupný adresní prostor virtuální paměti pro 64 aplikaci.
* Vyžaduje větší velikost zásobníku služby IIS.
* Má 64 nativní závislosti.

K publikování 64 aplikace použijte 64 .NET Core SDK (x64). V hostitelském systému musí být nainstalován 64 modul runtime.

## <a name="hosting-models"></a>Modely hostingu

### <a name="in-process-hosting-model"></a>Model hostování v procesu

Při použití hostování v rámci procesu ASP.NET Core aplikace běží ve stejném procesu jako jeho pracovní proces služby IIS. Hostování v rámci procesů poskytují lepší výkon než hostování mimo procesy, protože požadavky nejsou proxy serverem přes adaptér zpětné smyčky, síťové rozhraní, které vrátí odchozí síťový provoz zpátky do stejného počítače. Služba IIS zpracovává správu procesů pomocí [aktivační služby procesů systému Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

[Modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module):

* Provede inicializaci aplikace.
  * Načte [CoreCLR](/dotnet/standard/glossary#coreclr).
  * Volá `Program.Main`.
* Zpracovává životnost nativního požadavku služby IIS.

Model hostování v rámci procesu není podporován pro aplikace ASP.NET Core, které cílí na .NET Framework.

Následující diagram znázorňuje vztah mezi službou IIS, modulem ASP.NET Core a aplikací hostovanou v procesu:

![ASP.NET Core modul ve scénáři hostování v rámci procesu](index/_static/ancm-inprocess.png)

Požadavek přijde z webu do ovladače HTTP. sys v režimu jádra. Ovladač směruje nativní požadavek na IIS na konfigurovaném portu webu, obvykle 80 (HTTP) nebo 443 (HTTPS). Modul ASP.NET Core obdrží nativní požadavek a předá ho k serveru HTTP služby IIS (`IISHttpServer`). HTTP server IIS je vnitroprocesové implementace v rámci procesového serveru pro službu IIS, která převádí požadavek z nativního na spravovanou.

Poté, co server HTTP služby IIS požadavek zpracuje, je požadavek vložen do kanálu middleware ASP.NET Core. Kanál middlewaru zpracovává požadavek a předá ho jako instanci `HttpContext` do logiky aplikace. Odpověď aplikace se předává zpět službě IIS prostřednictvím serveru IIS HTTP. Služba IIS odešle odpověď klientovi, který žádost inicioval.

Vnitroprocesové hostování v rámci procesu je výslovný souhlas pro existující aplikace, ale [dotnet nové](/dotnet/core/tools/dotnet-new) šablony jsou výchozí pro všechny scénáře hostování v rámci procesu pro všechny služby IIS a IIS Express.

`CreateDefaultBuilder` přidá instanci <xref:Microsoft.AspNetCore.Hosting.Server.IServer> voláním metody <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> pro spuštění [CoreCLR](/dotnet/standard/glossary#coreclr) a hostování aplikace uvnitř pracovního procesu služby IIS (*W3wp. exe* nebo *IISExpress. exe*). Testy výkonu označují, že hostování aplikace .NET Core v rámci procesu přináší výrazně vyšší propustnost žádostí v porovnání s hostováním aplikací mimo proces a požadavky na proxy serveru na [Kestrel](xref:fundamentals/servers/kestrel) Server.

> [!NOTE]
> Aplikace publikované jako spustitelný soubor s jedním souborem nejde načíst pomocí modelu hostování v rámci procesu.

### <a name="out-of-process-hosting-model"></a>Model hostování mimo proces

Vzhledem k tomu, že ASP.NET Core aplikace běží v procesu odděleném od pracovního procesu služby IIS, modul ASP.NET Core zpracovává správu procesů. Modul spustí proces pro aplikaci ASP.NET Core, když první požadavek dorazí a restartuje aplikaci, pokud se ukončí nebo dojde k chybě. To je v podstatě stejné chování jako u aplikací, které běží v procesu, které jsou spravovány službou was [(Windows Process Activation Service)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

Následující diagram znázorňuje vztah mezi službou IIS, modulem ASP.NET Core a aplikací hostovanou mimo proces:

![ASP.NET Core modul ve scénáři hostování mimo proces](index/_static/ancm-outofprocess.png)

Požadavky přicházející z webu do ovladače HTTP. sys v režimu jádra. Ovladač směruje požadavky do služby IIS na konfigurovaném portu webu, obvykle 80 (HTTP) nebo 443 (HTTPS). Modul předá požadavky do Kestrel na náhodném portu pro aplikaci, což není port 80 nebo 443.

Modul Určuje port prostřednictvím proměnné prostředí při spuštění a rozšíření <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> nakonfiguruje server tak, aby naslouchal `http://localhost:{PORT}`. Budou provedeny další kontroly a požadavky, které nepocházejí z modulu, jsou odmítnuty. Modul nepodporuje předávání HTTPS, takže požadavky se předávají přes protokol HTTP i v případě, že je služba IIS prostřednictvím protokolu HTTPS přijímá.

Po Kestrel žádosti z modulu se požadavek odešle do kanálu middlewaru ASP.NET Core. Kanál middlewaru zpracovává požadavek a předá ho jako instanci `HttpContext` do logiky aplikace. Middleware přidaný integrací služby IIS: aktualizace schématu, vzdálené IP adresy a pathbase pro přesměrování požadavku do Kestrel. Odpověď aplikace se předává zpátky do služby IIS, která ji přenáší zpátky do klienta HTTP, který žádost inicioval.

Pokyny ke konfiguraci ASP.NET Core modulu najdete v tématu <xref:host-and-deploy/aspnet-core-module>.

Další informace o hostování najdete v tématu [hostitel v ASP.NET Core](xref:fundamentals/index#host).

## <a name="application-configuration"></a>Konfigurace aplikací

### <a name="enable-the-iisintegration-components"></a>Povolit IISIntegration součásti

Při sestavování hostitele v `CreateHostBuilder` (*program.cs*) volejte <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> a povolte integraci služby IIS:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        ...
```

Další informace o `CreateDefaultBuilder`najdete v tématu <xref:fundamentals/host/generic-host#default-builder-settings>.

### <a name="iis-options"></a>Možnosti služby IIS

**Model hostování v procesu**

Pokud chcete nakonfigurovat možnosti serveru IIS, zahrňte do <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>konfiguraci služby pro <xref:Microsoft.AspNetCore.Builder.IISServerOptions>. Následující příklad zakazuje AutomaticAuthentication:

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| Možnost                         | Výchozí | Nastavení |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | Pokud `true`, server IIS nastaví `HttpContext.User` ověřený [ověřováním systému Windows](xref:security/authentication/windowsauth). Pokud `false`, Server poskytuje pouze identitu pro `HttpContext.User` a reaguje na výzvy, které jsou výslovně požadovány `AuthenticationScheme`. Aby mohla funkce `AutomaticAuthentication` fungovat, musí být ve službě IIS povolená možnost ověřování systému Windows. Další informace najdete v tématu [ověřování systému Windows](xref:security/authentication/windowsauth). |
| `AuthenticationDisplayName`    | `null`  | Nastaví zobrazovaný název, který se uživatelům na přihlašovací stránky zobrazí. |
| `AllowSynchronousIO`           | `false` | Zda je povolena synchronní v/v pro `HttpContext.Request` a `HttpContext.Response`. |
| `MaxRequestBodySize`           | `30000000`  | Získá nebo nastaví maximální velikost textu požadavku pro `HttpRequest`. Všimněte si, že služba IIS má limit `maxAllowedContentLength`, který bude zpracován před `MaxRequestBodySize` nastavenou v `IISServerOptions`. Změna `MaxRequestBodySize` nebude mít vliv na `maxAllowedContentLength`. Chcete-li zvýšit `maxAllowedContentLength`, přidejte položku do *souboru Web. config* a nastavte `maxAllowedContentLength` na vyšší hodnotu. Další podrobnosti najdete v tématu [Konfigurace](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration). |

**Model hostování mimo proces**

Pokud chcete nakonfigurovat možnosti služby IIS, zahrňte do <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>konfiguraci služby pro <xref:Microsoft.AspNetCore.Builder.IISOptions>. Následující příklad zabrání aplikaci v naplnění `HttpContext.Connection.ClientCertificate`:

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| Možnost                         | Výchozí | Nastavení |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | Pokud `true`, [middleware integrace služby IIS](#enable-the-iisintegration-components) nastaví `HttpContext.User` ověřováno [ověřováním systému Windows](xref:security/authentication/windowsauth). Pokud `false`, middleware poskytuje pouze identitu pro `HttpContext.User` a reaguje na výzvy, které jsou explicitně požadovány `AuthenticationScheme`. Aby mohla funkce `AutomaticAuthentication` fungovat, musí být ve službě IIS povolená možnost ověřování systému Windows. Další informace najdete v tématu [ověřování systému Windows](xref:security/authentication/windowsauth) . |
| `AuthenticationDisplayName`    | `null`  | Nastaví zobrazovaný název, který se uživatelům na přihlašovací stránky zobrazí. |
| `ForwardClientCertificate`     | `true`  | Pokud se `true` a nachází se hlavička žádosti `MS-ASPNETCORE-CLIENTCERT`, `HttpContext.Connection.ClientCertificate` se naplní. |

### <a name="proxy-server-and-load-balancer-scenarios"></a>Proxy server a scénáře pro nástroj pro vyrovnávání zatížení

[Služba IIS Integration middleware](#enable-the-iisintegration-components), která konfiguruje middleware předávaných hlaviček, a modul ASP.NET Core je nakonfigurován tak, aby přenesl schéma (http/https) a vzdálenou IP adresu, kam pochází požadavek. Další konfigurace může být nezbytný pro aplikací hostovaných za službou další proxy servery a nástroje pro vyrovnávání zatížení. Další informace najdete v tématu [konfigurace ASP.NET Core pro práci se servery proxy a nástroji pro vyrovnávání zatížení](xref:host-and-deploy/proxy-load-balancer).

### <a name="webconfig-file"></a>soubor Web.config

Soubor *Web. config* konfiguruje [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module). Vytváření, transformace a publikování souboru *Web. config* je zpracováno cílem MSBuild (`_TransformWebConfig`) při publikování projektu. Tento cíl je k dispozici v rámci cílů web SDK (`Microsoft.NET.Sdk.Web`). V horní části souboru projektu je sada SDK:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Pokud soubor *Web. config* není v projektu přítomen, je vytvořen soubor se správným *processPath* a *argumenty* pro konfiguraci modulu ASP.NET Core a přesunutí do [publikovaného výstupu](xref:host-and-deploy/directory-structure).

Pokud je soubor *Web. config* přítomen v projektu, soubor je transformován se správným *processPath* a *argumenty* pro konfiguraci modulu ASP.NET Core a přesunutí do publikovaného výstupu. Transformace nezmění nastavení konfigurace služby IIS v souboru.

Soubor *Web. config* může poskytovat další nastavení konfigurace služby IIS, která ovládají aktivní moduly IIS. Informace o modulech služby IIS, které jsou schopné zpracovávat požadavky s ASP.NET Core aplikacemi, najdete v tématu [IIS modules](xref:host-and-deploy/iis/modules) .

Chcete-li webové sadě SDK zabránit ve transformaci souboru *Web. config* , použijte vlastnost **\<IsTransformWebConfigDisabled >** v souboru projektu:

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

Při zakazování webové sady SDK z transformace souboru by měl vývojář *processPath* a *argumenty* ručně nastavit. Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module>.

### <a name="webconfig-file-location"></a>Umístění souboru Web.config

Aby bylo možné správně nastavit [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) , musí být soubor *Web. config* přítomen v [kořenové cestě obsahu](xref:fundamentals/index#content-root) (obvykle v základní cestě aplikace) nasazené aplikace. Jedná se o stejné umístění jako fyzická cesta webu služby IIS k dispozici. Soubor *Web. config* je vyžadován v kořenu aplikace, aby bylo možné publikovat více aplikací pomocí nasazení webu.

Citlivé soubory existují na fyzické cestě aplikace, jako je například *\<sestavení >. runtimeconfig. JSON*, *\<Assembly >. XML* (dokumentační dokumentace xml), a *\<sestavení >. DEPS. JSON*. Když je přítomen soubor *Web. config* a lokalita se spouští normálně, služba IIS tyto citlivé soubory po vyžádání neobsluhuje. Pokud soubor *Web. config* chybí, je nesprávně pojmenovaný nebo nemůže nakonfigurovat lokalitu pro normální spuštění, služba IIS může obsluhovat citlivé soubory veřejně.

**Soubor *Web. config* musí být současně přítomen v nasazení, správně pojmenován a může nakonfigurovat lokalitu pro normální spuštění. Nikdy neodstraňujte soubor *Web. config* z produkčního nasazení.**

### <a name="transform-webconfig"></a>Transformace souboru web.config

Pokud potřebujete transformovat *Web. config* při publikování (například nastavit proměnné prostředí na základě konfigurace, profilu nebo prostředí), přečtěte si téma <xref:host-and-deploy/iis/transform-webconfig>.

## <a name="iis-configuration"></a>Konfigurace služby IIS

**Operační systémy Windows Server**

Povolte roli serveru **webový server (IIS)** a vytvořte služby rolí.

1. Použijte průvodce **přidáním rolí a funkcí** z nabídky **Správa** nebo odkazu v **Správce serveru**. V kroku **role serveru** zaškrtněte políčko **webový server (IIS)** .

   ![V kroku výběr serveru role je vybrána role webového serveru IIS.](index/_static/server-roles-ws2016.png)

1. Po kroku **funkce** se krok **služby rolí** načte pro webový server (IIS). Vyberte požadovaných služeb role služby IIS nebo přijměte výchozí nastavení role služby za předpokladu.

   ![Výchozí služby rolí jsou vybrané v kroku vybrat roli služby.](index/_static/role-services-ws2016.png)

   **Ověřování systému Windows (volitelné)**  
   Chcete-li povolit ověřování systému Windows, rozbalte následující uzly: **webový Server** > **zabezpečení**. Vyberte funkci **ověřování systému Windows** . Další informace najdete v tématu [ověřování systému windows \<windowsAuthentication >](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) a [Konfigurace ověřování systému Windows](xref:security/authentication/windowsauth).

   **WebSockets (volitelné)**  
   Protokoly Websocket je podporována s ASP.NET Core 1.1 nebo vyšší. Pokud chcete povolit objekty WebSockets, rozbalte následující uzly: **webový Server** > **vývoj aplikací**. Vyberte funkci **protokolu WebSocket** . Další informace najdete v tématu [objekty WebSockets](xref:fundamentals/websockets).

1. Chcete-li nainstalovat roli a služby webového serveru, postupujte podle kroků pro **potvrzení** . Po instalaci role **webový server (IIS)** není nutné restartovat server nebo službu IIS.

**Desktopové operační systémy Windows**

Povolte **konzolu pro správu služby IIS** a **webové služby**.

1. Přejděte na **Ovládací panely** > **programy** > **programy a funkce** > **zapnout nebo vypnout funkce systému Windows** (levá strana obrazovky).

1. Otevřete uzel **Internetová informační služba** . Otevřete uzel **Nástroje webové správy** .

1. Zaškrtněte políčko pro **konzolu pro správu služby IIS**.

1. Zaškrtněte políčko u **webových služeb**.

1. Přijměte výchozí funkce pro **webové služby** nebo upravte funkce služby IIS.

   **Ověřování systému Windows (volitelné)**  
   Chcete-li povolit ověřování systému Windows, rozbalte následující uzly: **webové služby** > **zabezpečení**webu. Vyberte funkci **ověřování systému Windows** . Další informace najdete v tématu [ověřování systému windows \<windowsAuthentication >](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) a [Konfigurace ověřování systému Windows](xref:security/authentication/windowsauth).

   **WebSockets (volitelné)**  
   Protokoly Websocket je podporována s ASP.NET Core 1.1 nebo vyšší. Chcete-li povolit objekty WebSockets, rozbalte následující uzly: **webové služby** > **funkce pro vývoj aplikací**. Vyberte funkci **protokolu WebSocket** . Další informace najdete v tématu [objekty WebSockets](xref:fundamentals/websockets).

1. Pokud instalace služby IIS vyžaduje restartování, restartujte systém.

![Konzola pro správu služby IIS a webové služby jsou vybrány v funkce Windows.](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a>Instalace .NET Core, který je hostitelem svazku

Nainstalujte *hostující sadu .NET Core* do hostitelského systému. Svazek nainstaluje modul runtime .NET Core, knihovnu .NET Core a [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module). Povolí modul ASP.NET Core aplikací ke spuštění za služby IIS.

> [!IMPORTANT]
> Pokud před službou IIS instalovanou sadou hostování, je nutné opravit instalaci sady. Spusťte instalační program sady hostování znovu po instalaci služby IIS.
>
> Pokud se hostující sada nainstaluje po instalaci 64 (x64) verze .NET Core, můžou se zdát, že sady SDK chybí ([nezjistily se žádné sady .NET Core SDK](xref:test/troubleshoot#no-net-core-sdks-were-detected)). Chcete-li tento problém vyřešit, přečtěte si téma <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.

### <a name="direct-download-current-version"></a>Přímé stažení (aktuální verze)

Stažení instalačního programu pomocí následujícího odkazu:

[Aktuální instalační program sady hostujících sad .NET Core (přímé stahování)](https://www.microsoft.com/net/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a>Starší verze instalačního programu

Chcete-li získat starší verzi instalačního programu:

1. Přejděte do [archivu stahování v rozhraní .NET](https://www.microsoft.com/net/download/archives).
1. V části **.NET Core**vyberte verzi .NET Core.
1. Ve sloupci **Spustit aplikace – modul runtime** vyhledejte řádek požadované verze modulu runtime .NET Core.
1. Stáhněte instalační program pomocí odkazu **& hostování sady prostředků modulu runtime** .

> [!WARNING]
> Některé instalační programy obsahují verze, které bylo dosaženo jejich konci životnosti (konce řádku) a již nejsou podporovány společností Microsoft. Další informace najdete v tématu [zásady podpory](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

### <a name="install-the-hosting-bundle"></a>Instalaci sady hostování

1. Spusťte instalační program na serveru. Při spuštění instalačního programu z příkazového prostředí správce jsou k dispozici následující parametry:

   * `OPT_NO_ANCM=1` &ndash; Přeskočit instalaci modulu ASP.NET Core.
   * `OPT_NO_RUNTIME=1` &ndash; přeskočení instalace modulu runtime .NET Core. Používá se, když Server hostuje jenom [samostatná nasazení (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).
   * `OPT_NO_SHAREDFX=1` &ndash; Přeskočit instalaci sdílené architektury ASP.NET (modul runtime ASP.NET). Používá se, když Server hostuje jenom [samostatná nasazení (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).
   * `OPT_NO_X86=1` &ndash; Přeskočit instalaci běhových prostředí x86. Tento parametr použijte, pokud víte, že nebudete hostovat 32 aplikace. Pokud existuje možnost, že v budoucnosti budete hostovat jak 32, tak i 64 aplikace, tento parametr nepoužívejte a nainstalujete oba moduly runtime.
   * `OPT_NO_SHARED_CONFIG_CHECK=1` &ndash; zakázat kontrolu použití sdílené konfigurace služby IIS, pokud je sdílená konfigurace (*ApplicationHost. config*) ve stejném počítači jako instalace služby IIS. *K dispozici jenom pro ASP.NET Core 2,2 nebo novější instalační programy hostujících prostředků.* Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.
1. Restartujte systém nebo v příkazovém prostředí spusťte následující příkazy:

   ```console
   net stop was /y
   net start w3svc
   ```
   Restartování služby IIS příjmem změnu systému provedené CESTU, která je proměnná prostředí, instalační služby.

ASP.NET Core nesplňuje chování při přeposílání pro vydání oprav pro balíčky sdílených rozhraní. Po upgradu sdíleného rozhraní instalací nové hostitelské sady restartujte systém nebo v příkazovém prostředí spusťte následující příkazy:

```console
net stop was /y
net start w3svc
```

> [!NOTE]
> Informace o sdílené konfiguraci služby IIS najdete v tématu [modul ASP.NET Core se sdílenou konfigurací služby IIS](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a>Nainstalujte nástroj nasazení webu při publikování pomocí sady Visual Studio

Při nasazování aplikací na servery s [nasazení webu](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)nainstalujte na server nejnovější verzi nasazení webu. Chcete-li nainstalovat Nasazení webu, použijte [instalační program webové platformy (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) nebo si instalační program Získejte přímo z webu [služby Stažení softwaru](https://www.microsoft.com/download/details.aspx?id=43717). Upřednostňovanou metodou je použití instalace webové platformy. Instalace webové platformy nabízí samostatné instalace a konfigurace pro poskytovatele hostingu.

## <a name="create-the-iis-site"></a>Vytvořte web služby IIS

1. V hostitelském systému vytvořte složku obsahující soubory a složky publikované aplikace. V následujícím kroku je jako fyzická cesta k aplikaci služba IIS poskytována jako cesta k této složce. Další informace o složce nasazení aplikace a rozložení souborů naleznete v tématu <xref:host-and-deploy/directory-structure>.

1. Ve Správci služby IIS otevřete uzel serveru na panelu **připojení** . Klikněte pravým tlačítkem na složku **weby** . V místní nabídce vyberte **Přidat web** .

1. Zadejte **název lokality** a nastavte **fyzickou cestu** ke složce pro nasazení aplikace. Zadejte konfiguraci **vazby** a vytvořte web výběrem **OK**:

   ![Zadejte název lokality, fyzickou cestu a název hostitele v kroku přidat web.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > **Nesmí** být použita vazba zástupných znaků nejvyšší úrovně (`http://*:80/` a `http://+:80`). Vazby nejvyšší úrovně zástupný znak můžete otevřít aplikaci tak, aby slabá místa zabezpečení. To platí pro silné a slabé zástupné znaky. Používejte explicitní hostitele názvy místo zástupných znaků. Vazba zástupných znaků subdomény (například `*.mysub.com`) nemá toto bezpečnostní riziko, pokud ovládáte celou nadřazenou doménu (na rozdíl od `*.com`, která je zranitelná). Další informace najdete v [části rfc7230 část-5,4](https://tools.ietf.org/html/rfc7230#section-5.4) .

1. V uzlu serveru vyberte **fondy aplikací**.

1. Klikněte pravým tlačítkem myši na fond aplikací webu a v místní nabídce vyberte **základní nastavení** .

1. V okně **Upravit fond aplikací** nastavte **verzi .NET CLR** na **bez spravovaného kódu**:

   ![Nastavit bez spravovaného kódu pro verze .NET CLR.](index/_static/edit-apppool-ws2016.png)

    ASP.NET Core běží v samostatném procesu a spravuje modulu runtime. ASP.NET Core nespoléhá na načítání CLR desktopu (.NET CLR)&mdash;se modul CLR (Common Language Runtime) pro .NET Core spouští k hostování aplikace v pracovním procesu. Nastavení **verze .NET CLR** na **žádný spravovaný kód** není volitelné, ale doporučuje se.

1. *ASP.NET Core 2,2 nebo novější*: 64 u samostatného [nasazeného nasazení](/dotnet/core/deploying/#self-contained-deployments-scd) (x64), které používá [model hostování v rámci procesu](#in-process-hosting-model), zakažte fond aplikací pro procesy 32 (x86).

   V bočním panelu **Akce** Správce služby IIS > **fondy aplikací**vyberte možnost **nastavit výchozí hodnoty fondu aplikací** nebo **Upřesnit nastavení**. Vyhledejte **možnost povolit 32-bitové aplikace** a nastavte hodnotu na `False`. Toto nastavení nemá vliv na aplikace nasazené pro [hostování mimo proces](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).

1. Potvrďte, že identita model procesu má příslušná oprávnění.

   Pokud se výchozí identita fondu aplikací (**model procesu** > **identity**) změní z **ApplicationPoolIdentity** na jinou identitu, ověřte, že Nová identita má požadovaná oprávnění pro přístup ke složce, databázi a dalším požadovaným prostředkům aplikace. Například fond aplikací vyžaduje čtení a zápisu do složky, kde aplikace čte a zapisuje soubory.

**Konfigurace ověřování systému Windows (volitelné)**  
Další informace najdete v tématu [Konfigurace ověřování systému Windows](xref:security/authentication/windowsauth).

## <a name="deploy-the-app"></a>Nasazení aplikace

Nasaďte aplikaci do složky **fyzické cesty** služby IIS, která byla navázána v části [Vytvoření webu služby IIS](#create-the-iis-site) . [Nasazení webu](/iis/publish/using-web-deploy/introduction-to-web-deploy) je doporučeným mechanismem pro nasazení, ale existuje několik možností pro přesun aplikace ze složky *publikování* projektu do složky pro nasazení hostitelského systému.

### <a name="web-deploy-with-visual-studio"></a>Nasazení webu pomocí sady Visual Studio

Informace o tom, jak vytvořit profil publikování pro použití s Nasazení webu, najdete v tématu [publikační profily sady Visual Studio pro ASP.NET Core nasazení aplikací](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) . Pokud poskytovatel hostingu poskytuje profil publikování nebo podporu pro jeho vytvoření, Stáhněte si jeho profil a importujte ho pomocí dialogového okna pro **publikování** sady Visual Studio:

![Publikovat stránku dialogového okna](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a>Nasazení webu mimo sadu Visual Studio

[Nasazení webu](/iis/publish/using-web-deploy/introduction-to-web-deploy) lze použít také mimo sadu Visual Studio z příkazového řádku. Další informace najdete v tématu [Nástroj pro nasazení webu](/iis/publish/using-web-deploy/use-the-web-deployment-tool).

### <a name="alternatives-to-web-deploy"></a>Alternativy k webové nasazení

K přesunu aplikace do hostitelského systému, jako je ruční kopírování, [xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy)nebo [PowerShell](/powershell/), použijte libovolný z několika způsobů.

Další informace o nasazení ASP.NET Core do služby IIS najdete v části [prostředky nasazení pro správce služby IIS](#deployment-resources-for-iis-administrators) .

## <a name="browse-the-website"></a>Přejděte na web

Po nasazení aplikace do hostitelského systému vytvořte žádost jednomu z veřejných koncových bodů aplikace.

V následujícím příkladu je lokalita svázána s **názvem hostitele** služby IIS `www.mysite.com` na **portu** `80`. Je učiněna žádost o `http://www.mysite.com`:

![Prohlížeč Microsoft Edge načetl úvodní stránka služby IIS.](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a>Soubory uzamčené nasazení

Při spuštění aplikace jsou zamknuté soubory ve složce pro nasazení. Uzamčené soubory nemohou být přepsána během nasazení. Chcete-li uvolnit uzamčené soubory v nasazení, zastavte fond aplikací pomocí **jednoho** z následujících přístupů:

* Použijte Nasazení webu a referenční `Microsoft.NET.Sdk.Web` v souboru projektu. Soubor *App_offline. htm* se umístí do kořenové složky adresáře webové aplikace. Když je soubor přítomen, modul ASP.NET Core aplikaci korektně ukončí a během nasazování zachová soubor *App_offline. htm* . Další informace najdete v referenčních informacích k [konfiguraci modulu ASP.NET Core](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).
* Ručně zastavte fond aplikací ve Správci služby IIS na serveru.
* Použití PowerShellu k vyřazení *App_offline. htm* (vyžaduje PowerShell 5 nebo novější):

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a>Ochrana dat

[Sada ASP.NET Core Data Protection Stack](xref:security/data-protection/introduction) je používána několika ASP.NET Core [middlewary](xref:fundamentals/middleware/index), včetně middlewaru používaného při ověřování. I v případě, že rozhraní API ochrany dat není voláno uživatelským kódem, je třeba nakonfigurovat ochranu dat pomocí skriptu nasazení nebo v uživatelském kódu, aby bylo možné vytvořit trvalé úložiště kryptografických [klíčů](xref:security/data-protection/implementation/key-management). Pokud není nakonfigurovaná ochrana dat, jsou klíče uložené v paměti a při restartování aplikace.

Pokud kanál klíče jsou uloženy v paměti, při restartování aplikace:

* Všechny tokeny ověřování na základě souborů cookie nejsou zneplatněny. 
* Uživatelé se musí znovu přihlásit v jejich další požadavek. 
* Všechna data chráněná pomocí aktualizační kanál, který klíč můžete už nebude možné dešifrovat. To může zahrnovat [CSRF tokeny](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) a [ASP.NET Core soubory cookie TempData MVC](xref:fundamentals/app-state#tempdata).

Pokud chcete v rámci služby IIS nakonfigurovat ochranu dat a zachovat přitom klíčového prstence, použijte **některý** z následujících přístupů:

* **Vytvoření klíčů registru ochrany dat**

  Používá aplikace ASP.NET Core klíče ochrany dat jsou uložené v registru, které jsou externí vzhledem k aplikacím. Pokud chcete zachovat klíče pro danou aplikaci, vytvořte klíče registru pro fond aplikací.

  Pro samostatnou instalaci služby IIS, která není součástí webfarmu, se dá [skript PowerShellu pro ochranu dat provision-AutoGenKeys. ps1](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) použít pro každý fond aplikací, který se používá v aplikaci ASP.NET Core. Tento skript vytvoří klíč registru HKLM registru, který je přístupný pouze pro účet pracovního procesu fondu aplikací aplikaci. Klíče se zašifrují neaktivní uložená data pomocí rozhraní DPAPI klíčem celý počítač.

  Ve webových farem lze nastavit aplikaci pro použití cesty UNC pro ukládání jeho data protection klíč kanál. Ve výchozím nastavení klíče ochrany dat nejsou šifrovány. Zajistěte, aby byly omezené na účet Windows, které aplikace běží pod oprávnění pro sdílené síťové složce. X X509 certifikát můžete použít k ochraně klíčů v klidovém stavu. Vezměte v úvahu mechanismus pro uživatelům umožní nahrát certifikáty: místo certifikátů do důvěryhodného certifikátu uživatele ukládat a ujistěte se, jsou k dispozici na všech počítačích, ve kterém běží aplikace uživatele. Podrobnosti najdete v tématu [Konfigurace ochrany ASP.NET Core dat](xref:security/data-protection/configuration/overview) .

* **Konfigurace fondu aplikací služby IIS pro načtení profilu uživatele**

  Toto nastavení se nachází v části **model procesu** v části **Rozšířená nastavení** fondu aplikací. Nastavte **načíst profil uživatele** na `True`. Když nastavíte `True`, klíče se ukládají v adresáři profilu uživatele a jsou chráněné pomocí DPAPI s klíčem specifickým pro uživatelský účet. Klíče jsou uchovány ve složce *% localappdata%/ASP.NET/DataProtection-Keys* .

  Musí být povolený i [atribut setProfileEnvironment](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) fondu aplikací. Výchozí hodnota `setProfileEnvironment` je `true`. V některých scénářích (například operační systém Windows) je `setProfileEnvironment` nastaveno na `false`. Pokud se klíče neukládají v adresáři profilu uživatele podle očekávání:

  1. Přejděte do složky *% windir%/system32/Inetsrv/config* .
  1. Otevřete soubor *ApplicationHost. config* .
  1. Vyhledejte element `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>`.
  1. Potvrďte, že atribut `setProfileEnvironment` není přítomen, což nastaví hodnotu `true`, nebo explicitně nastavte hodnotu atributu na `true`.

* **Použití systému souborů jako úložiště pro Key Ring**

  Upravte kód aplikace tak, aby [používal systém souborů jako úložiště ve službě Key Ring](xref:security/data-protection/configuration/overview). Použijte X509 certifikátu k ochraně klíče kanál a zajistit certifikát není důvěryhodný certifikát. Pokud certifikát podepsaný svým držitelem, můžete certifikát umístěte do úložiště důvěryhodných kořenových certifikátů.

  Pokud používáte IIS ve webové farmě:

  * Použití sdílené složky, ke kterému přístup všechny počítače.
  * Nasazení x X509 certifikát pro každý počítač. Konfigurace [ochrany dat v kódu](xref:security/data-protection/configuration/overview).

* **Nastavení zásad pro ochranu dat na úrovni počítače**

  Systém ochrany dat má omezená podpora pro nastavení výchozích [zásad](xref:security/data-protection/configuration/machine-wide-policy) pro všechny aplikace, které využívají rozhraní API pro ochranu dat. Další informace naleznete v tématu <xref:security/data-protection/introduction>.

## <a name="virtual-directories"></a>Virtuální adresáře

Aplikace ASP.NET Core nepodporují [virtuální adresáře služby IIS](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) . Aplikace může být hostována jako [dílčí aplikace](#sub-applications).

## <a name="sub-applications"></a>Dílčí aplikace

Aplikace ASP.NET Core může být hostovaná jako [podaplikace služby IIS (dílčí aplikace)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications). Sub – aplikace cesta stane součástí adresy URL kořenového aplikace.

Propojení statických prostředků v rámci dílčí aplikace by měla používat notaci vlnovku (`~/`). Znak tildy vlnovkou spustí [pomocnou nápovědu](xref:mvc/views/tag-helpers/intro) k předřazení pathbase dílčí aplikace na vykreslený relativní odkaz. V případě dílčí aplikace na `/subapp_path`se obrázek propojený s `src="~/image.png"` vykresluje jako `src="/subapp_path/image.png"`. Middleware kořenové aplikace statické soubory nelze zpracovat požadavek statický soubor. Žádost zpracovává Middleware sub aplikace statické soubory.

Pokud je atribut `src` statického prostředku nastavený na absolutní cestu (například `src="/image.png"`), odkaz se vykreslí bez pathbase dílčí aplikace. Middleware statických souborů v kořenové aplikaci se pokusí o poskytování assetu z [kořenového adresáře webu](xref:fundamentals/index#web-root)kořenové aplikace, což má za následek *404 – nenalezené* odpovědi, pokud není k dispozici statický prostředek z kořenové aplikace.

K hostování aplikace v ASP.NET Core jako podřízeným aplikacím v rámci jiné aplikace ASP.NET Core:

1. Vytvořte fond aplikací pro aplikaci sub. Nastavte **verzi .NET CLR** na **žádný spravovaný kód** , protože základní modul CLR (Common Language Runtime) pro .NET Core se spouští k hostování aplikace v pracovním procesu, ne CLR Desktop (.NET CLR).

1. Přidání kořenového webu s podřízeným aplikacím v rámci kořenového webu ve Správci služby IIS.

1. Klikněte pravým tlačítkem na složku dílčí aplikace ve Správci služby IIS a vyberte **převést na aplikaci**.

1. V dialogovém okně **Přidat aplikaci** použijte pro **fond aplikací** tlačítko **Vybrat** , abyste přiřadili fond aplikací, který jste vytvořili pro dílčí aplikaci. Vyberte **OK**.

Přiřazení fondu samostatné aplikace k podřízeným aplikacím je požadavek, pokud používáte model hostingu v procesu.

Další informace o modelu hostování v rámci procesu a konfiguraci modulu ASP.NET Core naleznete v tématu <xref:host-and-deploy/aspnet-core-module>.

## <a name="configuration-of-iis-with-webconfig"></a>Konfigurace služby IIS pomocí souboru web.config

Konfigurace služby IIS má vliv na `<system.webServer>` části *Web. config* pro scénáře služby IIS, které jsou funkční pro ASP.NET Core aplikace s modulem ASP.NET Core. Konfigurace služby IIS je třeba funkční dynamické komprese. Pokud je služba IIS nakonfigurovaná na úrovni serveru, aby používala dynamickou kompresi, `<urlCompression>` element v souboru *Web. config* aplikace ho může zakázat pro ASP.NET Core aplikace.

Další informace najdete v následujících tématech:

* [Odkaz na konfiguraci pro \<System. webServer >](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

Chcete-li nastavit proměnné prostředí pro jednotlivé aplikace spuštěné v izolovaných fondech aplikací (podporované pro IIS 10,0 nebo novější), přečtěte si část *příkazového řádku Appcmd. exe* v tématu [proměnné prostředí \<environmentVariables >](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) v referenční dokumentaci služby IIS.

## <a name="configuration-sections-of-webconfig"></a>Konfigurační oddíly souboru Web.config

Konfigurační oddíly aplikací ASP.NET 4. x v *souboru Web. config* nepoužívá aplikace ASP.NET Core pro konfiguraci:

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

Aplikace ASP.NET Core jsou nakonfigurované pomocí jiných poskytovatelů konfigurace. Další informace najdete v tématu [Konfigurace](xref:fundamentals/configuration/index).

## <a name="application-pools"></a>Fondy aplikací

Izolace fond aplikací je určeno model hostingu:

* Hostování v procesu &ndash; aplikace se vyžadují ke spuštění v samostatných fondech aplikací.
* Mimo procesy hostování &ndash; doporučujeme vzájemně izolovat aplikace tím, že spustíte každou aplikaci ve svém vlastním fondu aplikací.

Dialogové okno **Přidat web** do služby IIS je ve výchozím nastavení nastaveno na jeden fond aplikací na aplikaci. Když je zadaný **název lokality** , text se automaticky přenese do textového pole **fondu aplikací** . Nový fond aplikací je vytvořený pomocí názvu serveru po přidání serveru.

## <a name="application-pool-identity"></a>Identita fondu aplikací

Účet identita fondu aplikací umožňuje aplikaci běžet pod účtem jedinečný bez nutnosti vytvářet a spravovat domény nebo místní účty. IIS 8.0 nebo novější procesů pro pracovníka Správce služby IIS (WAS) vytvoří virtuální účet s názvem nového fondu aplikací a aplikace spouští fondu pracovních procesů pod tímto účtem ve výchozím nastavení. V konzole pro správu služby IIS v části **Upřesnit nastavení** fondu aplikací zajistěte, aby byla **Identita** nastavená na použití **ApplicationPoolIdentity**:

![Dialogové okno pokročilé nastavení fondu aplikací](index/_static/apppool-identity.png)

Proces správy služby IIS vytvoří zabezpečeného identifikátoru se název fondu aplikací v zabezpečení systému Windows. Pomocí této identity, je možné svázat prostředky. Ale tato identita není skutečný účet a nezobrazí se v konzole pro správu uživatelů Windows.

Pokud pracovní proces služby IIS vyžaduje přístup k aplikaci přes se zvýšenými oprávněními, upravte seznam řízení přístupu (ACL) pro adresář obsahující aplikaci:

1. Otevřete Průzkumníka Windows a přejděte do adresáře.

1. Klikněte pravým tlačítkem na adresář a vyberte **vlastnosti**.

1. Na kartě **zabezpečení** vyberte tlačítko **Upravit** a pak klikněte na tlačítko **Přidat** .

1. Vyberte tlačítko **umístění** a ujistěte se, že je vybraný systém.

1. Do pole **Zadejte názvy objektů pro výběr** oblasti zadejte **\\< služby IIS AppPool > APP_POOL_NAME** . Vyberte tlačítko pro **kontrolu názvů** . Pro službu *DefaultAppPool* ověřte názvy pomocí **služby IIS AppPool\DefaultAppPool**. Když je vybráno tlačítko pro **kontrolu názvů** , hodnota **DefaultAppPool** je uvedena v oblasti názvy objektů. Není možné zadat název fondu aplikací přímo do oblasti názvy objektu. Při kontrole názvu objektu použijte **\\< app_pool_name > formátu IIS AppPool** .

   ![Dialogová okna pro výběr uživatelů nebo skupin pro složku aplikace: název fondu aplikací "DefaultAppPool" se připojí k "rozhraní IIS AppPool\" v oblasti názvy objektů před výběrem" kontrolovat jména ".](index/_static/select-users-or-groups-1.png)

1. Vyberte **OK**.

   ![Vyberte uživatele nebo skupiny dialogové okno pro složky aplikace: Po výběru "Kontrola názvů", "DefaultAppPool" se zobrazí v objektu název objektu názvy oblasti.](index/_static/select-users-or-groups-2.png)

1. Ve výchozím nastavení by měla být udělena oprávnění ke čtení &amp;mu spouštění. Zadejte další oprávnění podle potřeby.

Přístup se dá taky udělit na příkazovém řádku pomocí nástroje **Icacls** . Pomocí příkazu *DefaultAppPool* jako příkladu se používá následující příkaz:

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

Další informace najdete v tématu [Icacls](/windows-server/administration/windows-commands/icacls) .

## <a name="http2-support"></a>Podpora HTTP/2

[Http/2](https://httpwg.org/specs/rfc7540.html) se podporuje s ASP.NET Core v následujících scénářích nasazení služby IIS:

* V procesu
  * Windows Server 2016 nebo Windows 10 nebo novější; IIS 10 nebo novější.
  * Protokol TLS 1.2 nebo vyšší připojení
* Mimo proces
  * Windows Server 2016 nebo Windows 10 nebo novější; IIS 10 nebo novější.
  * Veřejná připojení hraničních serverů používají protokol HTTP/2, ale připojení reverzního proxy [serveru Kestrel](xref:fundamentals/servers/kestrel) používá protokol HTTP/1.1.
  * Protokol TLS 1.2 nebo vyšší připojení

Pro nasazení v rámci procesu, když je navázáno připojení HTTP/2, `HTTP/2`sestavy [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) . Pro nasazení mimo proces, když je navázáno připojení HTTP/2, `HTTP/1.1`sestavy [protokolu HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) .

Další informace o modelech hostování v procesu a mimoprocesové procesy naleznete v tématu <xref:host-and-deploy/aspnet-core-module>.

HTTP/2 je standardně povolená. Připojení vrátit zpět k protokolu HTTP/1.1, pokud nedojde k připojení k protokolu HTTP/2. Další informace o konfiguraci HTTP/2 v nasazeních služby IIS najdete v tématu [http/2 ve službě IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).

## <a name="cors-preflight-requests"></a>Požadavky na kontrolu před výstupem CORS

*Tato část platí jenom pro ASP.NET Core aplikace, které cílí na .NET Framework.*

Pro ASP.NET Core aplikaci, která cílí na .NET Framework, požadavky na možnosti nejsou ve výchozím nastavení ve službě IIS předány do aplikace. Informace o tom, jak nakonfigurovat obslužné rutiny IIS aplikace v *souboru Web. config* , aby předávala požadavky na možnosti, najdete v tématu [Povolení žádostí o více zdrojů v ASP.NET webovém rozhraní API 2: jak CORS funguje](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).

## <a name="application-initialization-module-and-idle-timeout"></a>Modul inicializace aplikace a časový limit nečinnosti

Při hostování ve službě IIS pomocí modulu ASP.NET Core verze 2:

* [Modul inicializace aplikace](#application-initialization-module) &ndash; hostovaný [v procesu](#in-process-hosting-model) nebo [mimo proces](#out-of-process-hosting-model) aplikace se dá nakonfigurovat tak, aby se automaticky spouštěl v případě restartování nebo restartování serveru.
* [Časový limit nečinnosti](#idle-timeout) &ndash; hostovaný [v procesu](#in-process-hosting-model) aplikace se dá nakonfigurovat tak, aby nevypršel časový limit v období nečinnosti.

### <a name="application-initialization-module"></a>Modul inicializace aplikace

*Platí pro aplikace hostované v procesu a mimo proces.*

[Inicializace aplikace IIS](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) je funkce služby IIS, která do aplikace pošle požadavek HTTP, když se spustí nebo recykluje fond aplikací. Požadavek spustí spuštění aplikace. Ve výchozím nastavení služba IIS vydá požadavek na kořenovou adresu URL aplikace (`/`) k inicializaci aplikace (Další informace o konfiguraci najdete v [dalších zdrojích](#application-initialization-module-and-idle-timeout-additional-resources) ).

Potvrďte, že je povolená funkce role inicializace aplikace služby IIS:

V systémech Windows 7 nebo novějších stolních počítačích při používání služby IIS v místním prostředí:

1. Přejděte na **Ovládací panely** > **programy** > **programy a funkce** > **zapnout nebo vypnout funkce systému Windows** (levá strana obrazovky).
1. Otevřete **Internetová informační služba** > **webové služby** > funkce pro **vývoj aplikací**.
1. Zaškrtněte políčko pro **inicializaci aplikace**.

V systému Windows Server 2008 R2 nebo novějším:

1. Otevřete **Průvodce přidáním rolí a funkcí**.
1. Na panelu **Vybrat služby rolí** otevřete uzel **vývoj aplikací** .
1. Zaškrtněte políčko pro **inicializaci aplikace**.

Pomocí některého z následujících přístupů povolte modul inicializace aplikace pro danou lokalitu:

* Pomocí Správce služby IIS:

  1. Na panelu **připojení** vyberte **fondy aplikací** .
  1. V seznamu klikněte pravým tlačítkem na fond aplikací aplikace a vyberte **Upřesnit nastavení**.
  1. Výchozí **režim spuštění** je **OnDemand**. Nastavte **režim Start** na **AlwaysRunning**. Vyberte **OK**.
  1. Otevřete uzel **lokality** na panelu **připojení** .
  1. Klikněte pravým tlačítkem na aplikaci a vyberte **Spravovat web** > **Upřesnit nastavení**.
  1. Výchozí nastavení pro **Povolení přednačtení** je **false**. Nastavte **předběžné načtení povoleno** na **hodnotu true**. Vyberte **OK**.

* Pomocí souboru *Web. config*přidejte prvek `<applicationInitialization>` s `doAppInitAfterRestart` nastavenou na `true` do `<system.webServer>` prvků v souboru *Web. config* aplikace:

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <applicationInitialization doAppInitAfterRestart="true" />
      </system.webServer>
    </location>
  </configuration>
  ```

### <a name="idle-timeout"></a>Časový limit nečinnosti

*Platí jenom pro aplikace hostované v procesu.*

Pokud chcete zabránit volnoběhu aplikace, nastavte časový limit nečinnosti fondu aplikací pomocí Správce služby IIS:

1. Na panelu **připojení** vyberte **fondy aplikací** .
1. V seznamu klikněte pravým tlačítkem na fond aplikací aplikace a vyberte **Upřesnit nastavení**.
1. Výchozí **časový limit nečinnosti (minuty)** je **20** minut. Nastavte **časový limit nečinnosti (minuty)** na **0** (nula). Vyberte **OK**.
1. Recyklujte pracovní proces.

Chcete-li zabránit aplikacím hostovaným v [procesu](#out-of-process-hosting-model) vypršení časového limitu, použijte některý z následujících přístupů:

* Pomocí příkazu otestujete aplikaci z externí služby, aby byla spuštěná.
* Pokud aplikace hostuje jenom služby na pozadí, vyhněte se hostování služby IIS a použití [služby Windows k hostování aplikace ASP.NET Core](xref:host-and-deploy/windows-service).

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a>Modul inicializace aplikace a další prostředky časového limitu nečinnosti

* [Inicializace aplikace IIS 8,0](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* [Inicializace aplikace \<applicationInitialization >](/iis/configuration/system.webserver/applicationinitialization/).
* [Nastavení modelu procesu pro fond aplikací \<processModel >](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).

## <a name="deployment-resources-for-iis-administrators"></a>Materiály pro nasazení pro správce služby IIS

* [Dokumentace ke službě IIS](/iis)
* [Začínáme ve službě IIS pomocí Správce služby IIS](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [Nasazení aplikace .NET Core](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a>Další zdroje

* <xref:test/troubleshoot>
* <xref:index>
* [Oficiální web Microsoft IIS](https://www.iis.net/)
* [Knihovna technických obsahu pro Windows Server](/windows-server/windows-server)
* [HTTP/2 ve službě IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Kurz týkající se publikování ASP.NET Core aplikace na server služby IIS najdete v tématu <xref:tutorials/publish-to-iis>.

[Instalace hostující sady .NET Core](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a>Podporované operační systémy

Podporovány jsou následující operační systémy:

* Windows 7 nebo novější
* Windows Server 2008 R2 nebo novější

[Http. sys Server](xref:fundamentals/servers/httpsys) (dříve označovaný jako weblisten) nefunguje v konfiguraci reverzního proxy serveru se službou IIS. Použijte [Server Kestrel](xref:fundamentals/servers/kestrel).

Informace o hostování v Azure najdete v tématu <xref:host-and-deploy/azure-apps/index>.

Pokyny k řešení potíží najdete v tématu <xref:test/troubleshoot>.

## <a name="supported-platforms"></a>Podporované platformy

Podporují se aplikace publikované pro nasazení 32 (x86) nebo 64-bit (x64). Nasazení 32 aplikace s 32 (x86) .NET Core SDK, pokud aplikace:

* Vyžaduje větší dostupný adresní prostor virtuální paměti pro 64 aplikaci.
* Vyžaduje větší velikost zásobníku služby IIS.
* Má 64 nativní závislosti.

K publikování 64 aplikace použijte 64 .NET Core SDK (x64). V hostitelském systému musí být nainstalován 64 modul runtime.

## <a name="hosting-models"></a>Modely hostingu

### <a name="in-process-hosting-model"></a>Model hostování v procesu

Při použití hostování v rámci procesu ASP.NET Core aplikace běží ve stejném procesu jako jeho pracovní proces služby IIS. Hostování v rámci procesů poskytují lepší výkon než hostování mimo procesy, protože požadavky nejsou proxy serverem přes adaptér zpětné smyčky, síťové rozhraní, které vrátí odchozí síťový provoz zpátky do stejného počítače. Služba IIS zpracovává správu procesů pomocí [aktivační služby procesů systému Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

[Modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module):

* Provede inicializaci aplikace.
  * Načte [CoreCLR](/dotnet/standard/glossary#coreclr).
  * Volá `Program.Main`.
* Zpracovává životnost nativního požadavku služby IIS.

Model hostování v rámci procesu není podporován pro aplikace ASP.NET Core, které cílí na .NET Framework.

Následující diagram znázorňuje vztah mezi službou IIS, modulem ASP.NET Core a aplikací hostovanou v procesu:

![ASP.NET Core modul ve scénáři hostování v rámci procesu](index/_static/ancm-inprocess.png)

Požadavek přijde z webu do ovladače HTTP. sys v režimu jádra. Ovladač směruje nativní požadavek na IIS na konfigurovaném portu webu, obvykle 80 (HTTP) nebo 443 (HTTPS). Modul ASP.NET Core obdrží nativní požadavek a předá ho k serveru HTTP služby IIS (`IISHttpServer`). HTTP server IIS je vnitroprocesové implementace v rámci procesového serveru pro službu IIS, která převádí požadavek z nativního na spravovanou.

Poté, co server HTTP služby IIS požadavek zpracuje, je požadavek vložen do kanálu middleware ASP.NET Core. Kanál middlewaru zpracovává požadavek a předá ho jako instanci `HttpContext` do logiky aplikace. Odpověď aplikace se předává zpět službě IIS prostřednictvím serveru IIS HTTP. Služba IIS odešle odpověď klientovi, který žádost inicioval.

Vnitroprocesové hostování v rámci procesu je výslovný souhlas pro existující aplikace, ale [dotnet nové](/dotnet/core/tools/dotnet-new) šablony jsou výchozí pro všechny scénáře hostování v rámci procesu pro všechny služby IIS a IIS Express.

`CreateDefaultBuilder` přidá instanci <xref:Microsoft.AspNetCore.Hosting.Server.IServer> voláním metody <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> pro spuštění [CoreCLR](/dotnet/standard/glossary#coreclr) a hostování aplikace uvnitř pracovního procesu služby IIS (*W3wp. exe* nebo *IISExpress. exe*). Testy výkonu označují, že hostování aplikace .NET Core v rámci procesu přináší výrazně vyšší propustnost žádostí v porovnání s hostováním aplikací mimo proces a požadavky na proxy serveru na [Kestrel](xref:fundamentals/servers/kestrel) Server.

### <a name="out-of-process-hosting-model"></a>Model hostování mimo proces

Vzhledem k tomu, že ASP.NET Core aplikace běží v procesu odděleném od pracovního procesu služby IIS, modul ASP.NET Core zpracovává správu procesů. Modul spustí proces pro aplikaci ASP.NET Core, když první požadavek dorazí a restartuje aplikaci, pokud se ukončí nebo dojde k chybě. To je v podstatě stejné chování jako u aplikací, které běží v procesu, které jsou spravovány službou was [(Windows Process Activation Service)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

Následující diagram znázorňuje vztah mezi službou IIS, modulem ASP.NET Core a aplikací hostovanou mimo proces:

![ASP.NET Core modul ve scénáři hostování mimo proces](index/_static/ancm-outofprocess.png)

Požadavky přicházející z webu do ovladače HTTP. sys v režimu jádra. Ovladač směruje požadavky do služby IIS na konfigurovaném portu webu, obvykle 80 (HTTP) nebo 443 (HTTPS). Modul předá požadavky do Kestrel na náhodném portu pro aplikaci, což není port 80 nebo 443.

Modul Určuje port prostřednictvím proměnné prostředí při spuštění a rozšíření <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> nakonfiguruje server tak, aby naslouchal `http://localhost:{PORT}`. Budou provedeny další kontroly a požadavky, které nepocházejí z modulu, jsou odmítnuty. Modul nepodporuje předávání HTTPS, takže požadavky se předávají přes protokol HTTP i v případě, že je služba IIS prostřednictvím protokolu HTTPS přijímá.

Po Kestrel žádosti z modulu se požadavek odešle do kanálu middlewaru ASP.NET Core. Kanál middlewaru zpracovává požadavek a předá ho jako instanci `HttpContext` do logiky aplikace. Middleware přidaný integrací služby IIS: aktualizace schématu, vzdálené IP adresy a pathbase pro přesměrování požadavku do Kestrel. Odpověď aplikace se předává zpátky do služby IIS, která ji přenáší zpátky do klienta HTTP, který žádost inicioval.

Pokyny ke konfiguraci ASP.NET Core modulu najdete v tématu <xref:host-and-deploy/aspnet-core-module>.

Další informace o hostování najdete v tématu [hostitel v ASP.NET Core](xref:fundamentals/index#host).

## <a name="application-configuration"></a>Konfigurace aplikací

### <a name="enable-the-iisintegration-components"></a>Povolit IISIntegration součásti

Při sestavování hostitele v `CreateWebHostBuilder` (*program.cs*) volejte <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> a povolte integraci služby IIS:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

Další informace o `CreateDefaultBuilder`najdete v tématu <xref:fundamentals/host/web-host#set-up-a-host>.

### <a name="iis-options"></a>Možnosti služby IIS

**Model hostování v procesu**

Pokud chcete nakonfigurovat možnosti serveru IIS, zahrňte do <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>konfiguraci služby pro <xref:Microsoft.AspNetCore.Builder.IISServerOptions>. Následující příklad zakazuje AutomaticAuthentication:

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| Možnost                         | Výchozí | Nastavení |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | Pokud `true`, server IIS nastaví `HttpContext.User` ověřený [ověřováním systému Windows](xref:security/authentication/windowsauth). Pokud `false`, Server poskytuje pouze identitu pro `HttpContext.User` a reaguje na výzvy, které jsou výslovně požadovány `AuthenticationScheme`. Aby mohla funkce `AutomaticAuthentication` fungovat, musí být ve službě IIS povolená možnost ověřování systému Windows. Další informace najdete v tématu [ověřování systému Windows](xref:security/authentication/windowsauth). |
| `AuthenticationDisplayName`    | `null`  | Nastaví zobrazovaný název, který se uživatelům na přihlašovací stránky zobrazí. |

**Model hostování mimo proces**

Pokud chcete nakonfigurovat možnosti služby IIS, zahrňte do <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>konfiguraci služby pro <xref:Microsoft.AspNetCore.Builder.IISOptions>. Následující příklad zabrání aplikaci v naplnění `HttpContext.Connection.ClientCertificate`:

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| Možnost                         | Výchozí | Nastavení |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | Pokud `true`, [middleware integrace služby IIS](#enable-the-iisintegration-components) nastaví `HttpContext.User` ověřováno [ověřováním systému Windows](xref:security/authentication/windowsauth). Pokud `false`, middleware poskytuje pouze identitu pro `HttpContext.User` a reaguje na výzvy, které jsou explicitně požadovány `AuthenticationScheme`. Aby mohla funkce `AutomaticAuthentication` fungovat, musí být ve službě IIS povolená možnost ověřování systému Windows. Další informace najdete v tématu [ověřování systému Windows](xref:security/authentication/windowsauth) . |
| `AuthenticationDisplayName`    | `null`  | Nastaví zobrazovaný název, který se uživatelům na přihlašovací stránky zobrazí. |
| `ForwardClientCertificate`     | `true`  | Pokud se `true` a nachází se hlavička žádosti `MS-ASPNETCORE-CLIENTCERT`, `HttpContext.Connection.ClientCertificate` se naplní. |

### <a name="proxy-server-and-load-balancer-scenarios"></a>Proxy server a scénáře pro nástroj pro vyrovnávání zatížení

[Služba IIS Integration middleware](#enable-the-iisintegration-components), která konfiguruje middleware předávaných hlaviček, a modul ASP.NET Core je nakonfigurován tak, aby přenesl schéma (http/https) a vzdálenou IP adresu, kam pochází požadavek. Další konfigurace může být nezbytný pro aplikací hostovaných za službou další proxy servery a nástroje pro vyrovnávání zatížení. Další informace najdete v tématu [konfigurace ASP.NET Core pro práci se servery proxy a nástroji pro vyrovnávání zatížení](xref:host-and-deploy/proxy-load-balancer).

### <a name="webconfig-file"></a>soubor Web.config

Soubor *Web. config* konfiguruje [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module). Vytváření, transformace a publikování souboru *Web. config* je zpracováno cílem MSBuild (`_TransformWebConfig`) při publikování projektu. Tento cíl je k dispozici v rámci cílů web SDK (`Microsoft.NET.Sdk.Web`). V horní části souboru projektu je sada SDK:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Pokud soubor *Web. config* není v projektu přítomen, je vytvořen soubor se správným *processPath* a *argumenty* pro konfiguraci modulu ASP.NET Core a přesunutí do [publikovaného výstupu](xref:host-and-deploy/directory-structure).

Pokud je soubor *Web. config* přítomen v projektu, soubor je transformován se správným *processPath* a *argumenty* pro konfiguraci modulu ASP.NET Core a přesunutí do publikovaného výstupu. Transformace nezmění nastavení konfigurace služby IIS v souboru.

Soubor *Web. config* může poskytovat další nastavení konfigurace služby IIS, která ovládají aktivní moduly IIS. Informace o modulech služby IIS, které jsou schopné zpracovávat požadavky s ASP.NET Core aplikacemi, najdete v tématu [IIS modules](xref:host-and-deploy/iis/modules) .

Chcete-li webové sadě SDK zabránit ve transformaci souboru *Web. config* , použijte vlastnost **\<IsTransformWebConfigDisabled >** v souboru projektu:

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

Při zakazování webové sady SDK z transformace souboru by měl vývojář *processPath* a *argumenty* ručně nastavit. Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module>.

### <a name="webconfig-file-location"></a>Umístění souboru Web.config

Aby bylo možné správně nastavit [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) , musí být soubor *Web. config* přítomen v [kořenové cestě obsahu](xref:fundamentals/index#content-root) (obvykle v základní cestě aplikace) nasazené aplikace. Jedná se o stejné umístění jako fyzická cesta webu služby IIS k dispozici. Soubor *Web. config* je vyžadován v kořenu aplikace, aby bylo možné publikovat více aplikací pomocí nasazení webu.

Citlivé soubory existují na fyzické cestě aplikace, jako je například *\<sestavení >. runtimeconfig. JSON*, *\<Assembly >. XML* (dokumentační dokumentace xml), a *\<sestavení >. DEPS. JSON*. Když je přítomen soubor *Web. config* a lokalita se spouští normálně, služba IIS tyto citlivé soubory po vyžádání neobsluhuje. Pokud soubor *Web. config* chybí, je nesprávně pojmenovaný nebo nemůže nakonfigurovat lokalitu pro normální spuštění, služba IIS může obsluhovat citlivé soubory veřejně.

**Soubor *Web. config* musí být současně přítomen v nasazení, správně pojmenován a může nakonfigurovat lokalitu pro normální spuštění. Nikdy neodstraňujte soubor *Web. config* z produkčního nasazení.**

### <a name="transform-webconfig"></a>Transformace souboru web.config

Pokud potřebujete transformovat *Web. config* při publikování (například nastavit proměnné prostředí na základě konfigurace, profilu nebo prostředí), přečtěte si téma <xref:host-and-deploy/iis/transform-webconfig>.

## <a name="iis-configuration"></a>Konfigurace služby IIS

**Operační systémy Windows Server**

Povolte roli serveru **webový server (IIS)** a vytvořte služby rolí.

1. Použijte průvodce **přidáním rolí a funkcí** z nabídky **Správa** nebo odkazu v **Správce serveru**. V kroku **role serveru** zaškrtněte políčko **webový server (IIS)** .

   ![V kroku výběr serveru role je vybrána role webového serveru IIS.](index/_static/server-roles-ws2016.png)

1. Po kroku **funkce** se krok **služby rolí** načte pro webový server (IIS). Vyberte požadovaných služeb role služby IIS nebo přijměte výchozí nastavení role služby za předpokladu.

   ![Výchozí služby rolí jsou vybrané v kroku vybrat roli služby.](index/_static/role-services-ws2016.png)

   **Ověřování systému Windows (volitelné)**  
   Chcete-li povolit ověřování systému Windows, rozbalte následující uzly: **webový Server** > **zabezpečení**. Vyberte funkci **ověřování systému Windows** . Další informace najdete v tématu [ověřování systému windows \<windowsAuthentication >](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) a [Konfigurace ověřování systému Windows](xref:security/authentication/windowsauth).

   **WebSockets (volitelné)**  
   Protokoly Websocket je podporována s ASP.NET Core 1.1 nebo vyšší. Pokud chcete povolit objekty WebSockets, rozbalte následující uzly: **webový Server** > **vývoj aplikací**. Vyberte funkci **protokolu WebSocket** . Další informace najdete v tématu [objekty WebSockets](xref:fundamentals/websockets).

1. Chcete-li nainstalovat roli a služby webového serveru, postupujte podle kroků pro **potvrzení** . Po instalaci role **webový server (IIS)** není nutné restartovat server nebo službu IIS.

**Desktopové operační systémy Windows**

Povolte **konzolu pro správu služby IIS** a **webové služby**.

1. Přejděte na **Ovládací panely** > **programy** > **programy a funkce** > **zapnout nebo vypnout funkce systému Windows** (levá strana obrazovky).

1. Otevřete uzel **Internetová informační služba** . Otevřete uzel **Nástroje webové správy** .

1. Zaškrtněte políčko pro **konzolu pro správu služby IIS**.

1. Zaškrtněte políčko u **webových služeb**.

1. Přijměte výchozí funkce pro **webové služby** nebo upravte funkce služby IIS.

   **Ověřování systému Windows (volitelné)**  
   Chcete-li povolit ověřování systému Windows, rozbalte následující uzly: **webové služby** > **zabezpečení**webu. Vyberte funkci **ověřování systému Windows** . Další informace najdete v tématu [ověřování systému windows \<windowsAuthentication >](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) a [Konfigurace ověřování systému Windows](xref:security/authentication/windowsauth).

   **WebSockets (volitelné)**  
   Protokoly Websocket je podporována s ASP.NET Core 1.1 nebo vyšší. Chcete-li povolit objekty WebSockets, rozbalte následující uzly: **webové služby** > **funkce pro vývoj aplikací**. Vyberte funkci **protokolu WebSocket** . Další informace najdete v tématu [objekty WebSockets](xref:fundamentals/websockets).

1. Pokud instalace služby IIS vyžaduje restartování, restartujte systém.

![Konzola pro správu služby IIS a webové služby jsou vybrány v funkce Windows.](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a>Instalace .NET Core, který je hostitelem svazku

Nainstalujte *hostující sadu .NET Core* do hostitelského systému. Svazek nainstaluje modul runtime .NET Core, knihovnu .NET Core a [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module). Povolí modul ASP.NET Core aplikací ke spuštění za služby IIS.

> [!IMPORTANT]
> Pokud před službou IIS instalovanou sadou hostování, je nutné opravit instalaci sady. Spusťte instalační program sady hostování znovu po instalaci služby IIS.
>
> Pokud se hostující sada nainstaluje po instalaci 64 (x64) verze .NET Core, můžou se zdát, že sady SDK chybí ([nezjistily se žádné sady .NET Core SDK](xref:test/troubleshoot#no-net-core-sdks-were-detected)). Chcete-li tento problém vyřešit, přečtěte si téma <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.

### <a name="direct-download-current-version"></a>Přímé stažení (aktuální verze)

Stažení instalačního programu pomocí následujícího odkazu:

[Aktuální instalační program sady hostujících sad .NET Core (přímé stahování)](https://www.microsoft.com/net/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a>Starší verze instalačního programu

Chcete-li získat starší verzi instalačního programu:

1. Přejděte do [archivu stahování v rozhraní .NET](https://www.microsoft.com/net/download/archives).
1. V části **.NET Core**vyberte verzi .NET Core.
1. Ve sloupci **Spustit aplikace – modul runtime** vyhledejte řádek požadované verze modulu runtime .NET Core.
1. Stáhněte instalační program pomocí odkazu **& hostování sady prostředků modulu runtime** .

> [!WARNING]
> Některé instalační programy obsahují verze, které bylo dosaženo jejich konci životnosti (konce řádku) a již nejsou podporovány společností Microsoft. Další informace najdete v tématu [zásady podpory](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

### <a name="install-the-hosting-bundle"></a>Instalaci sady hostování

1. Spusťte instalační program na serveru. Při spuštění instalačního programu z příkazového prostředí správce jsou k dispozici následující parametry:

   * `OPT_NO_ANCM=1` &ndash; Přeskočit instalaci modulu ASP.NET Core.
   * `OPT_NO_RUNTIME=1` &ndash; přeskočení instalace modulu runtime .NET Core. Používá se, když Server hostuje jenom [samostatná nasazení (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).
   * `OPT_NO_SHAREDFX=1` &ndash; Přeskočit instalaci sdílené architektury ASP.NET (modul runtime ASP.NET). Používá se, když Server hostuje jenom [samostatná nasazení (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).
   * `OPT_NO_X86=1` &ndash; Přeskočit instalaci běhových prostředí x86. Tento parametr použijte, pokud víte, že nebudete hostovat 32 aplikace. Pokud existuje možnost, že v budoucnosti budete hostovat jak 32, tak i 64 aplikace, tento parametr nepoužívejte a nainstalujete oba moduly runtime.
   * `OPT_NO_SHARED_CONFIG_CHECK=1` &ndash; zakázat kontrolu použití sdílené konfigurace služby IIS, pokud je sdílená konfigurace (*ApplicationHost. config*) ve stejném počítači jako instalace služby IIS. *K dispozici jenom pro ASP.NET Core 2,2 nebo novější instalační programy hostujících prostředků.* Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.
1. Restartujte systém nebo v příkazovém prostředí spusťte následující příkazy:

   ```console
   net stop was /y
   net start w3svc
   ```
   Restartování služby IIS příjmem změnu systému provedené CESTU, která je proměnná prostředí, instalační služby.

Při instalaci hostujícího balíčku není nutné ručně zastavit jednotlivé weby ve službě IIS. Hostované aplikace (weby IIS) se restartují po restartování služby IIS. Aplikace se po přijetí první žádosti spustí znovu, včetně [modulu inicializace aplikace](#application-initialization-module-and-idle-timeout).

ASP.NET Core přijímá postup při přeposílání pro vydání oprav pro balíčky sdílených balíčků rozhraní. Když se aplikace hostované službou IIS restartují se službou IIS, aplikace při přijetí jejich prvního požadavku načtou nejnovější verze oprav jejich odkazovaných balíčků. Pokud se služba IIS nerestartuje, aplikace se restartují a projeví se chování při přeposílání, když se jejich pracovní procesy recyklují a obdrží první požadavek.

> [!NOTE]
> Informace o sdílené konfiguraci služby IIS najdete v tématu [modul ASP.NET Core se sdílenou konfigurací služby IIS](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a>Nainstalujte nástroj nasazení webu při publikování pomocí sady Visual Studio

Při nasazování aplikací na servery s [nasazení webu](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)nainstalujte na server nejnovější verzi nasazení webu. Chcete-li nainstalovat Nasazení webu, použijte [instalační program webové platformy (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) nebo si instalační program Získejte přímo z webu [služby Stažení softwaru](https://www.microsoft.com/download/details.aspx?id=43717). Upřednostňovanou metodou je použití instalace webové platformy. Instalace webové platformy nabízí samostatné instalace a konfigurace pro poskytovatele hostingu.

## <a name="create-the-iis-site"></a>Vytvořte web služby IIS

1. V hostitelském systému vytvořte složku obsahující soubory a složky publikované aplikace. V následujícím kroku je jako fyzická cesta k aplikaci služba IIS poskytována jako cesta k této složce. Další informace o složce nasazení aplikace a rozložení souborů naleznete v tématu <xref:host-and-deploy/directory-structure>.

1. Ve Správci služby IIS otevřete uzel serveru na panelu **připojení** . Klikněte pravým tlačítkem na složku **weby** . V místní nabídce vyberte **Přidat web** .

1. Zadejte **název lokality** a nastavte **fyzickou cestu** ke složce pro nasazení aplikace. Zadejte konfiguraci **vazby** a vytvořte web výběrem **OK**:

   ![Zadejte název lokality, fyzickou cestu a název hostitele v kroku přidat web.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > **Nesmí** být použita vazba zástupných znaků nejvyšší úrovně (`http://*:80/` a `http://+:80`). Vazby nejvyšší úrovně zástupný znak můžete otevřít aplikaci tak, aby slabá místa zabezpečení. To platí pro silné a slabé zástupné znaky. Používejte explicitní hostitele názvy místo zástupných znaků. Vazba zástupných znaků subdomény (například `*.mysub.com`) nemá toto bezpečnostní riziko, pokud ovládáte celou nadřazenou doménu (na rozdíl od `*.com`, která je zranitelná). Další informace najdete v [části rfc7230 část-5,4](https://tools.ietf.org/html/rfc7230#section-5.4) .

1. V uzlu serveru vyberte **fondy aplikací**.

1. Klikněte pravým tlačítkem myši na fond aplikací webu a v místní nabídce vyberte **základní nastavení** .

1. V okně **Upravit fond aplikací** nastavte **verzi .NET CLR** na **bez spravovaného kódu**:

   ![Nastavit bez spravovaného kódu pro verze .NET CLR.](index/_static/edit-apppool-ws2016.png)

    ASP.NET Core běží v samostatném procesu a spravuje modulu runtime. ASP.NET Core nespoléhá na načítání CLR desktopu (.NET CLR)&mdash;se modul CLR (Common Language Runtime) pro .NET Core spouští k hostování aplikace v pracovním procesu. Nastavení **verze .NET CLR** na **žádný spravovaný kód** není volitelné, ale doporučuje se.

1. *ASP.NET Core 2,2 nebo novější*: 64 u samostatného [nasazeného nasazení](/dotnet/core/deploying/#self-contained-deployments-scd) (x64), které používá [model hostování v rámci procesu](#in-process-hosting-model), zakažte fond aplikací pro procesy 32 (x86).

   V bočním panelu **Akce** Správce služby IIS > **fondy aplikací**vyberte možnost **nastavit výchozí hodnoty fondu aplikací** nebo **Upřesnit nastavení**. Vyhledejte **možnost povolit 32-bitové aplikace** a nastavte hodnotu na `False`. Toto nastavení nemá vliv na aplikace nasazené pro [hostování mimo proces](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).

1. Potvrďte, že identita model procesu má příslušná oprávnění.

   Pokud se výchozí identita fondu aplikací (**model procesu** > **identity**) změní z **ApplicationPoolIdentity** na jinou identitu, ověřte, že Nová identita má požadovaná oprávnění pro přístup ke složce, databázi a dalším požadovaným prostředkům aplikace. Například fond aplikací vyžaduje čtení a zápisu do složky, kde aplikace čte a zapisuje soubory.

**Konfigurace ověřování systému Windows (volitelné)**  
Další informace najdete v tématu [Konfigurace ověřování systému Windows](xref:security/authentication/windowsauth).

## <a name="deploy-the-app"></a>Nasazení aplikace

Nasaďte aplikaci do složky **fyzické cesty** služby IIS, která byla navázána v části [Vytvoření webu služby IIS](#create-the-iis-site) . [Nasazení webu](/iis/publish/using-web-deploy/introduction-to-web-deploy) je doporučeným mechanismem pro nasazení, ale existuje několik možností pro přesun aplikace ze složky *publikování* projektu do složky pro nasazení hostitelského systému.

### <a name="web-deploy-with-visual-studio"></a>Nasazení webu pomocí sady Visual Studio

Informace o tom, jak vytvořit profil publikování pro použití s Nasazení webu, najdete v tématu [publikační profily sady Visual Studio pro ASP.NET Core nasazení aplikací](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) . Pokud poskytovatel hostingu poskytuje profil publikování nebo podporu pro jeho vytvoření, Stáhněte si jeho profil a importujte ho pomocí dialogového okna pro **publikování** sady Visual Studio:

![Publikovat stránku dialogového okna](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a>Nasazení webu mimo sadu Visual Studio

[Nasazení webu](/iis/publish/using-web-deploy/introduction-to-web-deploy) lze použít také mimo sadu Visual Studio z příkazového řádku. Další informace najdete v tématu [Nástroj pro nasazení webu](/iis/publish/using-web-deploy/use-the-web-deployment-tool).

### <a name="alternatives-to-web-deploy"></a>Alternativy k webové nasazení

K přesunu aplikace do hostitelského systému, jako je ruční kopírování, [xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy)nebo [PowerShell](/powershell/), použijte libovolný z několika způsobů.

Další informace o nasazení ASP.NET Core do služby IIS najdete v části [prostředky nasazení pro správce služby IIS](#deployment-resources-for-iis-administrators) .

## <a name="browse-the-website"></a>Přejděte na web

Po nasazení aplikace do hostitelského systému vytvořte žádost jednomu z veřejných koncových bodů aplikace.

V následujícím příkladu je lokalita svázána s **názvem hostitele** služby IIS `www.mysite.com` na **portu** `80`. Je učiněna žádost o `http://www.mysite.com`:

![Prohlížeč Microsoft Edge načetl úvodní stránka služby IIS.](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a>Soubory uzamčené nasazení

Při spuštění aplikace jsou zamknuté soubory ve složce pro nasazení. Uzamčené soubory nemohou být přepsána během nasazení. Chcete-li uvolnit uzamčené soubory v nasazení, zastavte fond aplikací pomocí **jednoho** z následujících přístupů:

* Použijte Nasazení webu a referenční `Microsoft.NET.Sdk.Web` v souboru projektu. Soubor *App_offline. htm* se umístí do kořenové složky adresáře webové aplikace. Když je soubor přítomen, modul ASP.NET Core aplikaci korektně ukončí a během nasazování zachová soubor *App_offline. htm* . Další informace najdete v referenčních informacích k [konfiguraci modulu ASP.NET Core](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).
* Ručně zastavte fond aplikací ve Správci služby IIS na serveru.
* Použití PowerShellu k vyřazení *App_offline. htm* (vyžaduje PowerShell 5 nebo novější):

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a>Ochrana dat

[Sada ASP.NET Core Data Protection Stack](xref:security/data-protection/introduction) je používána několika ASP.NET Core [middlewary](xref:fundamentals/middleware/index), včetně middlewaru používaného při ověřování. I v případě, že rozhraní API ochrany dat není voláno uživatelským kódem, je třeba nakonfigurovat ochranu dat pomocí skriptu nasazení nebo v uživatelském kódu, aby bylo možné vytvořit trvalé úložiště kryptografických [klíčů](xref:security/data-protection/implementation/key-management). Pokud není nakonfigurovaná ochrana dat, jsou klíče uložené v paměti a při restartování aplikace.

Pokud kanál klíče jsou uloženy v paměti, při restartování aplikace:

* Všechny tokeny ověřování na základě souborů cookie nejsou zneplatněny. 
* Uživatelé se musí znovu přihlásit v jejich další požadavek. 
* Všechna data chráněná pomocí aktualizační kanál, který klíč můžete už nebude možné dešifrovat. To může zahrnovat [CSRF tokeny](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) a [ASP.NET Core soubory cookie TempData MVC](xref:fundamentals/app-state#tempdata).

Pokud chcete v rámci služby IIS nakonfigurovat ochranu dat a zachovat přitom klíčového prstence, použijte **některý** z následujících přístupů:

* **Vytvoření klíčů registru ochrany dat**

  Používá aplikace ASP.NET Core klíče ochrany dat jsou uložené v registru, které jsou externí vzhledem k aplikacím. Pokud chcete zachovat klíče pro danou aplikaci, vytvořte klíče registru pro fond aplikací.

  Pro samostatnou instalaci služby IIS, která není součástí webfarmu, se dá [skript PowerShellu pro ochranu dat provision-AutoGenKeys. ps1](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) použít pro každý fond aplikací, který se používá v aplikaci ASP.NET Core. Tento skript vytvoří klíč registru HKLM registru, který je přístupný pouze pro účet pracovního procesu fondu aplikací aplikaci. Klíče se zašifrují neaktivní uložená data pomocí rozhraní DPAPI klíčem celý počítač.

  Ve webových farem lze nastavit aplikaci pro použití cesty UNC pro ukládání jeho data protection klíč kanál. Ve výchozím nastavení klíče ochrany dat nejsou šifrovány. Zajistěte, aby byly omezené na účet Windows, které aplikace běží pod oprávnění pro sdílené síťové složce. X X509 certifikát můžete použít k ochraně klíčů v klidovém stavu. Vezměte v úvahu mechanismus pro uživatelům umožní nahrát certifikáty: místo certifikátů do důvěryhodného certifikátu uživatele ukládat a ujistěte se, jsou k dispozici na všech počítačích, ve kterém běží aplikace uživatele. Podrobnosti najdete v tématu [Konfigurace ochrany ASP.NET Core dat](xref:security/data-protection/configuration/overview) .

* **Konfigurace fondu aplikací služby IIS pro načtení profilu uživatele**

  Toto nastavení se nachází v části **model procesu** v části **Rozšířená nastavení** fondu aplikací. Nastavte **načíst profil uživatele** na `True`. Když nastavíte `True`, klíče se ukládají v adresáři profilu uživatele a jsou chráněné pomocí DPAPI s klíčem specifickým pro uživatelský účet. Klíče jsou uchovány ve složce *% localappdata%/ASP.NET/DataProtection-Keys* .

  Musí být povolený i [atribut setProfileEnvironment](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) fondu aplikací. Výchozí hodnota `setProfileEnvironment` je `true`. V některých scénářích (například operační systém Windows) je `setProfileEnvironment` nastaveno na `false`. Pokud se klíče neukládají v adresáři profilu uživatele podle očekávání:

  1. Přejděte do složky *% windir%/system32/Inetsrv/config* .
  1. Otevřete soubor *ApplicationHost. config* .
  1. Vyhledejte element `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>`.
  1. Potvrďte, že atribut `setProfileEnvironment` není přítomen, což nastaví hodnotu `true`, nebo explicitně nastavte hodnotu atributu na `true`.

* **Použití systému souborů jako úložiště pro Key Ring**

  Upravte kód aplikace tak, aby [používal systém souborů jako úložiště ve službě Key Ring](xref:security/data-protection/configuration/overview). Použijte X509 certifikátu k ochraně klíče kanál a zajistit certifikát není důvěryhodný certifikát. Pokud certifikát podepsaný svým držitelem, můžete certifikát umístěte do úložiště důvěryhodných kořenových certifikátů.

  Pokud používáte IIS ve webové farmě:

  * Použití sdílené složky, ke kterému přístup všechny počítače.
  * Nasazení x X509 certifikát pro každý počítač. Konfigurace [ochrany dat v kódu](xref:security/data-protection/configuration/overview).

* **Nastavení zásad pro ochranu dat na úrovni počítače**

  Systém ochrany dat má omezená podpora pro nastavení výchozích [zásad](xref:security/data-protection/configuration/machine-wide-policy) pro všechny aplikace, které využívají rozhraní API pro ochranu dat. Další informace naleznete v tématu <xref:security/data-protection/introduction>.

## <a name="virtual-directories"></a>Virtuální adresáře

Aplikace ASP.NET Core nepodporují [virtuální adresáře služby IIS](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) . Aplikace může být hostována jako [dílčí aplikace](#sub-applications).

## <a name="sub-applications"></a>Dílčí aplikace

Aplikace ASP.NET Core může být hostovaná jako [podaplikace služby IIS (dílčí aplikace)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications). Sub – aplikace cesta stane součástí adresy URL kořenového aplikace.

Propojení statických prostředků v rámci dílčí aplikace by měla používat notaci vlnovku (`~/`). Znak tildy vlnovkou spustí [pomocnou nápovědu](xref:mvc/views/tag-helpers/intro) k předřazení pathbase dílčí aplikace na vykreslený relativní odkaz. V případě dílčí aplikace na `/subapp_path`se obrázek propojený s `src="~/image.png"` vykresluje jako `src="/subapp_path/image.png"`. Middleware kořenové aplikace statické soubory nelze zpracovat požadavek statický soubor. Žádost zpracovává Middleware sub aplikace statické soubory.

Pokud je atribut `src` statického prostředku nastavený na absolutní cestu (například `src="/image.png"`), odkaz se vykreslí bez pathbase dílčí aplikace. Middleware statických souborů v kořenové aplikaci se pokusí o poskytování assetu z [kořenového adresáře webu](xref:fundamentals/index#web-root)kořenové aplikace, což má za následek *404 – nenalezené* odpovědi, pokud není k dispozici statický prostředek z kořenové aplikace.

K hostování aplikace v ASP.NET Core jako podřízeným aplikacím v rámci jiné aplikace ASP.NET Core:

1. Vytvořte fond aplikací pro aplikaci sub. Nastavte **verzi .NET CLR** na **žádný spravovaný kód** , protože základní modul CLR (Common Language Runtime) pro .NET Core se spouští k hostování aplikace v pracovním procesu, ne CLR Desktop (.NET CLR).

1. Přidání kořenového webu s podřízeným aplikacím v rámci kořenového webu ve Správci služby IIS.

1. Klikněte pravým tlačítkem na složku dílčí aplikace ve Správci služby IIS a vyberte **převést na aplikaci**.

1. V dialogovém okně **Přidat aplikaci** použijte pro **fond aplikací** tlačítko **Vybrat** , abyste přiřadili fond aplikací, který jste vytvořili pro dílčí aplikaci. Vyberte **OK**.

Přiřazení fondu samostatné aplikace k podřízeným aplikacím je požadavek, pokud používáte model hostingu v procesu.

Další informace o modelu hostování v rámci procesu a konfiguraci modulu ASP.NET Core naleznete v tématu <xref:host-and-deploy/aspnet-core-module>.

## <a name="configuration-of-iis-with-webconfig"></a>Konfigurace služby IIS pomocí souboru web.config

Konfigurace služby IIS má vliv na `<system.webServer>` části *Web. config* pro scénáře služby IIS, které jsou funkční pro ASP.NET Core aplikace s modulem ASP.NET Core. Konfigurace služby IIS je třeba funkční dynamické komprese. Pokud je služba IIS nakonfigurovaná na úrovni serveru, aby používala dynamickou kompresi, `<urlCompression>` element v souboru *Web. config* aplikace ho může zakázat pro ASP.NET Core aplikace.

Další informace najdete v následujících tématech:

* [Odkaz na konfiguraci pro \<System. webServer >](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

Chcete-li nastavit proměnné prostředí pro jednotlivé aplikace spuštěné v izolovaných fondech aplikací (podporované pro IIS 10,0 nebo novější), přečtěte si část *příkazového řádku Appcmd. exe* v tématu [proměnné prostředí \<environmentVariables >](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) v referenční dokumentaci služby IIS.

## <a name="configuration-sections-of-webconfig"></a>Konfigurační oddíly souboru Web.config

Konfigurační oddíly aplikací ASP.NET 4. x v *souboru Web. config* nepoužívá aplikace ASP.NET Core pro konfiguraci:

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

Aplikace ASP.NET Core jsou nakonfigurované pomocí jiných poskytovatelů konfigurace. Další informace najdete v tématu [Konfigurace](xref:fundamentals/configuration/index).

## <a name="application-pools"></a>Fondy aplikací

Izolace fond aplikací je určeno model hostingu:

* Hostování v procesu &ndash; aplikace se vyžadují ke spuštění v samostatných fondech aplikací.
* Mimo procesy hostování &ndash; doporučujeme vzájemně izolovat aplikace tím, že spustíte každou aplikaci ve svém vlastním fondu aplikací.

Dialogové okno **Přidat web** do služby IIS je ve výchozím nastavení nastaveno na jeden fond aplikací na aplikaci. Když je zadaný **název lokality** , text se automaticky přenese do textového pole **fondu aplikací** . Nový fond aplikací je vytvořený pomocí názvu serveru po přidání serveru.

## <a name="application-pool-identity"></a>Identita fondu aplikací

Účet identita fondu aplikací umožňuje aplikaci běžet pod účtem jedinečný bez nutnosti vytvářet a spravovat domény nebo místní účty. IIS 8.0 nebo novější procesů pro pracovníka Správce služby IIS (WAS) vytvoří virtuální účet s názvem nového fondu aplikací a aplikace spouští fondu pracovních procesů pod tímto účtem ve výchozím nastavení. V konzole pro správu služby IIS v části **Upřesnit nastavení** fondu aplikací zajistěte, aby byla **Identita** nastavená na použití **ApplicationPoolIdentity**:

![Dialogové okno pokročilé nastavení fondu aplikací](index/_static/apppool-identity.png)

Proces správy služby IIS vytvoří zabezpečeného identifikátoru se název fondu aplikací v zabezpečení systému Windows. Pomocí této identity, je možné svázat prostředky. Ale tato identita není skutečný účet a nezobrazí se v konzole pro správu uživatelů Windows.

Pokud pracovní proces služby IIS vyžaduje přístup k aplikaci přes se zvýšenými oprávněními, upravte seznam řízení přístupu (ACL) pro adresář obsahující aplikaci:

1. Otevřete Průzkumníka Windows a přejděte do adresáře.

1. Klikněte pravým tlačítkem na adresář a vyberte **vlastnosti**.

1. Na kartě **zabezpečení** vyberte tlačítko **Upravit** a pak klikněte na tlačítko **Přidat** .

1. Vyberte tlačítko **umístění** a ujistěte se, že je vybraný systém.

1. Do pole **Zadejte názvy objektů pro výběr** oblasti zadejte **\\< služby IIS AppPool > APP_POOL_NAME** . Vyberte tlačítko pro **kontrolu názvů** . Pro službu *DefaultAppPool* ověřte názvy pomocí **služby IIS AppPool\DefaultAppPool**. Když je vybráno tlačítko pro **kontrolu názvů** , hodnota **DefaultAppPool** je uvedena v oblasti názvy objektů. Není možné zadat název fondu aplikací přímo do oblasti názvy objektu. Při kontrole názvu objektu použijte **\\< app_pool_name > formátu IIS AppPool** .

   ![Dialogová okna pro výběr uživatelů nebo skupin pro složku aplikace: název fondu aplikací "DefaultAppPool" se připojí k "rozhraní IIS AppPool\" v oblasti názvy objektů před výběrem" kontrolovat jména ".](index/_static/select-users-or-groups-1.png)

1. Vyberte **OK**.

   ![Vyberte uživatele nebo skupiny dialogové okno pro složky aplikace: Po výběru "Kontrola názvů", "DefaultAppPool" se zobrazí v objektu název objektu názvy oblasti.](index/_static/select-users-or-groups-2.png)

1. Ve výchozím nastavení by měla být udělena oprávnění ke čtení &amp;mu spouštění. Zadejte další oprávnění podle potřeby.

Přístup se dá taky udělit na příkazovém řádku pomocí nástroje **Icacls** . Pomocí příkazu *DefaultAppPool* jako příkladu se používá následující příkaz:

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

Další informace najdete v tématu [Icacls](/windows-server/administration/windows-commands/icacls) .

## <a name="http2-support"></a>Podpora HTTP/2

[Http/2](https://httpwg.org/specs/rfc7540.html) se podporuje s ASP.NET Core v následujících scénářích nasazení služby IIS:

* V procesu
  * Windows Server 2016 nebo Windows 10 nebo novější; IIS 10 nebo novější.
  * Protokol TLS 1.2 nebo vyšší připojení
* Mimo proces
  * Windows Server 2016 nebo Windows 10 nebo novější; IIS 10 nebo novější.
  * Veřejná připojení hraničních serverů používají protokol HTTP/2, ale připojení reverzního proxy [serveru Kestrel](xref:fundamentals/servers/kestrel) používá protokol HTTP/1.1.
  * Protokol TLS 1.2 nebo vyšší připojení

Pro nasazení v rámci procesu, když je navázáno připojení HTTP/2, `HTTP/2`sestavy [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) . Pro nasazení mimo proces, když je navázáno připojení HTTP/2, `HTTP/1.1`sestavy [protokolu HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) .

Další informace o modelech hostování v procesu a mimoprocesové procesy naleznete v tématu <xref:host-and-deploy/aspnet-core-module>.

HTTP/2 je standardně povolená. Připojení vrátit zpět k protokolu HTTP/1.1, pokud nedojde k připojení k protokolu HTTP/2. Další informace o konfiguraci HTTP/2 v nasazeních služby IIS najdete v tématu [http/2 ve službě IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).

## <a name="cors-preflight-requests"></a>Požadavky na kontrolu před výstupem CORS

*Tato část platí jenom pro ASP.NET Core aplikace, které cílí na .NET Framework.*

Pro ASP.NET Core aplikaci, která cílí na .NET Framework, požadavky na možnosti nejsou ve výchozím nastavení ve službě IIS předány do aplikace. Informace o tom, jak nakonfigurovat obslužné rutiny IIS aplikace v *souboru Web. config* , aby předávala požadavky na možnosti, najdete v tématu [Povolení žádostí o více zdrojů v ASP.NET webovém rozhraní API 2: jak CORS funguje](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).

## <a name="application-initialization-module-and-idle-timeout"></a>Modul inicializace aplikace a časový limit nečinnosti

Při hostování ve službě IIS pomocí modulu ASP.NET Core verze 2:

* [Modul inicializace aplikace](#application-initialization-module) &ndash; hostovaný [v procesu](#in-process-hosting-model) nebo [mimo proces](#out-of-process-hosting-model) aplikace se dá nakonfigurovat tak, aby se automaticky spouštěl v případě restartování nebo restartování serveru.
* [Časový limit nečinnosti](#idle-timeout) &ndash; hostovaný [v procesu](#in-process-hosting-model) aplikace se dá nakonfigurovat tak, aby nevypršel časový limit v období nečinnosti.

### <a name="application-initialization-module"></a>Modul inicializace aplikace

*Platí pro aplikace hostované v procesu a mimo proces.*

[Inicializace aplikace IIS](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) je funkce služby IIS, která do aplikace pošle požadavek HTTP, když se spustí nebo recykluje fond aplikací. Požadavek spustí spuštění aplikace. Ve výchozím nastavení služba IIS vydá požadavek na kořenovou adresu URL aplikace (`/`) k inicializaci aplikace (Další informace o konfiguraci najdete v [dalších zdrojích](#application-initialization-module-and-idle-timeout-additional-resources) ).

Potvrďte, že je povolená funkce role inicializace aplikace služby IIS:

V systémech Windows 7 nebo novějších stolních počítačích při používání služby IIS v místním prostředí:

1. Přejděte na **Ovládací panely** > **programy** > **programy a funkce** > **zapnout nebo vypnout funkce systému Windows** (levá strana obrazovky).
1. Otevřete **Internetová informační služba** > **webové služby** > funkce pro **vývoj aplikací**.
1. Zaškrtněte políčko pro **inicializaci aplikace**.

V systému Windows Server 2008 R2 nebo novějším:

1. Otevřete **Průvodce přidáním rolí a funkcí**.
1. Na panelu **Vybrat služby rolí** otevřete uzel **vývoj aplikací** .
1. Zaškrtněte políčko pro **inicializaci aplikace**.

Pomocí některého z následujících přístupů povolte modul inicializace aplikace pro danou lokalitu:

* Pomocí Správce služby IIS:

  1. Na panelu **připojení** vyberte **fondy aplikací** .
  1. V seznamu klikněte pravým tlačítkem na fond aplikací aplikace a vyberte **Upřesnit nastavení**.
  1. Výchozí **režim spuštění** je **OnDemand**. Nastavte **režim Start** na **AlwaysRunning**. Vyberte **OK**.
  1. Otevřete uzel **lokality** na panelu **připojení** .
  1. Klikněte pravým tlačítkem na aplikaci a vyberte **Spravovat web** > **Upřesnit nastavení**.
  1. Výchozí nastavení pro **Povolení přednačtení** je **false**. Nastavte **předběžné načtení povoleno** na **hodnotu true**. Vyberte **OK**.

* Pomocí souboru *Web. config*přidejte prvek `<applicationInitialization>` s `doAppInitAfterRestart` nastavenou na `true` do `<system.webServer>` prvků v souboru *Web. config* aplikace:

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <applicationInitialization doAppInitAfterRestart="true" />
      </system.webServer>
    </location>
  </configuration>
  ```

### <a name="idle-timeout"></a>Časový limit nečinnosti

*Platí jenom pro aplikace hostované v procesu.*

Pokud chcete zabránit volnoběhu aplikace, nastavte časový limit nečinnosti fondu aplikací pomocí Správce služby IIS:

1. Na panelu **připojení** vyberte **fondy aplikací** .
1. V seznamu klikněte pravým tlačítkem na fond aplikací aplikace a vyberte **Upřesnit nastavení**.
1. Výchozí **časový limit nečinnosti (minuty)** je **20** minut. Nastavte **časový limit nečinnosti (minuty)** na **0** (nula). Vyberte **OK**.
1. Recyklujte pracovní proces.

Chcete-li zabránit aplikacím hostovaným v [procesu](#out-of-process-hosting-model) vypršení časového limitu, použijte některý z následujících přístupů:

* Pomocí příkazu otestujete aplikaci z externí služby, aby byla spuštěná.
* Pokud aplikace hostuje jenom služby na pozadí, vyhněte se hostování služby IIS a použití [služby Windows k hostování aplikace ASP.NET Core](xref:host-and-deploy/windows-service).

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a>Modul inicializace aplikace a další prostředky časového limitu nečinnosti

* [Inicializace aplikace IIS 8,0](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* [Inicializace aplikace \<applicationInitialization >](/iis/configuration/system.webserver/applicationinitialization/).
* [Nastavení modelu procesu pro fond aplikací \<processModel >](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).

## <a name="deployment-resources-for-iis-administrators"></a>Materiály pro nasazení pro správce služby IIS

* [Dokumentace ke službě IIS](/iis)
* [Začínáme ve službě IIS pomocí Správce služby IIS](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [Nasazení aplikace .NET Core](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a>Další zdroje

* <xref:test/troubleshoot>
* <xref:index>
* [Oficiální web Microsoft IIS](https://www.iis.net/)
* [Knihovna technických obsahu pro Windows Server](/windows-server/windows-server)
* [HTTP/2 ve službě IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Kurz týkající se publikování ASP.NET Core aplikace na server služby IIS najdete v tématu <xref:tutorials/publish-to-iis>.

[Instalace hostující sady .NET Core](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a>Podporované operační systémy

Podporovány jsou následující operační systémy:

* Windows 7 nebo novější
* Windows Server 2008 R2 nebo novější

[Http. sys Server](xref:fundamentals/servers/httpsys) (dříve označovaný jako weblisten) nefunguje v konfiguraci reverzního proxy serveru se službou IIS. Použijte [Server Kestrel](xref:fundamentals/servers/kestrel).

Informace o hostování v Azure najdete v tématu <xref:host-and-deploy/azure-apps/index>.

Pokyny k řešení potíží najdete v tématu <xref:test/troubleshoot>.

## <a name="supported-platforms"></a>Podporované platformy

Podporují se aplikace publikované pro nasazení 32 (x86) nebo 64-bit (x64). Nasazení 32 aplikace s 32 (x86) .NET Core SDK, pokud aplikace:

* Vyžaduje větší dostupný adresní prostor virtuální paměti pro 64 aplikaci.
* Vyžaduje větší velikost zásobníku služby IIS.
* Má 64 nativní závislosti.

K publikování 64 aplikace použijte 64 .NET Core SDK (x64). V hostitelském systému musí být nainstalován 64 modul runtime.

ASP.NET Core se dodává se [serverem Kestrel](xref:fundamentals/servers/kestrel), výchozím serverem HTTP pro různé platformy.

Při použití [služby IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) nebo [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)aplikace běží v procesu odděleně od pracovního procesu služby IIS (*mimo proces*) se [serverem Kestrel](xref:fundamentals/servers/index#kestrel).

Vzhledem k tomu, že ASP.NET Core aplikace běží v procesu odděleném od pracovního procesu služby IIS, modul zpracovává správu procesů. Modul spustí proces pro aplikaci ASP.NET Core, když první požadavek dorazí a restartuje aplikaci, pokud se ukončí nebo dojde k chybě. To je v podstatě stejné chování jako u aplikací, které běží v procesu, které jsou spravovány službou was [(Windows Process Activation Service)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

Následující diagram znázorňuje vztah mezi službou IIS, modulem ASP.NET Core a aplikací hostovanou mimo proces:

![Modul ASP.NET Core](index/_static/ancm-outofprocess.png)

Požadavky přicházející z webu do ovladače HTTP. sys v režimu jádra. Ovladač směruje požadavky do služby IIS na konfigurovaném portu webu, obvykle 80 (HTTP) nebo 443 (HTTPS). Modul předá požadavky do Kestrel na náhodném portu pro aplikaci, což není port 80 nebo 443.

Modul Určuje port prostřednictvím proměnné prostředí při spuštění a [Služba IIS Integration middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) nakonfiguruje server tak, aby naslouchal `http://localhost:{port}`. Budou provedeny další kontroly a požadavky, které nepocházejí z modulu, jsou odmítnuty. Modul nepodporuje předávání HTTPS, takže požadavky se předávají přes protokol HTTP i v případě, že je služba IIS prostřednictvím protokolu HTTPS přijímá.

Po Kestrel žádosti z modulu se požadavek odešle do kanálu middlewaru ASP.NET Core. Kanál middlewaru zpracovává požadavek a předá ho jako instanci `HttpContext` do logiky aplikace. Middleware přidaný integrací služby IIS: aktualizace schématu, vzdálené IP adresy a pathbase pro přesměrování požadavku do Kestrel. Odpověď aplikace se předává zpátky do služby IIS, která ji přenáší zpátky do klienta HTTP, který žádost inicioval.

`CreateDefaultBuilder` nakonfiguruje server [Kestrel](xref:fundamentals/servers/kestrel) jako webový server a povolí integraci služby IIS konfigurací základní cesty a portu pro [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module).

Modul ASP.NET Core generuje dynamický port přiřadit k procesu back-endu. `CreateDefaultBuilder` volá metodu <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>. `UseIISIntegration` nakonfiguruje Kestrel k naslouchání na dynamickém portu na IP adrese localhost (`127.0.0.1`). Pokud je dynamický port 1234, Kestrel naslouchá na `127.0.0.1:1234`. Tato konfigurace nahrazuje jiné konfigurace adresy URL poskytnuté:

* `UseUrls`
* [Rozhraní API pro naslouchání Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [Konfigurace](xref:fundamentals/configuration/index) (nebo [příkazového řádku – možnost adresy URL](xref:fundamentals/host/web-host#override-configuration))

Při použití modulu se nevyžadují volání `UseUrls` nebo rozhraní API pro `Listen` Kestrel. Pokud je zavolána `UseUrls` nebo `Listen`, Kestrel naslouchá na portu určeném pouze při spuštění aplikace bez služby IIS.

Pokyny ke konfiguraci ASP.NET Core modulu najdete v tématu <xref:host-and-deploy/aspnet-core-module>.

Další informace o hostování najdete v tématu [hostitel v ASP.NET Core](xref:fundamentals/index#host).

## <a name="application-configuration"></a>Konfigurace aplikací

### <a name="enable-the-iisintegration-components"></a>Povolit IISIntegration součásti

Při sestavování hostitele v `CreateWebHostBuilder` (*program.cs*) volejte <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> a povolte integraci služby IIS:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

Další informace o `CreateDefaultBuilder`najdete v tématu <xref:fundamentals/host/web-host#set-up-a-host>.

### <a name="iis-options"></a>Možnosti služby IIS

| Možnost                         | Výchozí | Nastavení |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | Pokud `true`, server IIS nastaví `HttpContext.User` ověřený [ověřováním systému Windows](xref:security/authentication/windowsauth). Pokud `false`, Server poskytuje pouze identitu pro `HttpContext.User` a reaguje na výzvy, které jsou výslovně požadovány `AuthenticationScheme`. Aby mohla funkce `AutomaticAuthentication` fungovat, musí být ve službě IIS povolená možnost ověřování systému Windows. Další informace najdete v tématu [ověřování systému Windows](xref:security/authentication/windowsauth). |
| `AuthenticationDisplayName`    | `null`  | Nastaví zobrazovaný název, který se uživatelům na přihlašovací stránky zobrazí. |

Pokud chcete nakonfigurovat možnosti služby IIS, zahrňte do <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>konfiguraci služby pro <xref:Microsoft.AspNetCore.Builder.IISOptions>. Následující příklad zabrání aplikaci v naplnění `HttpContext.Connection.ClientCertificate`:

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| Možnost                         | Výchozí | Nastavení |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | Pokud `true`, [middleware integrace služby IIS](#enable-the-iisintegration-components) nastaví `HttpContext.User` ověřováno [ověřováním systému Windows](xref:security/authentication/windowsauth). Pokud `false`, middleware poskytuje pouze identitu pro `HttpContext.User` a reaguje na výzvy, které jsou explicitně požadovány `AuthenticationScheme`. Aby mohla funkce `AutomaticAuthentication` fungovat, musí být ve službě IIS povolená možnost ověřování systému Windows. Další informace najdete v tématu [ověřování systému Windows](xref:security/authentication/windowsauth) . |
| `AuthenticationDisplayName`    | `null`  | Nastaví zobrazovaný název, který se uživatelům na přihlašovací stránky zobrazí. |
| `ForwardClientCertificate`     | `true`  | Pokud se `true` a nachází se hlavička žádosti `MS-ASPNETCORE-CLIENTCERT`, `HttpContext.Connection.ClientCertificate` se naplní. |

### <a name="proxy-server-and-load-balancer-scenarios"></a>Proxy server a scénáře pro nástroj pro vyrovnávání zatížení

[Služba IIS Integration middleware](#enable-the-iisintegration-components), která konfiguruje middleware předávaných hlaviček, a modul ASP.NET Core je nakonfigurován tak, aby přenesl schéma (http/https) a vzdálenou IP adresu, kam pochází požadavek. Další konfigurace může být nezbytný pro aplikací hostovaných za službou další proxy servery a nástroje pro vyrovnávání zatížení. Další informace najdete v tématu [konfigurace ASP.NET Core pro práci se servery proxy a nástroji pro vyrovnávání zatížení](xref:host-and-deploy/proxy-load-balancer).

### <a name="webconfig-file"></a>soubor Web.config

Soubor *Web. config* konfiguruje [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module). Vytváření, transformace a publikování souboru *Web. config* je zpracováno cílem MSBuild (`_TransformWebConfig`) při publikování projektu. Tento cíl je k dispozici v rámci cílů web SDK (`Microsoft.NET.Sdk.Web`). V horní části souboru projektu je sada SDK:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Pokud soubor *Web. config* není v projektu přítomen, je vytvořen soubor se správným *processPath* a *argumenty* pro konfiguraci modulu ASP.NET Core a přesunutí do [publikovaného výstupu](xref:host-and-deploy/directory-structure).

Pokud je soubor *Web. config* přítomen v projektu, soubor je transformován se správným *processPath* a *argumenty* pro konfiguraci modulu ASP.NET Core a přesunutí do publikovaného výstupu. Transformace nezmění nastavení konfigurace služby IIS v souboru.

Soubor *Web. config* může poskytovat další nastavení konfigurace služby IIS, která ovládají aktivní moduly IIS. Informace o modulech služby IIS, které jsou schopné zpracovávat požadavky s ASP.NET Core aplikacemi, najdete v tématu [IIS modules](xref:host-and-deploy/iis/modules) .

Chcete-li webové sadě SDK zabránit ve transformaci souboru *Web. config* , použijte vlastnost **\<IsTransformWebConfigDisabled >** v souboru projektu:

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

Při zakazování webové sady SDK z transformace souboru by měl vývojář *processPath* a *argumenty* ručně nastavit. Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module>.

### <a name="webconfig-file-location"></a>Umístění souboru Web.config

Aby bylo možné správně nastavit [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) , musí být soubor *Web. config* přítomen v [kořenové cestě obsahu](xref:fundamentals/index#content-root) (obvykle v základní cestě aplikace) nasazené aplikace. Jedná se o stejné umístění jako fyzická cesta webu služby IIS k dispozici. Soubor *Web. config* je vyžadován v kořenu aplikace, aby bylo možné publikovat více aplikací pomocí nasazení webu.

Citlivé soubory existují na fyzické cestě aplikace, jako je například *\<sestavení >. runtimeconfig. JSON*, *\<Assembly >. XML* (dokumentační dokumentace xml), a *\<sestavení >. DEPS. JSON*. Když je přítomen soubor *Web. config* a lokalita se spouští normálně, služba IIS tyto citlivé soubory po vyžádání neobsluhuje. Pokud soubor *Web. config* chybí, je nesprávně pojmenovaný nebo nemůže nakonfigurovat lokalitu pro normální spuštění, služba IIS může obsluhovat citlivé soubory veřejně.

**Soubor *Web. config* musí být současně přítomen v nasazení, správně pojmenován a může nakonfigurovat lokalitu pro normální spuštění. Nikdy neodstraňujte soubor *Web. config* z produkčního nasazení.**

### <a name="transform-webconfig"></a>Transformace souboru web.config

Pokud potřebujete transformovat *Web. config* při publikování (například nastavit proměnné prostředí na základě konfigurace, profilu nebo prostředí), přečtěte si téma <xref:host-and-deploy/iis/transform-webconfig>.

## <a name="iis-configuration"></a>Konfigurace služby IIS

**Operační systémy Windows Server**

Povolte roli serveru **webový server (IIS)** a vytvořte služby rolí.

1. Použijte průvodce **přidáním rolí a funkcí** z nabídky **Správa** nebo odkazu v **Správce serveru**. V kroku **role serveru** zaškrtněte políčko **webový server (IIS)** .

   ![V kroku výběr serveru role je vybrána role webového serveru IIS.](index/_static/server-roles-ws2016.png)

1. Po kroku **funkce** se krok **služby rolí** načte pro webový server (IIS). Vyberte požadovaných služeb role služby IIS nebo přijměte výchozí nastavení role služby za předpokladu.

   ![Výchozí služby rolí jsou vybrané v kroku vybrat roli služby.](index/_static/role-services-ws2016.png)

   **Ověřování systému Windows (volitelné)**  
   Chcete-li povolit ověřování systému Windows, rozbalte následující uzly: **webový Server** > **zabezpečení**. Vyberte funkci **ověřování systému Windows** . Další informace najdete v tématu [ověřování systému windows \<windowsAuthentication >](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) a [Konfigurace ověřování systému Windows](xref:security/authentication/windowsauth).

   **WebSockets (volitelné)**  
   Protokoly Websocket je podporována s ASP.NET Core 1.1 nebo vyšší. Pokud chcete povolit objekty WebSockets, rozbalte následující uzly: **webový Server** > **vývoj aplikací**. Vyberte funkci **protokolu WebSocket** . Další informace najdete v tématu [objekty WebSockets](xref:fundamentals/websockets).

1. Chcete-li nainstalovat roli a služby webového serveru, postupujte podle kroků pro **potvrzení** . Po instalaci role **webový server (IIS)** není nutné restartovat server nebo službu IIS.

**Desktopové operační systémy Windows**

Povolte **konzolu pro správu služby IIS** a **webové služby**.

1. Přejděte na **Ovládací panely** > **programy** > **programy a funkce** > **zapnout nebo vypnout funkce systému Windows** (levá strana obrazovky).

1. Otevřete uzel **Internetová informační služba** . Otevřete uzel **Nástroje webové správy** .

1. Zaškrtněte políčko pro **konzolu pro správu služby IIS**.

1. Zaškrtněte políčko u **webových služeb**.

1. Přijměte výchozí funkce pro **webové služby** nebo upravte funkce služby IIS.

   **Ověřování systému Windows (volitelné)**  
   Chcete-li povolit ověřování systému Windows, rozbalte následující uzly: **webové služby** > **zabezpečení**webu. Vyberte funkci **ověřování systému Windows** . Další informace najdete v tématu [ověřování systému windows \<windowsAuthentication >](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) a [Konfigurace ověřování systému Windows](xref:security/authentication/windowsauth).

   **WebSockets (volitelné)**  
   Protokoly Websocket je podporována s ASP.NET Core 1.1 nebo vyšší. Chcete-li povolit objekty WebSockets, rozbalte následující uzly: **webové služby** > **funkce pro vývoj aplikací**. Vyberte funkci **protokolu WebSocket** . Další informace najdete v tématu [objekty WebSockets](xref:fundamentals/websockets).

1. Pokud instalace služby IIS vyžaduje restartování, restartujte systém.

![Konzola pro správu služby IIS a webové služby jsou vybrány v funkce Windows.](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a>Instalace .NET Core, který je hostitelem svazku

Nainstalujte *hostující sadu .NET Core* do hostitelského systému. Svazek nainstaluje modul runtime .NET Core, knihovnu .NET Core a [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module). Povolí modul ASP.NET Core aplikací ke spuštění za služby IIS.

> [!IMPORTANT]
> Pokud před službou IIS instalovanou sadou hostování, je nutné opravit instalaci sady. Spusťte instalační program sady hostování znovu po instalaci služby IIS.
>
> Pokud se hostující sada nainstaluje po instalaci 64 (x64) verze .NET Core, můžou se zdát, že sady SDK chybí ([nezjistily se žádné sady .NET Core SDK](xref:test/troubleshoot#no-net-core-sdks-were-detected)). Chcete-li tento problém vyřešit, přečtěte si téma <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.

### <a name="direct-download-current-version"></a>Přímé stažení (aktuální verze)

Stažení instalačního programu pomocí následujícího odkazu:

[Aktuální instalační program sady hostujících sad .NET Core (přímé stahování)](https://www.microsoft.com/net/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a>Starší verze instalačního programu

Chcete-li získat starší verzi instalačního programu:

1. Přejděte do [archivu stahování v rozhraní .NET](https://www.microsoft.com/net/download/archives).
1. V části **.NET Core**vyberte verzi .NET Core.
1. Ve sloupci **Spustit aplikace – modul runtime** vyhledejte řádek požadované verze modulu runtime .NET Core.
1. Stáhněte instalační program pomocí odkazu **& hostování sady prostředků modulu runtime** .

> [!WARNING]
> Některé instalační programy obsahují verze, které bylo dosaženo jejich konci životnosti (konce řádku) a již nejsou podporovány společností Microsoft. Další informace najdete v tématu [zásady podpory](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

### <a name="install-the-hosting-bundle"></a>Instalaci sady hostování

1. Spusťte instalační program na serveru. Při spuštění instalačního programu z příkazového prostředí správce jsou k dispozici následující parametry:

   * `OPT_NO_ANCM=1` &ndash; Přeskočit instalaci modulu ASP.NET Core.
   * `OPT_NO_RUNTIME=1` &ndash; přeskočení instalace modulu runtime .NET Core. Používá se, když Server hostuje jenom [samostatná nasazení (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).
   * `OPT_NO_SHAREDFX=1` &ndash; Přeskočit instalaci sdílené architektury ASP.NET (modul runtime ASP.NET). Používá se, když Server hostuje jenom [samostatná nasazení (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).
   * `OPT_NO_X86=1` &ndash; Přeskočit instalaci běhových prostředí x86. Tento parametr použijte, pokud víte, že nebudete hostovat 32 aplikace. Pokud existuje možnost, že v budoucnosti budete hostovat jak 32, tak i 64 aplikace, tento parametr nepoužívejte a nainstalujete oba moduly runtime.
   * `OPT_NO_SHARED_CONFIG_CHECK=1` &ndash; zakázat kontrolu použití sdílené konfigurace služby IIS, pokud je sdílená konfigurace (*ApplicationHost. config*) ve stejném počítači jako instalace služby IIS. *K dispozici jenom pro ASP.NET Core 2,2 nebo novější instalační programy hostujících prostředků.* Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.
1. Restartujte systém nebo v příkazovém prostředí spusťte následující příkazy:

   ```console
   net stop was /y
   net start w3svc
   ```
   Restartování služby IIS příjmem změnu systému provedené CESTU, která je proměnná prostředí, instalační služby.

Při instalaci hostujícího balíčku není nutné ručně zastavit jednotlivé weby ve službě IIS. Hostované aplikace (weby IIS) se restartují po restartování služby IIS. Aplikace se po přijetí první žádosti spustí znovu, včetně [modulu inicializace aplikace](#application-initialization-module-and-idle-timeout).

ASP.NET Core přijímá postup při přeposílání pro vydání oprav pro balíčky sdílených balíčků rozhraní. Když se aplikace hostované službou IIS restartují se službou IIS, aplikace při přijetí jejich prvního požadavku načtou nejnovější verze oprav jejich odkazovaných balíčků. Pokud se služba IIS nerestartuje, aplikace se restartují a projeví se chování při přeposílání, když se jejich pracovní procesy recyklují a obdrží první požadavek.

> [!NOTE]
> Informace o sdílené konfiguraci služby IIS najdete v tématu [modul ASP.NET Core se sdílenou konfigurací služby IIS](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a>Nainstalujte nástroj nasazení webu při publikování pomocí sady Visual Studio

Při nasazování aplikací na servery s [nasazení webu](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)nainstalujte na server nejnovější verzi nasazení webu. Chcete-li nainstalovat Nasazení webu, použijte [instalační program webové platformy (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) nebo si instalační program Získejte přímo z webu [služby Stažení softwaru](https://www.microsoft.com/download/details.aspx?id=43717). Upřednostňovanou metodou je použití instalace webové platformy. Instalace webové platformy nabízí samostatné instalace a konfigurace pro poskytovatele hostingu.

## <a name="create-the-iis-site"></a>Vytvořte web služby IIS

1. V hostitelském systému vytvořte složku obsahující soubory a složky publikované aplikace. V následujícím kroku je jako fyzická cesta k aplikaci služba IIS poskytována jako cesta k této složce. Další informace o složce nasazení aplikace a rozložení souborů naleznete v tématu <xref:host-and-deploy/directory-structure>.

1. Ve Správci služby IIS otevřete uzel serveru na panelu **připojení** . Klikněte pravým tlačítkem na složku **weby** . V místní nabídce vyberte **Přidat web** .

1. Zadejte **název lokality** a nastavte **fyzickou cestu** ke složce pro nasazení aplikace. Zadejte konfiguraci **vazby** a vytvořte web výběrem **OK**:

   ![Zadejte název lokality, fyzickou cestu a název hostitele v kroku přidat web.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > **Nesmí** být použita vazba zástupných znaků nejvyšší úrovně (`http://*:80/` a `http://+:80`). Vazby nejvyšší úrovně zástupný znak můžete otevřít aplikaci tak, aby slabá místa zabezpečení. To platí pro silné a slabé zástupné znaky. Používejte explicitní hostitele názvy místo zástupných znaků. Vazba zástupných znaků subdomény (například `*.mysub.com`) nemá toto bezpečnostní riziko, pokud ovládáte celou nadřazenou doménu (na rozdíl od `*.com`, která je zranitelná). Další informace najdete v [části rfc7230 část-5,4](https://tools.ietf.org/html/rfc7230#section-5.4) .

1. V uzlu serveru vyberte **fondy aplikací**.

1. Klikněte pravým tlačítkem myši na fond aplikací webu a v místní nabídce vyberte **základní nastavení** .

1. V okně **Upravit fond aplikací** nastavte **verzi .NET CLR** na **bez spravovaného kódu**:

   ![Nastavit bez spravovaného kódu pro verze .NET CLR.](index/_static/edit-apppool-ws2016.png)

    ASP.NET Core běží v samostatném procesu a spravuje modulu runtime. ASP.NET Core nespoléhá na načítání CLR desktopu (.NET CLR)&mdash;se modul CLR (Common Language Runtime) pro .NET Core spouští k hostování aplikace v pracovním procesu. Nastavení **verze .NET CLR** na **žádný spravovaný kód** není volitelné, ale doporučuje se.

1. *ASP.NET Core 2,2 nebo novější*: 64 u samostatného [nasazeného nasazení](/dotnet/core/deploying/#self-contained-deployments-scd) (x64), které používá [model hostování v rámci procesu](#in-process-hosting-model), zakažte fond aplikací pro procesy 32 (x86).

   V bočním panelu **Akce** Správce služby IIS > **fondy aplikací**vyberte možnost **nastavit výchozí hodnoty fondu aplikací** nebo **Upřesnit nastavení**. Vyhledejte **možnost povolit 32-bitové aplikace** a nastavte hodnotu na `False`. Toto nastavení nemá vliv na aplikace nasazené pro [hostování mimo proces](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).

1. Potvrďte, že identita model procesu má příslušná oprávnění.

   Pokud se výchozí identita fondu aplikací (**model procesu** > **identity**) změní z **ApplicationPoolIdentity** na jinou identitu, ověřte, že Nová identita má požadovaná oprávnění pro přístup ke složce, databázi a dalším požadovaným prostředkům aplikace. Například fond aplikací vyžaduje čtení a zápisu do složky, kde aplikace čte a zapisuje soubory.

**Konfigurace ověřování systému Windows (volitelné)**  
Další informace najdete v tématu [Konfigurace ověřování systému Windows](xref:security/authentication/windowsauth).

## <a name="deploy-the-app"></a>Nasazení aplikace

Nasaďte aplikaci do složky **fyzické cesty** služby IIS, která byla navázána v části [Vytvoření webu služby IIS](#create-the-iis-site) . [Nasazení webu](/iis/publish/using-web-deploy/introduction-to-web-deploy) je doporučeným mechanismem pro nasazení, ale existuje několik možností pro přesun aplikace ze složky *publikování* projektu do složky pro nasazení hostitelského systému.

### <a name="web-deploy-with-visual-studio"></a>Nasazení webu pomocí sady Visual Studio

Informace o tom, jak vytvořit profil publikování pro použití s Nasazení webu, najdete v tématu [publikační profily sady Visual Studio pro ASP.NET Core nasazení aplikací](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) . Pokud poskytovatel hostingu poskytuje profil publikování nebo podporu pro jeho vytvoření, Stáhněte si jeho profil a importujte ho pomocí dialogového okna pro **publikování** sady Visual Studio:

![Publikovat stránku dialogového okna](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a>Nasazení webu mimo sadu Visual Studio

[Nasazení webu](/iis/publish/using-web-deploy/introduction-to-web-deploy) lze použít také mimo sadu Visual Studio z příkazového řádku. Další informace najdete v tématu [Nástroj pro nasazení webu](/iis/publish/using-web-deploy/use-the-web-deployment-tool).

### <a name="alternatives-to-web-deploy"></a>Alternativy k webové nasazení

K přesunu aplikace do hostitelského systému, jako je ruční kopírování, [xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy)nebo [PowerShell](/powershell/), použijte libovolný z několika způsobů.

Další informace o nasazení ASP.NET Core do služby IIS najdete v části [prostředky nasazení pro správce služby IIS](#deployment-resources-for-iis-administrators) .

## <a name="browse-the-website"></a>Přejděte na web

Po nasazení aplikace do hostitelského systému vytvořte žádost jednomu z veřejných koncových bodů aplikace.

V následujícím příkladu je lokalita svázána s **názvem hostitele** služby IIS `www.mysite.com` na **portu** `80`. Je učiněna žádost o `http://www.mysite.com`:

![Prohlížeč Microsoft Edge načetl úvodní stránka služby IIS.](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a>Soubory uzamčené nasazení

Při spuštění aplikace jsou zamknuté soubory ve složce pro nasazení. Uzamčené soubory nemohou být přepsána během nasazení. Chcete-li uvolnit uzamčené soubory v nasazení, zastavte fond aplikací pomocí **jednoho** z následujících přístupů:

* Použijte Nasazení webu a referenční `Microsoft.NET.Sdk.Web` v souboru projektu. Soubor *App_offline. htm* se umístí do kořenové složky adresáře webové aplikace. Když je soubor přítomen, modul ASP.NET Core aplikaci korektně ukončí a během nasazování zachová soubor *App_offline. htm* . Další informace najdete v referenčních informacích k [konfiguraci modulu ASP.NET Core](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).
* Ručně zastavte fond aplikací ve Správci služby IIS na serveru.
* Použití PowerShellu k vyřazení *App_offline. htm* (vyžaduje PowerShell 5 nebo novější):

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a>Ochrana dat

[Sada ASP.NET Core Data Protection Stack](xref:security/data-protection/introduction) je používána několika ASP.NET Core [middlewary](xref:fundamentals/middleware/index), včetně middlewaru používaného při ověřování. I v případě, že rozhraní API ochrany dat není voláno uživatelským kódem, je třeba nakonfigurovat ochranu dat pomocí skriptu nasazení nebo v uživatelském kódu, aby bylo možné vytvořit trvalé úložiště kryptografických [klíčů](xref:security/data-protection/implementation/key-management). Pokud není nakonfigurovaná ochrana dat, jsou klíče uložené v paměti a při restartování aplikace.

Pokud kanál klíče jsou uloženy v paměti, při restartování aplikace:

* Všechny tokeny ověřování na základě souborů cookie nejsou zneplatněny. 
* Uživatelé se musí znovu přihlásit v jejich další požadavek. 
* Všechna data chráněná pomocí aktualizační kanál, který klíč můžete už nebude možné dešifrovat. To může zahrnovat [CSRF tokeny](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) a [ASP.NET Core soubory cookie TempData MVC](xref:fundamentals/app-state#tempdata).

Pokud chcete v rámci služby IIS nakonfigurovat ochranu dat a zachovat přitom klíčového prstence, použijte **některý** z následujících přístupů:

* **Vytvoření klíčů registru ochrany dat**

  Používá aplikace ASP.NET Core klíče ochrany dat jsou uložené v registru, které jsou externí vzhledem k aplikacím. Pokud chcete zachovat klíče pro danou aplikaci, vytvořte klíče registru pro fond aplikací.

  Pro samostatnou instalaci služby IIS, která není součástí webfarmu, se dá [skript PowerShellu pro ochranu dat provision-AutoGenKeys. ps1](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) použít pro každý fond aplikací, který se používá v aplikaci ASP.NET Core. Tento skript vytvoří klíč registru HKLM registru, který je přístupný pouze pro účet pracovního procesu fondu aplikací aplikaci. Klíče se zašifrují neaktivní uložená data pomocí rozhraní DPAPI klíčem celý počítač.

  Ve webových farem lze nastavit aplikaci pro použití cesty UNC pro ukládání jeho data protection klíč kanál. Ve výchozím nastavení klíče ochrany dat nejsou šifrovány. Zajistěte, aby byly omezené na účet Windows, které aplikace běží pod oprávnění pro sdílené síťové složce. X X509 certifikát můžete použít k ochraně klíčů v klidovém stavu. Vezměte v úvahu mechanismus pro uživatelům umožní nahrát certifikáty: místo certifikátů do důvěryhodného certifikátu uživatele ukládat a ujistěte se, jsou k dispozici na všech počítačích, ve kterém běží aplikace uživatele. Podrobnosti najdete v tématu [Konfigurace ochrany ASP.NET Core dat](xref:security/data-protection/configuration/overview) .

* **Konfigurace fondu aplikací služby IIS pro načtení profilu uživatele**

  Toto nastavení se nachází v části **model procesu** v části **Rozšířená nastavení** fondu aplikací. Nastavte **načíst profil uživatele** na `True`. Když nastavíte `True`, klíče se ukládají v adresáři profilu uživatele a jsou chráněné pomocí DPAPI s klíčem specifickým pro uživatelský účet. Klíče jsou uchovány ve složce *% localappdata%/ASP.NET/DataProtection-Keys* .

  Musí být povolený i [atribut setProfileEnvironment](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) fondu aplikací. Výchozí hodnota `setProfileEnvironment` je `true`. V některých scénářích (například operační systém Windows) je `setProfileEnvironment` nastaveno na `false`. Pokud se klíče neukládají v adresáři profilu uživatele podle očekávání:

  1. Přejděte do složky *% windir%/system32/Inetsrv/config* .
  1. Otevřete soubor *ApplicationHost. config* .
  1. Vyhledejte element `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>`.
  1. Potvrďte, že atribut `setProfileEnvironment` není přítomen, což nastaví hodnotu `true`, nebo explicitně nastavte hodnotu atributu na `true`.

* **Použití systému souborů jako úložiště pro Key Ring**

  Upravte kód aplikace tak, aby [používal systém souborů jako úložiště ve službě Key Ring](xref:security/data-protection/configuration/overview). Použijte X509 certifikátu k ochraně klíče kanál a zajistit certifikát není důvěryhodný certifikát. Pokud certifikát podepsaný svým držitelem, můžete certifikát umístěte do úložiště důvěryhodných kořenových certifikátů.

  Pokud používáte IIS ve webové farmě:

  * Použití sdílené složky, ke kterému přístup všechny počítače.
  * Nasazení x X509 certifikát pro každý počítač. Konfigurace [ochrany dat v kódu](xref:security/data-protection/configuration/overview).

* **Nastavení zásad pro ochranu dat na úrovni počítače**

  Systém ochrany dat má omezená podpora pro nastavení výchozích [zásad](xref:security/data-protection/configuration/machine-wide-policy) pro všechny aplikace, které využívají rozhraní API pro ochranu dat. Další informace naleznete v tématu <xref:security/data-protection/introduction>.

## <a name="virtual-directories"></a>Virtuální adresáře

Aplikace ASP.NET Core nepodporují [virtuální adresáře služby IIS](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) . Aplikace může být hostována jako [dílčí aplikace](#sub-applications).

## <a name="sub-applications"></a>Dílčí aplikace

Aplikace ASP.NET Core může být hostovaná jako [podaplikace služby IIS (dílčí aplikace)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications). Sub – aplikace cesta stane součástí adresy URL kořenového aplikace.

Odběr aplikace by neměl obsahovat modul ASP.NET Core jako obslužná rutina. Pokud se modul přidá jako obslužná rutina v souboru *Web. config* dílčí aplikace 500,19, při pokusu o procházení dílčí aplikace se obdrží *interní chyba serveru* odkazující na chybný konfigurační soubor.

Následující příklad ukazuje publikovaný soubor *Web. config* pro ASP.NET Core dílčí aplikaci:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <aspNetCore processPath="dotnet" 
      arguments=".\MyApp.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

Když se v aplikaci ASP.NET Core hostuje podaplikace Core non-ASP.NET, explicitně se odebere zděděná obslužná rutina v souboru *Web. config* dílčí aplikace:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <remove name="aspNetCore" />
    </handlers>
    <aspNetCore processPath="dotnet" 
      arguments=".\MyApp.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

Propojení statických prostředků v rámci dílčí aplikace by měla používat notaci vlnovku (`~/`). Znak tildy vlnovkou spustí [pomocnou nápovědu](xref:mvc/views/tag-helpers/intro) k předřazení pathbase dílčí aplikace na vykreslený relativní odkaz. V případě dílčí aplikace na `/subapp_path`se obrázek propojený s `src="~/image.png"` vykresluje jako `src="/subapp_path/image.png"`. Middleware kořenové aplikace statické soubory nelze zpracovat požadavek statický soubor. Žádost zpracovává Middleware sub aplikace statické soubory.

Pokud je atribut `src` statického prostředku nastavený na absolutní cestu (například `src="/image.png"`), odkaz se vykreslí bez pathbase dílčí aplikace. Middleware statických souborů v kořenové aplikaci se pokusí o poskytování assetu z [kořenového adresáře webu](xref:fundamentals/index#web-root)kořenové aplikace, což má za následek *404 – nenalezené* odpovědi, pokud není k dispozici statický prostředek z kořenové aplikace.

K hostování aplikace v ASP.NET Core jako podřízeným aplikacím v rámci jiné aplikace ASP.NET Core:

1. Vytvořte fond aplikací pro aplikaci sub. Nastavte **verzi .NET CLR** na **žádný spravovaný kód** , protože základní modul CLR (Common Language Runtime) pro .NET Core se spouští k hostování aplikace v pracovním procesu, ne CLR Desktop (.NET CLR).

1. Přidání kořenového webu s podřízeným aplikacím v rámci kořenového webu ve Správci služby IIS.

1. Klikněte pravým tlačítkem na složku dílčí aplikace ve Správci služby IIS a vyberte **převést na aplikaci**.

1. V dialogovém okně **Přidat aplikaci** použijte pro **fond aplikací** tlačítko **Vybrat** , abyste přiřadili fond aplikací, který jste vytvořili pro dílčí aplikaci. Vyberte **OK**.

Přiřazení fondu samostatné aplikace k podřízeným aplikacím je požadavek, pokud používáte model hostingu v procesu.

Další informace o modelu hostování v rámci procesu a konfiguraci modulu ASP.NET Core naleznete v tématu <xref:host-and-deploy/aspnet-core-module>.

## <a name="configuration-of-iis-with-webconfig"></a>Konfigurace služby IIS pomocí souboru web.config

Konfigurace služby IIS má vliv na `<system.webServer>` části *Web. config* pro scénáře služby IIS, které jsou funkční pro ASP.NET Core aplikace s modulem ASP.NET Core. Konfigurace služby IIS je třeba funkční dynamické komprese. Pokud je služba IIS nakonfigurovaná na úrovni serveru, aby používala dynamickou kompresi, `<urlCompression>` element v souboru *Web. config* aplikace ho může zakázat pro ASP.NET Core aplikace.

Další informace najdete v následujících tématech:

* [Odkaz na konfiguraci pro \<System. webServer >](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

Chcete-li nastavit proměnné prostředí pro jednotlivé aplikace spuštěné v izolovaných fondech aplikací (podporované pro IIS 10,0 nebo novější), přečtěte si část *příkazového řádku Appcmd. exe* v tématu [proměnné prostředí \<environmentVariables >](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) v referenční dokumentaci služby IIS.

## <a name="configuration-sections-of-webconfig"></a>Konfigurační oddíly souboru Web.config

Konfigurační oddíly aplikací ASP.NET 4. x v *souboru Web. config* nepoužívá aplikace ASP.NET Core pro konfiguraci:

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

Aplikace ASP.NET Core jsou nakonfigurované pomocí jiných poskytovatelů konfigurace. Další informace najdete v tématu [Konfigurace](xref:fundamentals/configuration/index).

## <a name="application-pools"></a>Fondy aplikací

Při hostování více webů na serveru, doporučujeme izoluje aplikace od sebe navzájem spuštěním každou aplikaci ve vlastním fondu aplikací. Do této konfigurace se nastaví dialogové okno **Přidat web** do služby IIS. Když je zadaný **název lokality** , text se automaticky přenese do textového pole **fondu aplikací** . Nový fond aplikací je vytvořený pomocí názvu serveru po přidání serveru.

## <a name="application-pool-identity"></a>Identita fondu aplikací

Účet identita fondu aplikací umožňuje aplikaci běžet pod účtem jedinečný bez nutnosti vytvářet a spravovat domény nebo místní účty. IIS 8.0 nebo novější procesů pro pracovníka Správce služby IIS (WAS) vytvoří virtuální účet s názvem nového fondu aplikací a aplikace spouští fondu pracovních procesů pod tímto účtem ve výchozím nastavení. V konzole pro správu služby IIS v části **Upřesnit nastavení** fondu aplikací zajistěte, aby byla **Identita** nastavená na použití **ApplicationPoolIdentity**:

![Dialogové okno pokročilé nastavení fondu aplikací](index/_static/apppool-identity.png)

Proces správy služby IIS vytvoří zabezpečeného identifikátoru se název fondu aplikací v zabezpečení systému Windows. Pomocí této identity, je možné svázat prostředky. Ale tato identita není skutečný účet a nezobrazí se v konzole pro správu uživatelů Windows.

Pokud pracovní proces služby IIS vyžaduje přístup k aplikaci přes se zvýšenými oprávněními, upravte seznam řízení přístupu (ACL) pro adresář obsahující aplikaci:

1. Otevřete Průzkumníka Windows a přejděte do adresáře.

1. Klikněte pravým tlačítkem na adresář a vyberte **vlastnosti**.

1. Na kartě **zabezpečení** vyberte tlačítko **Upravit** a pak klikněte na tlačítko **Přidat** .

1. Vyberte tlačítko **umístění** a ujistěte se, že je vybraný systém.

1. Do pole **Zadejte názvy objektů pro výběr** oblasti zadejte **\\< služby IIS AppPool > APP_POOL_NAME** . Vyberte tlačítko pro **kontrolu názvů** . Pro službu *DefaultAppPool* ověřte názvy pomocí **služby IIS AppPool\DefaultAppPool**. Když je vybráno tlačítko pro **kontrolu názvů** , hodnota **DefaultAppPool** je uvedena v oblasti názvy objektů. Není možné zadat název fondu aplikací přímo do oblasti názvy objektu. Při kontrole názvu objektu použijte **\\< app_pool_name > formátu IIS AppPool** .

   ![Dialogová okna pro výběr uživatelů nebo skupin pro složku aplikace: název fondu aplikací "DefaultAppPool" se připojí k "rozhraní IIS AppPool\" v oblasti názvy objektů před výběrem" kontrolovat jména ".](index/_static/select-users-or-groups-1.png)

1. Vyberte **OK**.

   ![Vyberte uživatele nebo skupiny dialogové okno pro složky aplikace: Po výběru "Kontrola názvů", "DefaultAppPool" se zobrazí v objektu název objektu názvy oblasti.](index/_static/select-users-or-groups-2.png)

1. Ve výchozím nastavení by měla být udělena oprávnění ke čtení &amp;mu spouštění. Zadejte další oprávnění podle potřeby.

Přístup se dá taky udělit na příkazovém řádku pomocí nástroje **Icacls** . Pomocí příkazu *DefaultAppPool* jako příkladu se používá následující příkaz:

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

Další informace najdete v tématu [Icacls](/windows-server/administration/windows-commands/icacls) .

## <a name="http2-support"></a>Podpora HTTP/2

[Http/2](https://httpwg.org/specs/rfc7540.html) se podporuje pro nasazení mimo procesy, která splňují následující základní požadavky:

* Windows Server 2016 nebo Windows 10 nebo novější; IIS 10 nebo novější.
* Veřejná připojení hraničních serverů používají protokol HTTP/2, ale připojení reverzního proxy [serveru Kestrel](xref:fundamentals/servers/kestrel) používá protokol HTTP/1.1.
* Cílová architektura: není k dispozici pro nasazení mimo proces, protože připojení HTTP/2 je zpracována zcela službou IIS.
* Protokol TLS 1.2 nebo vyšší připojení

Pokud je navázáno připojení HTTP/2, `HTTP/1.1`sestavy [protokolu HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) .

HTTP/2 je standardně povolená. Připojení vrátit zpět k protokolu HTTP/1.1, pokud nedojde k připojení k protokolu HTTP/2. Další informace o konfiguraci HTTP/2 v nasazeních služby IIS najdete v tématu [http/2 ve službě IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).

## <a name="cors-preflight-requests"></a>Požadavky na kontrolu před výstupem CORS

*Tato část platí jenom pro ASP.NET Core aplikace, které cílí na .NET Framework.*

Pro ASP.NET Core aplikaci, která cílí na .NET Framework, požadavky na možnosti nejsou ve výchozím nastavení ve službě IIS předány do aplikace. Informace o tom, jak nakonfigurovat obslužné rutiny IIS aplikace v *souboru Web. config* , aby předávala požadavky na možnosti, najdete v tématu [Povolení žádostí o více zdrojů v ASP.NET webovém rozhraní API 2: jak CORS funguje](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).

## <a name="deployment-resources-for-iis-administrators"></a>Materiály pro nasazení pro správce služby IIS

* [Dokumentace ke službě IIS](/iis)
* [Začínáme ve službě IIS pomocí Správce služby IIS](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [Nasazení aplikace .NET Core](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a>Další zdroje

* <xref:test/troubleshoot>
* <xref:index>
* [Oficiální web Microsoft IIS](https://www.iis.net/)
* [Knihovna technických obsahu pro Windows Server](/windows-server/windows-server)
* [HTTP/2 ve službě IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

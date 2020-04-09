---
title: Hostování ASP.NET jádra ve službě Windows se službou IIS
author: rick-anderson
description: Přečtěte si, jak hostovat aplikace ASP.NET Core v Internetové informační službě (IIS) systému Windows Server.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: host-and-deploy/iis/index
ms.openlocfilehash: 819c53f945c1e5bb2cedcef8fc39d4c8761e4549
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977025"
---
# <a name="host-aspnet-core-on-windows-with-iis"></a>Hostování ASP.NET jádra ve službě Windows se službou IIS

::: moniker range=">= aspnetcore-3.0"

Výukové prostředí o publikování aplikace ASP.NET Core na server <xref:tutorials/publish-to-iis>služby IIS naleznete v tématu .

[Instalace balíčku hostingu jádra .NET](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a>Podporované operační systémy

Podporovány jsou následující operační systémy:

* Windows 7 nebo novější
* Windows Server 2012 R2 nebo novější

[Server HTTP.sys](xref:fundamentals/servers/httpsys) (dříve nazývaný WebListener) nefunguje v konfiguraci reverzního proxy serveru se službou IIS. Použijte [server Kestrel](xref:fundamentals/servers/kestrel).

Informace o hostování v <xref:host-and-deploy/azure-apps/index>Azure najdete v tématu .

Pokyny pro řešení <xref:test/troubleshoot>potíží naleznete v tématu .

## <a name="supported-platforms"></a>Podporované platformy

Podporovány jsou aplikace publikované pro 32bitové (x86) nebo 64bitové (x64) nasazení. Nasazení 32bitové aplikace s 32bitovou sadou .NET Core SDK, pokud ji ne:

* Vyžaduje větší adresní prostor virtuální paměti, který je k dispozici pro 64bitovou aplikaci.
* Vyžaduje větší velikost zásobníku iis.
* Má 64bitové nativní závislosti.

K publikování 64bitové sady SDK jádra (x64) použijte 64bitovou sadu .NET Core SDK. V hostitelském systému musí být k dispozici 64bitový běhový čas.

## <a name="hosting-models"></a>Modely hostingu

### <a name="in-process-hosting-model"></a>Model hostování v procesu

Pomocí hostování v rámci procesu běží aplikace ASP.NET Core ve stejném procesu jako pracovní proces služby IIS. Hostování v procesu poskytuje lepší výkon než mimoproceshosting, protože požadavky nejsou proxied přes adaptér zpětné smyčky, síťové rozhraní, které vrací odchozí síťový provoz zpět do stejného počítače. Služba IIS zpracovává správu procesů pomocí [služby aktivace procesů systému Windows (WAS).](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)

Základní [modul ASP.NET](xref:host-and-deploy/aspnet-core-module):

* Provede inicializaci aplikace.
  * Načte [CoreCLR](/dotnet/standard/glossary#coreclr).
  * Volání `Program.Main`.
* Zpracovává životnost nativního požadavku služby IIS.

Model hostování v procesu není podporován pro aplikace ASP.NET Core, které cílí na rozhraní .NET Framework.

Následující diagram znázorňuje vztah mezi iis, ASP.NET základní modul a aplikace hostované v procesu:

![ASP.NET core modul ve scénáři hostování v procesu](index/_static/ancm-inprocess.png)

Požadavek přichází z webu na ovladač HTTP.sys v režimu jádra. Řidič směruje nativní požadavek do služby IIS na nakonfigurovaném portu webu, obvykle 80 (HTTP) nebo 443 (HTTPS). Základní modul ASP.NET obdrží nativní požadavek a předá`IISHttpServer`jej serveru HTTP služby IIS ( ). Server HTTP služby IIS je implementace serveru v procesu pro službu IIS, která převádí požadavek z nativního na spravovaný.

Po zpracování požadavku serverem HTTP služby IIS je požadavek odeslán do kanálu middlewaru ASP.NET Core. Middleware kanálu zpracovává požadavek a předá `HttpContext` jej jako instanci logiky aplikace. Odpověď aplikace je předána zpět do služby IIS prostřednictvím serveru HTTP služby IIS. Služba IIS odešle odpověď klientovi, který požadavek inicioval.

Hostování v procesu je přihlášení pro stávající aplikace, ale [dotnet nové](/dotnet/core/tools/dotnet-new) šablony výchozí v procesu hostování modelu pro všechny scénáře služby IIS a IIS Express.

`CreateDefaultBuilder`přidá <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instanci <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> voláním metody pro spuštění [coreclru](/dotnet/standard/glossary#coreclr) a hostování aplikace uvnitř pracovního procesu služby IIS (*w3wp.exe* nebo *iisexpress.exe*). Testy výkonu ukazují, že hostování aplikace .NET Core v procesu přináší výrazně vyšší propustnost požadavků ve srovnání s hostováním aplikací mimo proces a proxy požadavků na server [Kestrel.](xref:fundamentals/servers/kestrel)

> [!NOTE]
> Aplikace publikované jako jeden soubor spustitelný soubor nelze načíst v procesu hostování modelu.

### <a name="out-of-process-hosting-model"></a>Model hostování mimo proces

Vzhledem k tomu, že aplikace ASP.NET Core běží v procesu odděleném od pracovního procesu služby IIS, zpracovává ASP.NET core module správu procesů. Modul spustí proces pro aplikaci ASP.NET Core při příchodu prvního požadavku a restartuje aplikaci, pokud se vypne nebo dojde k chybě. Toto je v podstatě stejné chování jako u aplikací, které běží v procesu, které jsou spravovány [službou aktivace procesu systému Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

Následující diagram znázorňuje vztah mezi iis, ASP.NET základní modul a aplikace hostované mimo proces:

![ASP.NET základní modul ve scénáři mimoproceshostingového hostingu](index/_static/ancm-outofprocess.png)

Požadavky dorazí z webu do ovladače HTTP.sys v režimu jádra. Řidič směruje požadavky do služby IIS na nakonfigurovaném portu webu, obvykle 80 (HTTP) nebo 443 (HTTPS). Modul předá požadavky Kestrel na náhodný port pro aplikaci, která není port 80 nebo 443.

Modul určuje port prostřednictvím proměnné prostředí při <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> spuštění a rozšíření konfiguruje server pro naslouchání . `http://localhost:{PORT}` Jsou prováděny další kontroly a požadavky, které nepocházejí z modulu, jsou odmítnuty. Modul nepodporuje předávání https, takže požadavky jsou předávány přes HTTP i v případě, že přijaté službou IIS přes protokol HTTPS.

Poté, co Kestrel vyzvedne požadavek z modulu, je požadavek posunut do kanálu ASP.NET middleware. Middleware kanálu zpracovává požadavek a předá `HttpContext` jej jako instanci logiky aplikace. Middleware přidané integrací služby IIS aktualizuje schéma, vzdálenou IP adresu a pathbase tak, aby odpovídaly předání požadavku ke společnosti Kestrel. Odpověď aplikace je předána zpět do služby IIS, která ji odešle zpět do klienta HTTP, který inicioval požadavek.

Pokyny ke konfiguraci ASP.NET <xref:host-and-deploy/aspnet-core-module>core modulem naleznete v tématu .

Další informace o hostování najdete [v tématu Host in ASP.NET Core](xref:fundamentals/index#host).

## <a name="application-configuration"></a>Konfigurace aplikací

### <a name="enable-the-iisintegration-components"></a>Povolení součástí Integrace iIS

Při vytváření hostitele `CreateHostBuilder` v *(Program.cs*) volání <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> umožňující integraci iis:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        ...
```

Další informace `CreateDefaultBuilder`naleznete <xref:fundamentals/host/generic-host#default-builder-settings>v tématu .

### <a name="iis-options"></a>Možnosti iis

**Model hostování v procesu**

Chcete-li konfigurovat možnosti serveru IIS, zahrňte konfiguraci služby v <xref:Microsoft.AspNetCore.Builder.IISServerOptions> aplikaci <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>. Následující příklad zakáže automatické ověřování:

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| Možnost                         | Výchozí | Nastavení |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | Pokud `true`server IIS `HttpContext.User` nastaví ověřené ověřování [systému Windows](xref:security/authentication/windowsauth). Pokud `false`server poskytuje identitu pouze `HttpContext.User` pro výzvy a reaguje na `AuthenticationScheme`ně, pokud o to výslovně požádá . Aby bylo možné službu `AutomaticAuthentication` IIS fungovat, musí být ve službě IIS povoleno ověřování systému Windows. Další informace naleznete v tématu [Ověřování systému Windows](xref:security/authentication/windowsauth). |
| `AuthenticationDisplayName`    | `null`  | Nastaví zobrazované jméno zobrazené uživatelům na přihlašovacích stránkách. |
| `AllowSynchronousIO`           | `false` | Zda synchronní V/O je `HttpContext.Request` povoleno `HttpContext.Response`pro a . |
| `MaxRequestBodySize`           | `30000000`  | Získá nebo nastaví maximální velikost `HttpRequest`těla požadavku pro . Všimněte si, že `maxAllowedContentLength` iis sám má `MaxRequestBodySize` limit, `IISServerOptions`který bude zpracován před soubor v . Změna `MaxRequestBodySize` nebude mít vliv `maxAllowedContentLength`na . Chcete-li zvýšit `maxAllowedContentLength`, přidejte položku `maxAllowedContentLength` do *web.config* nastavit na vyšší hodnotu. Další podrobnosti naleznete v [tématu Konfigurace](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration). |

**Model hostování mimo proces**

Chcete-li konfigurovat možnosti služby <xref:Microsoft.AspNetCore.Builder.IISOptions> <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>IIS, zahrňte konfiguraci služby v aplikaci . Následující příklad zabrání aplikaci v naplnění `HttpContext.Connection.ClientCertificate`:

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| Možnost                         | Výchozí | Nastavení |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | Pokud `true` [middleware integrace služby IIS](#enable-the-iisintegration-components) nastaví ověřené ověřování systému `HttpContext.User` [Windows](xref:security/authentication/windowsauth). Pokud `false`middleware poskytuje identitu `HttpContext.User` a reaguje na problémy pouze na `AuthenticationScheme`žádost . Aby bylo možné službu `AutomaticAuthentication` IIS fungovat, musí být ve službě IIS povoleno ověřování systému Windows. Další informace naleznete v tématu [Ověřování systému Windows.](xref:security/authentication/windowsauth) |
| `AuthenticationDisplayName`    | `null`  | Nastaví zobrazované jméno zobrazené uživatelům na přihlašovacích stránkách. |
| `ForwardClientCertificate`     | `true`  | Pokud `true` a `MS-ASPNETCORE-CLIENTCERT` hlavička požadavku `HttpContext.Connection.ClientCertificate` je k dispozici, je naplněn. |

### <a name="proxy-server-and-load-balancer-scenarios"></a>Scénáře proxy serveru a vyrovnávání zatížení

[Middleware integrace služby IIS](#enable-the-iisintegration-components), který konfiguruje middleware předávaných záhlaví a ASP.NET základní modul, jsou nakonfigurovány tak, aby předávaly schéma (HTTP/HTTPS) a vzdálenou adresu IP, ze které požadavek pochází. Další konfigurace může být vyžadována pro aplikace hostované za dalšími proxy servery a nástroji pro vyrovnávání zatížení. Další informace naleznete v [tématu Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).

### <a name="webconfig-file"></a>soubor web.config

Soubor *web.config* konfiguruje [základní modul ASP.NET](xref:host-and-deploy/aspnet-core-module). Při publikování projektu je vytváření, transformace a publikování souboru *web.config* zpracováno cílem MSBuild (`_TransformWebConfig`). Tento cíl je k dispozici v`Microsoft.NET.Sdk.Web`cílech sady Web SDK ( ). Sada SDK je nastavena v horní části souboru projektu:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Pokud soubor *web.config* není v projektu přítomen, je soubor vytvořen se správnou *processPath* a *argumenty* pro konfiguraci ASP.NET základnímodul a přesunutna do [publikovaného výstupu](xref:host-and-deploy/directory-structure).

Pokud *web.config* soubor je přítomen v projektu, soubor je transformován se správným *processPath* a *argumenty* pro konfiguraci ASP.NET core modul a přesunuta do publikovaného výstupu. Transformace nezmění nastavení konfigurace služby IIS v souboru.

Soubor *web.config* může poskytovat další nastavení konfigurace služby IIS, která řídí aktivní moduly služby IIS. Informace o modulech služby IIS, které jsou schopné zpracovávat požadavky pomocí aplikací ASP.NET Core, naleznete v tématu [modulů služby IIS.](xref:host-and-deploy/iis/modules)

Chcete-li zabránit webové sady SDK v transformaci souboru *web.config,* použijte vlastnost ** \<IsTransformWebConfigDisabled>** v souboru projektu:

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

Při zakázání webové sady SDK z transformace souboru *processPath* a argumenty by měly být ručně *nastaveny* vývojářem. Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module>.

### <a name="webconfig-file-location"></a>umístění souboru web.config

Aby bylo možné správně nastavit [ASP.NET core modul,](xref:host-and-deploy/aspnet-core-module) musí být soubor *web.config* přítomen na [cestě kořenového obsahu](xref:fundamentals/index#content-root) (obvykle základní cesta aplikace) nasazené aplikace. Jedná se o stejné umístění jako fyzická cesta k webu poskytnutá službu IIS. Soubor *web.config* je vyžadován v kořenovém adresáři aplikace, aby bylo možné publikovat více aplikací pomocí nasazení webu.

Na fyzické cestě aplikace existují citlivé soubory, například * \<assembly>.runtimeconfig.json*, * \<assembly>.xml* (komentáře xml documentation) a * \<assembly>.deps.json*. Pokud je soubor *web.config* přítomen a web se spustí normálně, služby IIS tyto citlivé soubory neobsluhuje, pokud jsou požadovány. Pokud soubor *web.config* chybí, je nesprávně pojmenován nebo jej nelze nakonfigurovat pro normální spuštění, může služba IIS veřejně sloužit citlivým souborům.

**Soubor *web.config* musí být v nasazení vždy přítomen, správně pojmenovaný a musí být schopen nakonfigurovat web pro normální spuštění. Nikdy neodstraňujte soubor *web.config* z produkčního nasazení.**

### <a name="transform-webconfig"></a>Transformace souboru web.config

Pokud potřebujete transformovat *web.config* při publikování (například nastavit proměnné prostředí na základě konfigurace, profilu nebo prostředí), přečtěte si viz <xref:host-and-deploy/iis/transform-webconfig>.

## <a name="iis-configuration"></a>Konfigurace služby IIS

**Operační systémy Windows Server**

Povolte roli serveru **webového serveru (IIS)** a vytvořte služby rolí.

1. Použijte Průvodce **přidáním rolí a funkcí** z nabídky **Spravovat** nebo odkaz ve **Správci serveru**. V kroku **Role serveru** zaškrtněte políčko **U webového serveru (IIS).**

   ![Role služby IIS webového serveru je vybrána v kroku Vybrat role serveru.](index/_static/server-roles-ws2016.png)

1. Po kroku **Funkce** se krok **služby role** načte pro webový server (IIS). Vyberte požadované služby rolí služby IIS nebo přijměte výchozí služby rolí, které jsou k dispozici.

   ![Výchozí služby rolí jsou vybrány v kroku Vybrat služby rolí.](index/_static/role-services-ws2016.png)

   **Ověřování systému Windows (volitelné)**  
   Chcete-li povolit ověřování systému Windows, rozbalte následující uzly:**Zabezpečení** **webového serveru** > . Vyberte funkci **Ověřování systému Windows.** Další informace naleznete v [tématech Ověřování \<systému Windows windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) a Konfigurace ověřování [systému Windows](xref:security/authentication/windowsauth).

   **WebSockets (volitelné)**  
   WebSockets je podporován ASP.NET jádrem 1.1 nebo novějším. Chcete-li povolit rozhraní WebSockets, rozbalte následující uzly:**Vývoj aplikací webového** **serveru** > . Vyberte funkci **WebSocket Protocol.** Další informace naleznete v tématu [WebSockets](xref:fundamentals/websockets).

1. Pokračujte v **potvrzovacím** kroku a nainstalujte roli a služby webového serveru. Po instalaci role **webového serveru (IIS)** není restartování serveru nebo služby IIS vyžadováno.

**Desktopové operační systémy Windows**

Povolte **konzolu pro správu služby IIS** a **služby World Wide Web Services**.

1. Přejděte na **Ovládací panely** > **Programy** > **a funkce** > **Zapněte nebo vypněte funkce systému Windows** (levá strana obrazovky).

1. Otevřete uzel **Internetové informační služby.** Otevřete uzel **Nástroje pro správu webu.**

1. Zaškrtněte políčko **U konzole pro správu služby IIS**.

1. Zaškrtněte políčko **U služeb World Wide Web Services**.

1. Přijměte výchozí funkce **pro služby World Wide Web Services** nebo upravte funkce služby IIS.

   **Ověřování systému Windows (volitelné)**  
   Chcete-li povolit ověřování systému Windows, rozbalte následující uzly: **World Wide Web Services** > **Security**. Vyberte funkci **Ověřování systému Windows.** Další informace naleznete v [tématech Ověřování \<systému Windows windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) a Konfigurace ověřování [systému Windows](xref:security/authentication/windowsauth).

   **WebSockets (volitelné)**  
   WebSockets je podporován ASP.NET jádrem 1.1 nebo novějším. Chcete-li povolit rozhraní WebSockets, rozbalte následující uzly: Funkce**vývoje aplikací**pro **webové služby** > . Vyberte funkci **WebSocket Protocol.** Další informace naleznete v tématu [WebSockets](xref:fundamentals/websockets).

1. Pokud instalace služby IIS vyžaduje restartování, restartujte systém.

![V funkcích systému Windows jsou vybrány konzoly IIS Management Console a služby World Wide Web Services.](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a>Instalace balíčku hostingu jádra .NET

Nainstalujte *balíček .NET Core Hosting do* hostitelského systému. Balíček nainstaluje modul .NET Core Runtime, knihovnu .NET Core Library a [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module). Modul umožňuje ASP.NET aplikace Core běžet za službou IIS.

> [!IMPORTANT]
> Pokud je sada Hosting Bundle nainstalována před službou IIS, musí být instalace balíčku opravena. Po instalaci služby IIS znovu spusťte instalační program sady Hosting Bundle.
>
> Pokud je sada Hosting Bundle nainstalována po instalaci 64bitové (x64) verze rozhraní .NET Core, mohou se zdát, že sady SDK chybí ([byly zjištěny sady SDK .Net Core .](xref:test/troubleshoot#no-net-core-sdks-were-detected) Chcete-li problém <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>vyřešit, naleznete v tématu .

### <a name="direct-download-current-version"></a>Přímé stahování (aktuální verze)

Stáhněte si instalační program pomocí následujícího odkazu:

[Aktuální instalační program .NET Core Hosting Bundle (přímé stahování)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a>Dřívější verze instalačního programu

Chcete-li získat starší verzi instalačního programu:

1. Přejděte na stránku [Stáhnout jádro rozhraní .NET.](https://dotnet.microsoft.com/download/dotnet-core)
1. Klikněte na požadovanou verzi .NET Core.
1. Ve sloupci **Spustit aplikace – runtime** najděte požadovaný řádek požadované verze runtime .NET Core.
1. Stáhněte si instalační program pomocí odkazu **Runtime & Hosting Bundle.**

> [!WARNING]
> Některé instalační programy obsahují verze, které dosáhly konce životnosti (EOL) a které již nejsou podporovány společností Microsoft. Další informace naleznete v [zásadách podpory](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

### <a name="install-the-hosting-bundle"></a>Nainstalujte balíček hostingu

1. Spusťte instalační program na serveru. Při spuštění instalačního programu z příkazového prostředí správce jsou k dispozici následující parametry:

   * `OPT_NO_ANCM=1`&ndash; Přeskočte instalaci ASP.NET základního modulu.
   * `OPT_NO_RUNTIME=1`&ndash; Přeskočte instalaci runtime jádra .NET. Používá se v případě, že server hostuje pouze [samostatná nasazení (SCD).](/dotnet/core/deploying/#self-contained-deployments-scd)
   * `OPT_NO_SHAREDFX=1`&ndash; Přeskočit instalaci ASP.NET sdíleného rozhraní (ASP.NET runtime). Používá se v případě, že server hostuje pouze [samostatná nasazení (SCD).](/dotnet/core/deploying/#self-contained-deployments-scd)
   * `OPT_NO_X86=1`&ndash; Přeskočit instalaci za běhu x86. Tento parametr použijte, pokud víte, že nebudete hostovat 32bitové aplikace. Pokud existuje nějaká šance, že budete v budoucnu hostovat 32bitové i 64bitové aplikace, nepoužívejte tento parametr a nainstalujte oba runtimes.
   * `OPT_NO_SHARED_CONFIG_CHECK=1`&ndash; Zakažte kontrolu použití sdílené konfigurace služby IIS, pokud je sdílená konfigurace *(applicationHost.config)* ve stejném počítači jako instalace služby IIS. *K dispozici pouze pro ASP.NET instalačních programů hostingu Bundler nebo novějším.* Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.
1. Restartujte systém nebo proveďte následující příkazy v příkazovém prostředí:

   ```console
   net stop was /y
   net start w3svc
   ```
   Restartování služby IIS vyvolá změnu systémové cesty, což je proměnná prostředí provedená instalačním programem.

ASP.NET Core nepřijímá chování pro převrácení pro verze oprav sdílených rámců. Po upgradu sdíleného frameworku instalací nového hostingového balíčku restartujte systém nebo spusťte následující příkazy v příkazovém prostředí:

```console
net stop was /y
net start w3svc
```

> [!NOTE]
> Informace o sdílené konfiguraci služby IIS naleznete [v tématu ASP.NET Core Module se sdílenou konfigurací služby IIS](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a>Instalace nasazení webu při publikování pomocí sady Visual Studio

Při nasazování aplikací na servery s [nasazením webu](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)nainstalujte na server nejnovější verzi nasazení webu. Chcete-li nainstalovat nasazení webu, použijte [Instalační službu webové platformy (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) nebo získejte instalační program přímo ze [služby Stažení softwaru](https://www.microsoft.com/download/details.aspx?id=43717). Upřednostňovanou metodou je použití webpi. WebPI nabízí samostatné nastavení a konfiguraci pro poskytovatele hostingu.

## <a name="create-the-iis-site"></a>Vytvoření webu iis

1. V hostitelském systému vytvořte složku, která bude obsahovat publikované složky a soubory aplikace. V následujícím kroku je cesta ke složce k dispozici službu IIS jako fyzická cesta k aplikaci. Další informace o složce nasazení aplikace a <xref:host-and-deploy/directory-structure>rozložení souborů naleznete v tématu .

1. Ve Správci služby IIS otevřete uzel serveru v panelu **Připojení.** Klikněte pravým tlačítkem myši na složku **Weby.** V kontextové nabídce vyberte **Přidat web.**

1. Zadejte **název webu** a nastavte **fyzickou cestu** ke složce nasazení aplikace. Zadejte konfiguraci **vazby** a vytvořte web výběrem **možnosti OK**:

   ![V kroku Přidat web zadejte název webu, fyzickou cestu a název hostitele.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > Vazby se zástupnými`http://*:80/` symboly nejvyšší úrovně ( a `http://+:80`) by **neměly** být používány. Vazby se zástupnými symboly nejvyšší úrovně mohou otevřít vaši aplikaci slabá místa zabezpečení. To platí pro silné i slabé zástupné znaky. Používejte explicitní názvy hostitelů místo zástupných znaků. Vazba se zástupnými znaky subdomény (například) nemá toto bezpečnostní riziko, `*.mysub.com` `*.com`pokud řídíte celou nadřazenou doménu (na rozdíl od , která je zranitelná). Viz [rfc7230 sekce-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) pro více informací.

1. Pod uzlovým serverem vyberte **fondy aplikací**.

1. Klikněte pravým tlačítkem myši na fond aplikací webu a v kontextové nabídce vyberte **Základní nastavení.**

1. V okně **Upravit fond aplikací** nastavte verzi **.NET CLR** na **Žádný spravovaný kód**:

   ![Pro verzi CLR rozhraní .NET nenastavte žádný spravovaný kód.](index/_static/edit-apppool-ws2016.png)

    ASP.NET Core běží v samostatném procesu a spravuje runtime. ASP.NET Core nespoléhá na načtení plochy CLR (.NET CLR)&mdash;Core Common Language Runtime (CoreCLR) pro .NET Core je spuštěn pro hostování aplikace v pracovním procesu. Nastavení **verze .NET CLR** na **žádný spravovaný kód** je volitelné, ale doporučeno.

1. *ASP.NET jádra 2.2 nebo novějšího:* Pro 64bitové (x64) [samostatné nasazení,](/dotnet/core/deploying/#self-contained-deployments-scd) které používá [model hostování v procesu](#in-process-hosting-model), zakažte fond aplikací pro 32bitové (x86) procesy.

   V postranním panelu **Akce** správce služby IIS > **fondy aplikací**vyberte **možnost Nastavit výchozí nastavení fondu aplikací** nebo **Upřesnit nastavení**. Vyhledejte **funkci Povolit 32bitové aplikace** a nastavte hodnotu na . `False` Toto nastavení nemá vliv na aplikace nasazené pro [mimoprocesový hosting](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).

1. Potvrďte, že identita modelu procesu má správná oprávnění.

   Pokud se výchozí identita fondu aplikací **(identita****procesního modelu** > ) změní z **ApplicationPoolIdentity** na jinou identitu, ověřte, zda nová identita má požadovaná oprávnění pro přístup ke složce, databázi a dalším požadovaným prostředkům aplikace. Fond aplikací například vyžaduje přístup pro čtení a zápis do složek, kde aplikace čte a zapisuje soubory.

**Konfigurace ověřování systému Windows (volitelná)**  
Další informace naleznete v [tématu Configure Windows authentication](xref:security/authentication/windowsauth).

## <a name="deploy-the-app"></a>Nasazení aplikace

Nasaďte aplikaci do složky **Fyzické cesty** služby IIS, která byla vytvořena v části Vytvořit [web služby IIS.](#create-the-iis-site) [Nasazení webu](/iis/publish/using-web-deploy/introduction-to-web-deploy) je doporučený mechanismus pro nasazení, ale existuje několik možností pro přesunutí aplikace ze složky *publikování* projektu do složky nasazení hostitelského systému.

### <a name="web-deploy-with-visual-studio"></a>Nasazení webu pomocí sady Visual Studio

Podívejte se na [profily publikování aplikace Visual Studio pro téma nasazení aplikace ASP.NET Core,](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) kde se dozvíte, jak vytvořit profil publikování pro použití s nasazením webu. Pokud poskytovatel hostingu poskytuje profil publikování nebo podporu pro jeho vytvoření, stáhněte si jeho profil a importujte jej pomocí dialogového okna **Publikovat** sady Visual Studio:

![Stránka dialogového okna Publikovat](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a>Nasazení webu mimo Visual Studio

[Nasazení webu](/iis/publish/using-web-deploy/introduction-to-web-deploy) lze také použít mimo Visual Studio z příkazového řádku. Další informace naleznete v [tématu Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).

### <a name="alternatives-to-web-deploy"></a>Alternativy k nasazení webu

Pomocí některé z několika metod přesuňte aplikaci do hostitelského systému, jako je ruční kopírování, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy)nebo [PowerShell](/powershell/).

Další informace o ASP.NET nasazení jádra do služby IIS naleznete v části [Prostředky nasazení pro správce služby IIS.](#deployment-resources-for-iis-administrators)

## <a name="browse-the-website"></a>Procházení webu

Po nasazení aplikace do hostitelského systému požádejte o jeden z veřejných koncových bodů aplikace.

V následujícím příkladu je web vázán na název `www.mysite.com` **hostitele** iis na **portu** `80`. Žádost se podává `http://www.mysite.com`na :

![Prohlížeč Microsoft Edge načetl úvodní stránku služby IIS.](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a>Uzamčené soubory nasazení

Soubory ve složce nasazení jsou uzamčeny, když je aplikace spuštěná. Uzamčené soubory nelze během nasazení přepsat. Chcete-li uvolnit uzamčené soubory v nasazení, zastavte fond aplikací pomocí **jednoho z** následujících přístupů:

* Použijte nasazení webu `Microsoft.NET.Sdk.Web` a odkaz v souboru projektu. Soubor *app_offline.htm* je umístěn v kořenovém adresáři webové aplikace. Když je soubor k dispozici, ASP.NET základní modul řádně vypne aplikaci a slouží *souboru app_offline.htm* během nasazení. Další informace naleznete v [odkazu na konfiguraci ASP.NET core modulem](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).
* Ručně zastavit fond aplikací ve Správci služby IIS na serveru.
* Použití Prostředí PowerShell k přetažení *app_offline.htm* (vyžaduje PowerShell 5 nebo novější):

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a>Ochrana dat

Zásobník [ASP.NET Core Data Protection](xref:security/data-protection/introduction) používá několik [middlewares](xref:fundamentals/middleware/index)ASP.NET Core , včetně middlewaru používaného při ověřování. I v případě, že rozhraní API ochrany dat nejsou volána uživatelským kódem, ochrana dat by měla být nakonfigurována pomocí skriptu nasazení nebo v uživatelském kódu, aby se vytvořilo trvalé úložiště kryptografických [klíčů](xref:security/data-protection/implementation/key-management). Pokud ochrana dat není nakonfigurována, klíče jsou uloženy v paměti a po restartování aplikace zahozeny.

Pokud je kroužek klíče uložen v paměti při restartování aplikace:

* Všechny ověřovací tokeny založené na souborech cookie jsou zrušeny. 
* Uživatelé se musí znovu přihlásit při dalším požadavku. 
* Všechna data chráněná kroužkem klíče již nelze dešifrovat. To může zahrnovat [tokeny CSRF](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) a [ASP.NET základní soubory cookie MVC TempData](xref:fundamentals/app-state#tempdata).

Chcete-li nakonfigurovat ochranu dat ve službě IIS tak, aby zachycovací byl kroužek klíče, použijte **jeden** z následujících postupů:

* **Vytvoření klíčů registru ochrany dat**

  Klíče ochrany dat používané ASP.NET aplikace Core jsou uloženy v registru mimo aplikace. Chcete-li zachovat klíče pro danou aplikaci, vytvořte klíče registru pro fond aplikací.

  Pro samostatné instalace služby IIS mimo webfarmu lze [skript prostředí PowerShell Pro poskytování ochrany dat-AutoGenKeys.ps1](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) použít pro každý fond aplikací používaný s aplikací ASP.NET Core. Tento skript vytvoří klíč registru v registru HKLM, který je přístupný pouze pro účet pracovního procesu fondu aplikací. Klíče jsou šifrovány v klidovém stavu pomocí dpapi s klíčem pro celý počítač.

  Ve scénářích webové farmy lze aplikaci nakonfigurovat tak, aby k ukládání kroužku klíčů pro ochranu dat používala cestu UNC. Ve výchozím nastavení nejsou klíče ochrany dat šifrovány. Ujistěte se, že oprávnění k souborům pro sdílenou síť ovou složku jsou omezena na účet Windows, pod kterým aplikace běží. Certifikát X509 lze použít k ochraně klíčů v klidovém stavu. Zvažte mechanismus, který uživatelům umožní nahrávat certifikáty: Umístěte certifikáty do úložiště důvěryhodných certifikátů uživatele a ujistěte se, že jsou k dispozici na všech počítačích, kde je spuštěna aplikace uživatele. Podrobnosti [najdete v tématu Konfigurace ochrany základních dat ASP.NET.](xref:security/data-protection/configuration/overview)

* **Konfigurace fondu aplikací služby IIS pro načtení profilu uživatele**

  Toto nastavení je v části **Model procesu** v části **Upřesnit nastavení** pro fond aplikací. Nastavte **profil načtení uživatele** na `True`. Pokud je `True`nastavena možnost , jsou klíče uloženy v adresáři profilu uživatele a chráněny pomocí rozhraní DPAPI klíčem specifickým pro uživatelský účet. Klíče jsou uloženy ve složce *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys.*

  [Atribut setProfileEnvironment](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) fondu aplikací musí být také povolen. Výchozí hodnota `setProfileEnvironment` je `true`. V některých scénářích (například `setProfileEnvironment` operační `false`systém Windows) je nastavena na . Pokud klíče nejsou uloženy v adresáři profilu uživatele podle očekávání:

  1. Přejděte do složky *%windir%/system32/inetsrv/config.*
  1. Otevřete soubor *applicationHost.config.*
  1. Vyhledejte element `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>`.
  1. Zkontrolujte, `setProfileEnvironment` zda atribut není k dispozici, `true`což je výchozí hodnota na `true`, nebo explicitně nastavte hodnotu atributu na .

* **Použití systému souborů jako úložiště kroužků na klíče**

  Upravte kód aplikace tak, aby [používal systém souborů jako úložiště kroužků klíčů](xref:security/data-protection/configuration/overview). K ochraně kroužku na klíče a zajištění, že certifikát je důvěryhodný, použijte certifikát X509. Pokud je certifikát podepsaný svým držitelem, umístěte jej do úložiště Důvěryhodného kořenového adresáře.

  Při použití služby IIS ve webové farmě:

  * Použijte sdílenou složku, ke které mají přístup všechny počítače.
  * Nasazení certifikátu X509 do každého počítače. Konfigurace [ochrany dat v kódu](xref:security/data-protection/configuration/overview).

* **Nastavení celopočítačových zásad pro ochranu dat**

  Systém ochrany dat má omezenou podporu pro nastavení výchozí [zásady celého počítače](xref:security/data-protection/configuration/machine-wide-policy) pro všechny aplikace, které spotřebovávají data protection API. Další informace naleznete v tématu <xref:security/data-protection/introduction>.

## <a name="virtual-directories"></a>Virtuální adresáře

[Virtuální adresáře služby IIS](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) nejsou podporovány aplikacemi ASP.NET Core. Aplikace může být hostována jako [podaplikace](#sub-applications).

## <a name="sub-applications"></a>Dílčí aplikace

Aplikaci ASP.NET Core lze hostovat jako [podaplikaci služby IIS (podaplikaci).](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications) Cesta podaplikace se stane součástí adresy URL kořenové aplikace.

Statické odkazy datových zdrojů v rámci podaplikace`~/`by měly používat zápis tildového lomítka ( ). Zápis tildového lomítka aktivuje [pomocníka tagu,](xref:mvc/views/tag-helpers/intro) který předřává základnu cesty podaplikace k vykreslenérelativní vazbě. Pro podaplikaci `/subapp_path`na adrese , `src="~/image.png"` obrázek `src="/subapp_path/image.png"`spojený s je vykreslen jako . Middleware statického souboru kořenové aplikace nezpracovává požadavek na statický soubor. Požadavek je zpracován middlewarem statického souboru podaplikace.

Pokud je atribut `src` statického datového zdroje nastaven na `src="/image.png"`absolutní cestu (například), odkaz se vykreslí bez vytvoření cesty podaplikace. Middleware statického souboru kořenové aplikace se pokouší obsluhovat datový zdroj z [kořenového kořenového kořenového adresáře](xref:fundamentals/index#web-root)aplikace , což má za následek odpověď *404 - Not Found,* pokud není statický datový zdroj k dispozici z kořenové aplikace.

Hostování aplikace ASP.NET Core jako podaplikace pod jinou aplikací ASP.NET Core:

1. Vytvořte fond aplikací pro podaplikaci. Nastavte **verzi .NET CLR** na **žádný spravovaný kód,** protože prostředí Core Common Language Runtime (CoreCLR) pro .NET Core je spuštěno pro hostování aplikace v pracovním procesu, nikoli na ploše CLR (.NET CLR).

1. Přidejte kořenový web ve Správci služby IIS s podaplikací ve složce pod kořenovým webem.

1. Klikněte pravým tlačítkem myši na složku podaplikace ve Správci služby IIS a vyberte **příkaz Převést na aplikaci**.

1. V dialogovém okně **Přidat aplikaci** přiřaďte pomocí tlačítka **Vybrat** pro **fond aplikací** fond aplikací, který jste vytvořili pro podaplikaci. Vyberte **OK**.

Přiřazení samostatného fondu aplikací do podaplikace je požadavek při použití modelu hostování v procesu.

Další informace o modelu hostování v procesu a konfiguraci <xref:host-and-deploy/aspnet-core-module>ASP.NET core modulu naleznete v tématu .

## <a name="configuration-of-iis-with-webconfig"></a>Konfigurace služby IIS pomocí souboru web.config

Konfigurace služby IIS `<system.webServer>` je ovlivněna částí *web.config* pro scénáře služby IIS, které jsou funkční pro ASP.NET aplikace Core pomocí ASP.NET core modulem. Například konfigurace služby IIS je funkční pro dynamickou kompresi. Pokud je služba IIS nakonfigurována na `<urlCompression>` úrovni serveru pro použití dynamické komprese, prvek v souboru *web.config* aplikace jej může zakázat pro aplikaci ASP.NET Core.

Další informace najdete v následujících tématech:

* [Odkaz na \<konfiguraci pro>system.webServer](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

Chcete-li nastavit proměnné prostředí pro jednotlivé aplikace spuštěné v izolovaných fondech aplikací (podporované pro službu IIS 10.0 nebo novější), přečtěte si část *příkazu AppCmd.exe* v tématu [Environment Variables environmentvariables>\<](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) tématu v referenční dokumentaci služby IIS.

## <a name="configuration-sections-of-webconfig"></a>Konfigurační části web.config

Části konfigurace aplikací ASP.NET 4.x v *souboru web.config* nepoužívají aplikace ASP.NET Core pro konfiguraci:

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

ASP.NET aplikace Core jsou konfigurovány pomocí jiných poskytovatelů konfigurace. Další informace naleznete v [tématu Konfigurace](xref:fundamentals/configuration/index).

## <a name="application-pools"></a>Fondy aplikací

Izolace fondu aplikací je určena modelem hostování:

* V průběhu &ndash; procesu hosting ové aplikace jsou nutné ke spuštění v samostatných fondech aplikací.
* Mimoprocesový hosting &ndash; Doporučujeme izolovat aplikace od sebe navzájem spuštěním každé aplikace ve vlastním fondu aplikací.

Dialogové okno **IIS Přidat web** se ve výchozím nastavení používá do jednoho fondu aplikací na aplikaci. Když je k dispozici **název webu,** text se automaticky přenese do textového pole **Fondu aplikací.** Při přidání webu se vytvoří nový fond aplikací s názvem webu.

## <a name="application-pool-identity"></a>Identita fondu aplikací

Účet identity fondu aplikací umožňuje aplikaci spouštět pod jedinečným účtem, aniž by bylo třeba vytvářet a spravovat domény nebo místní účty. Ve službě IIS 8.0 nebo novější vytvoří pracovní proces správce služby IIS (WAS) virtuální účet s názvem nového fondu aplikací a ve výchozím nastavení spustí pracovní procesy fondu aplikací v rámci tohoto účtu. V konzole pro správu služby IIS v části **Upřesnit nastavení** fondu aplikací zkontrolujte, zda je **identita** nastavená na použití **applicationpoolidentity**:

![Dialogové okno Upřesnit nastavení fondu aplikací](index/_static/apppool-identity.png)

Proces správy služby IIS vytvoří zabezpečený identifikátor s názvem fondu aplikací v systému zabezpečení systému Windows. Prostředky lze zabezpečit pomocí této identity. Tato identita však není reálný uživatelský účet a nezobrazuje se v konzole pro správu uživatelů systému Windows.

Pokud pracovní proces služby IIS vyžaduje zvýšený přístup k aplikaci, upravte seznam řízení přístupu (ACL) pro adresář obsahující aplikaci:

1. Sem otevřete Průzkumníka Windows a přejděte do adresáře.

1. Klepněte pravým tlačítkem myši na adresář a vyberte příkaz **Vlastnosti**.

1. Na kartě **Zabezpečení** vyberte tlačítko **Upravit** a pak **tlačítko Přidat.**

1. Vyberte tlačítko **Umístění** a ujistěte se, že je vybrán systém.

1. Zadejte **\\<<iIS app_pool_name>** **zadejte názvy objektů, které chcete vybrat.** Vyberte tlačítko **Zkontrolovat jména.** Pro *DefaultAppPool* zkontrolujte názvy pomocí **IIS AppPool\DefaultAppPool**. Když je vybráno tlačítko **Zkontrolovat názvy,** je v oblasti názvů objektů uvedena hodnota **DefaultAppPool.** Název fondu aplikací není možné zadat přímo do oblasti názvů objektů. Při kontrole názvu objektu použijte<<app_pool_name>formátu **IIS AppPool.\\ **

   ![Dialogové okno Vybrat uživatele nebo skupiny pro složku aplikace: Název fondu aplikací "DefaultAppPool" se připojí k "IIS AppPool\" v oblasti názvů objektů před výběrem možnosti Zkontrolovat názvy".](index/_static/select-users-or-groups-1.png)

1. Vyberte **OK**.

   ![Dialogové okno Vybrat uživatele nebo skupiny pro složku aplikace: Po výběru možnosti Zkontrolovat názvy se v oblasti názvů objektů zobrazí název objektu DefaultAppPool.](index/_static/select-users-or-groups-2.png)

1. Oprávnění &amp; ke spuštění čtení by měla být udělena ve výchozím nastavení. Podle potřeby zadejte další oprávnění.

Přístup lze také udělit na příkazovém řádku pomocí nástroje **ICACLS.** Jako příklad použijete *defaultapppool,* který používá následující příkaz:

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

Další informace naleznete v tématu [icacls.](/windows-server/administration/windows-commands/icacls)

## <a name="http2-support"></a>Podpora HTTP/2

[Protokol HTTP/2](https://httpwg.org/specs/rfc7540.html) je podporován ASP.NET jádrem v následujících scénářích nasazení služby IIS:

* Neprocesové
  * Windows Server 2016/Windows 10 nebo novější; IIS 10 nebo novější
  * Připojení TLS 1.2 nebo novější
* Mimo proces
  * Windows Server 2016/Windows 10 nebo novější; IIS 10 nebo novější
  * Připojení serveru edge směřující mj. [Kestrel server](xref:fundamentals/servers/kestrel)
  * Připojení TLS 1.2 nebo novější

Pro nasazení v procesu při navázání připojení HTTP/2 `HTTP/2`protokol [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) hlásí . Pro mimoprocesové nasazení při navázání připojení HTTP/2 hlásí protokol `HTTP/1.1` [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) .

Další informace o modelech hostování v procesu a <xref:host-and-deploy/aspnet-core-module>mimo proces naleznete v tématu .

Protokol HTTP/2 je ve výchozím nastavení povolen. Připojení se vrátí na HTTP/1.1, pokud není navázáno připojení HTTP/2. Další informace o konfiguraci protokolu HTTP/2 s nasazením služby IIS naleznete [v tématu HTTP/2 ve službě IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).

## <a name="cors-preflight-requests"></a>Požadavky cors kontroly před výstupem

*Tato část se vztahuje pouze na ASP.NET základní aplikace, které se zaměřují na rozhraní .NET Framework.*

Pro aplikaci ASP.NET Core, která cílí na rozhraní .NET Framework, nejsou požadavky OPTIONS ve výchozím nastavení ve službách IIS předány do aplikace. Informace o konfiguraci obslužných rutin služby IIS aplikace v *souboru web.config* tak, aby předávály požadavky OPTIONS, naleznete v tématu [Povolení požadavků napříč původy v ASP.NET webovém rozhraní API 2: Jak funguje cors](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).

## <a name="application-initialization-module-and-idle-timeout"></a>Modul inicializace aplikace a časový limit nečinnosti

Pokud je ve správě ve správě iis hostován ASP.NET základnímodul verze 2:

* [Aplikace Inicializační modul](#application-initialization-module) &ndash; aplikace hostované [v procesu](#in-process-hosting-model) nebo [mimo proces](#out-of-process-hosting-model) lze nakonfigurovat tak, aby se automaticky spouštěla při restartování pracovního procesu nebo restartování serveru.
* [Nevyužitý časový limit](#idle-timeout) &ndash; aplikace hostované [v procesu](#in-process-hosting-model) lze nakonfigurovat tak, aby časový limit během období nečinnosti.

### <a name="application-initialization-module"></a>Inicializační modul aplikace

*Platí pro aplikace hostované v průběhu procesu a mimo proces.*

[Inicializace aplikace služby IIS](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) je funkce služby IIS, která odesílá požadavek HTTP do aplikace při spuštění nebo recyklaci fondu aplikací. Požadavek spustí spuštění aplikace. Ve výchozím nastavení služba IIS vydá požadavek`/`na kořenovou adresu URL aplikace ( ) o inicializaci aplikace (další podrobnosti o konfiguraci najdete v [dalších zdrojích](#application-initialization-module-and-idle-timeout-additional-resources) informací).

Zkontrolujte, zda je funkce role Inicializace aplikace služby IIS povolena:

V desktopových systémech se systémem Windows 7 nebo novějších při místním používání služby IIS:

1. Přejděte na **Ovládací panely** > **Programy** > **a funkce** > **Zapněte nebo vypněte funkce systému Windows** (levá strana obrazovky).
1. Otevřete funkce **vývoje aplikací** **internetových informačních služeb** > **World Wide Web Services** > .
1. Zaškrtněte políčko pro **inicializaci aplikace**.

V systému Windows Server 2008 R2 nebo novějším:

1. Otevřete **Průvodce přidáním rolí a funkcí**.
1. V panelu **Vybrat služby rolí** otevřete uzel **Vývoj aplikací.**
1. Zaškrtněte políčko pro **inicializaci aplikace**.

K povolení modulu inicializace aplikace pro lokalitu použijte některý z následujících přístupů:

* Pomocí Správce služby IIS:

  1. V panelu **Připojení** vyberte **fondy aplikací.**
  1. Klikněte pravým tlačítkem myši na fond aplikací v seznamu a vyberte **Upřesnit nastavení**.
  1. Výchozí **režim spuštění** je **OnDemand**. Nastavte **režim spuštění** na **vždy spuštěný**. Vyberte **OK**.
  1. Otevřete uzel **Weby** v panelu **Připojení.**
  1. Klikněte pravým tlačítkem myši na aplikaci a vyberte Spravovat **upřesnit nastavení** **webu** > .
  1. Výchozí nastavení **Preload Enabled** je **False**. Nastavte **předběžné načtení povoleno** na **hodnotu True**. Vyberte **OK**.

* Pomocí `<applicationInitialization>` `doAppInitAfterRestart` *souboru web.config*přidejte `true` prvek `<system.webServer>` s nastavenou na do prvků v souboru *web.config* aplikace:

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

*Platí pouze pro aplikace hostované v procesu.*

Chcete-li aplikaci zabránit v volnoběhu, nastavte časový limit nečinnosti fondu aplikací pomocí Správce služby IIS:

1. V panelu **Připojení** vyberte **fondy aplikací.**
1. Klikněte pravým tlačítkem myši na fond aplikací v seznamu a vyberte **Upřesnit nastavení**.
1. Výchozí **časový limit nečinnosti (minuty)** je **20** minut. Nastavte **časový limit nečinnosti (minuty)** na **0** (nula). Vyberte **OK**.
1. Recyklujte pracovní proces.

Chcete-li zabránit vypršení časového limitu aplikací hostovaných [mimo proces,](#out-of-process-hosting-model) použijte některý z následujících přístupů:

* Příkazping aplikace z externí služby, aby byla spuštěna.
* Pokud aplikace hostuje pouze služby na pozadí, vyhněte se hostování služby IIS a pomocí [služby Windows service hostujte aplikaci ASP.NET Core](xref:host-and-deploy/windows-service).

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a>Modul inicializace aplikace a časový limit nečinnosti další prostředky

* [Inicializace aplikace služby IIS 8.0](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* [Aplikace Inicializace \<aplikaceInicializační>](/iis/configuration/system.webserver/applicationinitialization/).
* [Zpracovat nastavení modelu \<pro proces fondu aplikacíModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).

## <a name="deployment-resources-for-iis-administrators"></a>Prostředky nasazení pro správce služby IIS

* [Dokumentace iis](/iis)
* [Začínáme se Správcem služby IIS ve službách IIS](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [Nasazení aplikace .NET Core](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a>Další zdroje

* <xref:test/troubleshoot>
* <xref:index>
* [Oficiální web služby Microsoft IIS](https://www.iis.net/)
* [Knihovna technického obsahu systému Windows Server](/windows-server/windows-server)
* [HTTP/2 ve službě IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Výukové prostředí o publikování aplikace ASP.NET Core na server <xref:tutorials/publish-to-iis>služby IIS naleznete v tématu .

[Instalace balíčku hostingu jádra .NET](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a>Podporované operační systémy

Podporovány jsou následující operační systémy:

* Windows 7 nebo novější
* Windows Server 2008 R2 nebo novější

[Server HTTP.sys](xref:fundamentals/servers/httpsys) (dříve nazývaný WebListener) nefunguje v konfiguraci reverzního proxy serveru se službou IIS. Použijte [server Kestrel](xref:fundamentals/servers/kestrel).

Informace o hostování v <xref:host-and-deploy/azure-apps/index>Azure najdete v tématu .

Pokyny pro řešení <xref:test/troubleshoot>potíží naleznete v tématu .

## <a name="supported-platforms"></a>Podporované platformy

Podporovány jsou aplikace publikované pro 32bitové (x86) nebo 64bitové (x64) nasazení. Nasazení 32bitové aplikace s 32bitovou sadou .NET Core SDK, pokud ji ne:

* Vyžaduje větší adresní prostor virtuální paměti, který je k dispozici pro 64bitovou aplikaci.
* Vyžaduje větší velikost zásobníku iis.
* Má 64bitové nativní závislosti.

K publikování 64bitové sady SDK jádra (x64) použijte 64bitovou sadu .NET Core SDK. V hostitelském systému musí být k dispozici 64bitový běhový čas.

## <a name="hosting-models"></a>Modely hostingu

### <a name="in-process-hosting-model"></a>Model hostování v procesu

Pomocí hostování v rámci procesu běží aplikace ASP.NET Core ve stejném procesu jako pracovní proces služby IIS. Hostování v procesu poskytuje lepší výkon než mimoproceshosting, protože požadavky nejsou proxied přes adaptér zpětné smyčky, síťové rozhraní, které vrací odchozí síťový provoz zpět do stejného počítače. Služba IIS zpracovává správu procesů pomocí [služby aktivace procesů systému Windows (WAS).](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)

Základní [modul ASP.NET](xref:host-and-deploy/aspnet-core-module):

* Provede inicializaci aplikace.
  * Načte [CoreCLR](/dotnet/standard/glossary#coreclr).
  * Volání `Program.Main`.
* Zpracovává životnost nativního požadavku služby IIS.

Model hostování v procesu není podporován pro aplikace ASP.NET Core, které cílí na rozhraní .NET Framework.

Následující diagram znázorňuje vztah mezi iis, ASP.NET základní modul a aplikace hostované v procesu:

![ASP.NET core modul ve scénáři hostování v procesu](index/_static/ancm-inprocess.png)

Požadavek přichází z webu na ovladač HTTP.sys v režimu jádra. Řidič směruje nativní požadavek do služby IIS na nakonfigurovaném portu webu, obvykle 80 (HTTP) nebo 443 (HTTPS). Základní modul ASP.NET obdrží nativní požadavek a předá`IISHttpServer`jej serveru HTTP služby IIS ( ). Server HTTP služby IIS je implementace serveru v procesu pro službu IIS, která převádí požadavek z nativního na spravovaný.

Po zpracování požadavku serverem HTTP služby IIS je požadavek odeslán do kanálu middlewaru ASP.NET Core. Middleware kanálu zpracovává požadavek a předá `HttpContext` jej jako instanci logiky aplikace. Odpověď aplikace je předána zpět do služby IIS prostřednictvím serveru HTTP služby IIS. Služba IIS odešle odpověď klientovi, který požadavek inicioval.

Hostování v procesu je přihlášení pro stávající aplikace, ale [dotnet nové](/dotnet/core/tools/dotnet-new) šablony výchozí v procesu hostování modelu pro všechny scénáře služby IIS a IIS Express.

`CreateDefaultBuilder`přidá <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instanci <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> voláním metody pro spuštění [coreclru](/dotnet/standard/glossary#coreclr) a hostování aplikace uvnitř pracovního procesu služby IIS (*w3wp.exe* nebo *iisexpress.exe*). Testy výkonu ukazují, že hostování aplikace .NET Core v procesu přináší výrazně vyšší propustnost požadavků ve srovnání s hostováním aplikací mimo proces a proxy požadavků na server [Kestrel.](xref:fundamentals/servers/kestrel)

### <a name="out-of-process-hosting-model"></a>Model hostování mimo proces

Vzhledem k tomu, že aplikace ASP.NET Core běží v procesu odděleném od pracovního procesu služby IIS, zpracovává ASP.NET core module správu procesů. Modul spustí proces pro aplikaci ASP.NET Core při příchodu prvního požadavku a restartuje aplikaci, pokud se vypne nebo dojde k chybě. Toto je v podstatě stejné chování jako u aplikací, které běží v procesu, které jsou spravovány [službou aktivace procesu systému Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

Následující diagram znázorňuje vztah mezi iis, ASP.NET základní modul a aplikace hostované mimo proces:

![ASP.NET základní modul ve scénáři mimoproceshostingového hostingu](index/_static/ancm-outofprocess.png)

Požadavky dorazí z webu do ovladače HTTP.sys v režimu jádra. Řidič směruje požadavky do služby IIS na nakonfigurovaném portu webu, obvykle 80 (HTTP) nebo 443 (HTTPS). Modul předá požadavky Kestrel na náhodný port pro aplikaci, která není port 80 nebo 443.

Modul určuje port prostřednictvím proměnné prostředí při <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> spuštění a rozšíření konfiguruje server pro naslouchání . `http://localhost:{PORT}` Jsou prováděny další kontroly a požadavky, které nepocházejí z modulu, jsou odmítnuty. Modul nepodporuje předávání https, takže požadavky jsou předávány přes HTTP i v případě, že přijaté službou IIS přes protokol HTTPS.

Poté, co Kestrel vyzvedne požadavek z modulu, je požadavek posunut do kanálu ASP.NET middleware. Middleware kanálu zpracovává požadavek a předá `HttpContext` jej jako instanci logiky aplikace. Middleware přidané integrací služby IIS aktualizuje schéma, vzdálenou IP adresu a pathbase tak, aby odpovídaly předání požadavku ke společnosti Kestrel. Odpověď aplikace je předána zpět do služby IIS, která ji odešle zpět do klienta HTTP, který inicioval požadavek.

Pokyny ke konfiguraci ASP.NET <xref:host-and-deploy/aspnet-core-module>core modulem naleznete v tématu .

Další informace o hostování najdete [v tématu Host in ASP.NET Core](xref:fundamentals/index#host).

## <a name="application-configuration"></a>Konfigurace aplikací

### <a name="enable-the-iisintegration-components"></a>Povolení součástí Integrace iIS

Při vytváření hostitele `CreateWebHostBuilder` v *(Program.cs*) volání <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> umožňující integraci iis:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

Další informace `CreateDefaultBuilder`naleznete <xref:fundamentals/host/web-host#set-up-a-host>v tématu .

### <a name="iis-options"></a>Možnosti iis

**Model hostování v procesu**

Chcete-li konfigurovat možnosti serveru IIS, zahrňte konfiguraci služby v <xref:Microsoft.AspNetCore.Builder.IISServerOptions> aplikaci <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>. Následující příklad zakáže automatické ověřování:

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| Možnost                         | Výchozí | Nastavení |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | Pokud `true`server IIS `HttpContext.User` nastaví ověřené ověřování [systému Windows](xref:security/authentication/windowsauth). Pokud `false`server poskytuje identitu pouze `HttpContext.User` pro výzvy a reaguje na `AuthenticationScheme`ně, pokud o to výslovně požádá . Aby bylo možné službu `AutomaticAuthentication` IIS fungovat, musí být ve službě IIS povoleno ověřování systému Windows. Další informace naleznete v tématu [Ověřování systému Windows](xref:security/authentication/windowsauth). |
| `AuthenticationDisplayName`    | `null`  | Nastaví zobrazované jméno zobrazené uživatelům na přihlašovacích stránkách. |

**Model hostování mimo proces**

Chcete-li konfigurovat možnosti služby <xref:Microsoft.AspNetCore.Builder.IISOptions> <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>IIS, zahrňte konfiguraci služby v aplikaci . Následující příklad zabrání aplikaci v naplnění `HttpContext.Connection.ClientCertificate`:

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| Možnost                         | Výchozí | Nastavení |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | Pokud `true` [middleware integrace služby IIS](#enable-the-iisintegration-components) nastaví ověřené ověřování systému `HttpContext.User` [Windows](xref:security/authentication/windowsauth). Pokud `false`middleware poskytuje identitu `HttpContext.User` a reaguje na problémy pouze na `AuthenticationScheme`žádost . Aby bylo možné službu `AutomaticAuthentication` IIS fungovat, musí být ve službě IIS povoleno ověřování systému Windows. Další informace naleznete v tématu [Ověřování systému Windows.](xref:security/authentication/windowsauth) |
| `AuthenticationDisplayName`    | `null`  | Nastaví zobrazované jméno zobrazené uživatelům na přihlašovacích stránkách. |
| `ForwardClientCertificate`     | `true`  | Pokud `true` a `MS-ASPNETCORE-CLIENTCERT` hlavička požadavku `HttpContext.Connection.ClientCertificate` je k dispozici, je naplněn. |

### <a name="proxy-server-and-load-balancer-scenarios"></a>Scénáře proxy serveru a vyrovnávání zatížení

[Middleware integrace služby IIS](#enable-the-iisintegration-components), který konfiguruje middleware předávaných záhlaví a ASP.NET základní modul, jsou nakonfigurovány tak, aby předávaly schéma (HTTP/HTTPS) a vzdálenou adresu IP, ze které požadavek pochází. Další konfigurace může být vyžadována pro aplikace hostované za dalšími proxy servery a nástroji pro vyrovnávání zatížení. Další informace naleznete v [tématu Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).

### <a name="webconfig-file"></a>soubor web.config

Soubor *web.config* konfiguruje [základní modul ASP.NET](xref:host-and-deploy/aspnet-core-module). Při publikování projektu je vytváření, transformace a publikování souboru *web.config* zpracováno cílem MSBuild (`_TransformWebConfig`). Tento cíl je k dispozici v`Microsoft.NET.Sdk.Web`cílech sady Web SDK ( ). Sada SDK je nastavena v horní části souboru projektu:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Pokud soubor *web.config* není v projektu přítomen, je soubor vytvořen se správnou *processPath* a *argumenty* pro konfiguraci ASP.NET základnímodul a přesunutna do [publikovaného výstupu](xref:host-and-deploy/directory-structure).

Pokud *web.config* soubor je přítomen v projektu, soubor je transformován se správným *processPath* a *argumenty* pro konfiguraci ASP.NET core modul a přesunuta do publikovaného výstupu. Transformace nezmění nastavení konfigurace služby IIS v souboru.

Soubor *web.config* může poskytovat další nastavení konfigurace služby IIS, která řídí aktivní moduly služby IIS. Informace o modulech služby IIS, které jsou schopné zpracovávat požadavky pomocí aplikací ASP.NET Core, naleznete v tématu [modulů služby IIS.](xref:host-and-deploy/iis/modules)

Chcete-li zabránit webové sady SDK v transformaci souboru *web.config,* použijte vlastnost ** \<IsTransformWebConfigDisabled>** v souboru projektu:

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

Při zakázání webové sady SDK z transformace souboru *processPath* a argumenty by měly být ručně *nastaveny* vývojářem. Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module>.

### <a name="webconfig-file-location"></a>umístění souboru web.config

Aby bylo možné správně nastavit [ASP.NET core modul,](xref:host-and-deploy/aspnet-core-module) musí být soubor *web.config* přítomen na [cestě kořenového obsahu](xref:fundamentals/index#content-root) (obvykle základní cesta aplikace) nasazené aplikace. Jedná se o stejné umístění jako fyzická cesta k webu poskytnutá službu IIS. Soubor *web.config* je vyžadován v kořenovém adresáři aplikace, aby bylo možné publikovat více aplikací pomocí nasazení webu.

Na fyzické cestě aplikace existují citlivé soubory, například * \<assembly>.runtimeconfig.json*, * \<assembly>.xml* (komentáře xml documentation) a * \<assembly>.deps.json*. Pokud je soubor *web.config* přítomen a web se spustí normálně, služby IIS tyto citlivé soubory neobsluhuje, pokud jsou požadovány. Pokud soubor *web.config* chybí, je nesprávně pojmenován nebo jej nelze nakonfigurovat pro normální spuštění, může služba IIS veřejně sloužit citlivým souborům.

**Soubor *web.config* musí být v nasazení vždy přítomen, správně pojmenovaný a musí být schopen nakonfigurovat web pro normální spuštění. Nikdy neodstraňujte soubor *web.config* z produkčního nasazení.**

### <a name="transform-webconfig"></a>Transformace souboru web.config

Pokud potřebujete transformovat *web.config* při publikování (například nastavit proměnné prostředí na základě konfigurace, profilu nebo prostředí), přečtěte si viz <xref:host-and-deploy/iis/transform-webconfig>.

## <a name="iis-configuration"></a>Konfigurace služby IIS

**Operační systémy Windows Server**

Povolte roli serveru **webového serveru (IIS)** a vytvořte služby rolí.

1. Použijte Průvodce **přidáním rolí a funkcí** z nabídky **Spravovat** nebo odkaz ve **Správci serveru**. V kroku **Role serveru** zaškrtněte políčko **U webového serveru (IIS).**

   ![Role služby IIS webového serveru je vybrána v kroku Vybrat role serveru.](index/_static/server-roles-ws2016.png)

1. Po kroku **Funkce** se krok **služby role** načte pro webový server (IIS). Vyberte požadované služby rolí služby IIS nebo přijměte výchozí služby rolí, které jsou k dispozici.

   ![Výchozí služby rolí jsou vybrány v kroku Vybrat služby rolí.](index/_static/role-services-ws2016.png)

   **Ověřování systému Windows (volitelné)**  
   Chcete-li povolit ověřování systému Windows, rozbalte následující uzly:**Zabezpečení** **webového serveru** > . Vyberte funkci **Ověřování systému Windows.** Další informace naleznete v [tématech Ověřování \<systému Windows windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) a Konfigurace ověřování [systému Windows](xref:security/authentication/windowsauth).

   **WebSockets (volitelné)**  
   WebSockets je podporován ASP.NET jádrem 1.1 nebo novějším. Chcete-li povolit rozhraní WebSockets, rozbalte následující uzly:**Vývoj aplikací webového** **serveru** > . Vyberte funkci **WebSocket Protocol.** Další informace naleznete v tématu [WebSockets](xref:fundamentals/websockets).

1. Pokračujte v **potvrzovacím** kroku a nainstalujte roli a služby webového serveru. Po instalaci role **webového serveru (IIS)** není restartování serveru nebo služby IIS vyžadováno.

**Desktopové operační systémy Windows**

Povolte **konzolu pro správu služby IIS** a **služby World Wide Web Services**.

1. Přejděte na **Ovládací panely** > **Programy** > **a funkce** > **Zapněte nebo vypněte funkce systému Windows** (levá strana obrazovky).

1. Otevřete uzel **Internetové informační služby.** Otevřete uzel **Nástroje pro správu webu.**

1. Zaškrtněte políčko **U konzole pro správu služby IIS**.

1. Zaškrtněte políčko **U služeb World Wide Web Services**.

1. Přijměte výchozí funkce **pro služby World Wide Web Services** nebo upravte funkce služby IIS.

   **Ověřování systému Windows (volitelné)**  
   Chcete-li povolit ověřování systému Windows, rozbalte následující uzly: **World Wide Web Services** > **Security**. Vyberte funkci **Ověřování systému Windows.** Další informace naleznete v [tématech Ověřování \<systému Windows windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) a Konfigurace ověřování [systému Windows](xref:security/authentication/windowsauth).

   **WebSockets (volitelné)**  
   WebSockets je podporován ASP.NET jádrem 1.1 nebo novějším. Chcete-li povolit rozhraní WebSockets, rozbalte následující uzly: Funkce**vývoje aplikací**pro **webové služby** > . Vyberte funkci **WebSocket Protocol.** Další informace naleznete v tématu [WebSockets](xref:fundamentals/websockets).

1. Pokud instalace služby IIS vyžaduje restartování, restartujte systém.

![V funkcích systému Windows jsou vybrány konzoly IIS Management Console a služby World Wide Web Services.](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a>Instalace balíčku hostingu jádra .NET

Nainstalujte *balíček .NET Core Hosting do* hostitelského systému. Balíček nainstaluje modul .NET Core Runtime, knihovnu .NET Core Library a [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module). Modul umožňuje ASP.NET aplikace Core běžet za službou IIS.

> [!IMPORTANT]
> Pokud je sada Hosting Bundle nainstalována před službou IIS, musí být instalace balíčku opravena. Po instalaci služby IIS znovu spusťte instalační program sady Hosting Bundle.
>
> Pokud je sada Hosting Bundle nainstalována po instalaci 64bitové (x64) verze rozhraní .NET Core, mohou se zdát, že sady SDK chybí ([byly zjištěny sady SDK .Net Core .](xref:test/troubleshoot#no-net-core-sdks-were-detected) Chcete-li problém <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>vyřešit, naleznete v tématu .

### <a name="direct-download-current-version"></a>Přímé stahování (aktuální verze)

Stáhněte si instalační program pomocí následujícího odkazu:

[Aktuální instalační program .NET Core Hosting Bundle (přímé stahování)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a>Dřívější verze instalačního programu

Chcete-li získat starší verzi instalačního programu:

1. Přejděte na stránku [Stáhnout jádro rozhraní .NET.](https://dotnet.microsoft.com/download/dotnet-core)
1. Klikněte na požadovanou verzi .NET Core.
1. Ve sloupci **Spustit aplikace – runtime** najděte požadovaný řádek požadované verze runtime .NET Core.
1. Stáhněte si instalační program pomocí odkazu **Runtime & Hosting Bundle.**

> [!WARNING]
> Některé instalační programy obsahují verze, které dosáhly konce životnosti (EOL) a které již nejsou podporovány společností Microsoft. Další informace naleznete v [zásadách podpory](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

### <a name="install-the-hosting-bundle"></a>Nainstalujte balíček hostingu

1. Spusťte instalační program na serveru. Při spuštění instalačního programu z příkazového prostředí správce jsou k dispozici následující parametry:

   * `OPT_NO_ANCM=1`&ndash; Přeskočte instalaci ASP.NET základního modulu.
   * `OPT_NO_RUNTIME=1`&ndash; Přeskočte instalaci runtime jádra .NET. Používá se v případě, že server hostuje pouze [samostatná nasazení (SCD).](/dotnet/core/deploying/#self-contained-deployments-scd)
   * `OPT_NO_SHAREDFX=1`&ndash; Přeskočit instalaci ASP.NET sdíleného rozhraní (ASP.NET runtime). Používá se v případě, že server hostuje pouze [samostatná nasazení (SCD).](/dotnet/core/deploying/#self-contained-deployments-scd)
   * `OPT_NO_X86=1`&ndash; Přeskočit instalaci za běhu x86. Tento parametr použijte, pokud víte, že nebudete hostovat 32bitové aplikace. Pokud existuje nějaká šance, že budete v budoucnu hostovat 32bitové i 64bitové aplikace, nepoužívejte tento parametr a nainstalujte oba runtimes.
   * `OPT_NO_SHARED_CONFIG_CHECK=1`&ndash; Zakažte kontrolu použití sdílené konfigurace služby IIS, pokud je sdílená konfigurace *(applicationHost.config)* ve stejném počítači jako instalace služby IIS. *K dispozici pouze pro ASP.NET instalačních programů hostingu Bundler nebo novějším.* Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.
1. Restartujte systém nebo proveďte následující příkazy v příkazovém prostředí:

   ```console
   net stop was /y
   net start w3svc
   ```
   Restartování služby IIS vyvolá změnu systémové cesty, což je proměnná prostředí provedená instalačním programem.

Při instalaci balíčku Hosting Bundle není nutné ručně zastavovat jednotlivé weby ve službách IIS. Hostované aplikace (weby služby IIS) se restartují při restartování služby IIS. Aplikace se znovu spustí, když obdrží svůj první požadavek, včetně z [modulu inicializace aplikace](#application-initialization-module-and-idle-timeout).

ASP.NET Core přijímá roll-forward chování pro opravy vydání sdílených rámců. Když se aplikace hostované službou IIS restartují se službou IIS, aplikace se načítají nejnovějšími verzemi oprav svých odkazovaných balíčků, když obdrží svůj první požadavek. Pokud služba IIS není restartována, aplikace se restartují a vykazují chování pro předávání vpřed, když jsou jejich pracovní procesy recyklovány a obdrží první požadavek.

> [!NOTE]
> Informace o sdílené konfiguraci služby IIS naleznete [v tématu ASP.NET Core Module se sdílenou konfigurací služby IIS](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a>Instalace nasazení webu při publikování pomocí sady Visual Studio

Při nasazování aplikací na servery s [nasazením webu](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)nainstalujte na server nejnovější verzi nasazení webu. Chcete-li nainstalovat nasazení webu, použijte [Instalační službu webové platformy (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) nebo získejte instalační program přímo ze [služby Stažení softwaru](https://www.microsoft.com/download/details.aspx?id=43717). Upřednostňovanou metodou je použití webpi. WebPI nabízí samostatné nastavení a konfiguraci pro poskytovatele hostingu.

## <a name="create-the-iis-site"></a>Vytvoření webu iis

1. V hostitelském systému vytvořte složku, která bude obsahovat publikované složky a soubory aplikace. V následujícím kroku je cesta ke složce k dispozici službu IIS jako fyzická cesta k aplikaci. Další informace o složce nasazení aplikace a <xref:host-and-deploy/directory-structure>rozložení souborů naleznete v tématu .

1. Ve Správci služby IIS otevřete uzel serveru v panelu **Připojení.** Klikněte pravým tlačítkem myši na složku **Weby.** V kontextové nabídce vyberte **Přidat web.**

1. Zadejte **název webu** a nastavte **fyzickou cestu** ke složce nasazení aplikace. Zadejte konfiguraci **vazby** a vytvořte web výběrem **možnosti OK**:

   ![V kroku Přidat web zadejte název webu, fyzickou cestu a název hostitele.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > Vazby se zástupnými`http://*:80/` symboly nejvyšší úrovně ( a `http://+:80`) by **neměly** být používány. Vazby se zástupnými symboly nejvyšší úrovně mohou otevřít vaši aplikaci slabá místa zabezpečení. To platí pro silné i slabé zástupné znaky. Používejte explicitní názvy hostitelů místo zástupných znaků. Vazba se zástupnými znaky subdomény (například) nemá toto bezpečnostní riziko, `*.mysub.com` `*.com`pokud řídíte celou nadřazenou doménu (na rozdíl od , která je zranitelná). Viz [rfc7230 sekce-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) pro více informací.

1. Pod uzlovým serverem vyberte **fondy aplikací**.

1. Klikněte pravým tlačítkem myši na fond aplikací webu a v kontextové nabídce vyberte **Základní nastavení.**

1. V okně **Upravit fond aplikací** nastavte verzi **.NET CLR** na **Žádný spravovaný kód**:

   ![Pro verzi CLR rozhraní .NET nenastavte žádný spravovaný kód.](index/_static/edit-apppool-ws2016.png)

    ASP.NET Core běží v samostatném procesu a spravuje runtime. ASP.NET Core nespoléhá na načtení plochy CLR (.NET CLR)&mdash;Core Common Language Runtime (CoreCLR) pro .NET Core je spuštěn pro hostování aplikace v pracovním procesu. Nastavení **verze .NET CLR** na **žádný spravovaný kód** je volitelné, ale doporučeno.

1. *ASP.NET jádra 2.2 nebo novějšího:* Pro 64bitové (x64) [samostatné nasazení,](/dotnet/core/deploying/#self-contained-deployments-scd) které používá [model hostování v procesu](#in-process-hosting-model), zakažte fond aplikací pro 32bitové (x86) procesy.

   V postranním panelu **Akce** správce služby IIS > **fondy aplikací**vyberte **možnost Nastavit výchozí nastavení fondu aplikací** nebo **Upřesnit nastavení**. Vyhledejte **funkci Povolit 32bitové aplikace** a nastavte hodnotu na . `False` Toto nastavení nemá vliv na aplikace nasazené pro [mimoprocesový hosting](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).

1. Potvrďte, že identita modelu procesu má správná oprávnění.

   Pokud se výchozí identita fondu aplikací **(identita****procesního modelu** > ) změní z **ApplicationPoolIdentity** na jinou identitu, ověřte, zda nová identita má požadovaná oprávnění pro přístup ke složce, databázi a dalším požadovaným prostředkům aplikace. Fond aplikací například vyžaduje přístup pro čtení a zápis do složek, kde aplikace čte a zapisuje soubory.

**Konfigurace ověřování systému Windows (volitelná)**  
Další informace naleznete v [tématu Configure Windows authentication](xref:security/authentication/windowsauth).

## <a name="deploy-the-app"></a>Nasazení aplikace

Nasaďte aplikaci do složky **Fyzické cesty** služby IIS, která byla vytvořena v části Vytvořit [web služby IIS.](#create-the-iis-site) [Nasazení webu](/iis/publish/using-web-deploy/introduction-to-web-deploy) je doporučený mechanismus pro nasazení, ale existuje několik možností pro přesunutí aplikace ze složky *publikování* projektu do složky nasazení hostitelského systému.

### <a name="web-deploy-with-visual-studio"></a>Nasazení webu pomocí sady Visual Studio

Podívejte se na [profily publikování aplikace Visual Studio pro téma nasazení aplikace ASP.NET Core,](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) kde se dozvíte, jak vytvořit profil publikování pro použití s nasazením webu. Pokud poskytovatel hostingu poskytuje profil publikování nebo podporu pro jeho vytvoření, stáhněte si jeho profil a importujte jej pomocí dialogového okna **Publikovat** sady Visual Studio:

![Stránka dialogového okna Publikovat](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a>Nasazení webu mimo Visual Studio

[Nasazení webu](/iis/publish/using-web-deploy/introduction-to-web-deploy) lze také použít mimo Visual Studio z příkazového řádku. Další informace naleznete v [tématu Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).

### <a name="alternatives-to-web-deploy"></a>Alternativy k nasazení webu

Pomocí některé z několika metod přesuňte aplikaci do hostitelského systému, jako je ruční kopírování, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy)nebo [PowerShell](/powershell/).

Další informace o ASP.NET nasazení jádra do služby IIS naleznete v části [Prostředky nasazení pro správce služby IIS.](#deployment-resources-for-iis-administrators)

## <a name="browse-the-website"></a>Procházení webu

Po nasazení aplikace do hostitelského systému požádejte o jeden z veřejných koncových bodů aplikace.

V následujícím příkladu je web vázán na název `www.mysite.com` **hostitele** iis na **portu** `80`. Žádost se podává `http://www.mysite.com`na :

![Prohlížeč Microsoft Edge načetl úvodní stránku služby IIS.](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a>Uzamčené soubory nasazení

Soubory ve složce nasazení jsou uzamčeny, když je aplikace spuštěná. Uzamčené soubory nelze během nasazení přepsat. Chcete-li uvolnit uzamčené soubory v nasazení, zastavte fond aplikací pomocí **jednoho z** následujících přístupů:

* Použijte nasazení webu `Microsoft.NET.Sdk.Web` a odkaz v souboru projektu. Soubor *app_offline.htm* je umístěn v kořenovém adresáři webové aplikace. Když je soubor k dispozici, ASP.NET základní modul řádně vypne aplikaci a slouží *souboru app_offline.htm* během nasazení. Další informace naleznete v [odkazu na konfiguraci ASP.NET core modulem](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).
* Ručně zastavit fond aplikací ve Správci služby IIS na serveru.
* Použití Prostředí PowerShell k přetažení *app_offline.htm* (vyžaduje PowerShell 5 nebo novější):

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a>Ochrana dat

Zásobník [ASP.NET Core Data Protection](xref:security/data-protection/introduction) používá několik [middlewares](xref:fundamentals/middleware/index)ASP.NET Core , včetně middlewaru používaného při ověřování. I v případě, že rozhraní API ochrany dat nejsou volána uživatelským kódem, ochrana dat by měla být nakonfigurována pomocí skriptu nasazení nebo v uživatelském kódu, aby se vytvořilo trvalé úložiště kryptografických [klíčů](xref:security/data-protection/implementation/key-management). Pokud ochrana dat není nakonfigurována, klíče jsou uloženy v paměti a po restartování aplikace zahozeny.

Pokud je kroužek klíče uložen v paměti při restartování aplikace:

* Všechny ověřovací tokeny založené na souborech cookie jsou zrušeny. 
* Uživatelé se musí znovu přihlásit při dalším požadavku. 
* Všechna data chráněná kroužkem klíče již nelze dešifrovat. To může zahrnovat [tokeny CSRF](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) a [ASP.NET základní soubory cookie MVC TempData](xref:fundamentals/app-state#tempdata).

Chcete-li nakonfigurovat ochranu dat ve službě IIS tak, aby zachycovací byl kroužek klíče, použijte **jeden** z následujících postupů:

* **Vytvoření klíčů registru ochrany dat**

  Klíče ochrany dat používané ASP.NET aplikace Core jsou uloženy v registru mimo aplikace. Chcete-li zachovat klíče pro danou aplikaci, vytvořte klíče registru pro fond aplikací.

  Pro samostatné instalace služby IIS mimo webfarmu lze [skript prostředí PowerShell Pro poskytování ochrany dat-AutoGenKeys.ps1](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) použít pro každý fond aplikací používaný s aplikací ASP.NET Core. Tento skript vytvoří klíč registru v registru HKLM, který je přístupný pouze pro účet pracovního procesu fondu aplikací. Klíče jsou šifrovány v klidovém stavu pomocí dpapi s klíčem pro celý počítač.

  Ve scénářích webové farmy lze aplikaci nakonfigurovat tak, aby k ukládání kroužku klíčů pro ochranu dat používala cestu UNC. Ve výchozím nastavení nejsou klíče ochrany dat šifrovány. Ujistěte se, že oprávnění k souborům pro sdílenou síť ovou složku jsou omezena na účet Windows, pod kterým aplikace běží. Certifikát X509 lze použít k ochraně klíčů v klidovém stavu. Zvažte mechanismus, který uživatelům umožní nahrávat certifikáty: Umístěte certifikáty do úložiště důvěryhodných certifikátů uživatele a ujistěte se, že jsou k dispozici na všech počítačích, kde je spuštěna aplikace uživatele. Podrobnosti [najdete v tématu Konfigurace ochrany základních dat ASP.NET.](xref:security/data-protection/configuration/overview)

* **Konfigurace fondu aplikací služby IIS pro načtení profilu uživatele**

  Toto nastavení je v části **Model procesu** v části **Upřesnit nastavení** pro fond aplikací. Nastavte **profil načtení uživatele** na `True`. Pokud je `True`nastavena možnost , jsou klíče uloženy v adresáři profilu uživatele a chráněny pomocí rozhraní DPAPI klíčem specifickým pro uživatelský účet. Klíče jsou uloženy ve složce *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys.*

  [Atribut setProfileEnvironment](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) fondu aplikací musí být také povolen. Výchozí hodnota `setProfileEnvironment` je `true`. V některých scénářích (například `setProfileEnvironment` operační `false`systém Windows) je nastavena na . Pokud klíče nejsou uloženy v adresáři profilu uživatele podle očekávání:

  1. Přejděte do složky *%windir%/system32/inetsrv/config.*
  1. Otevřete soubor *applicationHost.config.*
  1. Vyhledejte element `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>`.
  1. Zkontrolujte, `setProfileEnvironment` zda atribut není k dispozici, `true`což je výchozí hodnota na `true`, nebo explicitně nastavte hodnotu atributu na .

* **Použití systému souborů jako úložiště kroužků na klíče**

  Upravte kód aplikace tak, aby [používal systém souborů jako úložiště kroužků klíčů](xref:security/data-protection/configuration/overview). K ochraně kroužku na klíče a zajištění, že certifikát je důvěryhodný, použijte certifikát X509. Pokud je certifikát podepsaný svým držitelem, umístěte jej do úložiště Důvěryhodného kořenového adresáře.

  Při použití služby IIS ve webové farmě:

  * Použijte sdílenou složku, ke které mají přístup všechny počítače.
  * Nasazení certifikátu X509 do každého počítače. Konfigurace [ochrany dat v kódu](xref:security/data-protection/configuration/overview).

* **Nastavení celopočítačových zásad pro ochranu dat**

  Systém ochrany dat má omezenou podporu pro nastavení výchozí [zásady celého počítače](xref:security/data-protection/configuration/machine-wide-policy) pro všechny aplikace, které spotřebovávají data protection API. Další informace naleznete v tématu <xref:security/data-protection/introduction>.

## <a name="virtual-directories"></a>Virtuální adresáře

[Virtuální adresáře služby IIS](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) nejsou podporovány aplikacemi ASP.NET Core. Aplikace může být hostována jako [podaplikace](#sub-applications).

## <a name="sub-applications"></a>Dílčí aplikace

Aplikaci ASP.NET Core lze hostovat jako [podaplikaci služby IIS (podaplikaci).](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications) Cesta podaplikace se stane součástí adresy URL kořenové aplikace.

Statické odkazy datových zdrojů v rámci podaplikace`~/`by měly používat zápis tildového lomítka ( ). Zápis tildového lomítka aktivuje [pomocníka tagu,](xref:mvc/views/tag-helpers/intro) který předřává základnu cesty podaplikace k vykreslenérelativní vazbě. Pro podaplikaci `/subapp_path`na adrese , `src="~/image.png"` obrázek `src="/subapp_path/image.png"`spojený s je vykreslen jako . Middleware statického souboru kořenové aplikace nezpracovává požadavek na statický soubor. Požadavek je zpracován middlewarem statického souboru podaplikace.

Pokud je atribut `src` statického datového zdroje nastaven na `src="/image.png"`absolutní cestu (například), odkaz se vykreslí bez vytvoření cesty podaplikace. Middleware statického souboru kořenové aplikace se pokouší obsluhovat datový zdroj z [kořenového kořenového kořenového adresáře](xref:fundamentals/index#web-root)aplikace , což má za následek odpověď *404 - Not Found,* pokud není statický datový zdroj k dispozici z kořenové aplikace.

Hostování aplikace ASP.NET Core jako podaplikace pod jinou aplikací ASP.NET Core:

1. Vytvořte fond aplikací pro podaplikaci. Nastavte **verzi .NET CLR** na **žádný spravovaný kód,** protože prostředí Core Common Language Runtime (CoreCLR) pro .NET Core je spuštěno pro hostování aplikace v pracovním procesu, nikoli na ploše CLR (.NET CLR).

1. Přidejte kořenový web ve Správci služby IIS s podaplikací ve složce pod kořenovým webem.

1. Klikněte pravým tlačítkem myši na složku podaplikace ve Správci služby IIS a vyberte **příkaz Převést na aplikaci**.

1. V dialogovém okně **Přidat aplikaci** přiřaďte pomocí tlačítka **Vybrat** pro **fond aplikací** fond aplikací, který jste vytvořili pro podaplikaci. Vyberte **OK**.

Přiřazení samostatného fondu aplikací do podaplikace je požadavek při použití modelu hostování v procesu.

Další informace o modelu hostování v procesu a konfiguraci <xref:host-and-deploy/aspnet-core-module>ASP.NET core modulu naleznete v tématu .

## <a name="configuration-of-iis-with-webconfig"></a>Konfigurace služby IIS pomocí souboru web.config

Konfigurace služby IIS `<system.webServer>` je ovlivněna částí *web.config* pro scénáře služby IIS, které jsou funkční pro ASP.NET aplikace Core pomocí ASP.NET core modulem. Například konfigurace služby IIS je funkční pro dynamickou kompresi. Pokud je služba IIS nakonfigurována na `<urlCompression>` úrovni serveru pro použití dynamické komprese, prvek v souboru *web.config* aplikace jej může zakázat pro aplikaci ASP.NET Core.

Další informace najdete v následujících tématech:

* [Odkaz na \<konfiguraci pro>system.webServer](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

Chcete-li nastavit proměnné prostředí pro jednotlivé aplikace spuštěné v izolovaných fondech aplikací (podporované pro službu IIS 10.0 nebo novější), přečtěte si část *příkazu AppCmd.exe* v tématu [Environment Variables environmentvariables>\<](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) tématu v referenční dokumentaci služby IIS.

## <a name="configuration-sections-of-webconfig"></a>Konfigurační části web.config

Části konfigurace aplikací ASP.NET 4.x v *souboru web.config* nepoužívají aplikace ASP.NET Core pro konfiguraci:

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

ASP.NET aplikace Core jsou konfigurovány pomocí jiných poskytovatelů konfigurace. Další informace naleznete v [tématu Konfigurace](xref:fundamentals/configuration/index).

## <a name="application-pools"></a>Fondy aplikací

Izolace fondu aplikací je určena modelem hostování:

* V průběhu &ndash; procesu hosting ové aplikace jsou nutné ke spuštění v samostatných fondech aplikací.
* Mimoprocesový hosting &ndash; Doporučujeme izolovat aplikace od sebe navzájem spuštěním každé aplikace ve vlastním fondu aplikací.

Dialogové okno **IIS Přidat web** se ve výchozím nastavení používá do jednoho fondu aplikací na aplikaci. Když je k dispozici **název webu,** text se automaticky přenese do textového pole **Fondu aplikací.** Při přidání webu se vytvoří nový fond aplikací s názvem webu.

## <a name="application-pool-identity"></a>Identita fondu aplikací

Účet identity fondu aplikací umožňuje aplikaci spouštět pod jedinečným účtem, aniž by bylo třeba vytvářet a spravovat domény nebo místní účty. Ve službě IIS 8.0 nebo novější vytvoří pracovní proces správce služby IIS (WAS) virtuální účet s názvem nového fondu aplikací a ve výchozím nastavení spustí pracovní procesy fondu aplikací v rámci tohoto účtu. V konzole pro správu služby IIS v části **Upřesnit nastavení** fondu aplikací zkontrolujte, zda je **identita** nastavená na použití **applicationpoolidentity**:

![Dialogové okno Upřesnit nastavení fondu aplikací](index/_static/apppool-identity.png)

Proces správy služby IIS vytvoří zabezpečený identifikátor s názvem fondu aplikací v systému zabezpečení systému Windows. Prostředky lze zabezpečit pomocí této identity. Tato identita však není reálný uživatelský účet a nezobrazuje se v konzole pro správu uživatelů systému Windows.

Pokud pracovní proces služby IIS vyžaduje zvýšený přístup k aplikaci, upravte seznam řízení přístupu (ACL) pro adresář obsahující aplikaci:

1. Sem otevřete Průzkumníka Windows a přejděte do adresáře.

1. Klepněte pravým tlačítkem myši na adresář a vyberte příkaz **Vlastnosti**.

1. Na kartě **Zabezpečení** vyberte tlačítko **Upravit** a pak **tlačítko Přidat.**

1. Vyberte tlačítko **Umístění** a ujistěte se, že je vybrán systém.

1. Zadejte **\\<<iIS app_pool_name>** **zadejte názvy objektů, které chcete vybrat.** Vyberte tlačítko **Zkontrolovat jména.** Pro *DefaultAppPool* zkontrolujte názvy pomocí **IIS AppPool\DefaultAppPool**. Když je vybráno tlačítko **Zkontrolovat názvy,** je v oblasti názvů objektů uvedena hodnota **DefaultAppPool.** Název fondu aplikací není možné zadat přímo do oblasti názvů objektů. Při kontrole názvu objektu použijte<<app_pool_name>formátu **IIS AppPool.\\ **

   ![Dialogové okno Vybrat uživatele nebo skupiny pro složku aplikace: Název fondu aplikací "DefaultAppPool" se připojí k "IIS AppPool\" v oblasti názvů objektů před výběrem možnosti Zkontrolovat názvy".](index/_static/select-users-or-groups-1.png)

1. Vyberte **OK**.

   ![Dialogové okno Vybrat uživatele nebo skupiny pro složku aplikace: Po výběru možnosti Zkontrolovat názvy se v oblasti názvů objektů zobrazí název objektu DefaultAppPool.](index/_static/select-users-or-groups-2.png)

1. Oprávnění &amp; ke spuštění čtení by měla být udělena ve výchozím nastavení. Podle potřeby zadejte další oprávnění.

Přístup lze také udělit na příkazovém řádku pomocí nástroje **ICACLS.** Jako příklad použijete *defaultapppool,* který používá následující příkaz:

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

Další informace naleznete v tématu [icacls.](/windows-server/administration/windows-commands/icacls)

## <a name="http2-support"></a>Podpora HTTP/2

[Protokol HTTP/2](https://httpwg.org/specs/rfc7540.html) je podporován ASP.NET jádrem v následujících scénářích nasazení služby IIS:

* Neprocesové
  * Windows Server 2016/Windows 10 nebo novější; IIS 10 nebo novější
  * Připojení TLS 1.2 nebo novější
* Mimo proces
  * Windows Server 2016/Windows 10 nebo novější; IIS 10 nebo novější
  * Připojení serveru edge směřující mj. [Kestrel server](xref:fundamentals/servers/kestrel)
  * Připojení TLS 1.2 nebo novější

Pro nasazení v procesu při navázání připojení HTTP/2 `HTTP/2`protokol [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) hlásí . Pro mimoprocesové nasazení při navázání připojení HTTP/2 hlásí protokol `HTTP/1.1` [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) .

Další informace o modelech hostování v procesu a <xref:host-and-deploy/aspnet-core-module>mimo proces naleznete v tématu .

Protokol HTTP/2 je ve výchozím nastavení povolen. Připojení se vrátí na HTTP/1.1, pokud není navázáno připojení HTTP/2. Další informace o konfiguraci protokolu HTTP/2 s nasazením služby IIS naleznete [v tématu HTTP/2 ve službě IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).

## <a name="cors-preflight-requests"></a>Požadavky cors kontroly před výstupem

*Tato část se vztahuje pouze na ASP.NET základní aplikace, které se zaměřují na rozhraní .NET Framework.*

Pro aplikaci ASP.NET Core, která cílí na rozhraní .NET Framework, nejsou požadavky OPTIONS ve výchozím nastavení ve službách IIS předány do aplikace. Informace o konfiguraci obslužných rutin služby IIS aplikace v *souboru web.config* tak, aby předávály požadavky OPTIONS, naleznete v tématu [Povolení požadavků napříč původy v ASP.NET webovém rozhraní API 2: Jak funguje cors](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).

## <a name="application-initialization-module-and-idle-timeout"></a>Modul inicializace aplikace a časový limit nečinnosti

Pokud je ve správě ve správě iis hostován ASP.NET základnímodul verze 2:

* [Aplikace Inicializační modul](#application-initialization-module) &ndash; aplikace hostované [v procesu](#in-process-hosting-model) nebo [mimo proces](#out-of-process-hosting-model) lze nakonfigurovat tak, aby se automaticky spouštěla při restartování pracovního procesu nebo restartování serveru.
* [Nevyužitý časový limit](#idle-timeout) &ndash; aplikace hostované [v procesu](#in-process-hosting-model) lze nakonfigurovat tak, aby časový limit během období nečinnosti.

### <a name="application-initialization-module"></a>Inicializační modul aplikace

*Platí pro aplikace hostované v průběhu procesu a mimo proces.*

[Inicializace aplikace služby IIS](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) je funkce služby IIS, která odesílá požadavek HTTP do aplikace při spuštění nebo recyklaci fondu aplikací. Požadavek spustí spuštění aplikace. Ve výchozím nastavení služba IIS vydá požadavek`/`na kořenovou adresu URL aplikace ( ) o inicializaci aplikace (další podrobnosti o konfiguraci najdete v [dalších zdrojích](#application-initialization-module-and-idle-timeout-additional-resources) informací).

Zkontrolujte, zda je funkce role Inicializace aplikace služby IIS povolena:

V desktopových systémech se systémem Windows 7 nebo novějších při místním používání služby IIS:

1. Přejděte na **Ovládací panely** > **Programy** > **a funkce** > **Zapněte nebo vypněte funkce systému Windows** (levá strana obrazovky).
1. Otevřete funkce **vývoje aplikací** **internetových informačních služeb** > **World Wide Web Services** > .
1. Zaškrtněte políčko pro **inicializaci aplikace**.

V systému Windows Server 2008 R2 nebo novějším:

1. Otevřete **Průvodce přidáním rolí a funkcí**.
1. V panelu **Vybrat služby rolí** otevřete uzel **Vývoj aplikací.**
1. Zaškrtněte políčko pro **inicializaci aplikace**.

K povolení modulu inicializace aplikace pro lokalitu použijte některý z následujících přístupů:

* Pomocí Správce služby IIS:

  1. V panelu **Připojení** vyberte **fondy aplikací.**
  1. Klikněte pravým tlačítkem myši na fond aplikací v seznamu a vyberte **Upřesnit nastavení**.
  1. Výchozí **režim spuštění** je **OnDemand**. Nastavte **režim spuštění** na **vždy spuštěný**. Vyberte **OK**.
  1. Otevřete uzel **Weby** v panelu **Připojení.**
  1. Klikněte pravým tlačítkem myši na aplikaci a vyberte Spravovat **upřesnit nastavení** **webu** > .
  1. Výchozí nastavení **Preload Enabled** je **False**. Nastavte **předběžné načtení povoleno** na **hodnotu True**. Vyberte **OK**.

* Pomocí `<applicationInitialization>` `doAppInitAfterRestart` *souboru web.config*přidejte `true` prvek `<system.webServer>` s nastavenou na do prvků v souboru *web.config* aplikace:

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

*Platí pouze pro aplikace hostované v procesu.*

Chcete-li aplikaci zabránit v volnoběhu, nastavte časový limit nečinnosti fondu aplikací pomocí Správce služby IIS:

1. V panelu **Připojení** vyberte **fondy aplikací.**
1. Klikněte pravým tlačítkem myši na fond aplikací v seznamu a vyberte **Upřesnit nastavení**.
1. Výchozí **časový limit nečinnosti (minuty)** je **20** minut. Nastavte **časový limit nečinnosti (minuty)** na **0** (nula). Vyberte **OK**.
1. Recyklujte pracovní proces.

Chcete-li zabránit vypršení časového limitu aplikací hostovaných [mimo proces,](#out-of-process-hosting-model) použijte některý z následujících přístupů:

* Příkazping aplikace z externí služby, aby byla spuštěna.
* Pokud aplikace hostuje pouze služby na pozadí, vyhněte se hostování služby IIS a pomocí [služby Windows service hostujte aplikaci ASP.NET Core](xref:host-and-deploy/windows-service).

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a>Modul inicializace aplikace a časový limit nečinnosti další prostředky

* [Inicializace aplikace služby IIS 8.0](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* [Aplikace Inicializace \<aplikaceInicializační>](/iis/configuration/system.webserver/applicationinitialization/).
* [Zpracovat nastavení modelu \<pro proces fondu aplikacíModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).

## <a name="deployment-resources-for-iis-administrators"></a>Prostředky nasazení pro správce služby IIS

* [Dokumentace iis](/iis)
* [Začínáme se Správcem služby IIS ve službách IIS](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [Nasazení aplikace .NET Core](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a>Další zdroje

* <xref:test/troubleshoot>
* <xref:index>
* [Oficiální web služby Microsoft IIS](https://www.iis.net/)
* [Knihovna technického obsahu systému Windows Server](/windows-server/windows-server)
* [HTTP/2 ve službě IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Výukové prostředí o publikování aplikace ASP.NET Core na server <xref:tutorials/publish-to-iis>služby IIS naleznete v tématu .

[Instalace balíčku hostingu jádra .NET](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a>Podporované operační systémy

Podporovány jsou následující operační systémy:

* Windows 7 nebo novější
* Windows Server 2008 R2 nebo novější

[Server HTTP.sys](xref:fundamentals/servers/httpsys) (dříve nazývaný WebListener) nefunguje v konfiguraci reverzního proxy serveru se službou IIS. Použijte [server Kestrel](xref:fundamentals/servers/kestrel).

Informace o hostování v <xref:host-and-deploy/azure-apps/index>Azure najdete v tématu .

Pokyny pro řešení <xref:test/troubleshoot>potíží naleznete v tématu .

## <a name="supported-platforms"></a>Podporované platformy

Podporovány jsou aplikace publikované pro 32bitové (x86) nebo 64bitové (x64) nasazení. Nasazení 32bitové aplikace s 32bitovou sadou .NET Core SDK, pokud ji ne:

* Vyžaduje větší adresní prostor virtuální paměti, který je k dispozici pro 64bitovou aplikaci.
* Vyžaduje větší velikost zásobníku iis.
* Má 64bitové nativní závislosti.

K publikování 64bitové sady SDK jádra (x64) použijte 64bitovou sadu .NET Core SDK. V hostitelském systému musí být k dispozici 64bitový běhový čas.

ASP.NET Core je dodáván se [serverem Kestrel](xref:fundamentals/servers/kestrel), což je výchozí http server pro více platforem.

Při použití [služby IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) nebo [služby IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)je aplikace spuštěna v procesu odděleném od pracovního procesu služby IIS *(mimo proces)* se [serverem Kestrel](xref:fundamentals/servers/index#kestrel).

Vzhledem k tomu, ASP.NET základní aplikace spustit v procesu odděleném od pracovního procesu služby IIS, modul zpracovává správu procesů. Modul spustí proces pro aplikaci ASP.NET Core při příchodu prvního požadavku a restartuje aplikaci, pokud se vypne nebo dojde k chybě. Toto je v podstatě stejné chování jako u aplikací, které běží v procesu, které jsou spravovány [službou aktivace procesu systému Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

Následující diagram znázorňuje vztah mezi iis, ASP.NET základní modul a aplikace hostované mimo proces:

![Modul ASP.NET Core](index/_static/ancm-outofprocess.png)

Požadavky dorazí z webu do ovladače HTTP.sys v režimu jádra. Řidič směruje požadavky do služby IIS na nakonfigurovaném portu webu, obvykle 80 (HTTP) nebo 443 (HTTPS). Modul předá požadavky Kestrel na náhodný port pro aplikaci, která není port 80 nebo 443.

Modul určuje port prostřednictvím proměnné prostředí při spuštění a [middleware integrace služby IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) nakonfiguruje server tak, aby naslouchal . `http://localhost:{port}` Jsou prováděny další kontroly a požadavky, které nepocházejí z modulu, jsou odmítnuty. Modul nepodporuje předávání https, takže požadavky jsou předávány přes HTTP i v případě, že přijaté službou IIS přes protokol HTTPS.

Poté, co Kestrel vyzvedne požadavek z modulu, je požadavek posunut do kanálu ASP.NET middleware. Middleware kanálu zpracovává požadavek a předá `HttpContext` jej jako instanci logiky aplikace. Middleware přidané integrací služby IIS aktualizuje schéma, vzdálenou IP adresu a pathbase tak, aby odpovídaly předání požadavku ke společnosti Kestrel. Odpověď aplikace je předána zpět do služby IIS, která ji odešle zpět do klienta HTTP, který inicioval požadavek.

`CreateDefaultBuilder`Konfiguruje server [Kestrel](xref:fundamentals/servers/kestrel) jako webový server a umožňuje integraci služby IIS konfigurací základní cesty a portu pro [základní modul ASP.NET .](xref:host-and-deploy/aspnet-core-module)

Základní modul ASP.NET generuje dynamický port, který má být přiřazen k procesu back-endu. `CreateDefaultBuilder`volá <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> metodu. `UseIISIntegration`konfiguruje Kestrel poslouchat na dynamickém portu na localhost IP adresu (`127.0.0.1`). Pokud je dynamický port 1234, Kestrel naslouchá na `127.0.0.1:1234`. Tato konfigurace nahrazuje další konfigurace adres URL poskytované:

* `UseUrls`
* [Kestrel je Poslouchat API](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [Konfigurace](xref:fundamentals/configuration/index) (nebo [možnost --urls příkazového řádku)](xref:fundamentals/host/web-host#override-configuration)

Volání `UseUrls` nebo Kestrel `Listen` api nejsou vyžadovány při použití modulu. Pokud `UseUrls` `Listen` nebo je volána, Kestrel naslouchá na portu určeném pouze při spuštění aplikace bez iis.

Pokyny ke konfiguraci ASP.NET <xref:host-and-deploy/aspnet-core-module>core modulem naleznete v tématu .

Další informace o hostování najdete [v tématu Host in ASP.NET Core](xref:fundamentals/index#host).

## <a name="application-configuration"></a>Konfigurace aplikací

### <a name="enable-the-iisintegration-components"></a>Povolení součástí Integrace iIS

Při vytváření hostitele `CreateWebHostBuilder` v *(Program.cs*) volání <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> umožňující integraci iis:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

Další informace `CreateDefaultBuilder`naleznete <xref:fundamentals/host/web-host#set-up-a-host>v tématu .

### <a name="iis-options"></a>Možnosti iis

| Možnost                         | Výchozí | Nastavení |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | Pokud `true`server IIS `HttpContext.User` nastaví ověřené ověřování [systému Windows](xref:security/authentication/windowsauth). Pokud `false`server poskytuje identitu pouze `HttpContext.User` pro výzvy a reaguje na `AuthenticationScheme`ně, pokud o to výslovně požádá . Aby bylo možné službu `AutomaticAuthentication` IIS fungovat, musí být ve službě IIS povoleno ověřování systému Windows. Další informace naleznete v tématu [Ověřování systému Windows](xref:security/authentication/windowsauth). |
| `AuthenticationDisplayName`    | `null`  | Nastaví zobrazované jméno zobrazené uživatelům na přihlašovacích stránkách. |

Chcete-li konfigurovat možnosti služby <xref:Microsoft.AspNetCore.Builder.IISOptions> <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>IIS, zahrňte konfiguraci služby v aplikaci . Následující příklad zabrání aplikaci v naplnění `HttpContext.Connection.ClientCertificate`:

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| Možnost                         | Výchozí | Nastavení |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | Pokud `true` [middleware integrace služby IIS](#enable-the-iisintegration-components) nastaví ověřené ověřování systému `HttpContext.User` [Windows](xref:security/authentication/windowsauth). Pokud `false`middleware poskytuje identitu `HttpContext.User` a reaguje na problémy pouze na `AuthenticationScheme`žádost . Aby bylo možné službu `AutomaticAuthentication` IIS fungovat, musí být ve službě IIS povoleno ověřování systému Windows. Další informace naleznete v tématu [Ověřování systému Windows.](xref:security/authentication/windowsauth) |
| `AuthenticationDisplayName`    | `null`  | Nastaví zobrazované jméno zobrazené uživatelům na přihlašovacích stránkách. |
| `ForwardClientCertificate`     | `true`  | Pokud `true` a `MS-ASPNETCORE-CLIENTCERT` hlavička požadavku `HttpContext.Connection.ClientCertificate` je k dispozici, je naplněn. |

### <a name="proxy-server-and-load-balancer-scenarios"></a>Scénáře proxy serveru a vyrovnávání zatížení

[Middleware integrace služby IIS](#enable-the-iisintegration-components), který konfiguruje middleware předávaných záhlaví a ASP.NET základní modul, jsou nakonfigurovány tak, aby předávaly schéma (HTTP/HTTPS) a vzdálenou adresu IP, ze které požadavek pochází. Další konfigurace může být vyžadována pro aplikace hostované za dalšími proxy servery a nástroji pro vyrovnávání zatížení. Další informace naleznete v [tématu Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).

### <a name="webconfig-file"></a>soubor web.config

Soubor *web.config* konfiguruje [základní modul ASP.NET](xref:host-and-deploy/aspnet-core-module). Při publikování projektu je vytváření, transformace a publikování souboru *web.config* zpracováno cílem MSBuild (`_TransformWebConfig`). Tento cíl je k dispozici v`Microsoft.NET.Sdk.Web`cílech sady Web SDK ( ). Sada SDK je nastavena v horní části souboru projektu:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Pokud soubor *web.config* není v projektu přítomen, je soubor vytvořen se správnou *processPath* a *argumenty* pro konfiguraci ASP.NET základnímodul a přesunutna do [publikovaného výstupu](xref:host-and-deploy/directory-structure).

Pokud *web.config* soubor je přítomen v projektu, soubor je transformován se správným *processPath* a *argumenty* pro konfiguraci ASP.NET core modul a přesunuta do publikovaného výstupu. Transformace nezmění nastavení konfigurace služby IIS v souboru.

Soubor *web.config* může poskytovat další nastavení konfigurace služby IIS, která řídí aktivní moduly služby IIS. Informace o modulech služby IIS, které jsou schopné zpracovávat požadavky pomocí aplikací ASP.NET Core, naleznete v tématu [modulů služby IIS.](xref:host-and-deploy/iis/modules)

Chcete-li zabránit webové sady SDK v transformaci souboru *web.config,* použijte vlastnost ** \<IsTransformWebConfigDisabled>** v souboru projektu:

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

Při zakázání webové sady SDK z transformace souboru *processPath* a argumenty by měly být ručně *nastaveny* vývojářem. Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module>.

### <a name="webconfig-file-location"></a>umístění souboru web.config

Aby bylo možné správně nastavit [ASP.NET core modul,](xref:host-and-deploy/aspnet-core-module) musí být soubor *web.config* přítomen na [cestě kořenového obsahu](xref:fundamentals/index#content-root) (obvykle základní cesta aplikace) nasazené aplikace. Jedná se o stejné umístění jako fyzická cesta k webu poskytnutá službu IIS. Soubor *web.config* je vyžadován v kořenovém adresáři aplikace, aby bylo možné publikovat více aplikací pomocí nasazení webu.

Na fyzické cestě aplikace existují citlivé soubory, například * \<assembly>.runtimeconfig.json*, * \<assembly>.xml* (komentáře xml documentation) a * \<assembly>.deps.json*. Pokud je soubor *web.config* přítomen a web se spustí normálně, služby IIS tyto citlivé soubory neobsluhuje, pokud jsou požadovány. Pokud soubor *web.config* chybí, je nesprávně pojmenován nebo jej nelze nakonfigurovat pro normální spuštění, může služba IIS veřejně sloužit citlivým souborům.

**Soubor *web.config* musí být v nasazení vždy přítomen, správně pojmenovaný a musí být schopen nakonfigurovat web pro normální spuštění. Nikdy neodstraňujte soubor *web.config* z produkčního nasazení.**

### <a name="transform-webconfig"></a>Transformace souboru web.config

Pokud potřebujete transformovat *web.config* při publikování (například nastavit proměnné prostředí na základě konfigurace, profilu nebo prostředí), přečtěte si viz <xref:host-and-deploy/iis/transform-webconfig>.

## <a name="iis-configuration"></a>Konfigurace služby IIS

**Operační systémy Windows Server**

Povolte roli serveru **webového serveru (IIS)** a vytvořte služby rolí.

1. Použijte Průvodce **přidáním rolí a funkcí** z nabídky **Spravovat** nebo odkaz ve **Správci serveru**. V kroku **Role serveru** zaškrtněte políčko **U webového serveru (IIS).**

   ![Role služby IIS webového serveru je vybrána v kroku Vybrat role serveru.](index/_static/server-roles-ws2016.png)

1. Po kroku **Funkce** se krok **služby role** načte pro webový server (IIS). Vyberte požadované služby rolí služby IIS nebo přijměte výchozí služby rolí, které jsou k dispozici.

   ![Výchozí služby rolí jsou vybrány v kroku Vybrat služby rolí.](index/_static/role-services-ws2016.png)

   **Ověřování systému Windows (volitelné)**  
   Chcete-li povolit ověřování systému Windows, rozbalte následující uzly:**Zabezpečení** **webového serveru** > . Vyberte funkci **Ověřování systému Windows.** Další informace naleznete v [tématech Ověřování \<systému Windows windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) a Konfigurace ověřování [systému Windows](xref:security/authentication/windowsauth).

   **WebSockets (volitelné)**  
   WebSockets je podporován ASP.NET jádrem 1.1 nebo novějším. Chcete-li povolit rozhraní WebSockets, rozbalte následující uzly:**Vývoj aplikací webového** **serveru** > . Vyberte funkci **WebSocket Protocol.** Další informace naleznete v tématu [WebSockets](xref:fundamentals/websockets).

1. Pokračujte v **potvrzovacím** kroku a nainstalujte roli a služby webového serveru. Po instalaci role **webového serveru (IIS)** není restartování serveru nebo služby IIS vyžadováno.

**Desktopové operační systémy Windows**

Povolte **konzolu pro správu služby IIS** a **služby World Wide Web Services**.

1. Přejděte na **Ovládací panely** > **Programy** > **a funkce** > **Zapněte nebo vypněte funkce systému Windows** (levá strana obrazovky).

1. Otevřete uzel **Internetové informační služby.** Otevřete uzel **Nástroje pro správu webu.**

1. Zaškrtněte políčko **U konzole pro správu služby IIS**.

1. Zaškrtněte políčko **U služeb World Wide Web Services**.

1. Přijměte výchozí funkce **pro služby World Wide Web Services** nebo upravte funkce služby IIS.

   **Ověřování systému Windows (volitelné)**  
   Chcete-li povolit ověřování systému Windows, rozbalte následující uzly: **World Wide Web Services** > **Security**. Vyberte funkci **Ověřování systému Windows.** Další informace naleznete v [tématech Ověřování \<systému Windows windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) a Konfigurace ověřování [systému Windows](xref:security/authentication/windowsauth).

   **WebSockets (volitelné)**  
   WebSockets je podporován ASP.NET jádrem 1.1 nebo novějším. Chcete-li povolit rozhraní WebSockets, rozbalte následující uzly: Funkce**vývoje aplikací**pro **webové služby** > . Vyberte funkci **WebSocket Protocol.** Další informace naleznete v tématu [WebSockets](xref:fundamentals/websockets).

1. Pokud instalace služby IIS vyžaduje restartování, restartujte systém.

![V funkcích systému Windows jsou vybrány konzoly IIS Management Console a služby World Wide Web Services.](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a>Instalace balíčku hostingu jádra .NET

Nainstalujte *balíček .NET Core Hosting do* hostitelského systému. Balíček nainstaluje modul .NET Core Runtime, knihovnu .NET Core Library a [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module). Modul umožňuje ASP.NET aplikace Core běžet za službou IIS.

> [!IMPORTANT]
> Pokud je sada Hosting Bundle nainstalována před službou IIS, musí být instalace balíčku opravena. Po instalaci služby IIS znovu spusťte instalační program sady Hosting Bundle.
>
> Pokud je sada Hosting Bundle nainstalována po instalaci 64bitové (x64) verze rozhraní .NET Core, mohou se zdát, že sady SDK chybí ([byly zjištěny sady SDK .Net Core .](xref:test/troubleshoot#no-net-core-sdks-were-detected) Chcete-li problém <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>vyřešit, naleznete v tématu .

### <a name="direct-download-current-version"></a>Přímé stahování (aktuální verze)

Stáhněte si instalační program pomocí následujícího odkazu:

[Aktuální instalační program .NET Core Hosting Bundle (přímé stahování)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a>Dřívější verze instalačního programu

Chcete-li získat starší verzi instalačního programu:

1. Přejděte na stránku [Stáhnout jádro rozhraní .NET.](https://dotnet.microsoft.com/download/dotnet-core)
1. Klikněte na požadovanou verzi .NET Core.
1. Ve sloupci **Spustit aplikace – runtime** najděte požadovaný řádek požadované verze runtime .NET Core.
1. Stáhněte si instalační program pomocí odkazu **Runtime & Hosting Bundle.**

> [!WARNING]
> Některé instalační programy obsahují verze, které dosáhly konce životnosti (EOL) a které již nejsou podporovány společností Microsoft. Další informace naleznete v [zásadách podpory](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

### <a name="install-the-hosting-bundle"></a>Nainstalujte balíček hostingu

1. Spusťte instalační program na serveru. Při spuštění instalačního programu z příkazového prostředí správce jsou k dispozici následující parametry:

   * `OPT_NO_ANCM=1`&ndash; Přeskočte instalaci ASP.NET základního modulu.
   * `OPT_NO_RUNTIME=1`&ndash; Přeskočte instalaci runtime jádra .NET. Používá se v případě, že server hostuje pouze [samostatná nasazení (SCD).](/dotnet/core/deploying/#self-contained-deployments-scd)
   * `OPT_NO_SHAREDFX=1`&ndash; Přeskočit instalaci ASP.NET sdíleného rozhraní (ASP.NET runtime). Používá se v případě, že server hostuje pouze [samostatná nasazení (SCD).](/dotnet/core/deploying/#self-contained-deployments-scd)
   * `OPT_NO_X86=1`&ndash; Přeskočit instalaci za běhu x86. Tento parametr použijte, pokud víte, že nebudete hostovat 32bitové aplikace. Pokud existuje nějaká šance, že budete v budoucnu hostovat 32bitové i 64bitové aplikace, nepoužívejte tento parametr a nainstalujte oba runtimes.
   * `OPT_NO_SHARED_CONFIG_CHECK=1`&ndash; Zakažte kontrolu použití sdílené konfigurace služby IIS, pokud je sdílená konfigurace *(applicationHost.config)* ve stejném počítači jako instalace služby IIS. *K dispozici pouze pro ASP.NET instalačních programů hostingu Bundler nebo novějším.* Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.
1. Restartujte systém nebo proveďte následující příkazy v příkazovém prostředí:

   ```console
   net stop was /y
   net start w3svc
   ```
   Restartování služby IIS vyvolá změnu systémové cesty, což je proměnná prostředí provedená instalačním programem.

Při instalaci balíčku Hosting Bundle není nutné ručně zastavovat jednotlivé weby ve službách IIS. Hostované aplikace (weby služby IIS) se restartují při restartování služby IIS. Aplikace se znovu spustí, když obdrží svůj první požadavek, včetně z [modulu inicializace aplikace](#application-initialization-module-and-idle-timeout).

ASP.NET Core přijímá roll-forward chování pro opravy vydání sdílených rámců. Když se aplikace hostované službou IIS restartují se službou IIS, aplikace se načítají nejnovějšími verzemi oprav svých odkazovaných balíčků, když obdrží svůj první požadavek. Pokud služba IIS není restartována, aplikace se restartují a vykazují chování pro předávání vpřed, když jsou jejich pracovní procesy recyklovány a obdrží první požadavek.

> [!NOTE]
> Informace o sdílené konfiguraci služby IIS naleznete [v tématu ASP.NET Core Module se sdílenou konfigurací služby IIS](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a>Instalace nasazení webu při publikování pomocí sady Visual Studio

Při nasazování aplikací na servery s [nasazením webu](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)nainstalujte na server nejnovější verzi nasazení webu. Chcete-li nainstalovat nasazení webu, použijte [Instalační službu webové platformy (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) nebo získejte instalační program přímo ze [služby Stažení softwaru](https://www.microsoft.com/download/details.aspx?id=43717). Upřednostňovanou metodou je použití webpi. WebPI nabízí samostatné nastavení a konfiguraci pro poskytovatele hostingu.

## <a name="create-the-iis-site"></a>Vytvoření webu iis

1. V hostitelském systému vytvořte složku, která bude obsahovat publikované složky a soubory aplikace. V následujícím kroku je cesta ke složce k dispozici službu IIS jako fyzická cesta k aplikaci. Další informace o složce nasazení aplikace a <xref:host-and-deploy/directory-structure>rozložení souborů naleznete v tématu .

1. Ve Správci služby IIS otevřete uzel serveru v panelu **Připojení.** Klikněte pravým tlačítkem myši na složku **Weby.** V kontextové nabídce vyberte **Přidat web.**

1. Zadejte **název webu** a nastavte **fyzickou cestu** ke složce nasazení aplikace. Zadejte konfiguraci **vazby** a vytvořte web výběrem **možnosti OK**:

   ![V kroku Přidat web zadejte název webu, fyzickou cestu a název hostitele.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > Vazby se zástupnými`http://*:80/` symboly nejvyšší úrovně ( a `http://+:80`) by **neměly** být používány. Vazby se zástupnými symboly nejvyšší úrovně mohou otevřít vaši aplikaci slabá místa zabezpečení. To platí pro silné i slabé zástupné znaky. Používejte explicitní názvy hostitelů místo zástupných znaků. Vazba se zástupnými znaky subdomény (například) nemá toto bezpečnostní riziko, `*.mysub.com` `*.com`pokud řídíte celou nadřazenou doménu (na rozdíl od , která je zranitelná). Viz [rfc7230 sekce-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) pro více informací.

1. Pod uzlovým serverem vyberte **fondy aplikací**.

1. Klikněte pravým tlačítkem myši na fond aplikací webu a v kontextové nabídce vyberte **Základní nastavení.**

1. V okně **Upravit fond aplikací** nastavte verzi **.NET CLR** na **Žádný spravovaný kód**:

   ![Pro verzi CLR rozhraní .NET nenastavte žádný spravovaný kód.](index/_static/edit-apppool-ws2016.png)

    ASP.NET Core běží v samostatném procesu a spravuje runtime. ASP.NET Core nespoléhá na načtení plochy CLR (.NET CLR)&mdash;Core Common Language Runtime (CoreCLR) pro .NET Core je spuštěn pro hostování aplikace v pracovním procesu. Nastavení **verze .NET CLR** na **žádný spravovaný kód** je volitelné, ale doporučeno.

1. *ASP.NET jádra 2.2 nebo novějšího:* Pro 64bitové (x64) [samostatné nasazení,](/dotnet/core/deploying/#self-contained-deployments-scd) které používá [model hostování v procesu](#in-process-hosting-model), zakažte fond aplikací pro 32bitové (x86) procesy.

   V postranním panelu **Akce** správce služby IIS > **fondy aplikací**vyberte **možnost Nastavit výchozí nastavení fondu aplikací** nebo **Upřesnit nastavení**. Vyhledejte **funkci Povolit 32bitové aplikace** a nastavte hodnotu na . `False` Toto nastavení nemá vliv na aplikace nasazené pro [mimoprocesový hosting](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).

1. Potvrďte, že identita modelu procesu má správná oprávnění.

   Pokud se výchozí identita fondu aplikací **(identita****procesního modelu** > ) změní z **ApplicationPoolIdentity** na jinou identitu, ověřte, zda nová identita má požadovaná oprávnění pro přístup ke složce, databázi a dalším požadovaným prostředkům aplikace. Fond aplikací například vyžaduje přístup pro čtení a zápis do složek, kde aplikace čte a zapisuje soubory.

**Konfigurace ověřování systému Windows (volitelná)**  
Další informace naleznete v [tématu Configure Windows authentication](xref:security/authentication/windowsauth).

## <a name="deploy-the-app"></a>Nasazení aplikace

Nasaďte aplikaci do složky **Fyzické cesty** služby IIS, která byla vytvořena v části Vytvořit [web služby IIS.](#create-the-iis-site) [Nasazení webu](/iis/publish/using-web-deploy/introduction-to-web-deploy) je doporučený mechanismus pro nasazení, ale existuje několik možností pro přesunutí aplikace ze složky *publikování* projektu do složky nasazení hostitelského systému.

### <a name="web-deploy-with-visual-studio"></a>Nasazení webu pomocí sady Visual Studio

Podívejte se na [profily publikování aplikace Visual Studio pro téma nasazení aplikace ASP.NET Core,](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) kde se dozvíte, jak vytvořit profil publikování pro použití s nasazením webu. Pokud poskytovatel hostingu poskytuje profil publikování nebo podporu pro jeho vytvoření, stáhněte si jeho profil a importujte jej pomocí dialogového okna **Publikovat** sady Visual Studio:

![Stránka dialogového okna Publikovat](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a>Nasazení webu mimo Visual Studio

[Nasazení webu](/iis/publish/using-web-deploy/introduction-to-web-deploy) lze také použít mimo Visual Studio z příkazového řádku. Další informace naleznete v [tématu Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).

### <a name="alternatives-to-web-deploy"></a>Alternativy k nasazení webu

Pomocí některé z několika metod přesuňte aplikaci do hostitelského systému, jako je ruční kopírování, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy)nebo [PowerShell](/powershell/).

Další informace o ASP.NET nasazení jádra do služby IIS naleznete v části [Prostředky nasazení pro správce služby IIS.](#deployment-resources-for-iis-administrators)

## <a name="browse-the-website"></a>Procházení webu

Po nasazení aplikace do hostitelského systému požádejte o jeden z veřejných koncových bodů aplikace.

V následujícím příkladu je web vázán na název `www.mysite.com` **hostitele** iis na **portu** `80`. Žádost se podává `http://www.mysite.com`na :

![Prohlížeč Microsoft Edge načetl úvodní stránku služby IIS.](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a>Uzamčené soubory nasazení

Soubory ve složce nasazení jsou uzamčeny, když je aplikace spuštěná. Uzamčené soubory nelze během nasazení přepsat. Chcete-li uvolnit uzamčené soubory v nasazení, zastavte fond aplikací pomocí **jednoho z** následujících přístupů:

* Použijte nasazení webu `Microsoft.NET.Sdk.Web` a odkaz v souboru projektu. Soubor *app_offline.htm* je umístěn v kořenovém adresáři webové aplikace. Když je soubor k dispozici, ASP.NET základní modul řádně vypne aplikaci a slouží *souboru app_offline.htm* během nasazení. Další informace naleznete v [odkazu na konfiguraci ASP.NET core modulem](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).
* Ručně zastavit fond aplikací ve Správci služby IIS na serveru.
* Použití Prostředí PowerShell k přetažení *app_offline.htm* (vyžaduje PowerShell 5 nebo novější):

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a>Ochrana dat

Zásobník [ASP.NET Core Data Protection](xref:security/data-protection/introduction) používá několik [middlewares](xref:fundamentals/middleware/index)ASP.NET Core , včetně middlewaru používaného při ověřování. I v případě, že rozhraní API ochrany dat nejsou volána uživatelským kódem, ochrana dat by měla být nakonfigurována pomocí skriptu nasazení nebo v uživatelském kódu, aby se vytvořilo trvalé úložiště kryptografických [klíčů](xref:security/data-protection/implementation/key-management). Pokud ochrana dat není nakonfigurována, klíče jsou uloženy v paměti a po restartování aplikace zahozeny.

Pokud je kroužek klíče uložen v paměti při restartování aplikace:

* Všechny ověřovací tokeny založené na souborech cookie jsou zrušeny. 
* Uživatelé se musí znovu přihlásit při dalším požadavku. 
* Všechna data chráněná kroužkem klíče již nelze dešifrovat. To může zahrnovat [tokeny CSRF](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) a [ASP.NET základní soubory cookie MVC TempData](xref:fundamentals/app-state#tempdata).

Chcete-li nakonfigurovat ochranu dat ve službě IIS tak, aby zachycovací byl kroužek klíče, použijte **jeden** z následujících postupů:

* **Vytvoření klíčů registru ochrany dat**

  Klíče ochrany dat používané ASP.NET aplikace Core jsou uloženy v registru mimo aplikace. Chcete-li zachovat klíče pro danou aplikaci, vytvořte klíče registru pro fond aplikací.

  Pro samostatné instalace služby IIS mimo webfarmu lze [skript prostředí PowerShell Pro poskytování ochrany dat-AutoGenKeys.ps1](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) použít pro každý fond aplikací používaný s aplikací ASP.NET Core. Tento skript vytvoří klíč registru v registru HKLM, který je přístupný pouze pro účet pracovního procesu fondu aplikací. Klíče jsou šifrovány v klidovém stavu pomocí dpapi s klíčem pro celý počítač.

  Ve scénářích webové farmy lze aplikaci nakonfigurovat tak, aby k ukládání kroužku klíčů pro ochranu dat používala cestu UNC. Ve výchozím nastavení nejsou klíče ochrany dat šifrovány. Ujistěte se, že oprávnění k souborům pro sdílenou síť ovou složku jsou omezena na účet Windows, pod kterým aplikace běží. Certifikát X509 lze použít k ochraně klíčů v klidovém stavu. Zvažte mechanismus, který uživatelům umožní nahrávat certifikáty: Umístěte certifikáty do úložiště důvěryhodných certifikátů uživatele a ujistěte se, že jsou k dispozici na všech počítačích, kde je spuštěna aplikace uživatele. Podrobnosti [najdete v tématu Konfigurace ochrany základních dat ASP.NET.](xref:security/data-protection/configuration/overview)

* **Konfigurace fondu aplikací služby IIS pro načtení profilu uživatele**

  Toto nastavení je v části **Model procesu** v části **Upřesnit nastavení** pro fond aplikací. Nastavte **profil načtení uživatele** na `True`. Pokud je `True`nastavena možnost , jsou klíče uloženy v adresáři profilu uživatele a chráněny pomocí rozhraní DPAPI klíčem specifickým pro uživatelský účet. Klíče jsou uloženy ve složce *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys.*

  [Atribut setProfileEnvironment](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) fondu aplikací musí být také povolen. Výchozí hodnota `setProfileEnvironment` je `true`. V některých scénářích (například `setProfileEnvironment` operační `false`systém Windows) je nastavena na . Pokud klíče nejsou uloženy v adresáři profilu uživatele podle očekávání:

  1. Přejděte do složky *%windir%/system32/inetsrv/config.*
  1. Otevřete soubor *applicationHost.config.*
  1. Vyhledejte element `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>`.
  1. Zkontrolujte, `setProfileEnvironment` zda atribut není k dispozici, `true`což je výchozí hodnota na `true`, nebo explicitně nastavte hodnotu atributu na .

* **Použití systému souborů jako úložiště kroužků na klíče**

  Upravte kód aplikace tak, aby [používal systém souborů jako úložiště kroužků klíčů](xref:security/data-protection/configuration/overview). K ochraně kroužku na klíče a zajištění, že certifikát je důvěryhodný, použijte certifikát X509. Pokud je certifikát podepsaný svým držitelem, umístěte jej do úložiště Důvěryhodného kořenového adresáře.

  Při použití služby IIS ve webové farmě:

  * Použijte sdílenou složku, ke které mají přístup všechny počítače.
  * Nasazení certifikátu X509 do každého počítače. Konfigurace [ochrany dat v kódu](xref:security/data-protection/configuration/overview).

* **Nastavení celopočítačových zásad pro ochranu dat**

  Systém ochrany dat má omezenou podporu pro nastavení výchozí [zásady celého počítače](xref:security/data-protection/configuration/machine-wide-policy) pro všechny aplikace, které spotřebovávají data protection API. Další informace naleznete v tématu <xref:security/data-protection/introduction>.

## <a name="virtual-directories"></a>Virtuální adresáře

[Virtuální adresáře služby IIS](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) nejsou podporovány aplikacemi ASP.NET Core. Aplikace může být hostována jako [podaplikace](#sub-applications).

## <a name="sub-applications"></a>Dílčí aplikace

Aplikaci ASP.NET Core lze hostovat jako [podaplikaci služby IIS (podaplikaci).](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications) Cesta podaplikace se stane součástí adresy URL kořenové aplikace.

Podaplikace by neměla obsahovat ASP.NET core modul jako obslužnou rutinu. Pokud je modul přidán jako obslužná rutina do souboru *web.config* podaplikace, při pokusu o procházení podaplikace se zobrazí *interní chyba serveru 500.19* odkazující na vadný konfigurační soubor.

Následující příklad ukazuje publikovaný soubor *web.config* pro podaplikaci ASP.NET Core:

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

Při hostování podřízené aplikace non-ASP.NET Core pod aplikací ASP.NET Core explicitně odeberte zděděnou obslužnou rutinu v souboru *web.config* pod podřízené aplikace:

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

Statické odkazy datových zdrojů v rámci podaplikace`~/`by měly používat zápis tildového lomítka ( ). Zápis tildového lomítka aktivuje [pomocníka tagu,](xref:mvc/views/tag-helpers/intro) který předřává základnu cesty podaplikace k vykreslenérelativní vazbě. Pro podaplikaci `/subapp_path`na adrese , `src="~/image.png"` obrázek `src="/subapp_path/image.png"`spojený s je vykreslen jako . Middleware statického souboru kořenové aplikace nezpracovává požadavek na statický soubor. Požadavek je zpracován middlewarem statického souboru podaplikace.

Pokud je atribut `src` statického datového zdroje nastaven na `src="/image.png"`absolutní cestu (například), odkaz se vykreslí bez vytvoření cesty podaplikace. Middleware statického souboru kořenové aplikace se pokouší obsluhovat datový zdroj z [kořenového kořenového kořenového adresáře](xref:fundamentals/index#web-root)aplikace , což má za následek odpověď *404 - Not Found,* pokud není statický datový zdroj k dispozici z kořenové aplikace.

Hostování aplikace ASP.NET Core jako podaplikace pod jinou aplikací ASP.NET Core:

1. Vytvořte fond aplikací pro podaplikaci. Nastavte **verzi .NET CLR** na **žádný spravovaný kód,** protože prostředí Core Common Language Runtime (CoreCLR) pro .NET Core je spuštěno pro hostování aplikace v pracovním procesu, nikoli na ploše CLR (.NET CLR).

1. Přidejte kořenový web ve Správci služby IIS s podaplikací ve složce pod kořenovým webem.

1. Klikněte pravým tlačítkem myši na složku podaplikace ve Správci služby IIS a vyberte **příkaz Převést na aplikaci**.

1. V dialogovém okně **Přidat aplikaci** přiřaďte pomocí tlačítka **Vybrat** pro **fond aplikací** fond aplikací, který jste vytvořili pro podaplikaci. Vyberte **OK**.

Přiřazení samostatného fondu aplikací do podaplikace je požadavek při použití modelu hostování v procesu.

Další informace o modelu hostování v procesu a konfiguraci <xref:host-and-deploy/aspnet-core-module>ASP.NET core modulu naleznete v tématu .

## <a name="configuration-of-iis-with-webconfig"></a>Konfigurace služby IIS pomocí souboru web.config

Konfigurace služby IIS `<system.webServer>` je ovlivněna částí *web.config* pro scénáře služby IIS, které jsou funkční pro ASP.NET aplikace Core pomocí ASP.NET core modulem. Například konfigurace služby IIS je funkční pro dynamickou kompresi. Pokud je služba IIS nakonfigurována na `<urlCompression>` úrovni serveru pro použití dynamické komprese, prvek v souboru *web.config* aplikace jej může zakázat pro aplikaci ASP.NET Core.

Další informace najdete v následujících tématech:

* [Odkaz na \<konfiguraci pro>system.webServer](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

Chcete-li nastavit proměnné prostředí pro jednotlivé aplikace spuštěné v izolovaných fondech aplikací (podporované pro službu IIS 10.0 nebo novější), přečtěte si část *příkazu AppCmd.exe* v tématu [Environment Variables environmentvariables>\<](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) tématu v referenční dokumentaci služby IIS.

## <a name="configuration-sections-of-webconfig"></a>Konfigurační části web.config

Části konfigurace aplikací ASP.NET 4.x v *souboru web.config* nepoužívají aplikace ASP.NET Core pro konfiguraci:

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

ASP.NET aplikace Core jsou konfigurovány pomocí jiných poskytovatelů konfigurace. Další informace naleznete v [tématu Konfigurace](xref:fundamentals/configuration/index).

## <a name="application-pools"></a>Fondy aplikací

Když hostujete více webů na serveru, doporučujeme izolovat aplikace od sebe spuštěním jednotlivých aplikací ve vlastním fondu aplikací. Dialogové okno **Přidat web** služby IIS je výchozí pro tuto konfiguraci. Když je k dispozici **název webu,** text se automaticky přenese do textového pole **Fondu aplikací.** Při přidání webu se vytvoří nový fond aplikací s názvem webu.

## <a name="application-pool-identity"></a>Identita fondu aplikací

Účet identity fondu aplikací umožňuje aplikaci spouštět pod jedinečným účtem, aniž by bylo třeba vytvářet a spravovat domény nebo místní účty. Ve službě IIS 8.0 nebo novější vytvoří pracovní proces správce služby IIS (WAS) virtuální účet s názvem nového fondu aplikací a ve výchozím nastavení spustí pracovní procesy fondu aplikací v rámci tohoto účtu. V konzole pro správu služby IIS v části **Upřesnit nastavení** fondu aplikací zkontrolujte, zda je **identita** nastavená na použití **applicationpoolidentity**:

![Dialogové okno Upřesnit nastavení fondu aplikací](index/_static/apppool-identity.png)

Proces správy služby IIS vytvoří zabezpečený identifikátor s názvem fondu aplikací v systému zabezpečení systému Windows. Prostředky lze zabezpečit pomocí této identity. Tato identita však není reálný uživatelský účet a nezobrazuje se v konzole pro správu uživatelů systému Windows.

Pokud pracovní proces služby IIS vyžaduje zvýšený přístup k aplikaci, upravte seznam řízení přístupu (ACL) pro adresář obsahující aplikaci:

1. Sem otevřete Průzkumníka Windows a přejděte do adresáře.

1. Klepněte pravým tlačítkem myši na adresář a vyberte příkaz **Vlastnosti**.

1. Na kartě **Zabezpečení** vyberte tlačítko **Upravit** a pak **tlačítko Přidat.**

1. Vyberte tlačítko **Umístění** a ujistěte se, že je vybrán systém.

1. Zadejte **\\<<iIS app_pool_name>** **zadejte názvy objektů, které chcete vybrat.** Vyberte tlačítko **Zkontrolovat jména.** Pro *DefaultAppPool* zkontrolujte názvy pomocí **IIS AppPool\DefaultAppPool**. Když je vybráno tlačítko **Zkontrolovat názvy,** je v oblasti názvů objektů uvedena hodnota **DefaultAppPool.** Název fondu aplikací není možné zadat přímo do oblasti názvů objektů. Při kontrole názvu objektu použijte<<app_pool_name>formátu **IIS AppPool.\\ **

   ![Dialogové okno Vybrat uživatele nebo skupiny pro složku aplikace: Název fondu aplikací "DefaultAppPool" se připojí k "IIS AppPool\" v oblasti názvů objektů před výběrem možnosti Zkontrolovat názvy".](index/_static/select-users-or-groups-1.png)

1. Vyberte **OK**.

   ![Dialogové okno Vybrat uživatele nebo skupiny pro složku aplikace: Po výběru možnosti Zkontrolovat názvy se v oblasti názvů objektů zobrazí název objektu DefaultAppPool.](index/_static/select-users-or-groups-2.png)

1. Oprávnění &amp; ke spuštění čtení by měla být udělena ve výchozím nastavení. Podle potřeby zadejte další oprávnění.

Přístup lze také udělit na příkazovém řádku pomocí nástroje **ICACLS.** Jako příklad použijete *defaultapppool,* který používá následující příkaz:

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

Další informace naleznete v tématu [icacls.](/windows-server/administration/windows-commands/icacls)

## <a name="http2-support"></a>Podpora HTTP/2

[Protokol HTTP/2](https://httpwg.org/specs/rfc7540.html) je podporován pro mimoprocesová nasazení, která splňují následující základní požadavky:

* Windows Server 2016/Windows 10 nebo novější; IIS 10 nebo novější
* Připojení serveru edge směřující mj. [Kestrel server](xref:fundamentals/servers/kestrel)
* Cílová architektura: Nevztahuje se na mimoprocesová nasazení, protože připojení HTTP/2 je plně zpracováno službou IIS.
* Připojení TLS 1.2 nebo novější

Pokud je navázáno připojení HTTP/2, `HTTP/1.1`protokol [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) hlásí .

Protokol HTTP/2 je ve výchozím nastavení povolen. Připojení se vrátí na HTTP/1.1, pokud není navázáno připojení HTTP/2. Další informace o konfiguraci protokolu HTTP/2 s nasazením služby IIS naleznete [v tématu HTTP/2 ve službě IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).

## <a name="cors-preflight-requests"></a>Požadavky cors kontroly před výstupem

*Tato část se vztahuje pouze na ASP.NET základní aplikace, které se zaměřují na rozhraní .NET Framework.*

Pro aplikaci ASP.NET Core, která cílí na rozhraní .NET Framework, nejsou požadavky OPTIONS ve výchozím nastavení ve službách IIS předány do aplikace. Informace o konfiguraci obslužných rutin služby IIS aplikace v *souboru web.config* tak, aby předávály požadavky OPTIONS, naleznete v tématu [Povolení požadavků napříč původy v ASP.NET webovém rozhraní API 2: Jak funguje cors](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).

## <a name="deployment-resources-for-iis-administrators"></a>Prostředky nasazení pro správce služby IIS

* [Dokumentace iis](/iis)
* [Začínáme se Správcem služby IIS ve službách IIS](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [Nasazení aplikace .NET Core](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a>Další zdroje

* <xref:test/troubleshoot>
* <xref:index>
* [Oficiální web služby Microsoft IIS](https://www.iis.net/)
* [Knihovna technického obsahu systému Windows Server](/windows-server/windows-server)
* [HTTP/2 ve službě IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

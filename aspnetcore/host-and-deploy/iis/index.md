---
title: Hostování ASP.NET Core ve Windows se službou IIS
author: guardrex
description: Naučte se hostovat aplikace ASP.NET Core v systému Windows Server Internetová informační služba (IIS).
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/13/2019
uid: host-and-deploy/iis/index
ms.openlocfilehash: bf535134277a08103ba8ce55eeed540a9fce8260
ms.sourcegitcommit: 07d98ada57f2a5f6d809d44bdad7a15013109549
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333883"
---
# <a name="host-aspnet-core-on-windows-with-iis"></a>Hostování ASP.NET Core ve Windows se službou IIS

Od [Luke Latham](https://github.com/guardrex)

Kurz týkající se publikování ASP.NET Core aplikace na server služby IIS najdete v tématu <xref:tutorials/publish-to-iis>.

[Instalace hostující sady .NET Core](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a>Podporované operační systémy

Podporovány jsou následující operační systémy:

* Windows 7 nebo novější
* Windows Server 2008 R2 nebo novější

[Http. sys Server](xref:fundamentals/servers/httpsys) (dříve označovaný jako weblisten) nefunguje v konfiguraci reverzního proxy serveru se službou IIS. Použijte [Server Kestrel](xref:fundamentals/servers/kestrel).

Informace o hostování v Azure najdete v tématu <xref:host-and-deploy/azure-apps/index>.

Pokyny k řešení potíží najdete v tématu @no__t – 0.

## <a name="supported-platforms"></a>Podporované platformy

Podporují se aplikace publikované pro nasazení 32 (x86) nebo 64-bit (x64). Nasazení 32 aplikace s 32 (x86) .NET Core SDK, pokud aplikace:

* Vyžaduje větší dostupný adresní prostor virtuální paměti pro 64 aplikaci.
* Vyžaduje větší velikost zásobníku služby IIS.
* Má 64 nativní závislosti.

K publikování 64 aplikace použijte 64 .NET Core SDK (x64). V hostitelském systému musí být nainstalován 64 modul runtime.

::: moniker range=">= aspnetcore-2.2"

## <a name="hosting-models"></a>Modely hostingu

### <a name="in-process-hosting-model"></a>Model hostování v procesu

Při použití hostování v rámci procesu ASP.NET Core aplikace běží ve stejném procesu jako jeho pracovní proces služby IIS. Hostování v rámci procesů poskytují lepší výkon než hostování mimo procesy, protože požadavky nejsou proxy serverem přes adaptér zpětné smyčky, síťové rozhraní, které vrátí odchozí síťový provoz zpátky do stejného počítače. Služba IIS zpracovává správu procesů pomocí [aktivační služby procesů systému Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

[Modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module):

* Provede inicializaci aplikace.
  * Načte [CoreCLR](/dotnet/standard/glossary#coreclr).
  * Volá @no__t – 0.
* Zpracovává životnost nativního požadavku služby IIS.

Model hostování v rámci procesu není podporován pro aplikace ASP.NET Core, které cílí na .NET Framework.

Následující diagram znázorňuje vztah mezi službou IIS, modulem ASP.NET Core a aplikací hostovanou v procesu:

![ASP.NET Core modul ve scénáři hostování v rámci procesu](index/_static/ancm-inprocess.png)

Požadavek přijde z webu do ovladače HTTP. sys v režimu jádra. Ovladač směruje nativní požadavek na IIS na konfigurovaném portu webu, obvykle 80 (HTTP) nebo 443 (HTTPS). Modul ASP.NET Core obdrží nativní požadavek a předá ho serveru HTTP služby IIS (`IISHttpServer`). HTTP server IIS je vnitroprocesové implementace v rámci procesového serveru pro službu IIS, která převádí požadavek z nativního na spravovanou.

Poté, co server HTTP služby IIS požadavek zpracuje, je požadavek vložen do kanálu middleware ASP.NET Core. Kanál middlewaru zpracovává požadavek a předá ho jako instanci `HttpContext` do logiky aplikace. Odpověď aplikace se předává zpět službě IIS prostřednictvím serveru IIS HTTP. Služba IIS odešle odpověď klientovi, který žádost inicioval.

Vnitroprocesové hostování v rámci procesu je výslovný souhlas pro existující aplikace, ale [dotnet nové](/dotnet/core/tools/dotnet-new) šablony jsou výchozí pro všechny scénáře hostování v rámci procesu pro všechny služby IIS a IIS Express.

`CreateDefaultBuilder` přidá instanci <xref:Microsoft.AspNetCore.Hosting.Server.IServer> voláním metody <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*>, která spustí [CoreCLR](/dotnet/standard/glossary#coreclr) a hostuje aplikaci uvnitř pracovního procesu služby IIS (*W3wp. exe* nebo *IISExpress. exe*). Testy výkonu označují, že hostování aplikace .NET Core v rámci procesu přináší výrazně vyšší propustnost žádostí v porovnání s hostováním aplikací mimo proces a požadavky na proxy serveru na [Kestrel](xref:fundamentals/servers/kestrel) Server.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

> [!NOTE]
> Aplikace publikované jako spustitelný soubor s jedním souborem nejde načíst pomocí modelu hostování v rámci procesu.

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="out-of-process-hosting-model"></a>Model hostování mimo proces

Vzhledem k tomu, že ASP.NET Core aplikace běží v procesu odděleném od pracovního procesu služby IIS, modul ASP.NET Core zpracovává správu procesů. Modul spustí proces pro aplikaci ASP.NET Core, když první požadavek dorazí a restartuje aplikaci, pokud se ukončí nebo dojde k chybě. To je v podstatě stejné chování jako u aplikací, které běží v procesu, které jsou spravovány službou was [(Windows Process Activation Service)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

Následující diagram znázorňuje vztah mezi službou IIS, modulem ASP.NET Core a aplikací hostovanou mimo proces:

![ASP.NET Core modul ve scénáři hostování mimo proces](index/_static/ancm-outofprocess.png)

Požadavky přicházející z webu do ovladače HTTP. sys v režimu jádra. Ovladač směruje požadavky do služby IIS na konfigurovaném portu webu, obvykle 80 (HTTP) nebo 443 (HTTPS). Modul předá požadavky do Kestrel na náhodném portu pro aplikaci, což není port 80 nebo 443.

Modul Určuje port přes proměnnou prostředí při spuštění a rozšíření <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> nakonfiguruje server tak, aby naslouchal `http://localhost:{PORT}`. Budou provedeny další kontroly a požadavky, které nepocházejí z modulu, jsou odmítnuty. Modul nepodporuje předávání HTTPS, takže požadavky se předávají přes protokol HTTP i v případě, že je služba IIS prostřednictvím protokolu HTTPS přijímá.

Po Kestrel žádosti z modulu se požadavek odešle do kanálu middlewaru ASP.NET Core. Kanál middlewaru zpracovává požadavek a předá ho jako instanci `HttpContext` do logiky aplikace. Middleware přidaný integrací služby IIS: aktualizace schématu, vzdálené IP adresy a pathbase pro přesměrování požadavku do Kestrel. Odpověď aplikace se předává zpátky do služby IIS, která ji přenáší zpátky do klienta HTTP, který žádost inicioval.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

ASP.NET Core se dodává se [serverem Kestrel](xref:fundamentals/servers/kestrel), výchozím serverem HTTP pro různé platformy.

Při použití [služby IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) nebo [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)aplikace běží v procesu odděleně od pracovního procesu služby IIS (*mimo proces*) se [serverem Kestrel](xref:fundamentals/servers/index#kestrel).

Vzhledem k tomu, že ASP.NET Core aplikace běží v procesu odděleném od pracovního procesu služby IIS, modul zpracovává správu procesů. Modul spustí proces pro aplikaci ASP.NET Core, když první požadavek dorazí a restartuje aplikaci, pokud se ukončí nebo dojde k chybě. To je v podstatě stejné chování jako u aplikací, které běží v procesu, které jsou spravovány službou was [(Windows Process Activation Service)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

Následující diagram znázorňuje vztah mezi službou IIS, modulem ASP.NET Core a aplikací hostovanou mimo proces:

![Modul ASP.NET Core](index/_static/ancm-outofprocess.png)

Požadavky přicházející z webu do ovladače HTTP. sys v režimu jádra. Ovladač směruje požadavky do služby IIS na konfigurovaném portu webu, obvykle 80 (HTTP) nebo 443 (HTTPS). Modul předá požadavky do Kestrel na náhodném portu pro aplikaci, což není port 80 nebo 443.

Modul Určuje port prostřednictvím proměnné prostředí při spuštění a [Služba IIS Integration middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) nakonfiguruje server tak, aby naslouchal `http://localhost:{port}`. Budou provedeny další kontroly a požadavky, které nepocházejí z modulu, jsou odmítnuty. Modul nepodporuje předávání HTTPS, takže požadavky se předávají přes protokol HTTP i v případě, že je služba IIS prostřednictvím protokolu HTTPS přijímá.

Po Kestrel žádosti z modulu se požadavek odešle do kanálu middlewaru ASP.NET Core. Kanál middlewaru zpracovává požadavek a předá ho jako instanci `HttpContext` do logiky aplikace. Middleware přidaný integrací služby IIS: aktualizace schématu, vzdálené IP adresy a pathbase pro přesměrování požadavku do Kestrel. Odpověď aplikace se předává zpátky do služby IIS, která ji přenáší zpátky do klienta HTTP, který žádost inicioval.

`CreateDefaultBuilder` nakonfiguruje server [Kestrel](xref:fundamentals/servers/kestrel) jako webový server a povolí integraci služby IIS konfigurací základní cesty a portu pro [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module).

Modul ASP.NET Core generuje dynamický port, který se přiřadí back-end procesu. `CreateDefaultBuilder` volá metodu <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>. `UseIISIntegration` konfiguruje Kestrel k naslouchání na dynamickém portu na IP adrese localhost (`127.0.0.1`). Pokud je dynamický port 1234, Kestrel naslouchá na `127.0.0.1:1234`. Tato konfigurace nahrazuje jiné konfigurace adres URL, které poskytl:

* `UseUrls`
* [Rozhraní API pro naslouchání Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [Konfigurace](xref:fundamentals/configuration/index) (nebo [příkazového řádku – možnost adresy URL](xref:fundamentals/host/web-host#override-configuration))

Při použití modulu se nevyžadují volání `UseUrls` nebo Kestrel rozhraní API `Listen`. Pokud je zavolána hodnota `UseUrls` nebo `Listen`, Kestrel naslouchá na portu určeném pouze při spuštění aplikace bez služby IIS.

Další informace o modelech hostování v procesu a předběžném procesu naleznete v tématu [ASP.NET Core modul](xref:host-and-deploy/aspnet-core-module) a [ASP.NET Core konfigurace modulu](xref:host-and-deploy/aspnet-core-module).

::: moniker-end

Pokyny ke konfiguraci ASP.NET Core modulu najdete v tématu <xref:host-and-deploy/aspnet-core-module>.

Další informace o hostování najdete v tématu [hostitel v ASP.NET Core](xref:fundamentals/index#host).

## <a name="application-configuration"></a>Konfigurace aplikace

### <a name="enable-the-iisintegration-components"></a>Povolit komponenty IISIntegration

Typická *program.cs* volá <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> pro zahájení nastavování hostitele, který umožňuje integraci se službou IIS:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

### <a name="iis-options"></a>Možnosti služby IIS

::: moniker range=">= aspnetcore-2.2"

**Model hostování v procesu**

Pokud chcete nakonfigurovat možnosti serveru IIS, zahrňte do <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> konfiguraci služby pro <xref:Microsoft.AspNetCore.Builder.IISServerOptions>. Následující příklad zakáže AutomaticAuthentication:

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

| Možnost                         | Výchozí | Nastavením |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | Pokud `true`, nastaví server IIS `HttpContext.User` ověřený [ověřováním systému Windows](xref:security/authentication/windowsauth). Pokud `false`, Server poskytuje identitu pro `HttpContext.User` a reaguje na výzvy, které jsou explicitně požadovány `AuthenticationScheme`. Ověřování systému Windows musí být ve službě IIS povoleno, aby bylo možné funkci `AutomaticAuthentication`. Další informace najdete v tématu [ověřování systému Windows](xref:security/authentication/windowsauth). |
| `AuthenticationDisplayName`    | `null`  | Nastaví zobrazovaný název, který se zobrazí uživatelům na přihlašovacích stránkách. |
| `AllowSynchronousIO`           | `false` | Zda je povolena synchronní v/v pro `HttpContext.Request` a `HttpContext.Response`. |
| `MaxRequestBodySize`           | `30000000`  | Získá nebo nastaví maximální velikost textu požadavku pro `HttpRequest`. Všimněte si, že služba IIS má limit `maxAllowedContentLength`, který bude zpracován před `MaxRequestBodySize` nastavenou v `IISServerOptions`. Změna `MaxRequestBodySize` nebude mít vliv na `maxAllowedContentLength`. Chcete-li zvýšit `maxAllowedContentLength`, přidejte položku do *souboru Web. config* a nastavte `maxAllowedContentLength` na vyšší hodnotu. Další podrobnosti najdete v tématu [Konfigurace](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration). |

**Model hostování mimo proces**

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| Možnost                         | Výchozí | Nastavením |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | Pokud `true`, nastaví server IIS `HttpContext.User` ověřený [ověřováním systému Windows](xref:security/authentication/windowsauth). Pokud `false`, Server poskytuje identitu pro `HttpContext.User` a reaguje na výzvy, které jsou explicitně požadovány `AuthenticationScheme`. Ověřování systému Windows musí být ve službě IIS povoleno, aby bylo možné funkci `AutomaticAuthentication`. Další informace najdete v tématu [ověřování systému Windows](xref:security/authentication/windowsauth). |
| `AuthenticationDisplayName`    | `null`  | Nastaví zobrazovaný název, který se zobrazí uživatelům na přihlašovacích stránkách. |

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

**Model hostování mimo proces**

::: moniker-end

Pokud chcete nakonfigurovat možnosti služby IIS, zahrňte do <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> konfiguraci služby pro <xref:Microsoft.AspNetCore.Builder.IISOptions>. Následující příklad zabrání aplikaci v naplnění `HttpContext.Connection.ClientCertificate`:

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| Možnost                         | Výchozí | Nastavením |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | Pokud `true`, [Služba IIS Integration middleware](#enable-the-iisintegration-components) nastaví `HttpContext.User` ověřený [ověřováním systému Windows](xref:security/authentication/windowsauth). Pokud `false`, middleware poskytuje pouze identitu pro `HttpContext.User` a reaguje na výzvy, které jsou explicitně požadovány `AuthenticationScheme`. Ověřování systému Windows musí být ve službě IIS povoleno, aby bylo možné funkci `AutomaticAuthentication`. Další informace najdete v tématu [ověřování systému Windows](xref:security/authentication/windowsauth) . |
| `AuthenticationDisplayName`    | `null`  | Nastaví zobrazovaný název, který se zobrazí uživatelům na přihlašovacích stránkách. |
| `ForwardClientCertificate`     | `true`  | Pokud je k dispozici `true` a hlavička žádosti `MS-ASPNETCORE-CLIENTCERT`, naplní se `HttpContext.Connection.ClientCertificate`. |

### <a name="proxy-server-and-load-balancer-scenarios"></a>Scénáře proxy serveru a nástroje pro vyrovnávání zatížení

[Služba IIS Integration middleware](#enable-the-iisintegration-components), která konfiguruje middleware předávaných hlaviček, a modul ASP.NET Core je nakonfigurován tak, aby přenesl schéma (http/https) a vzdálenou IP adresu, kam pochází požadavek. Pro aplikace hostované za dalšími proxy servery a nástroji pro vyrovnávání zatížení může být vyžadována další konfigurace. Další informace najdete v tématu [konfigurace ASP.NET Core pro práci se servery proxy a nástroji pro vyrovnávání zatížení](xref:host-and-deploy/proxy-load-balancer).

### <a name="webconfig-file"></a>soubor Web. config

Soubor *Web. config* konfiguruje [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module). Vytváření, transformace a publikování souboru *Web. config* je zpracováno cílem MSBuild (`_TransformWebConfig`) při publikování projektu. Tento cíl je k dispozici v rámci cílů web SDK (`Microsoft.NET.Sdk.Web`). Sada SDK je nastavena v horní části souboru projektu:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Pokud soubor *Web. config* není v projektu přítomen, je vytvořen soubor se správným *processPath* a *argumenty* pro konfiguraci [modulu ASP.NET Core](xref:host-and-deploy/aspnet-core-module) a přesunutí do [publikovaného výstupu](xref:host-and-deploy/directory-structure).

Pokud je soubor *Web. config* přítomen v projektu, soubor je transformován se správným *processPath* a *argumenty* pro konfiguraci modulu ASP.NET Core a přesunutí do publikovaného výstupu. Transformace nemění nastavení konfigurace služby IIS v souboru.

Soubor *Web. config* může poskytovat další nastavení konfigurace služby IIS, která ovládají aktivní moduly IIS. Informace o modulech služby IIS, které jsou schopné zpracovávat požadavky s ASP.NET Core aplikacemi, najdete v tématu [IIS modules](xref:host-and-deploy/iis/modules) .

Chcete-li webové sadě SDK zabránit ve transformaci souboru *Web. config* , použijte vlastnost **\<IsTransformWebConfigDisabled >** v souboru projektu:

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

Při zakazování webové sady SDK z transformace souboru by měl vývojář *processPath* a *argumenty* ručně nastavit. Další informace najdete v referenčních informacích k [konfiguraci modulu ASP.NET Core](xref:host-and-deploy/aspnet-core-module).

### <a name="webconfig-file-location"></a>umístění souboru Web. config

Aby bylo možné správně nastavit [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) , musí být soubor *Web. config* přítomen v [kořenové cestě obsahu](xref:fundamentals/index#content-root) (obvykle v základní cestě aplikace) nasazené aplikace. Toto je stejné umístění jako fyzická cesta k webu poskytované službě IIS. Soubor *Web. config* je vyžadován v kořenu aplikace, aby bylo možné publikovat více aplikací pomocí nasazení webu.

Citlivé soubory existují na fyzické cestě aplikace, například *\<assembly >. runtimeconfig. JSON*, *\<assembly >. XML* (komentáře dokumentace XML) a *\<assembly >. DEPS. JSON*. Když je přítomen soubor *Web. config* a lokalita se spouští normálně, služba IIS tyto citlivé soubory po vyžádání neobsluhuje. Pokud soubor *Web. config* chybí, je nesprávně pojmenovaný nebo nemůže nakonfigurovat lokalitu pro normální spuštění, služba IIS může obsluhovat citlivé soubory veřejně.

**Soubor *Web. config* musí být současně přítomen v nasazení, správně pojmenován a může nakonfigurovat lokalitu pro normální spuštění. Nikdy neodstraňujte soubor *Web. config* z produkčního nasazení.**

### <a name="transform-webconfig"></a>Transformace souboru web.config

Pokud potřebujete transformovat *Web. config* při publikování (například nastavit proměnné prostředí na základě konfigurace, profilu nebo prostředí), přečtěte si téma <xref:host-and-deploy/iis/transform-webconfig>.

## <a name="iis-configuration"></a>Konfigurace služby IIS

**Operační systémy Windows Server**

Povolte roli serveru **webový server (IIS)** a vytvořte služby rolí.

1. Použijte průvodce **přidáním rolí a funkcí** z nabídky **Správa** nebo odkazu v **Správce serveru**. V kroku **role serveru** zaškrtněte políčko **webový server (IIS)** .

   ![V kroku vybrat role serveru je vybraná role Webový server IIS.](index/_static/server-roles-ws2016.png)

1. Po kroku **funkce** se krok **služby rolí** načte pro webový server (IIS). Vyberte požadované služby role IIS nebo přijměte výchozí poskytnuté služby rolí.

   ![Výchozí služby rolí se vyberou v kroku vybrat služby rolí.](index/_static/role-services-ws2016.png)

   **Ověřování systému Windows (volitelné)**  
   Chcete-li povolit ověřování systému Windows, rozbalte následující uzly: **webový Server** > **zabezpečení**. Vyberte funkci **ověřování systému Windows** . Další informace najdete v tématu [ověřování windows @no__t – 1windowsAuthentication >](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) a [Konfigurace ověřování systému Windows](xref:security/authentication/windowsauth).

   **WebSockets (volitelné)**  
   Objekty WebSocket jsou podporované ASP.NET Core 1,1 nebo novějším. Chcete-li povolit objekty WebSockets, rozbalte následující uzly: **webový Server** > **vývoj aplikací**. Vyberte funkci **protokolu WebSocket** . Další informace najdete v tématu [objekty WebSockets](xref:fundamentals/websockets).

1. Chcete-li nainstalovat roli a služby webového serveru, postupujte podle kroků pro **potvrzení** . Po instalaci role **webový server (IIS)** není nutné restartovat server nebo službu IIS.

**Desktopové operační systémy Windows**

Povolte **konzolu pro správu služby IIS** a **webové služby**.

1. Přejděte na **Ovládací panely** > **programy** >  programy**a funkce** > **zapnout nebo vypnout funkce systému Windows** (levá strana obrazovky).

1. Otevřete uzel **Internetová informační služba** . Otevřete uzel **Nástroje webové správy** .

1. Zaškrtněte políčko pro **konzolu pro správu služby IIS**.

1. Zaškrtněte políčko u **webových služeb**.

1. Přijměte výchozí funkce pro **webové služby** nebo upravte funkce služby IIS.

   **Ověřování systému Windows (volitelné)**  
   Ověřování systému Windows povolíte tak, že rozbalíte následující uzly:**zabezpečení** **webové služby** > . Vyberte funkci **ověřování systému Windows** . Další informace najdete v tématu [ověřování windows @no__t – 1windowsAuthentication >](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) a [Konfigurace ověřování systému Windows](xref:security/authentication/windowsauth).

   **WebSockets (volitelné)**  
   Objekty WebSocket jsou podporované ASP.NET Core 1,1 nebo novějším. Chcete-li povolit objekty WebSockets, rozbalte následující uzly: **webové služby** > **funkce pro vývoj aplikací**. Vyberte funkci **protokolu WebSocket** . Další informace najdete v tématu [objekty WebSockets](xref:fundamentals/websockets).

1. Pokud instalace služby IIS vyžaduje restart, restartujte systém.

![V rámci funkcí systému Windows jsou vybrána Konzola pro správu služby IIS a webové služby.](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a>Instalace hostující sady .NET Core

Nainstalujte *hostující sadu .NET Core* do hostitelského systému. Svazek nainstaluje modul runtime .NET Core, knihovnu .NET Core a [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module). Modul umožňuje, aby aplikace ASP.NET Core běžely za službou IIS.

> [!IMPORTANT]
> Pokud je hostující sada nainstalována před službou IIS, je nutné opravit instalaci sady prostředků. Spusťte znovu instalační program hostující sady po instalaci služby IIS.
>
> Pokud se hostující sada nainstaluje po instalaci 64 (x64) verze .NET Core, můžou se zdát, že sady SDK chybí ([nezjistily se žádné sady .NET Core SDK](xref:test/troubleshoot#no-net-core-sdks-were-detected)). Chcete-li tento problém vyřešit, přečtěte si téma <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.

### <a name="direct-download-current-version"></a>Přímé stažení (aktuální verze)

Stáhněte instalační program pomocí následujícího odkazu:

[Aktuální instalační program sady hostujících sad .NET Core (přímé stahování)](https://www.microsoft.com/net/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a>Starší verze instalačního programu

Chcete-li získat starší verzi instalačního programu:

1. Přejděte do [archivu stahování v rozhraní .NET](https://www.microsoft.com/net/download/archives).
1. V části **.NET Core**vyberte verzi .NET Core.
1. Ve sloupci **Spustit aplikace – modul runtime** vyhledejte řádek požadované verze modulu runtime .NET Core.
1. Stáhněte instalační program pomocí odkazu **& hostování sady prostředků modulu runtime** .

> [!WARNING]
> Některé instalační programy obsahují verze vydaných verzí, které dosáhly svého konce životnosti (konce řádku) a které už nejsou podporovány společností Microsoft. Další informace najdete v tématu [zásady podpory](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

### <a name="install-the-hosting-bundle"></a>Instalace hostitelského balíčku

1. Spusťte instalační program na serveru. Při spuštění instalačního programu z příkazového prostředí správce jsou k dispozici následující parametry:

   * `OPT_NO_ANCM=1` &ndash; přeskočí instalace modulu ASP.NET Core.
   * `OPT_NO_RUNTIME=1` &ndash; přeskočí instalace modulu runtime .NET Core.
   * `OPT_NO_SHAREDFX=1` &ndash; Přeskočit instalaci sdílené architektury ASP.NET (modul runtime ASP.NET).
   * `OPT_NO_X86=1` &ndash; přeskočí instalace modulu runtime x86. Tento parametr použijte, pokud víte, že nebudete hostovat 32 aplikace. Pokud existuje možnost, že v budoucnosti budete hostovat jak 32, tak i 64 aplikace, tento parametr nepoužívejte a nainstalujete oba moduly runtime.
   * `OPT_NO_SHARED_CONFIG_CHECK=1` &ndash; zakažte kontrolu použití sdílené konfigurace služby IIS, pokud je sdílená konfigurace (*ApplicationHost. config*) ve stejném počítači jako instalace služby IIS. *K dispozici jenom pro ASP.NET Core 2,2 nebo novější instalační programy hostujících prostředků.* Další informace najdete v tématu <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.
1. Restartujte systém nebo spusťte příkaz **net stop/y**, následovaný příkazem **net start w3svc** z příkazového prostředí. Restartování služby IIS zabere změny v systémové cestě, což je proměnná prostředí vytvořená instalačním programem.

> [!NOTE]
> Informace o sdílené konfiguraci služby IIS najdete v tématu [modul ASP.NET Core se sdílenou konfigurací služby IIS](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a>Při publikování v aplikaci Visual Studio nainstalovat Nasazení webu

Při nasazování aplikací na servery s [nasazení webu](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)nainstalujte na server nejnovější verzi nasazení webu. Chcete-li nainstalovat Nasazení webu, použijte [instalační program webové platformy (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) nebo si instalační program Získejte přímo z webu [služby Stažení softwaru](https://www.microsoft.com/download/details.aspx?id=43717). Upřednostňovanou metodou je použití WebPI. WebPI nabízí samostatnou instalaci a konfiguraci pro poskytovatele hostingu.

## <a name="create-the-iis-site"></a>Vytvoření webu služby IIS

1. V hostitelském systému vytvořte složku, která bude obsahovat publikované složky a soubory aplikace. V následujícím kroku je jako fyzická cesta k aplikaci služba IIS poskytována jako cesta k této složce. Další informace o složce nasazení aplikace a rozložení souborů najdete v tématu <xref:host-and-deploy/directory-structure>.

1. Ve Správci služby IIS otevřete uzel serveru na panelu **připojení** . Klikněte pravým tlačítkem na složku **weby** . V místní nabídce vyberte **Přidat web** .

1. Zadejte **název lokality** a nastavte **fyzickou cestu** ke složce pro nasazení aplikace. Zadejte konfiguraci **vazby** a vytvořte web výběrem **OK**:

   ![Zadejte název lokality, fyzickou cestu a název hostitele v kroku přidat web.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > Zástupné vazby nejvyšší úrovně (`http://*:80/` a `http://+:80`) **by se neměly používat** . Vazby zástupných znaků nejvyšší úrovně můžou aplikaci otevřít pro slabá místa zabezpečení. To platí pro silné i slabé zástupné znaky. Místo zástupných znaků použijte explicitní názvy hostitelů. Vazba zástupných znaků subdomény (například `*.mysub.com`) nemá toto bezpečnostní riziko, pokud ovládáte celou nadřazenou doménu (na rozdíl od `*.com`, která je zranitelná). Další informace najdete v [části rfc7230 část-5,4](https://tools.ietf.org/html/rfc7230#section-5.4) .

1. V uzlu serveru vyberte **fondy aplikací**.

1. Klikněte pravým tlačítkem myši na fond aplikací webu a v místní nabídce vyberte **základní nastavení** .

1. V okně **Upravit fond aplikací** nastavte **verzi .NET CLR** na **bez spravovaného kódu**:

   ![Pro verzi .NET CLR nenastavte žádný spravovaný kód.](index/_static/edit-apppool-ws2016.png)

    ASP.NET Core běží v samostatném procesu a spravuje modul runtime. ASP.NET Core nespoléhá na načtení CLR desktopu (.NET CLR) &mdash;the Core Language Runtime (CoreCLR) pro .NET Core se spustí pro hostování aplikace v pracovním procesu. Nastavení **verze .NET CLR** na **žádný spravovaný kód** není volitelné, ale doporučuje se.

1. *ASP.NET Core 2,2 nebo novější*: 64 u samostatného [nasazeného nasazení](/dotnet/core/deploying/#self-contained-deployments-scd) (x64), které používá [model hostování v rámci procesu](#in-process-hosting-model), zakažte fond aplikací pro procesy 32 (x86).

   V bočním panelu **Akce** Správce služby IIS > **fondy aplikací**vyberte možnost **nastavit výchozí hodnoty fondu aplikací** nebo **Upřesnit nastavení**. Vyhledejte **možnost povolit 32-bit aplikací** a nastavte hodnotu na `False`. Toto nastavení nemá vliv na aplikace nasazené pro [hostování mimo proces](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).

1. Potvrďte, že identita modelu procesu má správná oprávnění.

   Pokud je výchozí identita fondu aplikací (**model procesu** > **Identita**) změněna z **ApplicationPoolIdentity** na jinou identitu, ověřte, že Nová identita má požadovaná oprávnění pro přístup ke složce aplikace, databázi, a další požadované prostředky. Například fond aplikací vyžaduje přístup pro čtení a zápis ke složkám, kde aplikace čte a zapisuje soubory.

**Konfigurace ověřování systému Windows (volitelné)**  
Další informace najdete v tématu [Konfigurace ověřování systému Windows](xref:security/authentication/windowsauth).

## <a name="deploy-the-app"></a>Nasazení aplikace

Nasaďte aplikaci do složky **fyzické cesty** služby IIS, která byla navázána v části [Vytvoření webu služby IIS](#create-the-iis-site) . [Nasazení webu](/iis/publish/using-web-deploy/introduction-to-web-deploy) je doporučeným mechanismem pro nasazení, ale existuje několik možností pro přesun aplikace ze složky *publikování* projektu do složky pro nasazení hostitelského systému.

### <a name="web-deploy-with-visual-studio"></a>Nasazení webu se sadou Visual Studio

Informace o tom, jak vytvořit profil publikování pro použití s Nasazení webu, najdete v tématu [publikační profily sady Visual Studio pro ASP.NET Core nasazení aplikací](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) . Pokud poskytovatel hostingu poskytuje profil publikování nebo podporu pro jeho vytvoření, Stáhněte si jeho profil a importujte ho pomocí dialogového okna pro **publikování** sady Visual Studio:

![Stránka publikovat dialog](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a>Nasazení webu mimo Visual Studio

[Nasazení webu](/iis/publish/using-web-deploy/introduction-to-web-deploy) lze použít také mimo sadu Visual Studio z příkazového řádku. Další informace najdete v tématu [Nástroj pro nasazení webu](/iis/publish/using-web-deploy/use-the-web-deployment-tool).

### <a name="alternatives-to-web-deploy"></a>Alternativy k Nasazení webu

K přesunu aplikace do hostitelského systému, jako je ruční kopírování, [xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy)nebo [PowerShell](/powershell/), použijte libovolný z několika způsobů.

Další informace o nasazení ASP.NET Core do služby IIS najdete v části [prostředky nasazení pro správce služby IIS](#deployment-resources-for-iis-administrators) .

## <a name="browse-the-website"></a>Procházet web

Po nasazení aplikace do hostitelského systému vytvořte žádost jednomu z veřejných koncových bodů aplikace.

V následujícím příkladu je lokalita svázána s **názvem hostitele** služby IIS `www.mysite.com` na **portu** `80`. Je učiněn požadavek na `http://www.mysite.com`:

![Prohlížeč Microsoft Edge načetl spouštěcí stránku služby IIS.](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a>Uzamčené soubory nasazení

Soubory ve složce pro nasazení jsou zamčené, když je aplikace spuštěná. Uzamčené soubory nejde během nasazování přepsat. Chcete-li uvolnit uzamčené soubory v nasazení, zastavte fond aplikací pomocí **jednoho** z následujících přístupů:

* Použijte Nasazení webu a odkaz `Microsoft.NET.Sdk.Web` v souboru projektu. Soubor *App_offline. htm* je umístěn v kořenovém adresáři adresáře webové aplikace. Když je soubor přítomen, modul ASP.NET Core aplikaci řádně ukončí a během nasazování zachová soubor *App_offline. htm* . Další informace najdete v referenčních informacích k [konfiguraci modulu ASP.NET Core](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).
* Ručně Zastavte fond aplikací ve Správci služby IIS na serveru.
* Použití prostředí PowerShell k vyřazení *App_offline. htm* (vyžaduje PowerShell 5 nebo novější):

  ```PowerShell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a>Ochrana dat

[Sada ASP.NET Core Data Protection Stack](xref:security/data-protection/introduction) je používána několika ASP.NET Core [middlewary](xref:fundamentals/middleware/index), včetně middlewaru používaného při ověřování. I v případě, že rozhraní API ochrany dat není voláno uživatelským kódem, je třeba nakonfigurovat ochranu dat pomocí skriptu nasazení nebo v uživatelském kódu, aby bylo možné vytvořit trvalé úložiště kryptografických [klíčů](xref:security/data-protection/implementation/key-management). Pokud ochrana dat není nakonfigurovaná, klíče se uchovávají v paměti a při restartování aplikace se zahodí.

Pokud se klíčového prstence při restartu aplikace uloží do paměti:

* Všechny ověřovací tokeny založené na souborech cookie jsou neověřené. 
* Uživatelé se musí znovu přihlásit na svůj další požadavek. 
* Data chráněná pomocí Key ringu už nebude možné dešifrovat. To může zahrnovat [CSRF tokeny](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) a [ASP.NET Core soubory cookie TempData MVC](xref:fundamentals/app-state#tempdata).

Pokud chcete v rámci služby IIS nakonfigurovat ochranu dat a zachovat přitom klíčového prstence, použijte **některý** z následujících přístupů:

* **Vytvoření klíčů registru ochrany dat**

  Klíče ochrany dat, které používá aplikace ASP.NET Core, jsou uložené v registru externě pro aplikace. Pokud chcete zachovat klíče pro danou aplikaci, vytvořte klíče registru pro fond aplikací.

  Pro samostatnou instalaci služby IIS, která není součástí webfarmu, se dá [skript PowerShellu pro ochranu dat provision-AutoGenKeys. ps1](https://github.com/aspnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) použít pro každý fond aplikací, který se používá v aplikaci ASP.NET Core. Tento skript vytvoří klíč registru v registru HKLM, který je přístupný jenom pro účet pracovního procesu fondu aplikací aplikace. Klíče jsou v klidovém stavu zašifrované pomocí rozhraní DPAPI s klíčem celého počítače.

  Ve scénářích webové farmy může být aplikace nakonfigurovaná tak, aby používala cestu UNC k uložení svého prstence s klíčem pro ochranu dat. Ve výchozím nastavení nejsou klíče ochrany dat šifrované. Ujistěte se, že oprávnění k souborům pro sdílenou síťovou složku jsou omezená na účet systému Windows, pod kterým je aplikace spuštěná. K ochraně neaktivních klíčů je možné použít certifikát x509. Vezměte v úvahu mechanismus, který umožní uživatelům odeslat certifikáty: Umístěte certifikáty do důvěryhodného úložiště certifikátů uživatele a ujistěte se, že jsou k dispozici na všech počítačích, kde je aplikace uživatele spuštěna. Podrobnosti najdete v tématu [Konfigurace ochrany ASP.NET Core dat](xref:security/data-protection/configuration/overview) .

* **Konfigurace fondu aplikací služby IIS pro načtení profilu uživatele**

  Toto nastavení se nachází v části **model procesu** v části **Rozšířená nastavení** fondu aplikací. Nastavte **načíst profil uživatele** na `True`. Pokud je nastavena hodnota `True`, klíče jsou uloženy v adresáři profilu uživatele a chráněny pomocí rozhraní DPAPI s klíčem specifickým pro uživatelský účet. Klíče jsou uchovány ve složce *% localappdata%/ASP.NET/DataProtection-Keys* .

  Musí být povolený i [atribut setProfileEnvironment](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) fondu aplikací. Výchozí hodnota `setProfileEnvironment` je `true`. V některých scénářích (například v operačním systému Windows) je `setProfileEnvironment` nastavená na `false`. Pokud se klíče neukládají v adresáři profilu uživatele podle očekávání:

  1. Přejděte do složky *% windir%/system32/Inetsrv/config* .
  1. Otevřete soubor *ApplicationHost. config* .
  1. Vyhledejte element `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>`.
  1. Potvrďte, že atribut `setProfileEnvironment` není přítomen, což nastaví hodnotu `true` nebo explicitně nastaví hodnotu atributu na `true`.

* **Použití systému souborů jako úložiště pro Key Ring**

  Upravte kód aplikace tak, aby [používal systém souborů jako úložiště ve službě Key Ring](xref:security/data-protection/configuration/overview). K ochraně služby Key Ring použijte certifikát x509 a ujistěte se, že certifikát je důvěryhodný certifikát. Pokud je certifikát podepsaný svým držitelem, umístěte certifikát do důvěryhodného kořenového úložiště.

  Při použití služby IIS ve webové farmě:

  * Použijte sdílenou složku, ke které mají přístup všechny počítače.
  * Nasaďte do každého počítače certifikát x509. Konfigurace [ochrany dat v kódu](xref:security/data-protection/configuration/overview).

* **Nastavení zásad pro ochranu dat na úrovni počítače**

  Systém ochrany dat má omezená podpora pro nastavení výchozích [zásad](xref:security/data-protection/configuration/machine-wide-policy) pro všechny aplikace, které využívají rozhraní API pro ochranu dat. Další informace najdete v tématu <xref:security/data-protection/introduction>.

## <a name="virtual-directories"></a>Virtuální adresáře

Aplikace ASP.NET Core nepodporují [virtuální adresáře služby IIS](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) . Aplikace může být hostována jako [dílčí aplikace](#sub-applications).

## <a name="sub-applications"></a>Dílčí aplikace

Aplikace ASP.NET Core může být hostovaná jako [podaplikace služby IIS (dílčí aplikace)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications). Cesta k dílčí aplikaci se stala součástí adresy URL kořenové aplikace.

::: moniker range="< aspnetcore-2.2"

Dílčí aplikace by neměla obsahovat modul ASP.NET Core jako obslužnou rutinu. Pokud se modul přidá jako obslužná rutina v souboru *Web. config* dílčí aplikace 500,19, při pokusu o procházení dílčí aplikace se obdrží *interní chyba serveru* odkazující na chybný konfigurační soubor.

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

::: moniker-end

Propojení statických prostředků v rámci dílčí aplikace by měla používat notaci s vlnovkou vlnovkou (`~/`). Znak tildy vlnovkou spustí [pomocnou nápovědu](xref:mvc/views/tag-helpers/intro) k předřazení pathbase dílčí aplikace na vykreslený relativní odkaz. V případě dílčí aplikace na pozici `/subapp_path` se obrázek propojený s `src="~/image.png"` vykresluje jako `src="/subapp_path/image.png"`. Middleware statických souborů kořenové aplikace nezpracovává požadavek na statický soubor. Požadavek zpracovává middleware statických souborů v dílčí aplikaci.

Pokud je atribut `src` statického prostředku nastavený na absolutní cestu (například `src="/image.png"`), odkaz se vykreslí bez pathbase dílčí aplikace. Middleware statických souborů v kořenové aplikaci se pokusí o poskytování assetu z [kořenového adresáře webu](xref:fundamentals/index#web-root)kořenové aplikace, což má za následek *404 – nenalezené* odpovědi, pokud není k dispozici statický prostředek z kořenové aplikace.

Hostování aplikace ASP.NET Core jako dílčí aplikace v jiné aplikaci ASP.NET Core:

1. Vytvořte fond aplikací pro dílčí aplikaci. Nastavte **verzi .NET CLR** na **žádný spravovaný kód** , protože základní modul CLR (Common Language Runtime) pro .NET Core se spouští k hostování aplikace v pracovním procesu, ne CLR Desktop (.NET CLR).

1. Přidejte kořenovou lokalitu ve Správci služby IIS pomocí dílčí aplikace do složky v kořenové lokalitě.

1. Klikněte pravým tlačítkem na složku dílčí aplikace ve Správci služby IIS a vyberte **převést na aplikaci**.

1. V dialogovém okně **Přidat aplikaci** použijte pro **fond aplikací** tlačítko **Vybrat** , abyste přiřadili fond aplikací, který jste vytvořili pro dílčí aplikaci. Vyberte **OK**.

Přiřazení samostatného fondu aplikací k dílčí aplikaci je požadavek při použití modelu hostování v rámci procesu.

Další informace o modelu hostování v rámci procesu a konfiguraci modulu ASP.NET Core naleznete v tématu <xref:host-and-deploy/aspnet-core-module> a <xref:host-and-deploy/aspnet-core-module>.

## <a name="configuration-of-iis-with-webconfig"></a>Konfigurace služby IIS pomocí souboru Web. config

Konfigurace služby IIS má vliv na @no__t 0 v souboru *Web. config* pro scénáře služby IIS, které jsou funkční pro ASP.NET Core aplikace s modulem ASP.NET Core. Například konfigurace služby IIS je funkční pro dynamickou kompresi. Pokud je služba IIS nakonfigurovaná na úrovni serveru, aby používala dynamickou kompresi, element `<urlCompression>` v souboru *Web. config* aplikace ho může zakázat pro ASP.NET Core aplikaci.

Další informace najdete v [Referenční příručce ke konfiguraci pro \<System. webserver >](/iis/configuration/system.webServer/), [odkaz na konfiguraci modulu ASP.NET Core](xref:host-and-deploy/aspnet-core-module)a [moduly IIS s ASP.NET Core](xref:host-and-deploy/iis/modules). Chcete-li nastavit proměnné prostředí pro jednotlivé aplikace spuštěné v izolovaných fondech aplikací (podporované pro IIS 10,0 nebo novější), přečtěte si část *příkaz Appcmd. exe* v tématu [proměnné prostředí \<environmentVariables >](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) v referenci služby IIS. nápovědě.

## <a name="configuration-sections-of-webconfig"></a>Konfigurační oddíly souboru Web. config

Konfigurační oddíly aplikací ASP.NET 4. x v *souboru Web. config* nepoužívá aplikace ASP.NET Core pro konfiguraci:

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

Aplikace ASP.NET Core jsou nakonfigurovány pomocí jiných poskytovatelů konfigurace. Další informace najdete v tématu [Konfigurace](xref:fundamentals/configuration/index).

## <a name="application-pools"></a>Fondy aplikací

::: moniker range=">= aspnetcore-2.2"

Izolaci fondu aplikací určuje model hostování:

* Hostování v procesu @no__t – 0 aplikace se vyžadují ke spuštění v samostatných fondech aplikací.
* Hostování mimo procesy @no__t – 0 doporučujeme vzájemně izolovat aplikace tím, že spustíte každou aplikaci ve svém vlastním fondu aplikací.

Dialogové okno **Přidat web** do služby IIS je ve výchozím nastavení nastaveno na jeden fond aplikací na aplikaci. Když je zadaný **název lokality** , text se automaticky přenese do textového pole **fondu aplikací** . Po přidání webu se vytvoří nový fond aplikací s názvem lokality.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Při hostování více webů na serveru doporučujeme vzájemně izolovat aplikace tím, že spustíte každou aplikaci ve svém vlastním fondu aplikací. Do této konfigurace se nastaví dialogové okno **Přidat web** do služby IIS. Když je zadaný **název lokality** , text se automaticky přenese do textového pole **fondu aplikací** . Po přidání webu se vytvoří nový fond aplikací s názvem lokality.

::: moniker-end

## <a name="application-pool-identity"></a>Identita fondu aplikací

Účet identity fondu aplikací umožňuje aplikaci běžet v rámci jedinečného účtu, aniž by bylo nutné vytvářet a spravovat domény nebo místní účty. Ve službě IIS 8,0 nebo novější služba pracovní proces správce služby IIS (WAS) vytvoří virtuální účet s názvem nového fondu aplikací a ve výchozím nastavení spustí pracovní procesy fondu aplikací v rámci tohoto účtu. V konzole pro správu služby IIS v části **Upřesnit nastavení** fondu aplikací zajistěte, aby byla **Identita** nastavená na použití **ApplicationPoolIdentity**:

![Dialogové okno Upřesnit nastavení fondu aplikací](index/_static/apppool-identity.png)

Proces správy služby IIS vytvoří v systému zabezpečení systému Windows zabezpečený identifikátor s názvem fondu aplikací. Prostředky je možné zabezpečit pomocí této identity. Tato identita ale není skutečným uživatelským účtem a nezobrazuje se v konzole pro správu uživatelů Windows.

Pokud pracovní proces služby IIS vyžaduje zvýšený přístup k aplikaci, upravte seznam Access Control (ACL) pro adresář obsahující aplikaci:

1. Otevřete Průzkumníka Windows a přejděte do adresáře.

1. Klikněte pravým tlačítkem na adresář a vyberte **vlastnosti**.

1. Na kartě **zabezpečení** vyberte tlačítko **Upravit** a pak klikněte na tlačítko **Přidat** .

1. Vyberte tlačítko **umístění** a ujistěte se, že je vybraný systém.

1. Do pole **Zadejte názvy objektů pro výběr oblasti zadejte do pole název objektu** **IIS AppPool @ no__t-1 < > APP_POOL_NAME** . Vyberte tlačítko pro **kontrolu názvů** . Pro službu *DefaultAppPool* ověřte názvy pomocí **služby IIS AppPool\DefaultAppPool**. Když je vybráno tlačítko pro **kontrolu názvů** , hodnota **DefaultAppPool** je uvedena v oblasti názvy objektů. Není možné zadat název fondu aplikací přímo do oblasti názvy objektů. Při kontrole názvu objektu použijte formát **IIS AppPool @ no__t-1 < app_pool_name >** .

   ![Dialogová okna pro výběr uživatelů nebo skupin pro složku aplikace: název fondu aplikací "DefaultAppPool" se v oblasti názvů objektů připojí k objektu IIS AppPool @ no__t-0 před výběrem příkazu "kontrolovat jména".](index/_static/select-users-or-groups-1.png)

1. Vyberte **OK**.

   ![Dialogová okna pro výběr uživatelů nebo skupin pro složku aplikace: po výběru možnosti kontrolovat názvy se v oblasti názvy objektů zobrazí název objektu DefaultAppPool.](index/_static/select-users-or-groups-2.png)

1. Oprávnění ke čtení &amp; by měla být ve výchozím nastavení udělena. V případě potřeby zadejte další oprávnění.

Přístup se dá taky udělit na příkazovém řádku pomocí nástroje **Icacls** . Pomocí příkazu *DefaultAppPool* jako příkladu se používá následující příkaz:

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

Další informace najdete v tématu [Icacls](/windows-server/administration/windows-commands/icacls) .

## <a name="http2-support"></a>Podpora HTTP/2

::: moniker range=">= aspnetcore-2.2"

[Http/2](https://httpwg.org/specs/rfc7540.html) se podporuje s ASP.NET Core v následujících scénářích nasazení služby IIS:

* V procesu
  * Windows Server 2016/Windows 10 nebo novější; IIS 10 nebo novější
  * Připojení TLS 1,2 nebo novější
* Mimo proces
  * Windows Server 2016/Windows 10 nebo novější; IIS 10 nebo novější
  * Veřejná připojení hraničních serverů používají protokol HTTP/2, ale připojení reverzního proxy [serveru Kestrel](xref:fundamentals/servers/kestrel) používá protokol HTTP/1.1.
  * Připojení TLS 1,2 nebo novější

Pro nasazení v rámci procesu, když je navázáno připojení HTTP/2, sestavy [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) `HTTP/2`. Pro nasazení mimo proces, když je navázáno připojení HTTP/2, sestavy [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) `HTTP/1.1`.

Další informace o modelech hostování v procesu a předběžném procesu naleznete v tématu <xref:host-and-deploy/aspnet-core-module>.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

[Http/2](https://httpwg.org/specs/rfc7540.html) se podporuje pro nasazení mimo procesy, která splňují následující základní požadavky:

* Windows Server 2016/Windows 10 nebo novější; IIS 10 nebo novější
* Veřejná připojení hraničních serverů používají protokol HTTP/2, ale připojení reverzního proxy [serveru Kestrel](xref:fundamentals/servers/kestrel) používá protokol HTTP/1.1.
* Cílová architektura: neplatí pro nasazení mimo procesy, protože připojení HTTP/2 je zpracováváno výhradně službou IIS.
* Připojení TLS 1,2 nebo novější

Pokud je navázáno připojení HTTP/2, sestavy [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) `HTTP/1.1`.

::: moniker-end

HTTP/2 je ve výchozím nastavení povolené. Pokud není navázáno připojení HTTP/2, připojení se vrátí k HTTP/1.1. Další informace o konfiguraci HTTP/2 v nasazeních služby IIS najdete v tématu [http/2 ve službě IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).

## <a name="cors-preflight-requests"></a>Požadavky na kontrolu před výstupem CORS

*Tato část platí jenom pro ASP.NET Core aplikace, které cílí na .NET Framework.*

Pro ASP.NET Core aplikaci, která cílí na .NET Framework, požadavky na možnosti nejsou ve výchozím nastavení ve službě IIS předány do aplikace. Informace o tom, jak nakonfigurovat obslužné rutiny IIS aplikace v *souboru Web. config* , aby předávala požadavky na možnosti, najdete v tématu [Povolení žádostí o více zdrojů v ASP.NET webovém rozhraní API 2: jak CORS funguje](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).

::: moniker range=">= aspnetcore-2.2"

## <a name="application-initialization-module-and-idle-timeout"></a>Modul inicializace aplikace a časový limit nečinnosti

Při hostování ve službě IIS pomocí modulu ASP.NET Core verze 2:

* [Modul inicializace aplikace](#application-initialization-module) @no__t hostovaný [v procesu](#in-process-hosting-model) nebo [mimo proces](#out-of-process-hosting-model) aplikace je možné nakonfigurovat tak, aby se automaticky spouštěl v případě restartování nebo restartování serveru.
* [Časový limit nečinnosti](#idle-timeout) &ndash; hostovaný [v procesu](#in-process-hosting-model) aplikace se dá nakonfigurovat tak, aby nevypršel časový limit v období nečinnosti.

### <a name="application-initialization-module"></a>Modul inicializace aplikace

*Platí pro aplikace hostované v procesu a mimo proces.*

[Inicializace aplikace IIS](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) je funkce služby IIS, která do aplikace pošle požadavek HTTP, když se spustí nebo recykluje fond aplikací. Požadavek spustí spuštění aplikace. Služba IIS ve výchozím nastavení vydá požadavek na kořenovou adresu URL aplikace (`/`) k inicializaci aplikace (Další informace o konfiguraci najdete v [dalších zdrojích](#application-initialization-module-and-idle-timeout-additional-resources) ).

Potvrďte, že je povolená funkce role inicializace aplikace služby IIS:

V systémech Windows 7 nebo novějších stolních počítačích při používání služby IIS v místním prostředí:

1. Přejděte na **Ovládací panely** > **programy** > programy **a funkce** > **zapnout nebo vypnout funkce systému Windows** (levá strana obrazovky).
1. Open **Internetová informační služba** > **webové služby** > funkce pro **vývoj aplikací**.
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

* Pomocí souboru *Web. config*přidejte prvek `<applicationInitialization>` s `doAppInitAfterRestart` nastavenou na `true` do prvků `<system.webServer>` v souboru *Web. config* aplikace:

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
* [Inicializace aplikace @no__t – > 1applicationInitialization](/iis/configuration/system.webserver/applicationinitialization/).
* [Nastavení modelu procesu pro fond aplikací \<processModel >](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).

::: moniker-end

## <a name="deployment-resources-for-iis-administrators"></a>Prostředky nasazení pro správce služby IIS

Podrobné informace o službě IIS najdete v dokumentaci ke službě IIS.  
[Dokumentace ke službě IIS](/iis)

Přečtěte si o modelech nasazení aplikací .NET Core.  
[Nasazení aplikace .NET Core](/dotnet/core/deploying/)

Přečtěte si, jak modul ASP.NET Core umožňuje webovému serveru Kestrel používat jako reverzní proxy server službu IIS nebo IIS Express.  
[Modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module)

Naučte se konfigurovat modul ASP.NET Core pro hostování ASP.NET Corech aplikací.  
[Referenční dokumentace k modulu ASP.NET Core](xref:host-and-deploy/aspnet-core-module)

Přečtěte si o adresářové struktuře publikovaných aplikací ASP.NET Core.  
[Adresářová struktura](xref:host-and-deploy/directory-structure)

Objevte aktivní a neaktivní moduly IIS pro aplikace ASP.NET Core a Správa modulů IIS.  
[Moduly služby IIS](xref:host-and-deploy/iis/modules)

Naučte se diagnostikovat problémy s nasazeními aplikací ASP.NET Core služby IIS.  
[Řešení potíží](xref:test/troubleshoot-azure-iis)

Rozlišuje běžné chyby při hostování ASP.NET Corech aplikací ve službě IIS.  
[Referenční informace k běžným chybám u služeb Azure App Service a IIS](xref:host-and-deploy/azure-iis-errors-reference)

## <a name="additional-resources"></a>Další zdroje

* <xref:test/troubleshoot>
* [Úvod do ASP.NET Core](xref:index)
* [Oficiální web Microsoft IIS](https://www.iis.net/)
* [Knihovna technických obsahu pro Windows Server](/windows-server/windows-server)
* [HTTP/2 ve službě IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

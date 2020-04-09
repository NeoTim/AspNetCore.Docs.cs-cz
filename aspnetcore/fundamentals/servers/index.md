---
title: Implementace webového serveru v ASP.NET Core
author: rick-anderson
description: Objevte webové servery Kestrel a HTTP.sys pro ASP.NET Core. Přečtěte si, jak vybrat server a kdy použít reverzní proxy server.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
uid: fundamentals/servers/index
ms.openlocfilehash: d46793ef54c99fe609b5983c5a658fb7b20032fa
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78666339"
---
# <a name="web-server-implementations-in-aspnet-core"></a>Implementace webového serveru v ASP.NET Core

[Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), Stephen [Halter](https://twitter.com/halter73), a [Chris Ross](https://github.com/Tratcher)

Aplikace ASP.NET Core běží s implementací inprocesního http serveru. Implementace serveru naslouchá požadavkům HTTP a zobrazuje je do aplikace jako sadu <xref:Microsoft.AspNetCore.Http.HttpContext>funkcí [požadavků](xref:fundamentals/request-features) složených do aplikace .

## <a name="kestrel"></a>Kestrel

Poštolka je výchozí webový server určený šablonami projektu ASP.NET Core.

Použijte Poštolka:

* Samo o sobě jako hraniční server zpracování požadavků přímo ze sítě, včetně Internetu.

  ![Poštolka komunikuje přímo s Internetem bez reverzního proxy serveru](kestrel/_static/kestrel-to-internet2.png)

* S *reverzním proxy serverem*, například [Internetovou informační službou (IIS),](https://www.iis.net/) [Nginxem](https://nginx.org)nebo [Apache](https://httpd.apache.org/). Reverzní proxy server přijímá požadavky HTTP z Internetu a předá je ke strel.

  ![Poštolka komunikuje nepřímo s Internetem prostřednictvím reverzního proxy serveru, jako je služba IIS, Nginx nebo Apache](kestrel/_static/kestrel-to-internet.png)

Je podporována konfigurace&mdash;hostování s&mdash;reverzním proxy serverem nebo bez něj.

Pokyny ke konfiguraci kestrelu a informace o tom, kdy <xref:fundamentals/servers/kestrel>použít Kestrel v konfiguraci reverzního proxy serveru, naleznete v tématu .

::: moniker range=">= aspnetcore-2.2"

# <a name="windows"></a>[Windows](#tab/windows)

ASP.NET Core dodává s těmito loděmi:

* [Kestrel server](xref:fundamentals/servers/kestrel) je výchozí implementace http serveru napříč platformami.
* Server HTTP služby IIS je pro službu IIS [neprocesový server.](#hosting-models)
* [Server HTTP.sys](xref:fundamentals/servers/httpsys) je server HTTP pouze pro systém Windows založený na [ovladači jádra HTTP.sys a rozhraní HTTP Server API](/windows/desktop/Http/http-api-start-page).

Při používání [služby IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) nebo [služby IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)se aplikace spustí buď:

* Ve stejném procesu jako pracovní proces služby IIS [(model hostování v procesu)](#hosting-models)se serverem HTTP služby IIS. *V průběhu procesu* je doporučená konfigurace.
* V procesu odděleném od pracovního procesu služby IIS [(mimoprocesový model hostování)](#hosting-models)se [serverem Kestrel](#kestrel).

[Základní modul ASP.NET](xref:host-and-deploy/aspnet-core-module) je nativní modul služby IIS, který zpracovává nativní požadavky služby IIS mezi službou IIS a neprocesovým serverem HTTP služby IIS nebo poštolkou. Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module>.

## <a name="hosting-models"></a>Modely hostingu

Pomocí hostování v rámci procesu běží aplikace ASP.NET Core ve stejném procesu jako pracovní proces služby IIS. Hostování v procesu poskytuje lepší výkon než mimoproceshosting, protože požadavky nejsou proxied přes adaptér zpětné smyčky, síťové rozhraní, které vrací odchozí síťový provoz zpět do stejného počítače. Služba IIS zpracovává správu procesů pomocí [služby aktivace procesů systému Windows (WAS).](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)

Pomocí mimoprocesového hostingu ASP.NET základní aplikace spouštějí v procesu odděleném od pracovního procesu služby IIS a modul zpracovává správu procesů. Modul spustí proces pro aplikaci ASP.NET Core při příchodu prvního požadavku a restartuje aplikaci, pokud se vypne nebo dojde k chybě. Toto je v podstatě stejné chování jako u aplikací, které běží v procesu, které jsou spravovány [službou aktivace procesu systému Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

Další informace a pokyny ke konfiguraci naleznete v následujících tématech:

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[macOS](#tab/macos)

ASP.NET Core je dodáván se [serverem Kestrel](xref:fundamentals/servers/kestrel), což je výchozí http server pro více platforem.

# <a name="linux"></a>[Linux](#tab/linux)

ASP.NET Core je dodáván se [serverem Kestrel](xref:fundamentals/servers/kestrel), což je výchozí http server pro více platforem.

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[Windows](#tab/windows)

ASP.NET Core dodává s těmito loděmi:

* [Kestrel server](xref:fundamentals/servers/kestrel) je výchozí, multiplatformní HTTP server.
* [Server HTTP.sys](xref:fundamentals/servers/httpsys) je server HTTP pouze pro systém Windows založený na [ovladači jádra HTTP.sys a rozhraní HTTP Server API](/windows/desktop/Http/http-api-start-page).

Při použití [služby IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) nebo [služby IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)je aplikace spuštěna v procesu odděleném od pracovního procesu služby IIS *(mimo proces)* se [serverem Kestrel](#kestrel).

Vzhledem k tomu, ASP.NET základní aplikace spustit v procesu odděleném od pracovního procesu služby IIS, modul zpracovává správu procesů. Modul spustí proces pro aplikaci ASP.NET Core při příchodu prvního požadavku a restartuje aplikaci, pokud se vypne nebo dojde k chybě. Toto je v podstatě stejné chování jako u aplikací, které běží v procesu, které jsou spravovány [službou aktivace procesu systému Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

Následující diagram znázorňuje vztah mezi iis, ASP.NET základní modul a aplikace hostované mimo proces:

![Modul ASP.NET Core](_static/ancm-outofprocess.png)

Požadavky dorazí z webu do ovladače HTTP.sys v režimu jádra. Řidič směruje požadavky do služby IIS na nakonfigurovaném portu webu, obvykle 80 (HTTP) nebo 443 (HTTPS). Modul předá požadavky Kestrel na náhodný port pro aplikaci, která není port 80 nebo 443.

Modul určuje port prostřednictvím proměnné prostředí při spuštění a [middleware integrace služby IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) nakonfiguruje server tak, aby naslouchal . `http://localhost:{port}` Jsou prováděny další kontroly a požadavky, které nepocházejí z modulu, jsou odmítnuty. Modul nepodporuje předávání https, takže požadavky jsou předávány přes HTTP i v případě, že přijaté službou IIS přes protokol HTTPS.

Poté, co Kestrel vyzvedne požadavek z modulu, je požadavek posunut do kanálu ASP.NET middleware. Middleware kanálu zpracovává požadavek a předá `HttpContext` jej jako instanci logiky aplikace. Middleware přidané integrací služby IIS aktualizuje schéma, vzdálenou IP adresu a pathbase tak, aby odpovídaly předání požadavku ke společnosti Kestrel. Odpověď aplikace je předána zpět do služby IIS, která ji odešle zpět do klienta HTTP, který inicioval požadavek.

Pokyny ke konfiguraci služby IIS a ASP.NET základního modulu naleznete v následujících tématech:

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[macOS](#tab/macos)

ASP.NET Core je dodáván se [serverem Kestrel](xref:fundamentals/servers/kestrel), což je výchozí http server pro více platforem.

# <a name="linux"></a>[Linux](#tab/linux)

ASP.NET Core je dodáván se [serverem Kestrel](xref:fundamentals/servers/kestrel), což je výchozí http server pro více platforem.

---

::: moniker-end

### <a name="nginx-with-kestrel"></a>Nginx s Poštolem

Informace o tom, jak používat Nginx na Linuxu jako <xref:host-and-deploy/linux-nginx>reverzní proxy server pro Kestrel, viz .

### <a name="apache-with-kestrel"></a>Apache s Kestrelem

Informace o tom, jak používat Apache na Linuxu jako <xref:host-and-deploy/linux-apache>reverzní proxy server pro Kestrel, viz .

## <a name="httpsys"></a>HTTP.sys

Pokud ASP.NET základní aplikace jsou spuštěny v systému Windows, HTTP.sys je alternativou ke Kestrel. Kestrel se obecně doporučuje pro nejlepší výkon. HTTP.sys lze použít ve scénářích, kde je aplikace vystavena internetu a požadované funkce jsou podporovány HTTP.sys, ale ne Kestrel. Další informace naleznete v tématu <xref:fundamentals/servers/httpsys>.

![HTTP.sys komunikuje přímo s Internetem](httpsys/_static/httpsys-to-internet.png)

Http.sys lze také použít pro aplikace, které jsou vystaveny pouze interní síti.

![HTTP.sys komunikuje přímo s interní sítí](httpsys/_static/httpsys-to-internal.png)

Pokyny ke konfiguraci http.sys naleznete v tématu <xref:fundamentals/servers/httpsys>.

## <a name="aspnet-core-server-infrastructure"></a>ASP.NET základní serverová infrastruktura

K <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> dispozici `Startup.Configure` v metodě <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> zpřístupňuje vlastnost typu <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection>. Poštolky a HTTP.sys pouze vystavit <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>jednu funkci každý , , ale různé implementace serveru může vystavit další funkce.

`IServerAddressesFeature`lze zjistit, který port má implementace serveru vázána za běhu.

## <a name="custom-servers"></a>Vlastní servery

Pokud integrované servery nesplňují požadavky aplikace, lze vytvořit vlastní implementaci serveru. [Průvodce Open Web Interface for .NET (OWIN)](xref:fundamentals/owin) ukazuje, jak <xref:Microsoft.AspNetCore.Hosting.Server.IServer> napsat implementaci založenou na [aplikaci Nowin.](https://github.com/Bobris/Nowin) Pouze rozhraní funkcí, které aplikace používá, vyžadují <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> implementaci, i když minimálně a <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature> musí být podporována.

## <a name="server-startup"></a>Spuštění serveru

Server se spustí, když integrované vývojové prostředí (IDE) nebo editor spustí aplikaci:

* Profily spuštění [sady Visual Studio](https://visualstudio.microsoft.com) &ndash; lze použít ke spuštění aplikace a serveru pomocí aplikace a serveru pomocí[modulu](xref:host-and-deploy/aspnet-core-module) [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/ASP.NET Core Module nebo konzoly.
* [Visual Studio Code](https://code.visualstudio.com/) &ndash; Aplikace a server jsou schované [omnisharp](https://github.com/OmniSharp/omnisharp-vscode), který aktivuje ladicí program CoreCLR.
* [Visual Studio pro Mac](https://visualstudio.microsoft.com/vs/mac/) &ndash; Aplikace a server jsou schované [ladicím programem mono soft-mode](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/).

Při spuštění aplikace z příkazového řádku ve složce projektu [spustí dotnet spuštění](/dotnet/core/tools/dotnet-run) aplikace a serveru (pouze Kestrel a HTTP.sys). Konfigurace je určena `-c|--configuration` možností, která je `Debug` nastavena na `Release`hodnotu buď (výchozí) nebo .

Soubor *launchSettings.json* poskytuje konfiguraci při `dotnet run` spouštění aplikace s ladicím programem integrovaným do nástrojů, jako je například Visual Studio. Pokud jsou profily spuštění k dispozici v souboru `--launch-profile {PROFILE NAME}` *launchSettings.json,* použijte možnost s příkazem `dotnet run` nebo vyberte profil v sadě Visual Studio. Další informace naleznete [v tématu dotnet run](/dotnet/core/tools/dotnet-run) a [.NET Core distribution packaging](/dotnet/core/build/distribution-packaging).

## <a name="http2-support"></a>Podpora HTTP/2

[HTTP/2](https://httpwg.org/specs/rfc7540.html) je podporován ASP.NET jádrem v následujících scénářích nasazení:

::: moniker range=">= aspnetcore-2.2"

* [Kestrel](xref:fundamentals/servers/kestrel#http2-support)
  * Operační systém
    * Windows Server 2016/Windows 10 nebo novější&dagger;
    * Linux s OpenSSL 1.0.2 nebo novějším (například Ubuntu 16.04 nebo novější)
    * HTTP/2 bude v budoucí verzi v systému macOS podporován.
  * Cílová architektura: .NET Core 2.2 nebo novější
* [HTTP.sys](xref:fundamentals/servers/httpsys#http2-support)
  * Windows Server 2016/Windows 10 nebo novější
  * Cílová architektura: Nevztahuje se na nasazení HTTP.sys.
* [IIS (v průběhu)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016/Windows 10 nebo novější; IIS 10 nebo novější
  * Cílová architektura: .NET Core 2.2 nebo novější
* [IIS (mimo proces)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016/Windows 10 nebo novější; IIS 10 nebo novější
  * Připojení serveru edge směřující mj.
  * Cílový rámec: Nevztahuje se na mimoprocesová nasazení služby IIS.

&dagger;Kestrel má omezenou podporu pro HTTP/2 v systémech Windows Server 2012 R2 a Windows 8.1. Podpora je omezená, protože seznam podporovaných šifrovacích sad TLS dostupných v těchto operačních systémech je omezený. K zabezpečení připojení TLS může být vyžadován certifikát generovaný pomocí algoritmu digitálního podpisu eliptické křivky (ECDSA).

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [HTTP.sys](xref:fundamentals/servers/httpsys#http2-support)
  * Windows Server 2016/Windows 10 nebo novější
  * Cílová architektura: Nevztahuje se na nasazení HTTP.sys.
* [IIS (mimo proces)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016/Windows 10 nebo novější; IIS 10 nebo novější
  * Připojení serveru edge směřující mj.
  * Cílový rámec: Nevztahuje se na mimoprocesová nasazení služby IIS.

::: moniker-end

Připojení HTTP/2 musí používat [vyjednávání protokolu aplikační vrstvy (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) a TLS 1.2 nebo novější. Další informace naleznete v tématech, která se vztahuje k scénářům nasazení serveru.

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/servers/kestrel>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:fundamentals/servers/httpsys>

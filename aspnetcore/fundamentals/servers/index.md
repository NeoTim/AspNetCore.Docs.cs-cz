---
title: Implementací webového serveru v ASP.NET Core
author: rick-anderson
description: Zjišťování webové servery přes Kestrel a HTTP.sys pro ASP.NET Core. Zjistěte, jak vybrat server a kdy použít reverzní proxy server.
ms.author: tdykstra
ms.custom: mvc
ms.date: 09/21/2018
uid: fundamentals/servers/index
ms.openlocfilehash: 6b6ebbe9d31d571ea470fba0989d622dcf6e68af
ms.sourcegitcommit: fc2486ddbeb15ab4969168d99b3fe0fbe91e8661
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2018
ms.locfileid: "50758203"
---
# <a name="web-server-implementations-in-aspnet-core"></a>Implementací webového serveru v ASP.NET Core

Podle [Petr Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen Halter](https://twitter.com/halter73), a [Chris Ross](https://github.com/Tratcher)

Aplikace ASP.NET Core spouští implementaci serveru HTTP v procesu. Implementace server přijímá požadavky protokolu HTTP a poskytuje je na aplikaci jako sady [funkce požadavků](xref:fundamentals/request-features) složený do <xref:Microsoft.AspNetCore.Http.HttpContext>.

ASP.NET Core se dodává s následující implementace serveru:

::: moniker range=">= aspnetcore-2.2"

* [Kestrel](xref:fundamentals/servers/kestrel) je výchozí, server HTTP pro různé platformy pro ASP.NET Core.
* `IISHttpServer` se používá s [model hostingu v procesu](xref:fundamentals/servers/aspnet-core-module#in-process-hosting-model) a [modul ASP.NET Core](xref:fundamentals/servers/aspnet-core-module) na Windows.
* [Ovladač HTTP.sys](xref:fundamentals/servers/httpsys) je na základě protokolu HTTP jenom pro Windows server [ovladač HTTP.sys jádra a rozhraní API serveru HTTP](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx). (Ovladač HTTP.sys se nazývá [WebListener](xref:fundamentals/servers/weblistener) v ASP.NET Core 1.x.)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [Kestrel](xref:fundamentals/servers/kestrel) je výchozí, server HTTP pro různé platformy pro ASP.NET Core.
* [Ovladač HTTP.sys](xref:fundamentals/servers/httpsys) je na základě protokolu HTTP jenom pro Windows server [ovladač HTTP.sys jádra a rozhraní API serveru HTTP](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx). (Ovladač HTTP.sys se nazývá [WebListener](xref:fundamentals/servers/weblistener) v ASP.NET Core 1.x.)

::: moniker-end

## <a name="kestrel"></a>Kestrel

Kestrel je výchozí webový server, která je součástí šablony projektů ASP.NET Core.

::: moniker range=">= aspnetcore-2.0"

Kestrel lze použít samostatně nebo se *reverzní proxy server*, jako je například Apache, IIS nebo Nginx. Reverzní proxy server přijímá požadavky HTTP z Internetu a předává je na Kestrel po některé předběžného zpracování.

![Kestrel komunikuje přímo s Internetu bez reverzní proxy server](kestrel/_static/kestrel-to-internet2.png)

![Kestrel nepřímo komunikuje přes Internet prostřednictvím reverzního proxy serveru, jako je například Apache, IIS nebo Nginx](kestrel/_static/kestrel-to-internet.png)

Buď konfiguraci&mdash;s nebo bez něj reverzní proxy server&mdash;je platný a podporované konfigurace pro hostování pro ASP.NET Core 2.0 nebo novější. Další informace najdete v tématu [použití Kestrel s reverzní proxy server](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Pokud aplikace přijímá jenom požadavky z interní sítě, je možné Kestrel samostatně.

![Kestrel komunikuje přímo s interní sítě](kestrel/_static/kestrel-to-internal.png)

Pokud aplikace je přístupný z Internetu, Kestrel musíte použít službu IIS, serveru Nginx nebo Apache jako *reverzní proxy server*. Reverzní proxy server přijímá požadavky HTTP z Internetu a předává je na Kestrel po některé předběžné zpracování, jak je znázorněno v následujícím diagramu:

![Kestrel nepřímo komunikuje přes Internet prostřednictvím reverzního proxy serveru, jako je například Apache, IIS nebo Nginx](kestrel/_static/kestrel-to-internet.png)

Nejdůležitější důvod pomocí reverzního proxy serveru pro veřejnou hraniční server nasazení, které jsou vystaveny přímo k Internetu je zabezpečení. Verze 1.x Kestrel nemají funkcí důležité zabezpečení, ochranu před útoky z Internetu. To zahrnuje, ale není omezený na, odpovídající časové limity, žádost o velikosti omezení a omezení počtu souběžných připojení.

Další informace najdete v tématu [použití Kestrel s reverzní proxy server](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).

::: moniker-end

Apache, IIS a serveru Nginx nelze použít bez Kestrel nebo [implementace vlastního serveru](#custom-servers). ASP.NET Core je navržená ke spuštění ve svém vlastním procesu tak, aby se může chovat konzistentně napříč platformami. Apache, IIS a serveru Nginx určovat vlastní spouštěcí procedura a prostředí. K použití těchto technologií server přímo, ASP.NET Core potřebovat umožní reagovat na požadavky na každém serveru. Použití implementace webového serveru, jako je například Kestrel, ASP.NET Core má kontrolu nad procesu spouštění a prostředí, když jsou hostované na jiný server technologie.

### <a name="iis-with-kestrel"></a>IIS s Kestrel

::: moniker range=">= aspnetcore-2.2"

Při použití [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) nebo [služby IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), aplikace ASP.NET Core je buď spuštěn ve stejném procesu jako pracovní proces služby IIS ( *vnitroprocesové* model hostingu) nebo v samostatném procesu z pracovní proces služby IIS ( *mimo proces* model hostingu).

[Modul ASP.NET Core](xref:fundamentals/servers/aspnet-core-module) je nativní modul služby IIS, která zpracovává nativní požadavků služby IIS mezi Server Http v procesu služby IIS nebo server Kestrel mimo proces. Další informace naleznete v tématu <xref:fundamentals/servers/aspnet-core-module>.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Při použití [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) nebo [služby IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) jako reverzní proxy server pro ASP.NET Core, ASP.NET Core aplikace běží v procesu nezávisle na pracovní proces služby IIS. V procesu služby IIS [modul ASP.NET Core](xref:fundamentals/servers/aspnet-core-module) koordinuje vztah reverzního proxy serveru. Primární funkce modul ASP.NET Core jsou ke spuštění aplikace ASP.NET Core, restartujte aplikaci, pokud ho dojde k chybě a přesměrování provozu HTTP na aplikaci. Další informace naleznete v tématu <xref:fundamentals/servers/aspnet-core-module>.

::: moniker-end

### <a name="nginx-with-kestrel"></a>Server Nginx s Kestrel

Informace o tom, jak použít Nginx jako reverzní proxy server pro Kestrel v Linuxu najdete v tématu [hostování v Linuxu se serverem Nginx na](xref:host-and-deploy/linux-nginx).

### <a name="apache-with-kestrel"></a>Apache s Kestrel

Informace o tom, jak používat Apache na platformě Linux jako reverzní proxy server pro Kestrel najdete v tématu [hostitele v Linuxu pomocí Apache](xref:host-and-deploy/linux-apache).

## <a name="httpsys"></a>HTTP.sys

::: moniker range=">= aspnetcore-2.0"

Pokud aplikace ASP.NET Core běží na Windows, ovladač HTTP.sys se o alternativu k Kestrel. Kestrel obecně se doporučuje pro zajištění nejlepšího výkonu. Ovladač HTTP.sys lze použít v situacích, kdy aplikace je přístupný z Internetu a požadované funkce jsou podporovány, ale ne Kestrel HTTP.sys. Informace o souboru HTTP.sys, naleznete v tématu [HTTP.sys](xref:fundamentals/servers/httpsys).

![Ovladač HTTP.sys komunikuje přímo s Internetem](httpsys/_static/httpsys-to-internet.png)

Ovladač HTTP.sys lze použít také pro aplikace, které jsou přístupné pouze k interní síti.

![Ovladač HTTP.sys komunikuje přímo s interní sítě](httpsys/_static/httpsys-to-internal.png)

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Název souboru HTTP.sys [WebListener](xref:fundamentals/servers/weblistener) v ASP.NET Core 1.x. Pokud aplikace ASP.NET Core běží na Windows, WebListener se o alternativu pro scénářů, kdy není k dispozici pro hostování aplikací služby IIS.

![Weblistener komunikuje přímo s Internetem](weblistener/_static/weblistener-to-internet.png)

WebListener lze také místo Kestrel pro aplikace, které jsou přístupné pouze k interní síti, pokud je to nutné, že funkce jsou podporovány, ale ne Kestrel WebListener. Informace o WebListener najdete v tématu [WebListener](xref:fundamentals/servers/weblistener).

![Weblistener komunikuje přímo s interní sítě](weblistener/_static/weblistener-to-internal.png)

::: moniker-end

## <a name="aspnet-core-server-infrastructure"></a>ASP.NET Core server infrastruktury

[IApplicationBuilder](/dotnet/api/microsoft.aspnetcore.builder.iapplicationbuilder) k dispozici v `Startup.Configure` zpřístupňuje metodu [ServerFeatures](/dotnet/api/microsoft.aspnetcore.builder.iapplicationbuilder.serverfeatures) vlastnost typu [IFeatureCollection](/dotnet/api/microsoft.aspnetcore.http.features.ifeaturecollection). Přes kestrel a HTTP.sys (WebListener v ASP.NET Core 1.x) vystavit pouze jednu funkci, [IServerAddressesFeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature), ale implementace jiný server může vystavit další funkce.

`IServerAddressesFeature` umožňuje zjistit, port, který obsahuje implementaci serveru vázán v době běhu.

## <a name="custom-servers"></a>Vlastní servery

Pokud integrované servery nesplňují požadavky aplikace, implementace vlastního serveru vytvořit. [Open Web Interface pro .NET (OWIN) průvodce](xref:fundamentals/owin) ukazuje, jak psát [Nowin](https://github.com/Bobris/Nowin)– na základě [IServer](/dotnet/api/microsoft.aspnetcore.hosting.server.iserver) implementace. Pouze funkce rozhraní, které aplikace používá potřeba provádění, když minimálně [IHttpRequestFeature](/dotnet/api/microsoft.aspnetcore.http.features.ihttprequestfeature) a [IHttpResponseFeature](/dotnet/api/microsoft.aspnetcore.http.features.ihttpresponsefeature) musí podporovat.

## <a name="server-startup"></a>Při spuštění serveru

Při použití [sady Visual Studio](https://www.visualstudio.com/vs/), [Visual Studio for Mac](https://www.visualstudio.com/vs/mac/), nebo [Visual Studio Code](https://code.visualstudio.com/), server se spustí, když se aplikace spustí na integrované vývojové prostředí ( INTEGROVANÉ VÝVOJOVÉ PROSTŘEDÍ). V sadě Visual Studio na Windows, můžete profily spouštění použije ke spuštění aplikace a serveru s oběma [služby IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/[modul ASP.NET Core](xref:fundamentals/servers/aspnet-core-module) nebo konzoly. Ve Visual Studio Code, aplikace a serveru jsou spouštěny [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode), která aktivuje CoreCLR ladicího programu. Pomocí sady Visual Studio pro Mac, aplikace a serveru jsou spouštěny [ladicí program Mono konfigurace Soft-režim](http://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/).

Při spuštění aplikace z příkazového řádku ve složce projektu [dotnet spustit](/dotnet/core/tools/dotnet-run) spustí aplikace a serveru (přes Kestrel a pouze HTTP.sys). Konfigurace je určena `-c|--configuration` možnost, který je nastaven na hodnotu `Debug` (výchozí) nebo `Release`. Pokud jsou k dispozici v profily spouštění *launchSettings.json* souboru, použijte `--launch-profile <NAME>` možnost nastavit profil spuštění (například `Development` nebo `Production`). Další informace najdete v tématu [dotnet spustit](/dotnet/core/tools/dotnet-run) a [vytváření distribučních balíčků .NET Core](/dotnet/core/build/distribution-packaging) témata.

## <a name="http2-support"></a>Podpora HTTP/2

[HTTP/2](https://httpwg.org/specs/rfc7540.html) se podporuje s ASP.NET Core v následujících scénářích nasazení:

::: moniker range=">= aspnetcore-2.2"

* [Kestrel](xref:fundamentals/servers/kestrel#http2-support)
  * Operační systém
    * Windows Server 2012 R2 nebo Windows 8.1 nebo novější
    * Linux s OpenSSL 1.0.2 nebo novější (například Ubuntu 16.04 nebo novější)
    * HTTP/2 budou podporované v systému macOS v budoucí verzi.
  * Cílová architektura: .NET Core 2.2 nebo vyšší
* [HTTP.sys](xref:fundamentals/servers/httpsys#http2-support)
  * Windows Server 2016 nebo Windows 10 nebo novější
  * Cílová architektura: není k dispozici pro nasazení souboru HTTP.sys.
* [Služba IIS (v procesu)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016 nebo Windows 10 nebo novější; IIS 10 nebo novější.
  * Cílová architektura: .NET Core 2.2 nebo vyšší
* [Služba IIS (out-of-process)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016 nebo Windows 10 nebo novější; IIS 10 nebo novější.
  * Připojení k serveru edge veřejně přístupných používat HTTP/2, ale připojení reverzního proxy serveru na Kestrel používá HTTP/1.1.
  * Cílová architektura: není k dispozici pro nasazení na více instancí procesu služby IIS.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [HTTP.sys](xref:fundamentals/servers/httpsys#http2-support)
  * Windows Server 2016 nebo Windows 10 nebo novější
  * Cílová architektura: není k dispozici pro nasazení souboru HTTP.sys.
* [Služba IIS (out-of-process)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016 nebo Windows 10 nebo novější; IIS 10 nebo novější.
  * Připojení k serveru edge veřejně přístupných používat HTTP/2, ale připojení reverzního proxy serveru na Kestrel používá HTTP/1.1.
  * Cílová architektura: není k dispozici pro nasazení na více instancí procesu služby IIS.

::: moniker-end

Musíte použít připojení HTTP/2 [vyjednávání protokolu v aplikační vrstvě (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) a TLS 1.2 nebo vyšší. Další informace najdete v tématech, které se týkají scénářů nasazení serveru.

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/servers/kestrel>
* <xref:fundamentals/servers/aspnet-core-module>
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:fundamentals/servers/httpsys> (pro technologii ASP.NET Core 1.x, přečtěte si téma <xref:fundamentals/servers/weblistener>)

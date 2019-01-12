---
title: Implementací webového serveru v ASP.NET Core
author: guardrex
description: Zjišťování webové servery přes Kestrel a HTTP.sys pro ASP.NET Core. Zjistěte, jak vybrat server a kdy použít reverzní proxy server.
ms.author: tdykstra
ms.custom: mvc
ms.date: 01/11/2019
uid: fundamentals/servers/index
ms.openlocfilehash: 4210d67397c85a1608f79fc4ed9d283521356226
ms.sourcegitcommit: ec71fd5a988f927ae301813aae5ff764feb3bb6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2019
ms.locfileid: "54249487"
---
# <a name="web-server-implementations-in-aspnet-core"></a>Implementací webového serveru v ASP.NET Core

Podle [Petr Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen Halter](https://twitter.com/halter73), a [Chris Ross](https://github.com/Tratcher)

Aplikace ASP.NET Core spouští implementaci serveru HTTP v procesu. Implementace server přijímá požadavky protokolu HTTP a poskytuje je na aplikaci jako sada [funkce požadavků](xref:fundamentals/request-features) složený do <xref:Microsoft.AspNetCore.Http.HttpContext>.

::: moniker range=">= aspnetcore-2.2"

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

ASP.NET Core se dodává s následujícími možnostmi:

* [Kestrel server](xref:fundamentals/servers/kestrel) je výchozí, implementaci serveru HTTP pro různé platformy.
* Server služby IIS protokolu HTTP [v procesový server](#in-process-hosting-model) pro službu IIS.
* [HTTP.sys server](xref:fundamentals/servers/httpsys) je na základě protokolu HTTP jenom pro Windows server [ovladač HTTP.sys jádra a rozhraní API serveru HTTP](/windows/desktop/Http/http-api-start-page).

Při použití [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) nebo [služby IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), buď spuštění aplikace:

* Ve stejném procesu jako pracovní proces služby IIS ( [model hostingu v procesu](#in-process-hosting-model)) se [HTTP serveru služby IIS](#iis-http-server). *V procesu* je doporučená konfigurace.
* V procesu oddělit od pracovní proces služby IIS ( [model hostingu mimo proces](#out-of-process-hosting-model)) se [Kestrel server](#kestrel).

[Modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) je nativní modul služby IIS, která zpracovává nativní požadavků služby IIS mezi služby IIS a v rámci procesu serveru služby IIS protokolu HTTP nebo Kestrel. Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module>.

## <a name="hosting-models"></a>Modelech hostování

### <a name="in-process-hosting-model"></a>Model hostingu v procesu

Proces hostování, ASP.NET Core pomocí app běží ve stejném procesu jako jeho pracovní proces služby IIS. Tím snížení výkonu na více instancí procesu požadavků proxy prostřednictvím adaptéru zpětné smyčky, síťové rozhraní, které vrátí odchozí síťový provoz do stejného počítače. Služba IIS zpracovává proces správy pomocí [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

Modul ASP.NET Core:

* Provede inicializaci aplikace.
  * Načtení [CoreCLR](/dotnet/standard/glossary#coreclr).
  * Volání `Program.Main`.
* Zpracovává životnost nativní požadavků služby IIS.

Model hostingu v procesu není podporována pro aplikace ASP.NET Core, které jsou cíleny rozhraní .NET Framework.

Následující diagram znázorňuje vztah mezi služby IIS, že modul ASP.NET Core a aplikace hostované v procesu:

![Modul ASP.NET Core](_static/ancm-inprocess.png)

Žádost o přijetí z webu pro ovladač HTTP.sys režimu jádra. Ovladač přesměruje požadavek na nativní služby IIS na webu nakonfigurovaný port, obvykle 80 (HTTP) nebo 443 (HTTPS). Modul obdrží požadavek na nativní a předává je na serveru služby IIS protokolu HTTP (`IISHttpServer`). HTTP serveru služby IIS je implementace v rámci procesu serveru pro službu IIS, který převede žádosti z nativní do spravované.

Po zpracování požadavku HTTP Server služby IIS požadavek odesílají do kanálu middleware ASP.NET Core. Zpracuje požadavek a předává je jako middleware kanálu `HttpContext` instanci aplikace logiky. Odpověď aplikace je předán zpět do služby IIS, které nabízených oznámení je zpět klienta, který inicioval žádost.

Hostování v procesu je vyjádřit výslovný souhlas pro existující aplikace, ale [dotnet nové](/dotnet/core/tools/dotnet-new) šablony ve výchozím nastavení model hostingu v procesu pro všechny scénáře pro službu IIS a služby IIS Express.

### <a name="out-of-process-hosting-model"></a>Model hostingu mimo proces

Protože aplikace ASP.NET Core spuštění v procesu oddělit od pracovní proces služby IIS, zpracovává modul Správa procesů. V modulu začne proces pro aplikace ASP.NET Core, když první žádost o přijetí a restartuje aplikaci, pokud se vypne nebo dojde k chybě. Toto je v podstatě stejné chování jako aplikace, které běží v procesu, která jsou spravována [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

Následující diagram znázorňuje vztah mezi služby IIS, že modul ASP.NET Core a aplikace hostované na více instancí procesu:

![Modul ASP.NET Core](_static/ancm-outofprocess.png)

Požadavky přicházejí z webu pro ovladač HTTP.sys režimu jádra. Ovladač směruje požadavky do služby IIS na webu nakonfigurovaný port, obvykle 80 (HTTP) nebo 443 (HTTPS). V modulu předá požadavky Kestrel na náhodný port pro aplikaci, která není port 80 nebo 443.

V modulu určuje port, přes proměnnou prostředí při spuštění a Middleware pro integraci služby IIS nakonfiguruje server tak, aby naslouchala na `http://localhost:{PORT}`. Další kontroly jsou prováděny, a odmítne požadavky, které není pocházejí z modulu. Modul nepodporuje předávání protokolu HTTPS, takže žádosti se předávají prostřednictvím protokolu HTTP i v případě, že byla přijata službou IIS přes protokol HTTPS.

Po Kestrel převezme žádosti z modulu, požadavek se vloží do kanálu middleware ASP.NET Core. Zpracuje požadavek a předává je jako middleware kanálu `HttpContext` instanci aplikace logiky. Middleware, které jsou přidány pomocí integrace služby IIS aktualizuje schéma, vzdálenou IP adresu a pathbase pro předání požadavku do Kestrel. Odpověď aplikace je předán zpět do služby IIS, které nabízených oznámení je zpět klienta HTTP, který inicioval žádost.

Pro službu IIS a ASP.NET Core modulu pokyny ke konfiguraci naleznete v následujících tématech:

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macostabmacos"></a>[macOS](#tab/macos)

ASP.NET Core se dodává s [Kestrel server](xref:fundamentals/servers/kestrel), což je výchozí, server HTTP pro různé platformy.

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

ASP.NET Core se dodává s [Kestrel server](xref:fundamentals/servers/kestrel), což je výchozí, server HTTP pro různé platformy.

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

ASP.NET Core se dodává s následujícími možnostmi:

* [Kestrel server](xref:fundamentals/servers/kestrel) je výchozí, server HTTP pro různé platformy.
* [HTTP.sys server](xref:fundamentals/servers/httpsys) je na základě protokolu HTTP jenom pro Windows server [ovladač HTTP.sys jádra a rozhraní API serveru HTTP](/windows/desktop/Http/http-api-start-page).

Při použití [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) nebo [služby IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), aplikace běží v procesu nezávisle na pracovní proces služby IIS (*mimo proces*) se [Kestrel server](#kestrel).

Protože aplikace ASP.NET Core spuštění v procesu oddělit od pracovní proces služby IIS, zpracovává modul Správa procesů. V modulu začne proces pro aplikace ASP.NET Core, když první žádost o přijetí a restartuje aplikaci, pokud se vypne nebo dojde k chybě. Toto je v podstatě stejné chování jako aplikace, které běží v procesu, která jsou spravována [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

Následující diagram znázorňuje vztah mezi služby IIS, že modul ASP.NET Core a aplikace hostované na více instancí procesu:

![Modul ASP.NET Core](_static/ancm-outofprocess.png)

Požadavky přicházejí z webu pro ovladač HTTP.sys režimu jádra. Ovladač směruje požadavky do služby IIS na webu nakonfigurovaný port, obvykle 80 (HTTP) nebo 443 (HTTPS). V modulu předá požadavky Kestrel na náhodný port pro aplikaci, která není port 80 nebo 443.

V modulu určuje port, přes proměnnou prostředí při spuštění a Middleware pro integraci služby IIS nakonfiguruje server tak, aby naslouchala na `http://localhost:{port}`. Další kontroly jsou prováděny, a odmítne požadavky, které není pocházejí z modulu. Modul nepodporuje předávání protokolu HTTPS, takže žádosti se předávají prostřednictvím protokolu HTTP i v případě, že byla přijata službou IIS přes protokol HTTPS.

Po Kestrel převezme žádosti z modulu, požadavek se vloží do kanálu middleware ASP.NET Core. Zpracuje požadavek a předává je jako middleware kanálu `HttpContext` instanci aplikace logiky. Middleware, které jsou přidány pomocí integrace služby IIS aktualizuje schéma, vzdálenou IP adresu a pathbase pro předání požadavku do Kestrel. Odpověď aplikace je předán zpět do služby IIS, které nabízených oznámení je zpět klienta HTTP, který inicioval žádost.

Pro službu IIS a ASP.NET Core modulu pokyny ke konfiguraci naleznete v následujících tématech:

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macostabmacos"></a>[macOS](#tab/macos)

ASP.NET Core se dodává s [Kestrel server](xref:fundamentals/servers/kestrel), což je výchozí, server HTTP pro různé platformy.

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

ASP.NET Core se dodává s [Kestrel server](xref:fundamentals/servers/kestrel), což je výchozí, server HTTP pro různé platformy.

---

::: moniker-end

## <a name="kestrel"></a>Kestrel

Kestrel je výchozí webový server, která je součástí šablony projektů ASP.NET Core.

::: moniker range=">= aspnetcore-2.0"

Dá se kestrel:

* Samostatně jako hraniční server zpracování požadavků přímo ze sítě, včetně Internetu.

  ![Kestrel komunikuje přímo s Internetu bez reverzní proxy server](kestrel/_static/kestrel-to-internet2.png)

* S *reverzní proxy server*, jako například [Internetové informační služby (IIS)](https://www.iis.net/), [Nginx](http://nginx.org), nebo [Apache](https://httpd.apache.org/). Reverzní proxy server přijímá požadavky HTTP z Internetu a předává je na Kestrel.

  ![Kestrel nepřímo komunikuje přes Internet prostřednictvím reverzního proxy serveru, jako je například Apache, IIS nebo Nginx](kestrel/_static/kestrel-to-internet.png)

Konfigurace buď hostování&mdash;s nebo bez něj reverzní proxy server&mdash;se podporuje pro ASP.NET Core 2.1 nebo novější.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Pokud aplikace přijímá jenom požadavky z interní sítě, je možné Kestrel samostatně.

![Kestrel komunikuje přímo s interní sítě](kestrel/_static/kestrel-to-internal.png)

Pokud aplikace je přístupný z Internetu, musíte použít Kestrel *reverzní proxy server*, jako například [Internetové informační služby (IIS)](https://www.iis.net/), [Nginx](http://nginx.org), nebo [Apache ](https://httpd.apache.org/). Reverzní proxy server přijímá požadavky HTTP z Internetu a předává je na Kestrel.

![Kestrel nepřímo komunikuje přes Internet prostřednictvím reverzního proxy serveru, jako je například Apache, IIS nebo Nginx](kestrel/_static/kestrel-to-internet.png)

Nejdůležitější důvod pomocí reverzního proxy serveru pro veřejnou hraniční server nasazení, které jsou vystaveny přímo k Internetu je zabezpečení. Verze 1.x Kestrel nezahrnují funkcí důležité zabezpečení, ochranu před útoky z Internetu. To zahrnuje, ale není omezený na, odpovídající časové limity, žádost o velikosti omezení a omezení počtu souběžných připojení.

::: moniker-end

Pokyny ke konfiguraci Kestrel a informace o použití Kestrel v konfigurace reverzního proxy serveru najdete v tématu <xref:fundamentals/servers/kestrel>.

### <a name="nginx-with-kestrel"></a>Server Nginx s Kestrel

Informace o tom, jak použít Nginx jako reverzní proxy server pro Kestrel v Linuxu najdete v tématu <xref:host-and-deploy/linux-nginx>.

### <a name="apache-with-kestrel"></a>Apache s Kestrel

Informace o tom, jak používat Apache na platformě Linux jako reverzní proxy server pro Kestrel najdete v tématu <xref:host-and-deploy/linux-apache>.

::: moniker range=">= aspnetcore-2.2"

## <a name="iis-http-server"></a>HTTP serveru služby IIS

Server služby IIS protokolu HTTP [v procesový server](#in-process-hosting-model) pro službu IIS a požadované pro nasazení v rámci procesu. [Modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) zpracovává nativní služby IIS požadavky mezi službou IIS a Server služby IIS protokolu HTTP. Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module>.

::: moniker-end

## <a name="httpsys"></a>HTTP.sys

Pokud aplikace ASP.NET Core běží na Windows, ovladač HTTP.sys se o alternativu k Kestrel. Kestrel obecně se doporučuje pro zajištění nejlepšího výkonu. Ovladač HTTP.sys lze použít v situacích, kdy aplikace je přístupný z Internetu a požadované funkce jsou podporovány, ale ne Kestrel HTTP.sys. Další informace naleznete v tématu <xref:fundamentals/servers/httpsys>.

![Ovladač HTTP.sys komunikuje přímo s Internetem](httpsys/_static/httpsys-to-internet.png)

Ovladač HTTP.sys lze použít také pro aplikace, které jsou přístupné pouze k interní síti.

![Ovladač HTTP.sys komunikuje přímo s interní sítě](httpsys/_static/httpsys-to-internal.png)

Pokyny ke konfiguraci HTTP.sys, naleznete v tématu <xref:fundamentals/servers/httpsys>.

## <a name="aspnet-core-server-infrastructure"></a>ASP.NET Core server infrastruktury

[IApplicationBuilder](/dotnet/api/microsoft.aspnetcore.builder.iapplicationbuilder) k dispozici v `Startup.Configure` zpřístupňuje metodu [ServerFeatures](/dotnet/api/microsoft.aspnetcore.builder.iapplicationbuilder.serverfeatures) vlastnost typu [IFeatureCollection](/dotnet/api/microsoft.aspnetcore.http.features.ifeaturecollection). Kestrel a HTTP.sys vystavují jenom jednu funkci, [IServerAddressesFeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature), ale implementace jiný server může vystavit další funkce.

`IServerAddressesFeature` umožňuje zjistit, port, který obsahuje implementaci serveru vázán v době běhu.

## <a name="custom-servers"></a>Vlastní servery

Pokud integrované servery nesplňují požadavky aplikace, implementace vlastního serveru vytvořit. [Open Web Interface pro .NET (OWIN) průvodce](xref:fundamentals/owin) ukazuje, jak psát [Nowin](https://github.com/Bobris/Nowin)– na základě [IServer](/dotnet/api/microsoft.aspnetcore.hosting.server.iserver) implementace. Pouze funkce rozhraní, které aplikace používá potřeba provádění, když minimálně [IHttpRequestFeature](/dotnet/api/microsoft.aspnetcore.http.features.ihttprequestfeature) a [IHttpResponseFeature](/dotnet/api/microsoft.aspnetcore.http.features.ihttpresponsefeature) musí podporovat.

## <a name="server-startup"></a>Při spuštění serveru

Server se spustí, když je integrované vývojové prostředí (IDE) nebo editor spustí aplikaci:

* [Visual Studio](https://www.visualstudio.com/vs/) &ndash; profily spouštění můžete použít ke spuštění aplikace a serveru s oběma [služby IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/[modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) nebo konzoly.
* [Visual Studio Code](https://code.visualstudio.com/) &ndash; aplikace a serveru jsou spouštěny [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode), která aktivuje CoreCLR ladicího programu.
* [Visual Studio pro Mac](https://www.visualstudio.com/vs/mac/) &ndash; aplikace a serveru jsou spouštěny [ladicí program Mono konfigurace Soft-režim](http://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/).

Při spuštění aplikace z příkazového řádku ve složce projektu [dotnet spustit](/dotnet/core/tools/dotnet-run) spustí aplikace a serveru (přes Kestrel a pouze HTTP.sys). Konfigurace je určena `-c|--configuration` možnost, který je nastaven na hodnotu `Debug` (výchozí) nebo `Release`. Pokud jsou k dispozici v profily spouštění *launchSettings.json* souboru, použijte `--launch-profile <NAME>` možnost nastavit profil spuštění (například `Development` nebo `Production`). Další informace najdete v tématu [dotnet spustit](/dotnet/core/tools/dotnet-run) a [vytváření distribučních balíčků .NET Core](/dotnet/core/build/distribution-packaging).

## <a name="http2-support"></a>Podpora HTTP/2

[HTTP/2](https://httpwg.org/specs/rfc7540.html) se podporuje s ASP.NET Core v následujících scénářích nasazení:

::: moniker range=">= aspnetcore-2.2"

* [Kestrel](xref:fundamentals/servers/kestrel#http2-support)
  * Operační systém
    * Windows Server 2016 nebo Windows 10 nebo novější&dagger;
    * Linux s OpenSSL 1.0.2 nebo novější (například Ubuntu 16.04 nebo novější)
    * HTTP/2 budou podporované v systému macOS v budoucí verzi.
  * Cílová architektura: .NET Core 2.2 nebo vyšší
* [HTTP.sys](xref:fundamentals/servers/httpsys#http2-support)
  * Windows Server 2016 nebo Windows 10 nebo novější
  * Cílová architektura: Není k dispozici pro nasazení souboru HTTP.sys.
* [Služba IIS (v procesu)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016 nebo Windows 10 nebo novější; IIS 10 nebo novější.
  * Cílová architektura: .NET Core 2.2 nebo vyšší
* [Služba IIS (out-of-process)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016 nebo Windows 10 nebo novější; IIS 10 nebo novější.
  * Připojení k serveru edge veřejně přístupných používat HTTP/2, ale připojení reverzního proxy serveru na Kestrel používá HTTP/1.1.
  * Cílová architektura: Není k dispozici pro nasazení na více instancí procesu služby IIS.

&dagger;Kestrel má omezenou podporu pro HTTP/2 pro systém Windows Server 2012 R2 a Windows 8.1. Podpora je omezená, protože seznam podporovaných šifer TLS sady k dispozici v těchto operačních systémech je omezen. Certifikát vytvořený pomocí křivky digitální podpis algoritmů ECDSA (Elliptic) může být nutné zabezpečení připojení protokol TLS.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [HTTP.sys](xref:fundamentals/servers/httpsys#http2-support)
  * Windows Server 2016 nebo Windows 10 nebo novější
  * Cílová architektura: Není k dispozici pro nasazení souboru HTTP.sys.
* [Služba IIS (out-of-process)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016 nebo Windows 10 nebo novější; IIS 10 nebo novější.
  * Připojení k serveru edge veřejně přístupných používat HTTP/2, ale připojení reverzního proxy serveru na Kestrel používá HTTP/1.1.
  * Cílová architektura: Není k dispozici pro nasazení na více instancí procesu služby IIS.

::: moniker-end

Musíte použít připojení HTTP/2 [vyjednávání protokolu v aplikační vrstvě (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) a TLS 1.2 nebo vyšší. Další informace najdete v tématech, které se týkají scénářů nasazení serveru.

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/servers/kestrel>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:fundamentals/servers/httpsys>

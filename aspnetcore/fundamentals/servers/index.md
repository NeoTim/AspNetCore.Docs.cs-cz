---
title: Implementace webového serveru v ASP.NET Core
author: rick-anderson
description: Seznamte se s webovými servery Kestrel a HTTP. sys pro ASP.NET Core. Naučte se, jak vybrat server a kdy použít reverzní proxy server.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
uid: fundamentals/servers/index
ms.openlocfilehash: d46793ef54c99fe609b5983c5a658fb7b20032fa
ms.sourcegitcommit: f40c9311058c9b1add4ec043ddc5629384af6c56
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74289066"
---
# <a name="web-server-implementations-in-aspnet-core"></a>Implementace webového serveru v ASP.NET Core

Tím, že [Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen zastavení](https://twitter.com/halter73)a [Chris Rossův](https://github.com/Tratcher)

Aplikace ASP.NET Core běží s implementací vnitroprocesového serveru HTTP. Implementace serveru naslouchá požadavkům HTTP a rozloží je do aplikace jako sadu [funkcí požadavků](xref:fundamentals/request-features) složených do <xref:Microsoft.AspNetCore.Http.HttpContext>.

## <a name="kestrel"></a>Kestrel

Kestrel je výchozí webový server určený ASP.NET Core šablonami projektů.

Použijte Kestrel:

* Sám sebe jako hraniční Server zpracovávající požadavky přímo ze sítě, včetně Internetu.

  ![Kestrel komunikuje přímo s internetem bez zpětného proxy server](kestrel/_static/kestrel-to-internet2.png)

* Pomocí *reverzní proxy server*, například [Internetová informační služba (IIS)](https://www.iis.net/), [Nginx](https://nginx.org)nebo [Apache](https://httpd.apache.org/). Reverzní proxy server přijímá požadavky HTTP z Internetu a přesměruje je na Kestrel.

  ![Kestrel nepřímo komunikuje s internetem prostřednictvím reverzní proxy server, jako je IIS, Nginx nebo Apache.](kestrel/_static/kestrel-to-internet.png)

Je podporována buď&mdash;konfigurace hostování s nebo bez&mdash;proxy server.

Pokyny ke konfiguraci Kestrel a informace o tom, kdy použít Kestrel v konfiguraci reverzního proxy serveru, najdete v tématu <xref:fundamentals/servers/kestrel>.

::: moniker range=">= aspnetcore-2.2"

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

ASP.NET Core dodávána s následujícím:

* [Kestrel Server](xref:fundamentals/servers/kestrel) je výchozí implementace serveru HTTP pro různé platformy.
* HTTP server IIS je [vnitroprocesové Server](#hosting-models) pro službu IIS.
* [Http. sys Server](xref:fundamentals/servers/httpsys) je server http pouze pro systém Windows založený na [ovladači jádra HTTP. sys a rozhraní API serveru http](/windows/desktop/Http/http-api-start-page).

Při použití [služby IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) nebo [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)aplikace buď běží:

* Ve stejném procesu jako pracovní proces služby IIS (v rámci [procesu hostování modelu](#hosting-models)) se serverem HTTP služby IIS. Doporučená konfigurace je *vnitroprocesové* .
* V procesu odděleném od pracovního procesu služby IIS ( [mimo proces hostující model](#hosting-models)) se [serverem Kestrel](#kestrel).

[Modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) je nativní modul služby IIS, který zpracovává NATIVNÍ požadavky služby IIS mezi službou IIS a VNITROPROCESOVÉ serverem HTTP služby IIS nebo Kestrel. Další informace najdete v tématu <xref:host-and-deploy/aspnet-core-module>.

## <a name="hosting-models"></a>Modely hostingu

Při použití hostování v rámci procesu ASP.NET Core aplikace běží ve stejném procesu jako jeho pracovní proces služby IIS. Hostování v rámci procesů poskytují lepší výkon než hostování mimo procesy, protože požadavky nejsou proxy serverem přes adaptér zpětné smyčky, síťové rozhraní, které vrátí odchozí síťový provoz zpátky do stejného počítače. Služba IIS zpracovává správu procesů pomocí [aktivační služby procesů systému Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

Pomocí mimoprocesového hostování ASP.NET Core aplikace běží v procesu odděleném od pracovního procesu služby IIS a modul zpracovává správu procesů. Modul spustí proces pro aplikaci ASP.NET Core, když první požadavek dorazí a restartuje aplikaci, pokud se ukončí nebo dojde k chybě. To je v podstatě stejné chování jako u aplikací, které běží v procesu, které jsou spravovány službou was [(Windows Process Activation Service)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

Další informace a pokyny k konfiguraci najdete v následujících tématech:

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macostabmacos"></a>[macOS](#tab/macos)

ASP.NET Core se dodává se [serverem Kestrel](xref:fundamentals/servers/kestrel), což je výchozí server HTTP pro různé platformy.

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

ASP.NET Core se dodává se [serverem Kestrel](xref:fundamentals/servers/kestrel), což je výchozí server HTTP pro různé platformy.

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

ASP.NET Core dodávána s následujícím:

* [Kestrel Server](xref:fundamentals/servers/kestrel) je výchozím serverem HTTP pro různé platformy.
* [Http. sys Server](xref:fundamentals/servers/httpsys) je server http pouze pro systém Windows založený na [ovladači jádra HTTP. sys a rozhraní API serveru http](/windows/desktop/Http/http-api-start-page).

Při použití [služby IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) nebo [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)aplikace běží v procesu odděleně od pracovního procesu služby IIS (*mimo proces*) se [serverem Kestrel](#kestrel).

Vzhledem k tomu, že ASP.NET Core aplikace běží v procesu odděleném od pracovního procesu služby IIS, modul zpracovává správu procesů. Modul spustí proces pro aplikaci ASP.NET Core, když první požadavek dorazí a restartuje aplikaci, pokud se ukončí nebo dojde k chybě. To je v podstatě stejné chování jako u aplikací, které běží v procesu, které jsou spravovány službou was [(Windows Process Activation Service)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

Následující diagram znázorňuje vztah mezi službou IIS, modulem ASP.NET Core a aplikací hostovanou mimo proces:

![Modul ASP.NET Core](_static/ancm-outofprocess.png)

Požadavky přicházející z webu do ovladače HTTP. sys v režimu jádra. Ovladač směruje požadavky do služby IIS na konfigurovaném portu webu, obvykle 80 (HTTP) nebo 443 (HTTPS). Modul předá požadavky do Kestrel na náhodném portu pro aplikaci, což není port 80 nebo 443.

Modul Určuje port prostřednictvím proměnné prostředí při spuštění a [Služba IIS Integration middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) nakonfiguruje server tak, aby naslouchal `http://localhost:{port}`. Budou provedeny další kontroly a požadavky, které nepocházejí z modulu, jsou odmítnuty. Modul nepodporuje předávání HTTPS, takže požadavky se předávají přes protokol HTTP i v případě, že je služba IIS prostřednictvím protokolu HTTPS přijímá.

Po Kestrel žádosti z modulu se požadavek odešle do kanálu middlewaru ASP.NET Core. Kanál middlewaru zpracovává požadavek a předá ho jako instanci `HttpContext` do logiky aplikace. Middleware přidaný integrací služby IIS: aktualizace schématu, vzdálené IP adresy a pathbase pro přesměrování požadavku do Kestrel. Odpověď aplikace se předává zpátky do služby IIS, která ji přenáší zpátky do klienta HTTP, který žádost inicioval.

Pokyny ke konfiguraci služby IIS a ASP.NET Core modulu najdete v následujících tématech:

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macostabmacos"></a>[macOS](#tab/macos)

ASP.NET Core se dodává se [serverem Kestrel](xref:fundamentals/servers/kestrel), což je výchozí server HTTP pro různé platformy.

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

ASP.NET Core se dodává se [serverem Kestrel](xref:fundamentals/servers/kestrel), což je výchozí server HTTP pro různé platformy.

---

::: moniker-end

### <a name="nginx-with-kestrel"></a>Nginx s Kestrel

Informace o tom, jak používat Nginx v systému Linux jako reverzní proxy server pro Kestrel, najdete v tématu <xref:host-and-deploy/linux-nginx>.

### <a name="apache-with-kestrel"></a>Apache s Kestrel

Informace o tom, jak používat Apache v systému Linux jako reverzní proxy server pro Kestrel, najdete v tématu <xref:host-and-deploy/linux-apache>.

## <a name="httpsys"></a>HTTP.sys

Pokud ASP.NET Core aplikace běží na Windows, HTTP. sys je alternativou k Kestrel. Kestrel se obecně doporučuje pro nejlepší výkon. HTTP. sys se dá použít ve scénářích, kde se aplikace zveřejňuje na internetu a požadované funkce podporuje HTTP. sys, ale ne Kestrel. Další informace najdete v tématu <xref:fundamentals/servers/httpsys>.

![HTTP. sys komunikuje přímo s internetem.](httpsys/_static/httpsys-to-internet.png)

HTTP. sys se dá použít taky pro aplikace, které se zveřejňují jenom pro interní síť.

![HTTP. sys komunikuje přímo s interní sítí.](httpsys/_static/httpsys-to-internal.png)

Pokyny k konfiguraci HTTP. sys najdete v tématu <xref:fundamentals/servers/httpsys>.

## <a name="aspnet-core-server-infrastructure"></a>Serverová infrastruktura ASP.NET Core

<xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> k dispozici v metodě `Startup.Configure` zpřístupňuje vlastnost <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> typu <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection>. Kestrel a HTTP. sys zveřejňují jenom jednu funkci, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>, ale různé implementace serveru můžou vystavovat další funkce.

`IServerAddressesFeature` lze použít k zjištění, který port má implementace serveru svázána za běhu.

## <a name="custom-servers"></a>Vlastní servery

Pokud vestavěné servery nesplňují požadavky aplikace, je možné vytvořit vlastní implementaci serveru. [Průvodce rozhraním Open Web Interface for .NET (Owin)](xref:fundamentals/owin) ukazuje, jak napsat implementaci <xref:Microsoft.AspNetCore.Hosting.Server.IServer> založenou na [Nowin](https://github.com/Bobris/Nowin). Jenom rozhraní funkcí, které aplikace používá, vyžaduje implementaci, i když na minimální <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> a <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature> musí být podporované.

## <a name="server-startup"></a>Spuštění serveru

Server se spustí, když integrované vývojové prostředí (IDE) nebo editor spustí aplikaci:

* K spuštění aplikace a serveru pomocí modulu [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/[ASP.NET Core](xref:host-and-deploy/aspnet-core-module) nebo konzoly můžete použít profily spuštění sady [Visual Studio](https://visualstudio.microsoft.com) &ndash;.
* [Visual Studio Code](https://code.visualstudio.com/) &ndash; aplikace a Server spouští [omnisharp](https://github.com/OmniSharp/omnisharp-vscode), který aktivuje ladicí program CoreCLR.
* [Visual Studio pro Mac](https://visualstudio.microsoft.com/vs/mac/) &ndash; aplikace a Server jsou spouštěny pomocí [ladicího programu mono v tichém režimu](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/).

Při spuštění aplikace z příkazového řádku ve složce projektu spustí [dotnet spuštění](/dotnet/core/tools/dotnet-run) aplikace a serveru (jenom Kestrel a http. sys). Konfigurace je určena možností `-c|--configuration`, která je nastavena na hodnotu `Debug` (výchozí) nebo `Release`.

Soubor *launchSettings. JSON* poskytuje konfiguraci při spouštění aplikace s `dotnet run` nebo pomocí ladicího programu, který je integrovaný do nástrojů, jako je například Visual Studio. Pokud jsou v souboru *launchSettings. JSON* přítomné spouštěcí profily, použijte možnost `--launch-profile {PROFILE NAME}` s příkazem `dotnet run` nebo vyberte profil v aplikaci Visual Studio. Další informace najdete v tématu věnovaném vytváření balíčků [dotnet](/dotnet/core/tools/dotnet-run) a [balíčku pro distribuci .NET Core](/dotnet/core/build/distribution-packaging).

## <a name="http2-support"></a>Podpora HTTP/2

[Http/2](https://httpwg.org/specs/rfc7540.html) se podporuje s ASP.NET Core v následujících scénářích nasazení:

::: moniker range=">= aspnetcore-2.2"

* [Kestrel](xref:fundamentals/servers/kestrel#http2-support)
  * Operační systém
    * Windows Server 2016/Windows 10 nebo novější&dagger;
    * Linux s OpenSSL 1.0.2 nebo novějším (například Ubuntu 16,04 nebo novější)
    * HTTP/2 se v budoucí verzi podporuje v macOS.
  * Cílová architektura: .NET Core 2,2 nebo novější
* [HTTP.sys](xref:fundamentals/servers/httpsys#http2-support)
  * Windows Server 2016/Windows 10 nebo novější
  * Cílová architektura: neplatí pro nasazení HTTP. sys.
* [Služba IIS (v rámci procesu)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016 nebo Windows 10 nebo novější; IIS 10 nebo novější.
  * Cílová architektura: .NET Core 2,2 nebo novější
* [Služba IIS (mimo jiné procesy)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016 nebo Windows 10 nebo novější; IIS 10 nebo novější.
  * Veřejná připojení hraničních serverů používají HTTP/2, ale připojení reverzního proxy serveru k Kestrel používá protokol HTTP/1.1.
  * Cílová architektura: neplatí pro nasazení mimo procesy služby IIS.

&dagger;Kestrel má omezená podpora HTTP/2 na Windows Serveru 2012 R2 a Windows 8.1. Podpora je omezená, protože seznam podporovaných šifrovacích sad TLS, které jsou k dispozici v těchto operačních systémech, je omezený. Pro zabezpečení připojení TLS může být vyžadován certifikát vygenerovaný pomocí algoritmu ECDSA (s připojením typu eliptická křivka).

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [HTTP.sys](xref:fundamentals/servers/httpsys#http2-support)
  * Windows Server 2016/Windows 10 nebo novější
  * Cílová architektura: neplatí pro nasazení HTTP. sys.
* [Služba IIS (mimo jiné procesy)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016 nebo Windows 10 nebo novější; IIS 10 nebo novější.
  * Veřejná připojení hraničních serverů používají HTTP/2, ale připojení reverzního proxy serveru k Kestrel používá protokol HTTP/1.1.
  * Cílová architektura: neplatí pro nasazení mimo procesy služby IIS.

::: moniker-end

Připojení HTTP/2 musí používat [vyjednávání protokolu aplikační vrstvy (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) a TLS 1,2 nebo novější. Další informace najdete v tématech, která se týkají scénářů nasazení serveru.

## <a name="additional-resources"></a>Další zdroje informací:

* <xref:fundamentals/servers/kestrel>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:fundamentals/servers/httpsys>

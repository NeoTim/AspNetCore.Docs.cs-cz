---
title: Implementace serveru HTTP.sys web v ASP.NET Core
author: guardrex
description: Další informace o souboru HTTP.sys, webový server pro ASP.NET Core ve Windows. Založená na ovladač HTTP.sys režimu jádra, ovladač HTTP.sys se o alternativu k Kestrel, který lze použít pro přímé připojení k Internetu bez služby IIS.
monikerRange: '>= aspnetcore-2.0'
ms.author: tdykstra
ms.custom: mvc
ms.date: 12/01/2018
uid: fundamentals/servers/httpsys
ms.openlocfilehash: 8810fd295e8c4269812e712ce2fdc9b9fa2bbb4f
ms.sourcegitcommit: 9bb58d7c8dad4bbd03419bcc183d027667fefa20
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52861690"
---
# <a name="httpsys-web-server-implementation-in-aspnet-core"></a>Implementace serveru HTTP.sys web v ASP.NET Core

Podle [Petr Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), a [Luke Latham](https://github.com/guardrex)

> [!NOTE]
> Toto téma se vztahuje na verzi ASP.NET Core 2.0 nebo novější. V dřívějších verzích sady ASP.NET Core, má název souboru HTTP.sys [WebListener](xref:fundamentals/servers/weblistener).

[Ovladač HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) je [webového serveru pro ASP.NET Core](xref:fundamentals/servers/index) pouze spuštěného na Windows. Ovladač HTTP.sys se o alternativu k [Kestrel](xref:fundamentals/servers/kestrel) serveru a nabízí některé funkce, aby Kestrel neposkytuje.

> [!IMPORTANT]
> Není kompatibilní s HTTP.sys [modul ASP.NET Core](xref:fundamentals/servers/aspnet-core-module) a nelze jej použít s IIS nebo IIS Express.

Ovladač HTTP.sys podporuje následující funkce:

* [Ověřování Windows](xref:security/authentication/windowsauth)
* Sdílení portů
* Protokol HTTPS se SNI
* HTTP/2 přes protokol TLS (Windows 10 nebo novější)
* Přenos souborů s přímým přístupem
* Ukládání odpovědí do mezipaměti
* Protokoly Websocket (Windows 8 nebo novější)

Podporované verze Windows:

* Windows 7 nebo novější
* Windows Server 2008 R2 nebo novější

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/sample) ([stažení](xref:index#how-to-download-a-sample))

## <a name="when-to-use-httpsys"></a>Kdy použít HTTP.sys

Je užitečné pro nasazení HTTP.sys kde:

* Je potřeba zpřístupnit server přímo k Internetu bez použití služby IIS.

  ![Ovladač HTTP.sys komunikuje přímo s Internetem](httpsys/_static/httpsys-to-internet.png)

* Vnitřní nasazení vyžaduje funkci, není k dispozici v Kestrel, jako například [ověřování Windows](xref:security/authentication/windowsauth).

  ![Ovladač HTTP.sys komunikuje přímo s interní sítě](httpsys/_static/httpsys-to-internal.png)

Ovladač HTTP.sys je Vyspělá technologie, která chrání před mnoho typů útoků a poskytuje odolnost, zabezpečení a škálovatelnost plně funkční webového serveru. Služba IIS pracuje jako naslouchací proces protokolu HTTP na základě ovladače HTTP.sys.

## <a name="http2-support"></a>Podpora HTTP/2

[HTTP/2](https://httpwg.org/specs/rfc7540.html) je povolený pro aplikace ASP.NET Core, pokud tyto požadavky byly splněny:

* Windows Server 2016 nebo Windows 10 nebo novější
* [Vyjednávání protokolu v aplikační vrstvě (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) připojení
* Protokol TLS 1.2 nebo vyšší připojení

::: moniker range=">= aspnetcore-2.2"

Pokud se připojení HTTP/2, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) sestavy `HTTP/2`.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Pokud se připojení HTTP/2, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) sestavy `HTTP/1.1`.

::: moniker-end

HTTP/2 je standardně povolená. Pokud nedojde k připojení k protokolu HTTP/2, přejde připojení HTTP/1.1. V příští verzi Windows protokolu HTTP/2 konfigurace příznaky bude k dispozici, včetně zakázat HTTP/2 se souborem HTTP.sys.

## <a name="kernel-mode-authentication-with-kerberos"></a>Ověřování pomocí protokolu Kerberos v režimu jádra

Ovladač HTTP.sys delegáty pro ověřování v režimu jádra ověřování protokolem Kerberos. Režim ověřování uživatele nepodporuje protokolů Kerberos a HTTP.sys. Účet počítače musí být použité k dešifrování token/lístek služby Kerberos, která se získá z Active Directory a předá klienta na serveru k ověření uživatele. Zaregistrujte hlavní název služby (SPN) příslušného hostitele není uživatel aplikace.

## <a name="how-to-use-httpsys"></a>Jak používat ovladač HTTP.sys

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a>Nakonfigurovat aplikaci ASP.NET Core, aby používala HTTP.sys

1. Odkaz na balíček v souboru projektu není povinné, při použití [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)) (ASP.NET Core 2.1 nebo novější). Pokud nepoužíváte `Microsoft.AspNetCore.App` Microsoft.aspnetcore.all, přidejte odkaz na balíček [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).

2. Volání [UseHttpSys](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderhttpsysextensions.usehttpsys) rozšiřující metoda při vytváření webového hostitele, zadáte požadované [HTTP.sys možnosti](/dotnet/api/microsoft.aspnetcore.server.httpsys.httpsysoptions):

   [!code-csharp[](httpsys/sample/Program.cs?name=snippet1&highlight=4-12)]

   Další konfigurace HTTP.sys probíhá prostřednictvím funkce [nastavení registru](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).

   **Možnosti HTTP.sys**

   | Vlastnost | Popis | Výchozí |
   | -------- | ----------- | :-----: |
   | [AllowSynchronousIO](/dotnet/api/microsoft.aspnetcore.server.httpsys.httpsysoptions.allowsynchronousio) | Řídí, zda je povolen synchronní vstupně výstupní `HttpContext.Request.Body` a `HttpContext.Response.Body`. | `true` |
   | [Authentication.AllowAnonymous](/dotnet/api/microsoft.aspnetcore.server.httpsys.authenticationmanager.allowanonymous) | Povolit anonymní žádosti. | `true` |
   | [Authentication.Schemes](/dotnet/api/microsoft.aspnetcore.server.httpsys.authenticationmanager.schemes) | Určení povolených ověřovací schémata. Může změnit kdykoli před disposing naslouchací proces. Hodnoty jsou poskytovány [schémata AuthenticationSchemes výčtu](/dotnet/api/microsoft.aspnetcore.server.httpsys.authenticationschemes): `Basic`, `Kerberos`, `Negotiate`, `None`, a `NTLM`. | `None` |
   | [EnableResponseCaching](/dotnet/api/microsoft.aspnetcore.server.httpsys.httpsysoptions.enableresponsecaching) | Pokus o [režimu jádra](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) ukládání do mezipaměti pro odpovědi s oprávněné záhlaví. Odpověď nesmí obsahovat `Set-Cookie`, `Vary`, nebo `Pragma` záhlaví. Musí obsahovat `Cache-Control` záhlaví to `public` a buď `shared-max-age` nebo `max-age` hodnotu, nebo `Expires` záhlaví. | `true` |
   | [MaxAccepts](/dotnet/api/microsoft.aspnetcore.server.httpsys.httpsysoptions.maxaccepts) | Maximální počet souběžných přijímá. | 5 &times; [prostředí.<br> ProcessorCount](/dotnet/api/system.environment.processorcount) |
   | [MaxConnections](/dotnet/api/microsoft.aspnetcore.server.httpsys.httpsysoptions.maxconnections) | Maximální počet souběžných připojení tak, aby přijímal. Použití `-1` pro nekonečno. Použít `null` použít nastavení v registru počítače. | `null`<br>(bez omezení) |
   | [MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.httpsys.httpsysoptions.maxrequestbodysize) | Zobrazit <a href="#maxrequestbodysize">MaxRequestBodySize</a> oddílu. | 30000000 bajtů<br>(~28.6 MB) |
   | [RequestQueueLimit](/dotnet/api/microsoft.aspnetcore.server.httpsys.httpsysoptions.requestqueuelimit) | Maximální počet požadavků, které lze zařadit do fronty. | 1000 |
   | [ThrowWriteExceptions](/dotnet/api/microsoft.aspnetcore.server.httpsys.httpsysoptions.throwwriteexceptions) | Označení Pokud zápisů tělo odpovědi odpojí selžou z důvodu klienta by měl vyvolat výjimky nebo dokončení normálně. | `false`<br>(obvykle dokončení) |
   | [Vypršení časových limitů](/dotnet/api/microsoft.aspnetcore.server.httpsys.httpsysoptions.timeouts) | Vystavení HTTP.sys [TimeoutManager](/dotnet/api/microsoft.aspnetcore.server.httpsys.timeoutmanager) konfigurace, která může být také nakonfigurována v registru. Pomocí rozhraní API odkazů na další informace o každém nastavení, včetně výchozích hodnot:<ul><li>[TimeoutManager.DrainEntityBody](/dotnet/api/microsoft.aspnetcore.server.httpsys.timeoutmanager.drainentitybody) &ndash; povolený čas pro rozhraní API serveru HTTP, chcete-li vyprázdnit obsah entity pro zachování připojení.</li><li>[TimeoutManager.EntityBody](/dotnet/api/microsoft.aspnetcore.server.httpsys.timeoutmanager.entitybody) &ndash; povolený čas pro obsah entity žádosti k doručení.</li><li>[TimeoutManager.HeaderWait](/dotnet/api/microsoft.aspnetcore.server.httpsys.timeoutmanager.headerwait) &ndash; povolený čas pro rozhraní API serveru HTTP k analýze záhlaví žádosti.</li><li>[TimeoutManager.IdleConnection](/dotnet/api/microsoft.aspnetcore.server.httpsys.timeoutmanager.idleconnection) &ndash; povolený čas pro nečinné připojení.</li><li>[TimeoutManager.MinSendBytesPerSecond](/dotnet/api/microsoft.aspnetcore.server.httpsys.timeoutmanager.minsendbytespersecond) &ndash; minimální míra pro odpověď v pro odesílání.</li><li>[TimeoutManager.RequestQueue](/dotnet/api/microsoft.aspnetcore.server.httpsys.timeoutmanager.requestqueue) &ndash; dobu povolenou pro požadavek na zůstat ve frontě, než ji převezme ho.</li></ul> |  |
   | [UrlPrefixes](/dotnet/api/microsoft.aspnetcore.server.httpsys.httpsysoptions.urlprefixes) | Zadejte [UrlPrefixCollection](/dotnet/api/microsoft.aspnetcore.server.httpsys.urlprefixcollection) registrace se souborem HTTP.sys. Nejužitečnější je [UrlPrefixCollection.Add](/dotnet/api/microsoft.aspnetcore.server.httpsys.urlprefixcollection.add), který se používá k přidání předpony do kolekce. Ty mohou změnit kdykoli před disposing naslouchací proces. |  |

   <a name="maxrequestbodysize"></a>
   **MaxRequestBodySize**

   Maximální povolená velikost obsahu jakékoli žádosti v bajtech. Pokud je nastavena na `null`, žádost o maximální velikost textu neomezený. Toto omezení nemá žádný vliv na upgradované připojení, které jsou vždy neomezený počet.

   Doporučená metoda k přepsání omezení v aplikaci ASP.NET Core MVC pro jeden `IActionResult` , je použít [RequestSizeLimitAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requestsizelimitattribute) atributu na metodu akce:

   ```csharp
   [RequestSizeLimit(100000000)]
   public IActionResult MyActionMethod()
   ```

   Pokud se aplikace pokusí po aplikace bylo zahájeno čtení požadavku konfigurovat omezení na vyžádání, je vyvolána výjimka. `IsReadOnly` Vlastnost lze použít k označení, pokud `MaxRequestBodySize` vlastnost je ve stavu jen pro čtení, to znamená Konfigurace limitu bude příliš pozdě.

   Pokud aplikace by měly přepsat [MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.httpsys.httpsysoptions.maxrequestbodysize) na žádost, použijte [IHttpMaxRequestBodySizeFeature](/dotnet/api/microsoft.aspnetcore.http.features.ihttpmaxrequestbodysizefeature):

   [!code-csharp[](httpsys/sample/Startup.cs?name=snippet1&highlight=6-7)]

3. Pokud používáte Visual Studio, ujistěte se, že aplikace není nakonfigurovaná pro spuštění služby IIS nebo IIS Express.

   V sadě Visual Studio je výchozí profil spuštění pro službu IIS Express. Chcete-li spustit projekt jako konzolovou aplikaci, ručně změňte vybraný profil, jak je znázorněno na následujícím snímku obrazovky:

   ![Vyberte profil aplikace konzoly](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a>Konfigurace Windows serveru

1. Pokud je aplikace [nasazení závisí na architektuře](/dotnet/core/deploying/#framework-dependent-deployments-fdd), nainstalujte .NET Core a .NET Framework (Pokud aplikace cílí na rozhraní .NET Framework aplikace .NET Core).

   * **.NET core** &ndash; Pokud aplikace vyžaduje .NET Core, získání a spuštění instalačního programu .NET Core z [.NET všechny soubory ke stažení](https://www.microsoft.com/net/download/all).
   * **Rozhraní .NET framework** &ndash; Pokud aplikace vyžaduje rozhraní .NET Framework, přečtěte si téma [rozhraní .NET Framework: Průvodce instalací](/dotnet/framework/install/) najít pokyny k instalaci. Instalace vyžaduje rozhraní .NET Framework. Instalační program pro nejnovější rozhraní .NET Framework lze nalézt v [.NET všechny soubory ke stažení](https://www.microsoft.com/net/download/all).

2. Konfigurace adresy URL a portů pro aplikaci.

   Ve výchozím nastavení, ASP.NET Core váže k `http://localhost:5000`. Pokud chcete nakonfigurovat předpony adres URL a portů, mezi možnosti patří pomocí:

   * [UseUrls](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useurls)
   * `urls` argument příkazového řádku
   * `ASPNETCORE_URLS` Proměnná prostředí
   * [UrlPrefixes](/dotnet/api/microsoft.aspnetcore.server.httpsys.httpsysoptions.urlprefixes)

   Následující příklad kódu ukazuje, jak používat [UrlPrefixes](/dotnet/api/microsoft.aspnetcore.server.httpsys.httpsysoptions.urlprefixes):

   [!code-csharp[](httpsys/sample/Program.cs?name=snippet1&highlight=11)]

   Výhodou `UrlPrefixes` je okamžitě vygenerována chybová zpráva pro nesprávně formátovaná předpony.

   Nastavení v `UrlPrefixes` přepsat `UseUrls` / `urls` / `ASPNETCORE_URLS` nastavení. Proto výhodou `UseUrls`, `urls`a `ASPNETCORE_URLS` proměnná prostředí je, že je snadnější přepínání mezi Kestrel a HTTP.sys. Další informace o `UseUrls`, `urls`, a `ASPNETCORE_URLS`, najdete v článku [hostitele v ASP.NET Core](xref:fundamentals/host/index) tématu.

   Používá HTTP.sys [formáty řetězců HTTP Server API UrlPrefix](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx).

   > [!WARNING]
   > Vazby nejvyšší úrovně zástupný znak (`http://*:80/` a `http://+:80`) by měl **není** použít. Vazby nejvyšší úrovně zástupný znak můžete otevřít aplikaci tak, aby slabá místa zabezpečení. To platí pro silné a slabé zástupné znaky. Používejte explicitní hostitele názvy místo zástupných znaků. Vazby zástupný znak subdoménu (například `*.mysub.com`) nemá toto bezpečnostní riziko, pokud celé nadřazené domény (nikoli `*.com`, což je ohrožené). Zobrazit [rfc7230 části-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) Další informace.

3. Preregister předpony adres URL pro vytvoření vazby na ovladač HTTP.sys a nastavit certifikáty x.509.

   Pokud předpony adres URL nejsou registrované ve Windows, můžete tuto aplikaci spusťte s oprávněními správce. Jedinou výjimkou je při vytváření vazby na místního hostitele pomocí více než 1 024 číslo portu HTTP (nikoli HTTPS). V takovém případě se vyžaduje oprávnění správce.

   1. Integrované nástroje pro konfiguraci HTTP.sys se *netsh.exe*. *Netsh.exe* slouží k předpony adres URL rezervovat a přiřadit certifikáty X.509. Nástroj vyžaduje oprávnění správce.

      Následující příklad ukazuje příkazy pro rezervaci předpony adres URL pro porty 80 a 443:

      ```console
      netsh http add urlacl url=http://+:80/ user=Users
      netsh http add urlacl url=https://+:443/ user=Users
      ```

      Následující příklad ukazuje, jak přiřadit certifikát X.509:

      ```console
      netsh http add sslcert ipport=0.0.0.0:443 certhash=MyCertHash_Here appid="{00000000-0000-0000-0000-000000000000}"
      ```

      Referenční dokumentace pro *netsh.exe*:

      * [Příkazy Netsh pro Hypertext Transfer Protocol (HTTP)](https://technet.microsoft.com/library/cc725882.aspx)
      * [UrlPrefix řetězce](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)

   2. Vytvoření certifikátů X.509 podepsaných svým držitelem, podle potřeby.

      [!INCLUDE [How to make an X.509 cert](../../includes/make-x509-cert.md)]

4. Otevřete porty brány firewall pro povolení provozu k dosažení HTTP.sys. Použití *netsh.exe* nebo [rutin prostředí PowerShell](https://technet.microsoft.com/library/jj554906).

## <a name="proxy-server-and-load-balancer-scenarios"></a>Proxy server a scénáře pro nástroj pro vyrovnávání zatížení

Pro aplikace hostované souborem HTTP.sys, které pracují s žádostí z Internetu nebo podnikové sítě může být další konfigurace požadovaných při hostování za proxy servery a nástroje pro vyrovnávání zatížení. Další informace najdete v tématu [konfigurace ASP.NET Core práci se servery proxy a nástroje pro vyrovnávání zatížení](xref:host-and-deploy/proxy-load-balancer).

## <a name="additional-resources"></a>Další zdroje

* [Server HTTP rozhraní API](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)
* [úložiště GitHub ASPNET/HttpSysServer (zdrojového kódu)](https://github.com/aspnet/HttpSysServer/)
* <xref:fundamentals/host/index>
* <xref:test/troubleshoot>

---
title: Implementace serveru HTTP.sys web v ASP.NET Core
author: guardrex
description: Další informace o souboru HTTP.sys, webový server pro ASP.NET Core ve Windows. Založená na ovladač HTTP.sys režimu jádra, ovladač HTTP.sys se o alternativu k Kestrel, který lze použít pro přímé připojení k Internetu bez služby IIS.
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 06/20/2019
uid: fundamentals/servers/httpsys
ms.openlocfilehash: 3f48cda8f89d3f3dcce220cad4e405472b510df4
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67814880"
---
# <a name="httpsys-web-server-implementation-in-aspnet-core"></a>Implementace serveru HTTP.sys web v ASP.NET Core

Podle [Petr Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), a [Luke Latham](https://github.com/guardrex)

[Ovladač HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) je [webového serveru pro ASP.NET Core](xref:fundamentals/servers/index) pouze spuštěného na Windows. Ovladač HTTP.sys se o alternativu k [Kestrel](xref:fundamentals/servers/kestrel) serveru a nabízí některé funkce, aby Kestrel neposkytuje.

> [!IMPORTANT]
> Ovladač HTTP.sys není kompatibilní se systémem [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) a nelze jej použít s IIS nebo IIS Express.

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

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/sample) ([stažení](xref:index#how-to-download-a-sample))

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

2. Volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> rozšiřující metoda při vytváření hostitele, zadáte požadované <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>. Následující příklad nastaví možnosti na výchozí hodnoty:

::: moniker range=">= aspnetcore-3.0"

   ```csharp
   public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
      WebHost.CreateDefaultBuilder(args)
          .UseStartup<Startup>()
          .UseHttpSys(options =>
          {
              options.AllowSynchronousIO = false;
              options.Authentication.Schemes = AuthenticationSchemes.None;
              options.Authentication.AllowAnonymous = true;
              options.MaxConnections = null;
              options.MaxRequestBodySize = 30000000;
              options.UrlPrefixes.Add("http://localhost:5000");
          });
   ```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

   [!code-csharp[](httpsys/sample/Program.cs?name=snippet1&highlight=4-12)]

::: moniker-end

   Další konfigurace HTTP.sys probíhá prostřednictvím funkce [nastavení registru](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).

   **Možnosti HTTP.sys**

::: moniker range=">= aspnetcore-3.0"

   | Vlastnost | Popis | Výchozí |
   | -------- | ----------- | :-----: |
   | [AllowSynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | Řídí, zda je povolen synchronní vstupně výstupní `HttpContext.Request.Body` a `HttpContext.Response.Body`. | `false` |
   | [Authentication.AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | Povolit anonymní žádosti. | `true` |
   | [Authentication.Schemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | Určení povolených ověřovací schémata. Může změnit kdykoli před disposing naslouchací proces. Hodnoty jsou poskytovány [schémata AuthenticationSchemes výčtu](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, a `NTLM`. | `None` |
   | [EnableResponseCaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | Pokus o [režimu jádra](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) ukládání do mezipaměti pro odpovědi s oprávněné záhlaví. Odpověď nesmí obsahovat `Set-Cookie`, `Vary`, nebo `Pragma` záhlaví. Musí obsahovat `Cache-Control` záhlaví to `public` a buď `shared-max-age` nebo `max-age` hodnotu, nebo `Expires` záhlaví. | `true` |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | Maximální počet souběžných přijímá. | 5 &times; [prostředí.<br> ProcessorCount](xref:System.Environment.ProcessorCount) |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | Maximální počet souběžných připojení tak, aby přijímal. Použití `-1` pro nekonečno. Použít `null` použít nastavení v registru počítače. | `null`<br>(bez omezení) |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | Zobrazit <a href="#maxrequestbodysize">MaxRequestBodySize</a> oddílu. | 30000000 bajtů<br>(~28.6 MB) |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | Maximální počet požadavků, které lze zařadit do fronty. | 1000 |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | Označení Pokud zápisů tělo odpovědi odpojí selžou z důvodu klienta by měl vyvolat výjimky nebo dokončení normálně. | `false`<br>(obvykle dokončení) |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | Vystavení HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> konfigurace, která může být také nakonfigurována v registru. Pomocí rozhraní API odkazů na další informace o každém nastavení, včetně výchozích hodnot:<ul><li>[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody) &ndash; povolený čas pro rozhraní API serveru HTTP, chcete-li vyprázdnit obsah entity pro zachování připojení.</li><li>[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody) &ndash; povolený čas pro obsah entity žádosti k doručení.</li><li>[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait) &ndash; povolený čas pro rozhraní API serveru HTTP k analýze záhlaví žádosti.</li><li>[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection) &ndash; povolený čas pro nečinné připojení.</li><li>[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond) &ndash; minimální míra pro odpověď v pro odesílání.</li><li>[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue) &ndash; dobu povolenou pro požadavek na zůstat ve frontě, než ji převezme ho.</li></ul> |  |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | Zadejte <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> registrace se souborem HTTP.sys. Nejužitečnější je [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), který se používá k přidání předpony do kolekce. Ty mohou změnit kdykoli před disposing naslouchací proces. |  |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

   | Vlastnost | Popis | Výchozí |
   | -------- | ----------- | :-----: |
   | [AllowSynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | Řídí, zda je povolen synchronní vstupně výstupní `HttpContext.Request.Body` a `HttpContext.Response.Body`. | `true` |
   | [Authentication.AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | Povolit anonymní žádosti. | `true` |
   | [Authentication.Schemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | Určení povolených ověřovací schémata. Může změnit kdykoli před disposing naslouchací proces. Hodnoty jsou poskytovány [schémata AuthenticationSchemes výčtu](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, a `NTLM`. | `None` |
   | [EnableResponseCaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | Pokus o [režimu jádra](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) ukládání do mezipaměti pro odpovědi s oprávněné záhlaví. Odpověď nesmí obsahovat `Set-Cookie`, `Vary`, nebo `Pragma` záhlaví. Musí obsahovat `Cache-Control` záhlaví to `public` a buď `shared-max-age` nebo `max-age` hodnotu, nebo `Expires` záhlaví. | `true` |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | Maximální počet souběžných přijímá. | 5 &times; [prostředí.<br> ProcessorCount](xref:System.Environment.ProcessorCount) |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | Maximální počet souběžných připojení tak, aby přijímal. Použití `-1` pro nekonečno. Použít `null` použít nastavení v registru počítače. | `null`<br>(bez omezení) |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | Zobrazit <a href="#maxrequestbodysize">MaxRequestBodySize</a> oddílu. | 30000000 bajtů<br>(~28.6 MB) |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | Maximální počet požadavků, které lze zařadit do fronty. | 1000 |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | Označení Pokud zápisů tělo odpovědi odpojí selžou z důvodu klienta by měl vyvolat výjimky nebo dokončení normálně. | `false`<br>(obvykle dokončení) |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | Vystavení HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> konfigurace, která může být také nakonfigurována v registru. Pomocí rozhraní API odkazů na další informace o každém nastavení, včetně výchozích hodnot:<ul><li>[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody) &ndash; povolený čas pro rozhraní API serveru HTTP, chcete-li vyprázdnit obsah entity pro zachování připojení.</li><li>[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody) &ndash; povolený čas pro obsah entity žádosti k doručení.</li><li>[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait) &ndash; povolený čas pro rozhraní API serveru HTTP k analýze záhlaví žádosti.</li><li>[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection) &ndash; povolený čas pro nečinné připojení.</li><li>[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond) &ndash; minimální míra pro odpověď v pro odesílání.</li><li>[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue) &ndash; dobu povolenou pro požadavek na zůstat ve frontě, než ji převezme ho.</li></ul> |  |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | Zadejte <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> registrace se souborem HTTP.sys. Nejužitečnější je [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), který se používá k přidání předpony do kolekce. Ty mohou změnit kdykoli před disposing naslouchací proces. |  |

::: moniker-end

   <a name="maxrequestbodysize"></a>

   **MaxRequestBodySize**

   Maximální povolená velikost obsahu jakékoli žádosti v bajtech. Pokud je nastavena na `null`, žádost o maximální velikost textu neomezený. Toto omezení nemá žádný vliv na upgradované připojení, které jsou vždy neomezený počet.

   Doporučená metoda k přepsání omezení v aplikaci ASP.NET Core MVC pro jeden `IActionResult` , je použít <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> atributu na metodu akce:

   ```csharp
   [RequestSizeLimit(100000000)]
   public IActionResult MyActionMethod()
   ```

   Pokud se aplikace pokusí po aplikace bylo zahájeno čtení požadavku konfigurovat omezení na vyžádání, je vyvolána výjimka. `IsReadOnly` Vlastnost lze použít k označení, pokud `MaxRequestBodySize` vlastnost je ve stavu jen pro čtení, to znamená Konfigurace limitu bude příliš pozdě.

   Pokud aplikace by měly přepsat <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> na žádost, použijte <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:

   [!code-csharp[](httpsys/sample/Startup.cs?name=snippet1&highlight=6-7)]

3. Pokud používáte Visual Studio, ujistěte se, že aplikace není nakonfigurovaná pro spuštění služby IIS nebo IIS Express.

   V sadě Visual Studio je výchozí profil spuštění pro službu IIS Express. Chcete-li spustit projekt jako konzolovou aplikaci, ručně změňte vybraný profil, jak je znázorněno na následujícím snímku obrazovky:

   ![Vyberte profil aplikace konzoly](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a>Konfigurace Windows serveru

1. Určení porty otevřete pro aplikaci a použití [brány Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) nebo [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) rutinu Powershellu otevřít porty brány firewall pro povolení provozu k dosažení HTTP.sys. V následující příkazy a konfigurace aplikací se používá port 443.

1. Při nasazování do virtuálního počítače Azure, otevřít porty ve [skupinu zabezpečení sítě](/azure/virtual-machines/windows/nsg-quickstart-portal). V následující příkazy a konfigurace aplikací se používá port 443.

1. Získat a nainstalovat certifikáty X.509, pokud je to nutné.

   Na Windows, vytvořte pomocí certifikátů podepsaných svým držitelem [rutinu Powershellu New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate). Nepodporovaná příklad naleznete v tématu [UpdateIISExpressSSLForChrome.ps1](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).

   Nainstalovat certifikáty podepsané svým držitelem nebo podepsaný certifikační Autoritou na serveru **místního počítače** > **osobní** ukládat.

1. Pokud je aplikace [nasazení závisí na architektuře](/dotnet/core/deploying/#framework-dependent-deployments-fdd), nainstalujte .NET Core a .NET Framework (Pokud aplikace cílí na rozhraní .NET Framework aplikace .NET Core).

   * **.NET core** &ndash; Pokud aplikace vyžaduje .NET Core, získání a spuštění **.NET Core Runtime** instalačního programu z [soubory ke stažení rozhraní .NET Core](https://dotnet.microsoft.com/download). Neinstalujte úplnou sadu SDK na serveru.
   * **Rozhraní .NET framework** &ndash; Pokud aplikace vyžaduje rozhraní .NET Framework, přečtěte si článek [Průvodce instalací rozhraní .NET Framework](/dotnet/framework/install/). Instalace vyžaduje rozhraní .NET Framework. Instalační program pro nejnovější rozhraní .NET Framework je k dispozici [soubory ke stažení rozhraní .NET Core](https://dotnet.microsoft.com/download) stránky.

   Pokud je aplikace [samostatná nasazení](/dotnet/core/deploying/#self-contained-deployments-scd), tato aplikace obsahuje modul runtime v jeho nasazení. Na serveru není nutné instalovat rozhraní framework.

1. Konfigurace adresy URL a portů v aplikaci.

   Ve výchozím nastavení, ASP.NET Core váže k `http://localhost:5000`. Pokud chcete nakonfigurovat předpony adres URL a portů, mezi možnosti patří:

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * `urls` argument příkazového řádku
   * `ASPNETCORE_URLS` Proměnná prostředí
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   Následující příklad kódu ukazuje, jak používat <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> s místní IP adresa serveru `10.0.0.4` na portu 443:

   [!code-csharp[](httpsys/sample_snapshot/Program.cs?name=snippet1&highlight=6)]

   Výhodou `UrlPrefixes` je okamžitě vygenerována chybová zpráva pro nesprávně formátovaná předpony.

   Nastavení v `UrlPrefixes` přepsat `UseUrls` / `urls` / `ASPNETCORE_URLS` nastavení. Proto výhodou `UseUrls`, `urls`a `ASPNETCORE_URLS` proměnná prostředí je, že je snadnější přepínání mezi Kestrel a HTTP.sys.

   Používá HTTP.sys [formáty řetězců HTTP Server API UrlPrefix](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx).

   > [!WARNING]
   > Vazby nejvyšší úrovně zástupný znak (`http://*:80/` a `http://+:80`) by měl **není** použít. Nejvyšší úrovně zástupné vazby vzniknout slabá místa zabezpečení aplikace. To platí pro silné a slabé zástupné znaky. Používejte explicitní hostitele nebo IP adresy místo zástupné znaky. Vazby zástupný znak subdoménu (například `*.mysub.com`) není bezpečnostní riziko, pokud celé nadřazené domény (nikoli `*.com`, což je ohrožené). Další informace najdete v tématu [RFC 7230: Oddíl 5.4: Hostitel](https://tools.ietf.org/html/rfc7230#section-5.4).

1. Preregister předpony adres URL na serveru.

   Integrované nástroje pro konfiguraci HTTP.sys se *netsh.exe*. *Netsh.exe* slouží k předpony adres URL rezervovat a přiřadit certifikáty X.509. Nástroj vyžaduje oprávnění správce.

   Použití *netsh.exe* nástroj pro registraci adresy URL pro aplikaci:

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * `<URL>` &ndash; Plně kvalifikovanou URL Uniform Resource Locator (). Nepoužívejte zástupné vazby. Použijte platný název hostitele nebo IP adresa. *Adresa URL musí obsahovat koncové lomítko.*
   * `<USER>` &ndash; Určuje jméno uživatele nebo skupiny uživatelů.

   V následujícím příkladu je místní IP adresa serveru `10.0.0.4`:

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   Při registraci adresy URL nástroj odpoví `URL reservation successfully added`.

   Chcete-li odstranit registrovaný adresu URL, použijte `delete urlacl` příkaz:

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. Zaregistrujte certifikáty X.509 na serveru.

   Použití *netsh.exe* nástroj k registraci certifikátů pro aplikace:

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * `<IP>` &ndash; Určuje místní IP adresa pro vazbu. Nepoužívejte zástupné vazby. Použijte platnou IP adresu.
   * `<PORT>` &ndash; Určuje port pro vazby.
   * `<THUMBPRINT>` &ndash; Kryptografický otisk certifikátu X.509.
   * `<GUID>` &ndash; Vygeneruje pro vývojáře identifikátor GUID představující aplikace k informačním účelům.

   Pro referenční účely uložení identifikátoru GUID v aplikaci jako značku balíčku:

   * V sadě Visual Studio:
     * Otevřete vlastnosti projektu aplikace kliknutím pravým tlačítkem na aplikaci v **Průzkumníka řešení** a vyberete **vlastnosti**.
     * Vyberte **balíčku** kartu.
     * Zadejte identifikátor GUID, které jste vytvořili **značky** pole.
   * Pokud nepoužíváte Visual Studio:
     * Otevření souboru projektu vaší aplikace.
     * Přidat `<PackageTags>` vlastnost na nový nebo existující `<PropertyGroup>` identifikátor GUID, který jste vytvořili:

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   V následujícím příkladu:

   * Místní IP adresa serveru je `10.0.0.4`.
   * Poskytuje online náhodný GUID generator `appid` hodnotu.

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   Při registraci certifikátu nástroj odpoví `SSL Certificate successfully added`.

   Chcete-li odstranit registrace certifikátu, použijte `delete sslcert` příkaz:

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   Referenční dokumentace pro *netsh.exe*:

   * [Příkazy Netsh pro Hypertext Transfer Protocol (HTTP)](https://technet.microsoft.com/library/cc725882.aspx)
   * [UrlPrefix Strings](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)

1. Spusťte aplikaci.

   Oprávnění správce se vyžaduje ke spuštění aplikace při vytváření vazby na místního hostitele pomocí protokolu HTTP (nikoli HTTPS) s více než 1 024 číslo portu. Pro další konfiguraci (například pomocí místní IP adresa nebo vazbu na port 443) spusťte aplikaci s oprávněním správce.

   Aplikace jsou reaguje na veřejnou IP adresu serveru. V tomto příkladu je server dostupný z Internetu na veřejné IP adrese `104.214.79.47`.

   V tomto příkladu se používá certifikát pro vývoj. Stránka načte bezpečně po vynechání upozornění na nedůvěryhodný certifikát prohlížeče.

   ![Načíst okno prohlížeče zobrazující indexová stránka aplikace](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a>Proxy server a scénáře pro nástroj pro vyrovnávání zatížení

Pro aplikace hostované souborem HTTP.sys, které pracují s žádostí z Internetu nebo podnikové sítě může být další konfigurace požadovaných při hostování za proxy servery a nástroje pro vyrovnávání zatížení. Další informace najdete v tématu [konfigurace ASP.NET Core práci se servery proxy a nástroje pro vyrovnávání zatížení](xref:host-and-deploy/proxy-load-balancer).

## <a name="additional-resources"></a>Další zdroje

* [Povolení ověřování Windows pomocí HTTP.sys](xref:security/authentication/windowsauth#httpsys)
* [Server HTTP rozhraní API](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)
* [úložiště GitHub ASPNET/HttpSysServer (zdrojového kódu)](https://github.com/aspnet/HttpSysServer/)
* [Hostitel](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

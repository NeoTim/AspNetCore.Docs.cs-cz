---
title: Implementace webového serveru HTTP. sys v ASP.NET Core
author: guardrex
description: Přečtěte si o HTTP. sys, webovém serveru pro ASP.NET Core ve Windows. Ovladač HTTP. sys založený na ovladači režimu jádra HTTP. sys je alternativou k Kestrel, která se dá použít k přímému připojení k Internetu bez služby IIS.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/20/2019
uid: fundamentals/servers/httpsys
ms.openlocfilehash: 5ee866c862f16c2c22539bf880b5a93415504fb1
ms.sourcegitcommit: 8835b6777682da6fb3becf9f9121c03f89dc7614
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69975521"
---
# <a name="httpsys-web-server-implementation-in-aspnet-core"></a>Implementace webového serveru HTTP. sys v ASP.NET Core

[Dykstra](https://github.com/tdykstra), [Chris Rossův](https://github.com/Tratcher)a [Luke Latham](https://github.com/guardrex)

[Http. sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) je [webový server pro ASP.NET Core](xref:fundamentals/servers/index) , který běží pouze v systému Windows. HTTP. sys je alternativou k serveru [Kestrel](xref:fundamentals/servers/kestrel) a nabízí některé funkce, které Kestrel neposkytuje.

> [!IMPORTANT]
> HTTP. sys není kompatibilní s [modulem ASP.NET Core](xref:host-and-deploy/aspnet-core-module) a nedá se použít se službou IIS nebo IIS Express.

HTTP. sys podporuje následující funkce:

* [Ověřování systému Windows](xref:security/authentication/windowsauth)
* Sdílení portů
* HTTPS s SNI
* HTTP/2 přes TLS (Windows 10 nebo novější)
* Přímý přenos souborů
* Ukládání odpovědí do mezipaměti
* WebSockets (Windows 8 nebo novější)

Podporované verze systému Windows:

* Windows 7 nebo novější
* Windows Server 2008 R2 nebo novější

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/sample) ([stažení](xref:index#how-to-download-a-sample))

## <a name="when-to-use-httpsys"></a>Kdy použít protokol HTTP. sys

HTTP. sys je užitečné pro nasazení, kde:

* Server je potřeba zveřejnit přímo na internetu bez použití IIS.

  ![HTTP. sys komunikuje přímo s internetem.](httpsys/_static/httpsys-to-internet.png)

* Interní nasazení vyžaduje funkci, která není v Kestrel k dispozici, jako je například [ověřování systému Windows](xref:security/authentication/windowsauth).

  ![HTTP. sys komunikuje přímo s interní sítí.](httpsys/_static/httpsys-to-internal.png)

HTTP. sys je Vyspělá technologie, která chrání před mnoha typy útoků a poskytuje odolnost, zabezpečení a škálovatelnost plně funkčního webového serveru. Samotná služba IIS běží jako naslouchací proces HTTP nad HTTP. sys.

## <a name="http2-support"></a>Podpora HTTP/2

[Protokol HTTP/2](https://httpwg.org/specs/rfc7540.html) je povolený pro aplikace ASP.NET Core, pokud jsou splněné následující základní požadavky:

* Windows Server 2016/Windows 10 nebo novější
* Připojení [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3)
* Protokol TLS 1.2 nebo vyšší připojení

::: moniker range=">= aspnetcore-2.2"

Pokud se připojení HTTP/2, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) sestavy `HTTP/2`.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Pokud se připojení HTTP/2, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) sestavy `HTTP/1.1`.

::: moniker-end

HTTP/2 je standardně povolená. Pokud připojení HTTP/2 není navázáno, připojení se vrátí k HTTP/1.1. V budoucí verzi Windows budou k dispozici příznaky konfigurace protokolu HTTP/2, včetně možnosti zakázat protokol HTTP/2 s protokolem HTTP. sys.

## <a name="kernel-mode-authentication-with-kerberos"></a>Ověřování v režimu jádra pomocí protokolu Kerberos

Ovladač HTTP.sys delegáty pro ověřování v režimu jádra ověřování protokolem Kerberos. Režim ověřování uživatele nepodporuje protokolů Kerberos a HTTP.sys. Účet počítače musí být použité k dešifrování token/lístek služby Kerberos, která se získá z Active Directory a předá klienta na serveru k ověření uživatele. Zaregistrujte hlavní název služby (SPN) příslušného hostitele není uživatel aplikace.

## <a name="how-to-use-httpsys"></a>Jak používat HTTP. sys

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a>Konfigurace aplikace ASP.NET Core pro použití HTTP. sys

1. Odkaz na balíček v souboru projektu není vyžadován při použití [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)) (ASP.NET Core 2,1 nebo novější). Pokud nepoužíváte `Microsoft.AspNetCore.App` Metapackage, přidejte odkaz na balíček do [Microsoft. AspNetCore. Server. HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).

2. Při sestavování hostitele zavolejte metodu <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions> rozšířeníaurčetepožadované.<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> Následující příklad nastaví možnosti na jejich výchozí hodnoty:

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

   Další konfiguraci HTTP. sys bude zpracována prostřednictvím [nastavení registru](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).

   **Možnosti HTTP. sys**

::: moniker range=">= aspnetcore-3.0"

   | Vlastnost | Popis | Výchozí |
   | -------- | ----------- | :-----: |
   | [AllowSynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | Určuje, zda je povolen synchronní vstup/výstup pro `HttpContext.Request.Body` a `HttpContext.Response.Body`. | `false` |
   | [Ověřování. AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | Povoluje anonymní požadavky. | `true` |
   | [Ověřování. schémata](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | Určete povolená schémata ověřování. Může být kdykoli změněno před vyřazením naslouchacího procesu. Hodnoty jsou k dispozici [ve výčtu schémata AuthenticationSchemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes) `Basic`: `Kerberos`, `Negotiate`, `None`, a `NTLM`. | `None` |
   | [EnableResponseCaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | Pokusit se o ukládání do mezipaměti v [režimu jádra](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) pro odpovědi s oprávněnými záhlavími. Odpověď nesmí obsahovat `Set-Cookie`hlavičky, `Vary`ani `Pragma` . Musí `Cache-Control` obsahovat hlavičku, která je `public` a buď `shared-max-age` hodnota nebo `max-age` , nebo `Expires` záhlaví. | `true` |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | Maximální počet souběžných přijetí. | 5 &times; [prostředí.<br> ProcessorCount](xref:System.Environment.ProcessorCount) |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | Maximální počet souběžných připojení, která se mají přijmout Použijte `-1` pro nekonečné. Použijte `null` k použití nastavení pro počítač v rámci registru. | `null`<br>počet |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | Viz část <a href="#maxrequestbodysize">MaxRequestBodySize</a> . | 30000000 bajtů<br>(~ 28,6 MB) |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | Maximální počet požadavků, které lze zařadit do fronty. | 1000 |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | Určuje, jestli tělo odpovědi zapisuje, které selhalo kvůli odpojení klienta, by mělo vyvolat výjimky nebo dokončit normálně. | `false`<br>(normálně dokončit) |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | Vystavte konfiguraci http <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> . sys, která může být v registru nakonfigurovaná taky. Pomocí odkazů rozhraní API získáte další informace o jednotlivých nastaveních, včetně výchozích hodnot:<ul><li>[TimeoutManager. DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody) &ndash; je povolený v případě, že rozhraní API serveru http vyprázdní tělo entity u připojení Keep-Alive.</li><li>[TimeoutManager. EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody) &ndash; čas povolený pro doručení těla entity požadavku.</li><li>[TimeoutManager. HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait) &ndash; je povolený pro rozhraní API serveru http, aby bylo možné analyzovat hlavičku požadavku.</li><li>[TimeoutManager. IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection) &ndash; čas povolený pro nečinné připojení.</li><li>[TimeoutManager. MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond) &ndash; minimální rychlost odesílání odpovědi.</li><li>[TimeoutManager. RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue) &ndash; je povolený, aby žádost zůstala ve frontě požadavků ještě předtím, než ji aplikace vybere.</li></ul> |  |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | Zadejte, <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> který se má zaregistrovat v http. sys. Nejužitečnější je [UrlPrefixCollection. Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), který se používá k přidání předpony do kolekce. Ty mohou být kdykoliv změněny předtím, než se naslouchací proces vyřazuje. |  |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

   | Vlastnost | Popis | Výchozí |
   | -------- | ----------- | :-----: |
   | [AllowSynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | Určuje, zda je povolen synchronní vstup/výstup pro `HttpContext.Request.Body` a `HttpContext.Response.Body`. | `true` |
   | [Ověřování. AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | Povoluje anonymní požadavky. | `true` |
   | [Ověřování. schémata](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | Určete povolená schémata ověřování. Může být kdykoli změněno před vyřazením naslouchacího procesu. Hodnoty jsou k dispozici [ve výčtu schémata AuthenticationSchemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes) `Basic`: `Kerberos`, `Negotiate`, `None`, a `NTLM`. | `None` |
   | [EnableResponseCaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | Pokusit se o ukládání do mezipaměti v [režimu jádra](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) pro odpovědi s oprávněnými záhlavími. Odpověď nesmí obsahovat `Set-Cookie`hlavičky, `Vary`ani `Pragma` . Musí `Cache-Control` obsahovat hlavičku, která je `public` a buď `shared-max-age` hodnota nebo `max-age` , nebo `Expires` záhlaví. | `true` |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | Maximální počet souběžných přijetí. | 5 &times; [prostředí.<br> ProcessorCount](xref:System.Environment.ProcessorCount) |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | Maximální počet souběžných připojení, která se mají přijmout Použijte `-1` pro nekonečné. Použijte `null` k použití nastavení pro počítač v rámci registru. | `null`<br>počet |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | Viz část <a href="#maxrequestbodysize">MaxRequestBodySize</a> . | 30000000 bajtů<br>(~ 28,6 MB) |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | Maximální počet požadavků, které lze zařadit do fronty. | 1000 |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | Určuje, jestli tělo odpovědi zapisuje, které selhalo kvůli odpojení klienta, by mělo vyvolat výjimky nebo dokončit normálně. | `false`<br>(normálně dokončit) |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | Vystavte konfiguraci http <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> . sys, která může být v registru nakonfigurovaná taky. Pomocí odkazů rozhraní API získáte další informace o jednotlivých nastaveních, včetně výchozích hodnot:<ul><li>[TimeoutManager. DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody) &ndash; je povolený v případě, že rozhraní API serveru http vyprázdní tělo entity u připojení Keep-Alive.</li><li>[TimeoutManager. EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody) &ndash; čas povolený pro doručení těla entity požadavku.</li><li>[TimeoutManager. HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait) &ndash; je povolený pro rozhraní API serveru http, aby bylo možné analyzovat hlavičku požadavku.</li><li>[TimeoutManager. IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection) &ndash; čas povolený pro nečinné připojení.</li><li>[TimeoutManager. MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond) &ndash; minimální rychlost odesílání odpovědi.</li><li>[TimeoutManager. RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue) &ndash; je povolený, aby žádost zůstala ve frontě požadavků ještě předtím, než ji aplikace vybere.</li></ul> |  |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | Zadejte, <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> který se má zaregistrovat v http. sys. Nejužitečnější je [UrlPrefixCollection. Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), který se používá k přidání předpony do kolekce. Ty mohou být kdykoliv změněny předtím, než se naslouchací proces vyřazuje. |  |

::: moniker-end

   <a name="maxrequestbodysize"></a>

   **MaxRequestBodySize**

   Maximální povolená velikost libovolného textu žádosti v bajtech Při nastavení na `null`je maximální velikost textu požadavku neomezená. Toto omezení nemá žádný vliv na upgradovaná připojení, která jsou vždycky neomezená.

   Doporučená metoda pro přepsání limitu v aplikaci ASP.NET Core MVC pro jeden `IActionResult` je <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> použít atribut pro metodu akce:

   ```csharp
   [RequestSizeLimit(100000000)]
   public IActionResult MyActionMethod()
   ```

   Výjimka je vyvolána, pokud se aplikace pokusí nakonfigurovat limit žádosti poté, co aplikace začne číst požadavek. Vlastnost může být použita k označení, `MaxRequestBodySize` zda je vlastnost ve stavu jen pro čtení, což znamená, že je příliš pozdě pro konfiguraci limitu. `IsReadOnly`

   Pokud by měla aplikace přepsat <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> jednotlivé požadavky, <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>použijte:

   [!code-csharp[](httpsys/sample/Startup.cs?name=snippet1&highlight=6-7)]

3. Pokud používáte Visual Studio, ujistěte se, že aplikace není nakonfigurovaná tak, aby spouštěla službu IIS nebo IIS Express.

   V aplikaci Visual Studio je výchozím spouštěcím profilem IIS Express. Chcete-li spustit projekt jako konzolovou aplikaci, ručně změňte vybraný profil, jak je znázorněno na následujícím snímku obrazovky:

   ![Vybrat profil aplikace konzoly](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a>Konfigurace Windows serveru

1. Určete porty, které se mají otevřít pro aplikaci, a pomocí [brány Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) nebo rutiny [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) prostředí PowerShell otevřete porty brány firewall, aby bylo možné provozovat přes protokol HTTP. sys. V následujících příkazech a konfiguraci aplikace se používá port 443.

1. Při nasazování na virtuální počítač Azure otevřete porty ve [skupině zabezpečení sítě](/azure/virtual-machines/windows/nsg-quickstart-portal). V následujících příkazech a konfiguraci aplikace se používá port 443.

1. V případě potřeby Získejte a nainstalujte certifikáty X. 509.

   V systému Windows vytvořte pomocí [rutiny prostředí PowerShell New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate)certifikáty podepsané svým držitelem. Nepodporovaný příklad naleznete v tématu [UpdateIISExpressSSLForChrome. ps1](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).

   Nainstalujte certifikáty podepsané svým držitelem nebo certifikátem podepsaným certifikační autoritou do**osobního** úložiště **místního počítače** > serveru.

1. Pokud se jedná o [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd), nainstalujte rozhraní .NET core, .NET Framework nebo obojí (Pokud je aplikace aplikace .NET Core cílící na .NET Framework).

   * **.NET Core** Pokud aplikace vyžaduje .NET Core, Získejte a spusťte instalační program **modulu runtime .NET Core** ze [souborů ke stažení pro .NET Core.](https://dotnet.microsoft.com/download) &ndash; Neinstalujte na server plnou sadu SDK.
   * **.NET Framework** Pokud aplikace vyžaduje .NET Framework, přečtěte si [příručku k instalaci .NET Framework.](/dotnet/framework/install/) &ndash; Nainstalujte požadovanou .NET Framework. Instalační program pro nejnovější .NET Framework je k dispozici na stránce [soubory ke stažení pro .NET Core](https://dotnet.microsoft.com/download) .

   Pokud je aplikace samostatným [nasazením](/dotnet/core/deploying/#self-contained-deployments-scd), aplikace zahrne modul runtime do svého nasazení. Na serveru není nutná žádná instalace rozhraní.

1. Konfigurace adres URL a portů v aplikaci

   Ve výchozím nastavení ASP.NET Core váže `http://localhost:5000`. Pokud chcete nakonfigurovat předpony a porty adresy URL, můžete použít tyto možnosti:

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * `urls`argument příkazového řádku
   * `ASPNETCORE_URLS`Proměnná prostředí
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   Následující příklad kódu ukazuje, jak používat <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> s místní IP adresou `10.0.0.4` serveru na portu 443:

   [!code-csharp[](httpsys/sample_snapshot/Program.cs?name=snippet1&highlight=6)]

   Výhodou `UrlPrefixes` je, že se okamžitě generuje chybová zpráva pro nesprávně naformátované předpony.

   `UrlPrefixes` Nastavení v nastavení přepsání `UseUrls` / `urls` / `ASPNETCORE_URLS` Proto je výhodou `UseUrls`proměnné `ASPNETCORE_URLS` prostředí `urls`, a jednodušší přepínání mezi Kestrel a http. sys.

   HTTP. sys používá [formáty řetězce UrlPrefix HTTP serveru API](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx).

   > [!WARNING]
   > Vazby nejvyšší úrovně zástupný znak (`http://*:80/` a `http://+:80`) by měl **není** použít. Vazby na zástupné znaky na nejvyšší úrovni vytvoří chyby zabezpečení aplikace. To platí pro silné a slabé zástupné znaky. Místo zástupných znaků použijte explicitní názvy hostitelů nebo IP adresy. Vazba zástupných znaků subdomény ( `*.mysub.com`například) není bezpečnostní riziko `*.com`, pokud ovládáte celou nadřazenou doménu (na rozdíl od, která je zranitelná). Další informace najdete v [dokumentu RFC 7230: Oddíl 5,4: Hostitel](https://tools.ietf.org/html/rfc7230#section-5.4):

1. Předregistrujte předpony adresy URL na serveru.

   Integrovaný nástroj pro konfiguraci souboru HTTP. sys je *netsh. exe*. pomocí *nástroje Netsh. exe* můžete rezervovat PŘEDPONY adres URL a přiřazovat certifikáty X. 509. Nástroj vyžaduje oprávnění správce.

   Registrace adres URL pro aplikaci pomocí nástroje *netsh. exe* :

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * `<URL>`&ndash; Plně kvalifikovaná adresa URL (Uniform Resource Locator). Nepoužívejte vazbu zástupných znaků. Použijte platný název hostitele nebo místní IP adresu. *Adresa URL musí obsahovat koncové lomítko.*
   * `<USER>`&ndash; Určuje jméno uživatele nebo název skupiny.

   V následujícím příkladu je `10.0.0.4`místní IP adresa serveru:

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   Když je adresa URL registrována, nástroj odpoví `URL reservation successfully added`.

   Pokud chcete odstranit registrovanou adresu URL, použijte `delete urlacl` příkaz:

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. Zaregistrujte certifikáty X. 509 na serveru.

   K registraci certifikátů pro aplikaci použijte nástroj *netsh. exe* :

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * `<IP>`&ndash; Určuje místní IP adresu pro vazbu. Nepoužívejte vazbu zástupných znaků. Použijte platnou IP adresu.
   * `<PORT>`&ndash; Určuje port pro vazbu.
   * `<THUMBPRINT>`&ndash; Kryptografický otisk certifikátu X. 509
   * `<GUID>`&ndash; Identifikátor GUID generovaný vývojářem, který představuje aplikaci pro informativní účely.

   Pro referenční účely uložte identifikátor GUID do aplikace jako značku balíčku:

   * V sadě Visual Studio:
     * Otevřete vlastnosti projektu aplikace tak, že kliknete pravým tlačítkem na aplikaci v **Průzkumník řešení** a vyberete **vlastnosti**.
     * Vyberte kartu **balíček** .
     * Zadejte identifikátor GUID, který jste vytvořili v poli **značky** .
   * Když nepoužíváte Visual Studio:
     * Otevřete soubor projektu aplikace.
     * Přidejte vlastnost do nového nebo existujícího `<PropertyGroup>` s identifikátorem GUID, který jste vytvořili: `<PackageTags>`

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   V následujícím příkladu:

   * Místní IP adresa serveru je `10.0.0.4`.
   * Generátor náhodných identifikátorů GUID online `appid` poskytuje hodnotu.

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   Když je certifikát zaregistrován, nástroj odpoví `SSL Certificate successfully added`.

   K odstranění registrace certifikátu použijte `delete sslcert` příkaz:

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   Referenční dokumentace k nástroji *netsh. exe*:

   * [Příkazy Netsh pro protokol HTTP (Hypertext Transfer Protocol)](https://technet.microsoft.com/library/cc725882.aspx)
   * [UrlPrefix řetězce](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)

1. Spusťte aplikaci.

   Oprávnění správce nejsou nutná ke spuštění aplikace při vytváření vazby na localhost pomocí protokolu HTTP (ne HTTPS) s číslem portu větším než 1024. U ostatních konfigurací (například pomocí místní IP adresy nebo vazby na port 443) spusťte aplikaci s oprávněními správce.

   Aplikace odpoví na veřejnou IP adresu serveru. V tomto příkladu je server dosažitelný z Internetu na jeho veřejné IP adrese `104.214.79.47`.

   V tomto příkladu se používá vývojový certifikát. Stránka se načte bezpečně po obejít upozornění na nedůvěryhodný certifikát v prohlížeči.

   ![Okno prohlížeče zobrazující načtenou stránku indexu aplikace](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a>Proxy server a scénáře pro nástroj pro vyrovnávání zatížení

Pro aplikace hostované souborem HTTP. sys, které komunikují s požadavky z Internetu nebo podnikové sítě, se může při hostování za proxy servery a nástroji pro vyrovnávání zatížení vyžadovat další konfigurace. Další informace najdete v tématu [konfigurace ASP.NET Core práci se servery proxy a nástroje pro vyrovnávání zatížení](xref:host-and-deploy/proxy-load-balancer).

## <a name="additional-resources"></a>Další zdroje

* [Povolit ověřování systému Windows pomocí HTTP. sys](xref:security/authentication/windowsauth#httpsys)
* [Rozhraní API serveru HTTP](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)
* [úložiště GitHubu ASPNET/HttpSysServer (zdrojový kód)](https://github.com/aspnet/HttpSysServer/)
* [Hostitel](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

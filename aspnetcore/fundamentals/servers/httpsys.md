---
title: Implementace webového serveru HTTP.sys v ASP.NET Core
author: rick-anderson
description: Přečtěte si o http.sys, webovém serveru pro ASP.NET Core ve Windows. Http.sys je postaven na ovladači režimu jádra HTTP.sys alternativou ke Kestrelu, který lze použít pro přímé připojení k Internetu bez služby IIS.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: fundamentals/servers/httpsys
ms.openlocfilehash: 3e858a974d6a5c008969c3c51a507880cc25a7ff
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78666920"
---
# <a name="httpsys-web-server-implementation-in-aspnet-core"></a>Implementace webového serveru HTTP.sys v ASP.NET Core

Tom [Dykstra](https://github.com/tdykstra) a [Chris Ross](https://github.com/Tratcher)

::: moniker range=">= aspnetcore-3.1"

[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) je [webový server pro ASP.NET Core,](xref:fundamentals/servers/index) který běží pouze v systému Windows. HTTP.sys je alternativou k serveru [Kestrel](xref:fundamentals/servers/kestrel) a nabízí některé funkce, které Kestrel neposkytuje.

> [!IMPORTANT]
> Soubor HTTP.sys není kompatibilní s [ASP.NET základního modulu](xref:host-and-deploy/aspnet-core-module) a nelze jej použít se službou IIS nebo službou IIS Express.

HTTP.sys podporuje následující funkce:

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

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="when-to-use-httpsys"></a>Kdy použít soubor HTTP.sys

Http.sys je užitečné pro nasazení, kde:

* Je třeba vystavit server přímo na Internetu bez použití služby IIS.

  ![HTTP.sys komunikuje přímo s Internetem](httpsys/_static/httpsys-to-internet.png)

* Interní nasazení vyžaduje funkci, která není v kestrelu k dispozici, například [ověřování systému Windows](xref:security/authentication/windowsauth).

  ![HTTP.sys komunikuje přímo s interní sítí](httpsys/_static/httpsys-to-internal.png)

HTTP.sys je vyspělá technologie, která chrání před mnoha typy útoků a poskytuje robustnost, zabezpečení a škálovatelnost plně vybaveného webového serveru. Samotná iis běží jako naslouchací proces HTTP nad http.sys.

## <a name="http2-support"></a>Podpora HTTP/2

[HTTP/2](https://httpwg.org/specs/rfc7540.html) je povolena pro ASP.NET základní aplikace, pokud jsou splněny následující základní požadavky:

* Windows Server 2016/Windows 10 nebo novější
* Připojení [protokolu ap (APPLICATION-Layer Protocol) (ALPN)](https://tools.ietf.org/html/rfc7301#section-3)
* Připojení TLS 1.2 nebo novější

Pokud je navázáno připojení HTTP/2, `HTTP/2`protokol [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) hlásí .

Protokol HTTP/2 je ve výchozím nastavení povolen. Pokud není navázáno připojení HTTP/2, připojení přejde zpět na HTTP/1.1. V budoucí verzi systému Windows budou k dispozici příznaky konfigurace HTTP/2, včetně možnosti zakázat protokol HTTP/2 pomocí protokolu HTTP.sys.

## <a name="kernel-mode-authentication-with-kerberos"></a>Ověřování v režimu jádra pomocí protokolu Kerberos

Protokol HTTP.sys deleguje ověřování v režimu jádra pomocí ověřovacího protokolu Kerberos. Ověřování v uživatelském režimu není podporováno protokoly Kerberos a HTTP.sys. Účet počítače musí být použit k dešifrování tokenu nebo lístku protokolu Kerberos, který je získán ze služby Active Directory a předán klientem na server k ověření uživatele. Zaregistrujte hlavní název služby (SPN) pro hostitele, nikoli uživatele aplikace.

## <a name="how-to-use-httpsys"></a>Jak používat http.sys

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a>Konfigurace aplikace ASP.NET Core pro použití protokolu HTTP.sys

Volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> metody rozšíření při vytváření hostitele, <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>zadání mj. Následující příklad nastaví možnosti jejich výchozí hodnoty:

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

Další konfigurace http.sys je zpracována prostřednictvím [nastavení registru](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).

**Možnosti protokolu HTTP.sys**

| Vlastnost | Popis | Výchozí |
| -------- | ----------- | :-----: |
| [PovolitsynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | Ovládejte, zda je synchronní `HttpContext.Request.Body` vstup/výstup povolen pro a . `HttpContext.Response.Body` | `false` |
| [Ověřování.AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | Povolit anonymní požadavky. | `true` |
| [Ověřování.Schémata](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | Zadejte povolená schémata ověřování. Může být změněn kdykolipřed likvidací posluchače. Hodnoty jsou uvedeny ve [výčtu AuthenticationSchemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes) `Basic`: , `Kerberos` `Negotiate`, `None`, a `NTLM`. | `None` |
| [EnableResponseCaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | Pokus te se o ukládání odpovědí do [mezipaměti v režimu jádra](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) s způsobilými záhlavími. Odpověď nesmí `Set-Cookie`obsahovat `Vary`hlavičky nebo `Pragma` záhlaví . Musí obsahovat `Cache-Control` záhlaví, `public` které je `shared-max-age` `max-age` a nebo `Expires` hodnota, nebo záhlaví. | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | Maximální počet souběžných přijímá. | 5 &times; [Životní<br> prostředí. Počet procesorů](xref:System.Environment.ProcessorCount) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | Maximální počet souběžných připojení přijmout. Použít `-1` pro nekonečno. Slouží `null` k použití nastavení celého počítače v registru. | `null`<br>(celostrojově<br>nastavení) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | Viz část <a href="#maxrequestbodysize">MaxRequestBodySize.</a> | 30000000 bajtů<br>(~28,6 MB) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | Maximální počet požadavků, které mohou být zařazeny do fronty. | 1000 |
| `RequestQueueMode` | Označuje, zda je server zodpovědný za vytvoření a konfiguraci fronty požadavků nebo zda by se měl připojit k existující frontě.<br>Většina existujících možností konfigurace se nepoužije při připojování k existující frontě. | `RequestQueueMode.Create` |
| `RequestQueueName` | Název fronty požadavků HTTP.sys. | `null`(Anonymní fronta) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | Označte, zda by zápisy v těle odpovědi, které se nezdaří z důvodu odpojení klienta, měly vyvolat výjimky nebo je dokončit normálně. | `false`<br>(kompletní normálně) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | Vystavit konfiguraci HTTP.sys, <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> která může být také nakonfigurována v registru. Další informace o jednotlivých nastaveních, včetně výchozích hodnot, najdete v odkazech rozhraní API:<ul><li>[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody) &ndash; Čas povolen pro rozhraní API http serveru vyprázdnit tělo entity na keep-alive připojení.</li><li>[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody) &ndash; Čas povolen pro tělo entity požadavku k doručení.</li><li>[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait) &ndash; Čas povolen pro rozhraní API serveru HTTP analyzovat hlavičku požadavku.</li><li>[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection) &ndash; Čas povolen pro nečinné připojení.</li><li>[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond) &ndash; Minimální rychlost odesílání pro odpověď.</li><li>[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue) &ndash; Čas povolen pro požadavek zůstat ve frontě požadavků před aplikace zvedne.</li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | Zadejte <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> registraci pomocí souboru HTTP.sys. Nejužitečnější je [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), který se používá k přidání předpony do kolekce. Ty mohou být změněny kdykolipřed likvidací posluchače. |  |

<a name="maxrequestbodysize"></a>

**Velikost maxrequestbodysize**

Maximální povolená velikost libovolného těla požadavku v bajtech. Pokud je `null`nastavena na , maximální velikost těla požadavku je neomezená. Toto omezení nemá žádný vliv na inovovaná připojení, která jsou vždy neomezená.

Doporučená metoda přepsat limit v aplikaci ASP.NET Core MVC pro jeden `IActionResult` je použít <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> atribut na metodu akce:

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

Výjimka je vyvolána, pokud se aplikace pokusí nakonfigurovat limit na požadavek poté, co aplikace začala číst požadavek. Vlastnost `IsReadOnly` lze použít k označení, pokud je `MaxRequestBodySize` vlastnost ve stavu jen pro čtení, což znamená, že je příliš pozdě na konfiguraci limitu.

Pokud by aplikace <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> měla přepsat na <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>žádost, použijte :

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

Pokud používáte Visual Studio, ujistěte se, že aplikace není nakonfigurovaná pro spuštění služby IIS nebo Služby IIS Express.

V sadě Visual Studio je výchozí profil spuštění pro iis express. Chcete-li projekt spustit jako konzolovou aplikaci, ručně změňte vybraný profil, jak je znázorněno na následujícím snímku obrazovky:

![Výběr profilu konzolové aplikace](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a>Konfigurace systému Windows Server

1. Určete porty, které chcete pro aplikaci otevřít, a pomocí brány [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) nebo rutiny [Prostředí PowerShell New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) otevřete porty brány firewall, abyste umožnili přenosy dosáhnout souboru HTTP.sys. V následujících příkazech a konfiguraci aplikace se používá port 443.

1. Při nasazování do virtuálního počítače Azure otevřete porty ve [skupině zabezpečení sítě](/azure/virtual-machines/windows/nsg-quickstart-portal). V následujících příkazech a konfiguraci aplikace se používá port 443.

1. V případě potřeby získejte a nainstalujte certifikáty X.509.

   V systému Windows vytvořte certifikáty podepsané svým držitelem pomocí [rutiny prostředí New-SelfSignedCertificate PowerShell](/powershell/module/pkiclient/new-selfsignedcertificate). Nepodporovaný příklad naleznete v tématu [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).

   Nainstalujte certifikáty podepsané svým držitelem nebo certifikáty podepsané certifikační autoritou do **osobního** úložiště **místního počítače** > na serveru.

1. Pokud je aplikace [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd), nainstalujte .NET Core, .NET Framework nebo obojí (pokud je aplikace .NET Core aplikace cílení na rozhraní .NET Framework).

   * **.NET Core** &ndash; Pokud aplikace vyžaduje .NET Core, získejte a spusťte instalační program **.NET Core Runtime** z [souborů .NET Core Downloads](https://dotnet.microsoft.com/download). Neinstalujte úplnou sadu SDK na server.
   * **Rozhraní .NET Framework:** &ndash; Pokud aplikace vyžaduje rozhraní .NET Framework, přečtěte si [příručku k instalaci rozhraní .NET Framework](/dotnet/framework/install/). Nainstalujte požadovanou architekturu .NET Framework. Instalační program pro nejnovější rozhraní .NET Framework je k dispozici na stránce [Stahování jádra .NET.](https://dotnet.microsoft.com/download)

   Pokud je aplikace [samostatné nasazení](/dotnet/core/deploying/#self-contained-deployments-scd), aplikace obsahuje runtime v jeho nasazení. Na serveru není vyžadována žádná instalace architektury.

1. Konfigurace adres URL a portů v aplikaci.

   Ve výchozím nastavení se `http://localhost:5000`ASP.NET jádro váže na . Chcete-li nakonfigurovat předpony adres URL a porty, možnosti zahrnují:

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * `urls`argument příkazového řádku
   * `ASPNETCORE_URLS`proměnná prostředí
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   Následující příklad kódu ukazuje, <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> jak se používat s `10.0.0.4` místní IP adresou serveru na portu 443:

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

   Výhodou `UrlPrefixes` je, že chybová zpráva je generována okamžitě pro nesprávně formátované předpony.

   Nastavení v `UrlPrefixes` nastavení `UseUrls` / `urls` / `ASPNETCORE_URLS` přepsání. Výhodou aplikace `UseUrls`, `urls`a proměnné `ASPNETCORE_URLS` prostředí je proto, že je snazší přepínat mezi kestrelem a protokolem HTTP.sys.

   Protokol HTTP.sys používá [formáty řetězců URLPrefix rozhraní HTTP Server.](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)

   > [!WARNING]
   > Vazby se zástupnými`http://*:80/` symboly nejvyšší úrovně ( a `http://+:80`) by **neměly** být používány. Vazby se zástupnými znaky nejvyšší úrovně vytvářejí chyby zabezpečení aplikace. To platí pro silné i slabé zástupné znaky. Používejte explicitní názvy hostitelů nebo adresy IP místo zástupných znaků. Vazba se zástupnými znaky subdomény (například ) není bezpečnostní riziko, `*.mysub.com`pokud `*.com`řídíte celou nadřazenou doménu (na rozdíl od , která je zranitelná). Další informace naleznete v [tématu RFC 7230: Oddíl 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).

1. Předpony url předregister na serveru.

   Vestavěný nástroj pro konfiguraci http.sys je *netsh.exe*. *netsh.exe* se používá k rezervaci předpon URL a přiřazení certifikátů X.509. Nástroj vyžaduje oprávnění správce.

   Pomocí nástroje *netsh.exe* zaregistrujte adresy URL aplikace:

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * `<URL>`&ndash; Plně kvalifikovaný jednotný lokátor zdrojů (URL). Nepoužívejte vazby zástupných symbolů. Použijte platný název hostitele nebo místní ADRESU IP. *Adresa URL musí obsahovat koncové lomítko.*
   * `<USER>`&ndash; Určuje název uživatele nebo skupiny uživatelů.

   V následujícím příkladu je `10.0.0.4`místní IP adresa serveru :

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   Když je adresa URL zaregistrována, nástroj odpoví pomocí . `URL reservation successfully added`

   Chcete-li odstranit registrovanou `delete urlacl` adresu URL, použijte příkaz:

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. Zaregistrujte certifikáty X.509 na serveru.

   Pomocí nástroje *netsh.exe* zaregistrujte certifikáty pro aplikaci:

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * `<IP>`&ndash; Určuje místní adresu IP pro vazbu. Nepoužívejte vazby zástupných symbolů. Použijte platnou adresu IP.
   * `<PORT>`&ndash; Určuje port pro vazbu.
   * `<THUMBPRINT>`&ndash; Kryptografický otisk certifikátu X.509.
   * `<GUID>`&ndash; Identifikátor GUID generovaný vývojářem, který představuje aplikaci pro informační účely.

   Pro referenční účely uložte identifikátor GUID do aplikace jako značku balíčku:

   * V sadě Visual Studio:
     * Otevřete vlastnosti projektu aplikace kliknutím pravým tlačítkem myši na aplikaci v **Průzkumníku řešení** a výběrem **možnosti Vlastnosti**.
     * Vyberte kartu **Balíček.**
     * Zadejte identifikátor GUID, který jste vytvořili, do pole **Značky.**
   * Pokud nepoužíváte Visual Studio:
     * Otevřete soubor projektu aplikace.
     * Přidejte `<PackageTags>` vlastnost do nové `<PropertyGroup>` nebo existující s identifikátorem GUID, který jste vytvořili:

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   V následujícím příkladu:

   * Místní ADRESA IP serveru `10.0.0.4`je .
   * Online generátor náhodných identifikátorů GUID poskytuje hodnotu. `appid`

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   Při registraci certifikátu nástroj odpoví `SSL Certificate successfully added`pomocí .

   Chcete-li odstranit registraci `delete sslcert` certifikátu, použijte příkaz:

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   Referenční dokumentace pro *netsh.exe*:

   * [Příkazy Netsh pro protokol HTTP (HTTP)](https://technet.microsoft.com/library/cc725882.aspx)
   * [Řetězce urlprefix](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)

1. Spusťte aplikaci.

   Oprávnění správce nejsou nutné ke spuštění aplikace při vazbě na localhost pomocí PROTOKOLU HTTP (nikoli HTTPS) s číslem portu větší než 1024. Pro ostatní konfigurace (například pomocí místní IP adresy nebo vazby na port 443) spusťte aplikaci s oprávněními správce.

   Aplikace reaguje na veřejnou IP adresu serveru. V tomto příkladu je server osloven z Internetu `104.214.79.47`na jeho veřejné IP adrese .

   V tomto příkladu se používá certifikát vývoje. Stránka se načte bezpečně po vynechání upozornění nedůvěryhodného certifikátu prohlížeče.

   ![Okno prohlížeče zobrazující načtenou stránku Indexu aplikace](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a>Scénáře proxy serveru a vyrovnávání zatížení

U aplikací hostovaných protokolem HTTP.sys, které interagují s požadavky z Internetu nebo podnikové sítě, může být při hostování za proxy servery a nástroji pro vyrovnávání zatížení vyžadována další konfigurace. Další informace naleznete v [tématu Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).

## <a name="additional-resources"></a>Další zdroje

* [Povolení ověřování systému Windows pomocí protokolu HTTP.sys](xref:security/authentication/windowsauth#httpsys)
* [Rozhraní API serveru HTTP](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)
* [úložiště aspnet/HttpSysServer GitHub (zdrojový kód)](https://github.com/aspnet/HttpSysServer/)
* [Hostitel](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) je [webový server pro ASP.NET Core,](xref:fundamentals/servers/index) který běží pouze v systému Windows. HTTP.sys je alternativou k serveru [Kestrel](xref:fundamentals/servers/kestrel) a nabízí některé funkce, které Kestrel neposkytuje.

> [!IMPORTANT]
> Soubor HTTP.sys není kompatibilní s [ASP.NET základního modulu](xref:host-and-deploy/aspnet-core-module) a nelze jej použít se službou IIS nebo službou IIS Express.

HTTP.sys podporuje následující funkce:

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

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="when-to-use-httpsys"></a>Kdy použít soubor HTTP.sys

Http.sys je užitečné pro nasazení, kde:

* Je třeba vystavit server přímo na Internetu bez použití služby IIS.

  ![HTTP.sys komunikuje přímo s Internetem](httpsys/_static/httpsys-to-internet.png)

* Interní nasazení vyžaduje funkci, která není v kestrelu k dispozici, například [ověřování systému Windows](xref:security/authentication/windowsauth).

  ![HTTP.sys komunikuje přímo s interní sítí](httpsys/_static/httpsys-to-internal.png)

HTTP.sys je vyspělá technologie, která chrání před mnoha typy útoků a poskytuje robustnost, zabezpečení a škálovatelnost plně vybaveného webového serveru. Samotná iis běží jako naslouchací proces HTTP nad http.sys.

## <a name="http2-support"></a>Podpora HTTP/2

[HTTP/2](https://httpwg.org/specs/rfc7540.html) je povolena pro ASP.NET základní aplikace, pokud jsou splněny následující základní požadavky:

* Windows Server 2016/Windows 10 nebo novější
* Připojení [protokolu ap (APPLICATION-Layer Protocol) (ALPN)](https://tools.ietf.org/html/rfc7301#section-3)
* Připojení TLS 1.2 nebo novější

Pokud je navázáno připojení HTTP/2, `HTTP/2`protokol [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) hlásí .

Protokol HTTP/2 je ve výchozím nastavení povolen. Pokud není navázáno připojení HTTP/2, připojení přejde zpět na HTTP/1.1. V budoucí verzi systému Windows budou k dispozici příznaky konfigurace HTTP/2, včetně možnosti zakázat protokol HTTP/2 pomocí protokolu HTTP.sys.

## <a name="kernel-mode-authentication-with-kerberos"></a>Ověřování v režimu jádra pomocí protokolu Kerberos

Protokol HTTP.sys deleguje ověřování v režimu jádra pomocí ověřovacího protokolu Kerberos. Ověřování v uživatelském režimu není podporováno protokoly Kerberos a HTTP.sys. Účet počítače musí být použit k dešifrování tokenu nebo lístku protokolu Kerberos, který je získán ze služby Active Directory a předán klientem na server k ověření uživatele. Zaregistrujte hlavní název služby (SPN) pro hostitele, nikoli uživatele aplikace.

## <a name="how-to-use-httpsys"></a>Jak používat http.sys

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a>Konfigurace aplikace ASP.NET Core pro použití protokolu HTTP.sys

Volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> metody rozšíření při vytváření hostitele, <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>zadání mj. Následující příklad nastaví možnosti jejich výchozí hodnoty:

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

Další konfigurace http.sys je zpracována prostřednictvím [nastavení registru](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).

**Možnosti protokolu HTTP.sys**

| Vlastnost | Popis | Výchozí |
| -------- | ----------- | :-----: |
| [PovolitsynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | Ovládejte, zda je synchronní `HttpContext.Request.Body` vstup/výstup povolen pro a . `HttpContext.Response.Body` | `false` |
| [Ověřování.AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | Povolit anonymní požadavky. | `true` |
| [Ověřování.Schémata](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | Zadejte povolená schémata ověřování. Může být změněn kdykolipřed likvidací posluchače. Hodnoty jsou uvedeny ve [výčtu AuthenticationSchemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes) `Basic`: , `Kerberos` `Negotiate`, `None`, a `NTLM`. | `None` |
| [EnableResponseCaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | Pokus te se o ukládání odpovědí do [mezipaměti v režimu jádra](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) s způsobilými záhlavími. Odpověď nesmí `Set-Cookie`obsahovat `Vary`hlavičky nebo `Pragma` záhlaví . Musí obsahovat `Cache-Control` záhlaví, `public` které je `shared-max-age` `max-age` a nebo `Expires` hodnota, nebo záhlaví. | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | Maximální počet souběžných přijímá. | 5 &times; [Životní<br> prostředí. Počet procesorů](xref:System.Environment.ProcessorCount) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | Maximální počet souběžných připojení přijmout. Použít `-1` pro nekonečno. Slouží `null` k použití nastavení celého počítače v registru. | `null`<br>(celostrojově<br>nastavení) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | Viz část <a href="#maxrequestbodysize">MaxRequestBodySize.</a> | 30000000 bajtů<br>(~28,6 MB) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | Maximální počet požadavků, které mohou být zařazeny do fronty. | 1000 |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | Označte, zda by zápisy v těle odpovědi, které se nezdaří z důvodu odpojení klienta, měly vyvolat výjimky nebo je dokončit normálně. | `false`<br>(kompletní normálně) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | Vystavit konfiguraci HTTP.sys, <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> která může být také nakonfigurována v registru. Další informace o jednotlivých nastaveních, včetně výchozích hodnot, najdete v odkazech rozhraní API:<ul><li>[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody) &ndash; Čas povolen pro rozhraní API http serveru vyprázdnit tělo entity na keep-alive připojení.</li><li>[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody) &ndash; Čas povolen pro tělo entity požadavku k doručení.</li><li>[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait) &ndash; Čas povolen pro rozhraní API serveru HTTP analyzovat hlavičku požadavku.</li><li>[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection) &ndash; Čas povolen pro nečinné připojení.</li><li>[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond) &ndash; Minimální rychlost odesílání pro odpověď.</li><li>[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue) &ndash; Čas povolen pro požadavek zůstat ve frontě požadavků před aplikace zvedne.</li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | Zadejte <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> registraci pomocí souboru HTTP.sys. Nejužitečnější je [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), který se používá k přidání předpony do kolekce. Ty mohou být změněny kdykolipřed likvidací posluchače. |  |

<a name="maxrequestbodysize"></a>

**Velikost maxrequestbodysize**

Maximální povolená velikost libovolného těla požadavku v bajtech. Pokud je `null`nastavena na , maximální velikost těla požadavku je neomezená. Toto omezení nemá žádný vliv na inovovaná připojení, která jsou vždy neomezená.

Doporučená metoda přepsat limit v aplikaci ASP.NET Core MVC pro jeden `IActionResult` je použít <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> atribut na metodu akce:

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

Výjimka je vyvolána, pokud se aplikace pokusí nakonfigurovat limit na požadavek poté, co aplikace začala číst požadavek. Vlastnost `IsReadOnly` lze použít k označení, pokud je `MaxRequestBodySize` vlastnost ve stavu jen pro čtení, což znamená, že je příliš pozdě na konfiguraci limitu.

Pokud by aplikace <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> měla přepsat na <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>žádost, použijte :

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

Pokud používáte Visual Studio, ujistěte se, že aplikace není nakonfigurovaná pro spuštění služby IIS nebo Služby IIS Express.

V sadě Visual Studio je výchozí profil spuštění pro iis express. Chcete-li projekt spustit jako konzolovou aplikaci, ručně změňte vybraný profil, jak je znázorněno na následujícím snímku obrazovky:

![Výběr profilu konzolové aplikace](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a>Konfigurace systému Windows Server

1. Určete porty, které chcete pro aplikaci otevřít, a pomocí brány [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) nebo rutiny [Prostředí PowerShell New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) otevřete porty brány firewall, abyste umožnili přenosy dosáhnout souboru HTTP.sys. V následujících příkazech a konfiguraci aplikace se používá port 443.

1. Při nasazování do virtuálního počítače Azure otevřete porty ve [skupině zabezpečení sítě](/azure/virtual-machines/windows/nsg-quickstart-portal). V následujících příkazech a konfiguraci aplikace se používá port 443.

1. V případě potřeby získejte a nainstalujte certifikáty X.509.

   V systému Windows vytvořte certifikáty podepsané svým držitelem pomocí [rutiny prostředí New-SelfSignedCertificate PowerShell](/powershell/module/pkiclient/new-selfsignedcertificate). Nepodporovaný příklad naleznete v tématu [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).

   Nainstalujte certifikáty podepsané svým držitelem nebo certifikáty podepsané certifikační autoritou do **osobního** úložiště **místního počítače** > na serveru.

1. Pokud je aplikace [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd), nainstalujte .NET Core, .NET Framework nebo obojí (pokud je aplikace .NET Core aplikace cílení na rozhraní .NET Framework).

   * **.NET Core** &ndash; Pokud aplikace vyžaduje .NET Core, získejte a spusťte instalační program **.NET Core Runtime** z [souborů .NET Core Downloads](https://dotnet.microsoft.com/download). Neinstalujte úplnou sadu SDK na server.
   * **Rozhraní .NET Framework:** &ndash; Pokud aplikace vyžaduje rozhraní .NET Framework, přečtěte si [příručku k instalaci rozhraní .NET Framework](/dotnet/framework/install/). Nainstalujte požadovanou architekturu .NET Framework. Instalační program pro nejnovější rozhraní .NET Framework je k dispozici na stránce [Stahování jádra .NET.](https://dotnet.microsoft.com/download)

   Pokud je aplikace [samostatné nasazení](/dotnet/core/deploying/#self-contained-deployments-scd), aplikace obsahuje runtime v jeho nasazení. Na serveru není vyžadována žádná instalace architektury.

1. Konfigurace adres URL a portů v aplikaci.

   Ve výchozím nastavení se `http://localhost:5000`ASP.NET jádro váže na . Chcete-li nakonfigurovat předpony adres URL a porty, možnosti zahrnují:

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * `urls`argument příkazového řádku
   * `ASPNETCORE_URLS`proměnná prostředí
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   Následující příklad kódu ukazuje, <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> jak se používat s `10.0.0.4` místní IP adresou serveru na portu 443:

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

   Výhodou `UrlPrefixes` je, že chybová zpráva je generována okamžitě pro nesprávně formátované předpony.

   Nastavení v `UrlPrefixes` nastavení `UseUrls` / `urls` / `ASPNETCORE_URLS` přepsání. Výhodou aplikace `UseUrls`, `urls`a proměnné `ASPNETCORE_URLS` prostředí je proto, že je snazší přepínat mezi kestrelem a protokolem HTTP.sys.

   Protokol HTTP.sys používá [formáty řetězců URLPrefix rozhraní HTTP Server.](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)

   > [!WARNING]
   > Vazby se zástupnými`http://*:80/` symboly nejvyšší úrovně ( a `http://+:80`) by **neměly** být používány. Vazby se zástupnými znaky nejvyšší úrovně vytvářejí chyby zabezpečení aplikace. To platí pro silné i slabé zástupné znaky. Používejte explicitní názvy hostitelů nebo adresy IP místo zástupných znaků. Vazba se zástupnými znaky subdomény (například ) není bezpečnostní riziko, `*.mysub.com`pokud `*.com`řídíte celou nadřazenou doménu (na rozdíl od , která je zranitelná). Další informace naleznete v [tématu RFC 7230: Oddíl 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).

1. Předpony url předregister na serveru.

   Vestavěný nástroj pro konfiguraci http.sys je *netsh.exe*. *netsh.exe* se používá k rezervaci předpon URL a přiřazení certifikátů X.509. Nástroj vyžaduje oprávnění správce.

   Pomocí nástroje *netsh.exe* zaregistrujte adresy URL aplikace:

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * `<URL>`&ndash; Plně kvalifikovaný jednotný lokátor zdrojů (URL). Nepoužívejte vazby zástupných symbolů. Použijte platný název hostitele nebo místní ADRESU IP. *Adresa URL musí obsahovat koncové lomítko.*
   * `<USER>`&ndash; Určuje název uživatele nebo skupiny uživatelů.

   V následujícím příkladu je `10.0.0.4`místní IP adresa serveru :

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   Když je adresa URL zaregistrována, nástroj odpoví pomocí . `URL reservation successfully added`

   Chcete-li odstranit registrovanou `delete urlacl` adresu URL, použijte příkaz:

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. Zaregistrujte certifikáty X.509 na serveru.

   Pomocí nástroje *netsh.exe* zaregistrujte certifikáty pro aplikaci:

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * `<IP>`&ndash; Určuje místní adresu IP pro vazbu. Nepoužívejte vazby zástupných symbolů. Použijte platnou adresu IP.
   * `<PORT>`&ndash; Určuje port pro vazbu.
   * `<THUMBPRINT>`&ndash; Kryptografický otisk certifikátu X.509.
   * `<GUID>`&ndash; Identifikátor GUID generovaný vývojářem, který představuje aplikaci pro informační účely.

   Pro referenční účely uložte identifikátor GUID do aplikace jako značku balíčku:

   * V sadě Visual Studio:
     * Otevřete vlastnosti projektu aplikace kliknutím pravým tlačítkem myši na aplikaci v **Průzkumníku řešení** a výběrem **možnosti Vlastnosti**.
     * Vyberte kartu **Balíček.**
     * Zadejte identifikátor GUID, který jste vytvořili, do pole **Značky.**
   * Pokud nepoužíváte Visual Studio:
     * Otevřete soubor projektu aplikace.
     * Přidejte `<PackageTags>` vlastnost do nové `<PropertyGroup>` nebo existující s identifikátorem GUID, který jste vytvořili:

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   V následujícím příkladu:

   * Místní ADRESA IP serveru `10.0.0.4`je .
   * Online generátor náhodných identifikátorů GUID poskytuje hodnotu. `appid`

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   Při registraci certifikátu nástroj odpoví `SSL Certificate successfully added`pomocí .

   Chcete-li odstranit registraci `delete sslcert` certifikátu, použijte příkaz:

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   Referenční dokumentace pro *netsh.exe*:

   * [Příkazy Netsh pro protokol HTTP (HTTP)](https://technet.microsoft.com/library/cc725882.aspx)
   * [Řetězce urlprefix](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)

1. Spusťte aplikaci.

   Oprávnění správce nejsou nutné ke spuštění aplikace při vazbě na localhost pomocí PROTOKOLU HTTP (nikoli HTTPS) s číslem portu větší než 1024. Pro ostatní konfigurace (například pomocí místní IP adresy nebo vazby na port 443) spusťte aplikaci s oprávněními správce.

   Aplikace reaguje na veřejnou IP adresu serveru. V tomto příkladu je server osloven z Internetu `104.214.79.47`na jeho veřejné IP adrese .

   V tomto příkladu se používá certifikát vývoje. Stránka se načte bezpečně po vynechání upozornění nedůvěryhodného certifikátu prohlížeče.

   ![Okno prohlížeče zobrazující načtenou stránku Indexu aplikace](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a>Scénáře proxy serveru a vyrovnávání zatížení

U aplikací hostovaných protokolem HTTP.sys, které interagují s požadavky z Internetu nebo podnikové sítě, může být při hostování za proxy servery a nástroji pro vyrovnávání zatížení vyžadována další konfigurace. Další informace naleznete v [tématu Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).

## <a name="additional-resources"></a>Další zdroje

* [Povolení ověřování systému Windows pomocí protokolu HTTP.sys](xref:security/authentication/windowsauth#httpsys)
* [Rozhraní API serveru HTTP](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)
* [úložiště aspnet/HttpSysServer GitHub (zdrojový kód)](https://github.com/aspnet/HttpSysServer/)
* [Hostitel](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) je [webový server pro ASP.NET Core,](xref:fundamentals/servers/index) který běží pouze v systému Windows. HTTP.sys je alternativou k serveru [Kestrel](xref:fundamentals/servers/kestrel) a nabízí některé funkce, které Kestrel neposkytuje.

> [!IMPORTANT]
> Soubor HTTP.sys není kompatibilní s [ASP.NET základního modulu](xref:host-and-deploy/aspnet-core-module) a nelze jej použít se službou IIS nebo službou IIS Express.

HTTP.sys podporuje následující funkce:

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

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="when-to-use-httpsys"></a>Kdy použít soubor HTTP.sys

Http.sys je užitečné pro nasazení, kde:

* Je třeba vystavit server přímo na Internetu bez použití služby IIS.

  ![HTTP.sys komunikuje přímo s Internetem](httpsys/_static/httpsys-to-internet.png)

* Interní nasazení vyžaduje funkci, která není v kestrelu k dispozici, například [ověřování systému Windows](xref:security/authentication/windowsauth).

  ![HTTP.sys komunikuje přímo s interní sítí](httpsys/_static/httpsys-to-internal.png)

HTTP.sys je vyspělá technologie, která chrání před mnoha typy útoků a poskytuje robustnost, zabezpečení a škálovatelnost plně vybaveného webového serveru. Samotná iis běží jako naslouchací proces HTTP nad http.sys.

## <a name="http2-support"></a>Podpora HTTP/2

[HTTP/2](https://httpwg.org/specs/rfc7540.html) je povolena pro ASP.NET základní aplikace, pokud jsou splněny následující základní požadavky:

* Windows Server 2016/Windows 10 nebo novější
* Připojení [protokolu ap (APPLICATION-Layer Protocol) (ALPN)](https://tools.ietf.org/html/rfc7301#section-3)
* Připojení TLS 1.2 nebo novější

Pokud je navázáno připojení HTTP/2, `HTTP/2`protokol [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) hlásí .

Protokol HTTP/2 je ve výchozím nastavení povolen. Pokud není navázáno připojení HTTP/2, připojení přejde zpět na HTTP/1.1. V budoucí verzi systému Windows budou k dispozici příznaky konfigurace HTTP/2, včetně možnosti zakázat protokol HTTP/2 pomocí protokolu HTTP.sys.

## <a name="kernel-mode-authentication-with-kerberos"></a>Ověřování v režimu jádra pomocí protokolu Kerberos

Protokol HTTP.sys deleguje ověřování v režimu jádra pomocí ověřovacího protokolu Kerberos. Ověřování v uživatelském režimu není podporováno protokoly Kerberos a HTTP.sys. Účet počítače musí být použit k dešifrování tokenu nebo lístku protokolu Kerberos, který je získán ze služby Active Directory a předán klientem na server k ověření uživatele. Zaregistrujte hlavní název služby (SPN) pro hostitele, nikoli uživatele aplikace.

## <a name="how-to-use-httpsys"></a>Jak používat http.sys

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a>Konfigurace aplikace ASP.NET Core pro použití protokolu HTTP.sys

Odkaz na balíček v souboru projektu není vyžadován při použití [metabalíčku Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)). Pokud metabalíček nepoužíváte, `Microsoft.AspNetCore.App` přidejte odkaz na balíček [microsoft.aspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).

Volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> metody rozšíření při vytváření hostitele, <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>zadání mj. Následující příklad nastaví možnosti jejich výchozí hodnoty:

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

Další konfigurace http.sys je zpracována prostřednictvím [nastavení registru](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).

**Možnosti protokolu HTTP.sys**

| Vlastnost | Popis | Výchozí |
| -------- | ----------- | :-----: |
| [PovolitsynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | Ovládejte, zda je synchronní `HttpContext.Request.Body` vstup/výstup povolen pro a . `HttpContext.Response.Body` | `true` |
| [Ověřování.AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | Povolit anonymní požadavky. | `true` |
| [Ověřování.Schémata](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | Zadejte povolená schémata ověřování. Může být změněn kdykolipřed likvidací posluchače. Hodnoty jsou uvedeny ve [výčtu AuthenticationSchemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes) `Basic`: , `Kerberos` `Negotiate`, `None`, a `NTLM`. | `None` |
| [EnableResponseCaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | Pokus te se o ukládání odpovědí do [mezipaměti v režimu jádra](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) s způsobilými záhlavími. Odpověď nesmí `Set-Cookie`obsahovat `Vary`hlavičky nebo `Pragma` záhlaví . Musí obsahovat `Cache-Control` záhlaví, `public` které je `shared-max-age` `max-age` a nebo `Expires` hodnota, nebo záhlaví. | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | Maximální počet souběžných přijímá. | 5 &times; [Životní<br> prostředí. Počet procesorů](xref:System.Environment.ProcessorCount) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | Maximální počet souběžných připojení přijmout. Použít `-1` pro nekonečno. Slouží `null` k použití nastavení celého počítače v registru. | `null`<br>(celostrojově<br>nastavení) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | Viz část <a href="#maxrequestbodysize">MaxRequestBodySize.</a> | 30000000 bajtů<br>(~28,6 MB) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | Maximální počet požadavků, které mohou být zařazeny do fronty. | 1000 |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | Označte, zda by zápisy v těle odpovědi, které se nezdaří z důvodu odpojení klienta, měly vyvolat výjimky nebo je dokončit normálně. | `false`<br>(kompletní normálně) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | Vystavit konfiguraci HTTP.sys, <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> která může být také nakonfigurována v registru. Další informace o jednotlivých nastaveních, včetně výchozích hodnot, najdete v odkazech rozhraní API:<ul><li>[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody) &ndash; Čas povolen pro rozhraní API http serveru vyprázdnit tělo entity na keep-alive připojení.</li><li>[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody) &ndash; Čas povolen pro tělo entity požadavku k doručení.</li><li>[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait) &ndash; Čas povolen pro rozhraní API serveru HTTP analyzovat hlavičku požadavku.</li><li>[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection) &ndash; Čas povolen pro nečinné připojení.</li><li>[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond) &ndash; Minimální rychlost odesílání pro odpověď.</li><li>[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue) &ndash; Čas povolen pro požadavek zůstat ve frontě požadavků před aplikace zvedne.</li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | Zadejte <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> registraci pomocí souboru HTTP.sys. Nejužitečnější je [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), který se používá k přidání předpony do kolekce. Ty mohou být změněny kdykolipřed likvidací posluchače. |  |

<a name="maxrequestbodysize"></a>

**Velikost maxrequestbodysize**

Maximální povolená velikost libovolného těla požadavku v bajtech. Pokud je `null`nastavena na , maximální velikost těla požadavku je neomezená. Toto omezení nemá žádný vliv na inovovaná připojení, která jsou vždy neomezená.

Doporučená metoda přepsat limit v aplikaci ASP.NET Core MVC pro jeden `IActionResult` je použít <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> atribut na metodu akce:

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

Výjimka je vyvolána, pokud se aplikace pokusí nakonfigurovat limit na požadavek poté, co aplikace začala číst požadavek. Vlastnost `IsReadOnly` lze použít k označení, pokud je `MaxRequestBodySize` vlastnost ve stavu jen pro čtení, což znamená, že je příliš pozdě na konfiguraci limitu.

Pokud by aplikace <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> měla přepsat na <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>žádost, použijte :

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

Pokud používáte Visual Studio, ujistěte se, že aplikace není nakonfigurovaná pro spuštění služby IIS nebo Služby IIS Express.

V sadě Visual Studio je výchozí profil spuštění pro iis express. Chcete-li projekt spustit jako konzolovou aplikaci, ručně změňte vybraný profil, jak je znázorněno na následujícím snímku obrazovky:

![Výběr profilu konzolové aplikace](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a>Konfigurace systému Windows Server

1. Určete porty, které chcete pro aplikaci otevřít, a pomocí brány [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) nebo rutiny [Prostředí PowerShell New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) otevřete porty brány firewall, abyste umožnili přenosy dosáhnout souboru HTTP.sys. V následujících příkazech a konfiguraci aplikace se používá port 443.

1. Při nasazování do virtuálního počítače Azure otevřete porty ve [skupině zabezpečení sítě](/azure/virtual-machines/windows/nsg-quickstart-portal). V následujících příkazech a konfiguraci aplikace se používá port 443.

1. V případě potřeby získejte a nainstalujte certifikáty X.509.

   V systému Windows vytvořte certifikáty podepsané svým držitelem pomocí [rutiny prostředí New-SelfSignedCertificate PowerShell](/powershell/module/pkiclient/new-selfsignedcertificate). Nepodporovaný příklad naleznete v tématu [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).

   Nainstalujte certifikáty podepsané svým držitelem nebo certifikáty podepsané certifikační autoritou do **osobního** úložiště **místního počítače** > na serveru.

1. Pokud je aplikace [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd), nainstalujte .NET Core, .NET Framework nebo obojí (pokud je aplikace .NET Core aplikace cílení na rozhraní .NET Framework).

   * **.NET Core** &ndash; Pokud aplikace vyžaduje .NET Core, získejte a spusťte instalační program **.NET Core Runtime** z [souborů .NET Core Downloads](https://dotnet.microsoft.com/download). Neinstalujte úplnou sadu SDK na server.
   * **Rozhraní .NET Framework:** &ndash; Pokud aplikace vyžaduje rozhraní .NET Framework, přečtěte si [příručku k instalaci rozhraní .NET Framework](/dotnet/framework/install/). Nainstalujte požadovanou architekturu .NET Framework. Instalační program pro nejnovější rozhraní .NET Framework je k dispozici na stránce [Stahování jádra .NET.](https://dotnet.microsoft.com/download)

   Pokud je aplikace [samostatné nasazení](/dotnet/core/deploying/#self-contained-deployments-scd), aplikace obsahuje runtime v jeho nasazení. Na serveru není vyžadována žádná instalace architektury.

1. Konfigurace adres URL a portů v aplikaci.

   Ve výchozím nastavení se `http://localhost:5000`ASP.NET jádro váže na . Chcete-li nakonfigurovat předpony adres URL a porty, možnosti zahrnují:

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * `urls`argument příkazového řádku
   * `ASPNETCORE_URLS`proměnná prostředí
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   Následující příklad kódu ukazuje, <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> jak se používat s `10.0.0.4` místní IP adresou serveru na portu 443:

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

   Výhodou `UrlPrefixes` je, že chybová zpráva je generována okamžitě pro nesprávně formátované předpony.

   Nastavení v `UrlPrefixes` nastavení `UseUrls` / `urls` / `ASPNETCORE_URLS` přepsání. Výhodou aplikace `UseUrls`, `urls`a proměnné `ASPNETCORE_URLS` prostředí je proto, že je snazší přepínat mezi kestrelem a protokolem HTTP.sys.

   Protokol HTTP.sys používá [formáty řetězců URLPrefix rozhraní HTTP Server.](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)

   > [!WARNING]
   > Vazby se zástupnými`http://*:80/` symboly nejvyšší úrovně ( a `http://+:80`) by **neměly** být používány. Vazby se zástupnými znaky nejvyšší úrovně vytvářejí chyby zabezpečení aplikace. To platí pro silné i slabé zástupné znaky. Používejte explicitní názvy hostitelů nebo adresy IP místo zástupných znaků. Vazba se zástupnými znaky subdomény (například ) není bezpečnostní riziko, `*.mysub.com`pokud `*.com`řídíte celou nadřazenou doménu (na rozdíl od , která je zranitelná). Další informace naleznete v [tématu RFC 7230: Oddíl 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).

1. Předpony url předregister na serveru.

   Vestavěný nástroj pro konfiguraci http.sys je *netsh.exe*. *netsh.exe* se používá k rezervaci předpon URL a přiřazení certifikátů X.509. Nástroj vyžaduje oprávnění správce.

   Pomocí nástroje *netsh.exe* zaregistrujte adresy URL aplikace:

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * `<URL>`&ndash; Plně kvalifikovaný jednotný lokátor zdrojů (URL). Nepoužívejte vazby zástupných symbolů. Použijte platný název hostitele nebo místní ADRESU IP. *Adresa URL musí obsahovat koncové lomítko.*
   * `<USER>`&ndash; Určuje název uživatele nebo skupiny uživatelů.

   V následujícím příkladu je `10.0.0.4`místní IP adresa serveru :

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   Když je adresa URL zaregistrována, nástroj odpoví pomocí . `URL reservation successfully added`

   Chcete-li odstranit registrovanou `delete urlacl` adresu URL, použijte příkaz:

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. Zaregistrujte certifikáty X.509 na serveru.

   Pomocí nástroje *netsh.exe* zaregistrujte certifikáty pro aplikaci:

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * `<IP>`&ndash; Určuje místní adresu IP pro vazbu. Nepoužívejte vazby zástupných symbolů. Použijte platnou adresu IP.
   * `<PORT>`&ndash; Určuje port pro vazbu.
   * `<THUMBPRINT>`&ndash; Kryptografický otisk certifikátu X.509.
   * `<GUID>`&ndash; Identifikátor GUID generovaný vývojářem, který představuje aplikaci pro informační účely.

   Pro referenční účely uložte identifikátor GUID do aplikace jako značku balíčku:

   * V sadě Visual Studio:
     * Otevřete vlastnosti projektu aplikace kliknutím pravým tlačítkem myši na aplikaci v **Průzkumníku řešení** a výběrem **možnosti Vlastnosti**.
     * Vyberte kartu **Balíček.**
     * Zadejte identifikátor GUID, který jste vytvořili, do pole **Značky.**
   * Pokud nepoužíváte Visual Studio:
     * Otevřete soubor projektu aplikace.
     * Přidejte `<PackageTags>` vlastnost do nové `<PropertyGroup>` nebo existující s identifikátorem GUID, který jste vytvořili:

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   V následujícím příkladu:

   * Místní ADRESA IP serveru `10.0.0.4`je .
   * Online generátor náhodných identifikátorů GUID poskytuje hodnotu. `appid`

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   Při registraci certifikátu nástroj odpoví `SSL Certificate successfully added`pomocí .

   Chcete-li odstranit registraci `delete sslcert` certifikátu, použijte příkaz:

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   Referenční dokumentace pro *netsh.exe*:

   * [Příkazy Netsh pro protokol HTTP (HTTP)](https://technet.microsoft.com/library/cc725882.aspx)
   * [Řetězce urlprefix](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)

1. Spusťte aplikaci.

   Oprávnění správce nejsou nutné ke spuštění aplikace při vazbě na localhost pomocí PROTOKOLU HTTP (nikoli HTTPS) s číslem portu větší než 1024. Pro ostatní konfigurace (například pomocí místní IP adresy nebo vazby na port 443) spusťte aplikaci s oprávněními správce.

   Aplikace reaguje na veřejnou IP adresu serveru. V tomto příkladu je server osloven z Internetu `104.214.79.47`na jeho veřejné IP adrese .

   V tomto příkladu se používá certifikát vývoje. Stránka se načte bezpečně po vynechání upozornění nedůvěryhodného certifikátu prohlížeče.

   ![Okno prohlížeče zobrazující načtenou stránku Indexu aplikace](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a>Scénáře proxy serveru a vyrovnávání zatížení

U aplikací hostovaných protokolem HTTP.sys, které interagují s požadavky z Internetu nebo podnikové sítě, může být při hostování za proxy servery a nástroji pro vyrovnávání zatížení vyžadována další konfigurace. Další informace naleznete v [tématu Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).

## <a name="additional-resources"></a>Další zdroje

* [Povolení ověřování systému Windows pomocí protokolu HTTP.sys](xref:security/authentication/windowsauth#httpsys)
* [Rozhraní API serveru HTTP](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)
* [úložiště aspnet/HttpSysServer GitHub (zdrojový kód)](https://github.com/aspnet/HttpSysServer/)
* [Hostitel](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) je [webový server pro ASP.NET Core,](xref:fundamentals/servers/index) který běží pouze v systému Windows. HTTP.sys je alternativou k serveru [Kestrel](xref:fundamentals/servers/kestrel) a nabízí některé funkce, které Kestrel neposkytuje.

> [!IMPORTANT]
> Soubor HTTP.sys není kompatibilní s [ASP.NET základního modulu](xref:host-and-deploy/aspnet-core-module) a nelze jej použít se službou IIS nebo službou IIS Express.

HTTP.sys podporuje následující funkce:

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

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="when-to-use-httpsys"></a>Kdy použít soubor HTTP.sys

Http.sys je užitečné pro nasazení, kde:

* Je třeba vystavit server přímo na Internetu bez použití služby IIS.

  ![HTTP.sys komunikuje přímo s Internetem](httpsys/_static/httpsys-to-internet.png)

* Interní nasazení vyžaduje funkci, která není v kestrelu k dispozici, například [ověřování systému Windows](xref:security/authentication/windowsauth).

  ![HTTP.sys komunikuje přímo s interní sítí](httpsys/_static/httpsys-to-internal.png)

HTTP.sys je vyspělá technologie, která chrání před mnoha typy útoků a poskytuje robustnost, zabezpečení a škálovatelnost plně vybaveného webového serveru. Samotná iis běží jako naslouchací proces HTTP nad http.sys.

## <a name="http2-support"></a>Podpora HTTP/2

[HTTP/2](https://httpwg.org/specs/rfc7540.html) je povolena pro ASP.NET základní aplikace, pokud jsou splněny následující základní požadavky:

* Windows Server 2016/Windows 10 nebo novější
* Připojení [protokolu ap (APPLICATION-Layer Protocol) (ALPN)](https://tools.ietf.org/html/rfc7301#section-3)
* Připojení TLS 1.2 nebo novější

Pokud je navázáno připojení HTTP/2, `HTTP/1.1`protokol [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) hlásí .

Protokol HTTP/2 je ve výchozím nastavení povolen. Pokud není navázáno připojení HTTP/2, připojení přejde zpět na HTTP/1.1. V budoucí verzi systému Windows budou k dispozici příznaky konfigurace HTTP/2, včetně možnosti zakázat protokol HTTP/2 pomocí protokolu HTTP.sys.

## <a name="kernel-mode-authentication-with-kerberos"></a>Ověřování v režimu jádra pomocí protokolu Kerberos

Protokol HTTP.sys deleguje ověřování v režimu jádra pomocí ověřovacího protokolu Kerberos. Ověřování v uživatelském režimu není podporováno protokoly Kerberos a HTTP.sys. Účet počítače musí být použit k dešifrování tokenu nebo lístku protokolu Kerberos, který je získán ze služby Active Directory a předán klientem na server k ověření uživatele. Zaregistrujte hlavní název služby (SPN) pro hostitele, nikoli uživatele aplikace.

## <a name="how-to-use-httpsys"></a>Jak používat http.sys

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a>Konfigurace aplikace ASP.NET Core pro použití protokolu HTTP.sys

Odkaz na balíček v souboru projektu není vyžadován při použití [metabalíčku Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)). Pokud metabalíček nepoužíváte, `Microsoft.AspNetCore.App` přidejte odkaz na balíček [microsoft.aspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).

Volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> metody rozšíření při vytváření hostitele, <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>zadání mj. Následující příklad nastaví možnosti jejich výchozí hodnoty:

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

Další konfigurace http.sys je zpracována prostřednictvím [nastavení registru](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).

**Možnosti protokolu HTTP.sys**

| Vlastnost | Popis | Výchozí |
| -------- | ----------- | :-----: |
| [PovolitsynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | Ovládejte, zda je synchronní `HttpContext.Request.Body` vstup/výstup povolen pro a . `HttpContext.Response.Body` | `true` |
| [Ověřování.AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | Povolit anonymní požadavky. | `true` |
| [Ověřování.Schémata](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | Zadejte povolená schémata ověřování. Může být změněn kdykolipřed likvidací posluchače. Hodnoty jsou uvedeny ve [výčtu AuthenticationSchemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes) `Basic`: , `Kerberos` `Negotiate`, `None`, a `NTLM`. | `None` |
| [EnableResponseCaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | Pokus te se o ukládání odpovědí do [mezipaměti v režimu jádra](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) s způsobilými záhlavími. Odpověď nesmí `Set-Cookie`obsahovat `Vary`hlavičky nebo `Pragma` záhlaví . Musí obsahovat `Cache-Control` záhlaví, `public` které je `shared-max-age` `max-age` a nebo `Expires` hodnota, nebo záhlaví. | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | Maximální počet souběžných přijímá. | 5 &times; [Životní<br> prostředí. Počet procesorů](xref:System.Environment.ProcessorCount) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | Maximální počet souběžných připojení přijmout. Použít `-1` pro nekonečno. Slouží `null` k použití nastavení celého počítače v registru. | `null`<br>(celostrojově<br>nastavení) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | Viz část <a href="#maxrequestbodysize">MaxRequestBodySize.</a> | 30000000 bajtů<br>(~28,6 MB) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | Maximální počet požadavků, které mohou být zařazeny do fronty. | 1000 |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | Označte, zda by zápisy v těle odpovědi, které se nezdaří z důvodu odpojení klienta, měly vyvolat výjimky nebo je dokončit normálně. | `false`<br>(kompletní normálně) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | Vystavit konfiguraci HTTP.sys, <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> která může být také nakonfigurována v registru. Další informace o jednotlivých nastaveních, včetně výchozích hodnot, najdete v odkazech rozhraní API:<ul><li>[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody) &ndash; Čas povolen pro rozhraní API http serveru vyprázdnit tělo entity na keep-alive připojení.</li><li>[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody) &ndash; Čas povolen pro tělo entity požadavku k doručení.</li><li>[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait) &ndash; Čas povolen pro rozhraní API serveru HTTP analyzovat hlavičku požadavku.</li><li>[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection) &ndash; Čas povolen pro nečinné připojení.</li><li>[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond) &ndash; Minimální rychlost odesílání pro odpověď.</li><li>[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue) &ndash; Čas povolen pro požadavek zůstat ve frontě požadavků před aplikace zvedne.</li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | Zadejte <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> registraci pomocí souboru HTTP.sys. Nejužitečnější je [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), který se používá k přidání předpony do kolekce. Ty mohou být změněny kdykolipřed likvidací posluchače. |  |

<a name="maxrequestbodysize"></a>

**Velikost maxrequestbodysize**

Maximální povolená velikost libovolného těla požadavku v bajtech. Pokud je `null`nastavena na , maximální velikost těla požadavku je neomezená. Toto omezení nemá žádný vliv na inovovaná připojení, která jsou vždy neomezená.

Doporučená metoda přepsat limit v aplikaci ASP.NET Core MVC pro jeden `IActionResult` je použít <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> atribut na metodu akce:

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

Výjimka je vyvolána, pokud se aplikace pokusí nakonfigurovat limit na požadavek poté, co aplikace začala číst požadavek. Vlastnost `IsReadOnly` lze použít k označení, pokud je `MaxRequestBodySize` vlastnost ve stavu jen pro čtení, což znamená, že je příliš pozdě na konfiguraci limitu.

Pokud by aplikace <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> měla přepsat na <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>žádost, použijte :

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

Pokud používáte Visual Studio, ujistěte se, že aplikace není nakonfigurovaná pro spuštění služby IIS nebo Služby IIS Express.

V sadě Visual Studio je výchozí profil spuštění pro iis express. Chcete-li projekt spustit jako konzolovou aplikaci, ručně změňte vybraný profil, jak je znázorněno na následujícím snímku obrazovky:

![Výběr profilu konzolové aplikace](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a>Konfigurace systému Windows Server

1. Určete porty, které chcete pro aplikaci otevřít, a pomocí brány [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) nebo rutiny [Prostředí PowerShell New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) otevřete porty brány firewall, abyste umožnili přenosy dosáhnout souboru HTTP.sys. V následujících příkazech a konfiguraci aplikace se používá port 443.

1. Při nasazování do virtuálního počítače Azure otevřete porty ve [skupině zabezpečení sítě](/azure/virtual-machines/windows/nsg-quickstart-portal). V následujících příkazech a konfiguraci aplikace se používá port 443.

1. V případě potřeby získejte a nainstalujte certifikáty X.509.

   V systému Windows vytvořte certifikáty podepsané svým držitelem pomocí [rutiny prostředí New-SelfSignedCertificate PowerShell](/powershell/module/pkiclient/new-selfsignedcertificate). Nepodporovaný příklad naleznete v tématu [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).

   Nainstalujte certifikáty podepsané svým držitelem nebo certifikáty podepsané certifikační autoritou do **osobního** úložiště **místního počítače** > na serveru.

1. Pokud je aplikace [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd), nainstalujte .NET Core, .NET Framework nebo obojí (pokud je aplikace .NET Core aplikace cílení na rozhraní .NET Framework).

   * **.NET Core** &ndash; Pokud aplikace vyžaduje .NET Core, získejte a spusťte instalační program **.NET Core Runtime** z [souborů .NET Core Downloads](https://dotnet.microsoft.com/download). Neinstalujte úplnou sadu SDK na server.
   * **Rozhraní .NET Framework:** &ndash; Pokud aplikace vyžaduje rozhraní .NET Framework, přečtěte si [příručku k instalaci rozhraní .NET Framework](/dotnet/framework/install/). Nainstalujte požadovanou architekturu .NET Framework. Instalační program pro nejnovější rozhraní .NET Framework je k dispozici na stránce [Stahování jádra .NET.](https://dotnet.microsoft.com/download)

   Pokud je aplikace [samostatné nasazení](/dotnet/core/deploying/#self-contained-deployments-scd), aplikace obsahuje runtime v jeho nasazení. Na serveru není vyžadována žádná instalace architektury.

1. Konfigurace adres URL a portů v aplikaci.

   Ve výchozím nastavení se `http://localhost:5000`ASP.NET jádro váže na . Chcete-li nakonfigurovat předpony adres URL a porty, možnosti zahrnují:

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * `urls`argument příkazového řádku
   * `ASPNETCORE_URLS`proměnná prostředí
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   Následující příklad kódu ukazuje, <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> jak se používat s `10.0.0.4` místní IP adresou serveru na portu 443:

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

   Výhodou `UrlPrefixes` je, že chybová zpráva je generována okamžitě pro nesprávně formátované předpony.

   Nastavení v `UrlPrefixes` nastavení `UseUrls` / `urls` / `ASPNETCORE_URLS` přepsání. Výhodou aplikace `UseUrls`, `urls`a proměnné `ASPNETCORE_URLS` prostředí je proto, že je snazší přepínat mezi kestrelem a protokolem HTTP.sys.

   Protokol HTTP.sys používá [formáty řetězců URLPrefix rozhraní HTTP Server.](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)

   > [!WARNING]
   > Vazby se zástupnými`http://*:80/` symboly nejvyšší úrovně ( a `http://+:80`) by **neměly** být používány. Vazby se zástupnými znaky nejvyšší úrovně vytvářejí chyby zabezpečení aplikace. To platí pro silné i slabé zástupné znaky. Používejte explicitní názvy hostitelů nebo adresy IP místo zástupných znaků. Vazba se zástupnými znaky subdomény (například ) není bezpečnostní riziko, `*.mysub.com`pokud `*.com`řídíte celou nadřazenou doménu (na rozdíl od , která je zranitelná). Další informace naleznete v [tématu RFC 7230: Oddíl 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).

1. Předpony url předregister na serveru.

   Vestavěný nástroj pro konfiguraci http.sys je *netsh.exe*. *netsh.exe* se používá k rezervaci předpon URL a přiřazení certifikátů X.509. Nástroj vyžaduje oprávnění správce.

   Pomocí nástroje *netsh.exe* zaregistrujte adresy URL aplikace:

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * `<URL>`&ndash; Plně kvalifikovaný jednotný lokátor zdrojů (URL). Nepoužívejte vazby zástupných symbolů. Použijte platný název hostitele nebo místní ADRESU IP. *Adresa URL musí obsahovat koncové lomítko.*
   * `<USER>`&ndash; Určuje název uživatele nebo skupiny uživatelů.

   V následujícím příkladu je `10.0.0.4`místní IP adresa serveru :

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   Když je adresa URL zaregistrována, nástroj odpoví pomocí . `URL reservation successfully added`

   Chcete-li odstranit registrovanou `delete urlacl` adresu URL, použijte příkaz:

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. Zaregistrujte certifikáty X.509 na serveru.

   Pomocí nástroje *netsh.exe* zaregistrujte certifikáty pro aplikaci:

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * `<IP>`&ndash; Určuje místní adresu IP pro vazbu. Nepoužívejte vazby zástupných symbolů. Použijte platnou adresu IP.
   * `<PORT>`&ndash; Určuje port pro vazbu.
   * `<THUMBPRINT>`&ndash; Kryptografický otisk certifikátu X.509.
   * `<GUID>`&ndash; Identifikátor GUID generovaný vývojářem, který představuje aplikaci pro informační účely.

   Pro referenční účely uložte identifikátor GUID do aplikace jako značku balíčku:

   * V sadě Visual Studio:
     * Otevřete vlastnosti projektu aplikace kliknutím pravým tlačítkem myši na aplikaci v **Průzkumníku řešení** a výběrem **možnosti Vlastnosti**.
     * Vyberte kartu **Balíček.**
     * Zadejte identifikátor GUID, který jste vytvořili, do pole **Značky.**
   * Pokud nepoužíváte Visual Studio:
     * Otevřete soubor projektu aplikace.
     * Přidejte `<PackageTags>` vlastnost do nové `<PropertyGroup>` nebo existující s identifikátorem GUID, který jste vytvořili:

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   V následujícím příkladu:

   * Místní ADRESA IP serveru `10.0.0.4`je .
   * Online generátor náhodných identifikátorů GUID poskytuje hodnotu. `appid`

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   Při registraci certifikátu nástroj odpoví `SSL Certificate successfully added`pomocí .

   Chcete-li odstranit registraci `delete sslcert` certifikátu, použijte příkaz:

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   Referenční dokumentace pro *netsh.exe*:

   * [Příkazy Netsh pro protokol HTTP (HTTP)](https://technet.microsoft.com/library/cc725882.aspx)
   * [Řetězce urlprefix](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)

1. Spusťte aplikaci.

   Oprávnění správce nejsou nutné ke spuštění aplikace při vazbě na localhost pomocí PROTOKOLU HTTP (nikoli HTTPS) s číslem portu větší než 1024. Pro ostatní konfigurace (například pomocí místní IP adresy nebo vazby na port 443) spusťte aplikaci s oprávněními správce.

   Aplikace reaguje na veřejnou IP adresu serveru. V tomto příkladu je server osloven z Internetu `104.214.79.47`na jeho veřejné IP adrese .

   V tomto příkladu se používá certifikát vývoje. Stránka se načte bezpečně po vynechání upozornění nedůvěryhodného certifikátu prohlížeče.

   ![Okno prohlížeče zobrazující načtenou stránku Indexu aplikace](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a>Scénáře proxy serveru a vyrovnávání zatížení

U aplikací hostovaných protokolem HTTP.sys, které interagují s požadavky z Internetu nebo podnikové sítě, může být při hostování za proxy servery a nástroji pro vyrovnávání zatížení vyžadována další konfigurace. Další informace naleznete v [tématu Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).

## <a name="additional-resources"></a>Další zdroje

* [Povolení ověřování systému Windows pomocí protokolu HTTP.sys](xref:security/authentication/windowsauth#httpsys)
* [Rozhraní API serveru HTTP](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)
* [úložiště aspnet/HttpSysServer GitHub (zdrojový kód)](https://github.com/aspnet/HttpSysServer/)
* [Hostitel](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

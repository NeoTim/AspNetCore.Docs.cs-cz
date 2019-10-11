---
title: Implementace webového serveru HTTP. sys v ASP.NET Core
author: guardrex
description: Přečtěte si o HTTP. sys, webovém serveru pro ASP.NET Core ve Windows. Ovladač HTTP. sys založený na ovladači režimu jádra HTTP. sys je alternativou k Kestrel, která se dá použít k přímému připojení k Internetu bez služby IIS.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/08/2019
uid: fundamentals/servers/httpsys
ms.openlocfilehash: acdcdca3250f2aa3445458cc2c4e5f50360338a1
ms.sourcegitcommit: 73a451e9a58ac7102f90b608d661d8c23dd9bbaf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72037614"
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

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([stažení](xref:index#how-to-download-a-sample))

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

::: moniker range="< aspnetcore-3.0"

Odkaz na balíček v souboru projektu není vyžadován při použití [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) ([NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)). Pokud nepoužíváte `Microsoft.AspNetCore.App` Metapackage, přidejte odkaz na balíček do [Microsoft. AspNetCore. Server. HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).

::: moniker-end

Při sestavování hostitele zavolejte metodu rozšíření <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> a určete všechny požadované <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>. Následující příklad nastaví možnosti na jejich výchozí hodnoty:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

::: moniker-end

Další konfiguraci HTTP. sys bude zpracována prostřednictvím [nastavení registru](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).

   **Možnosti HTTP. sys**

::: moniker range=">= aspnetcore-3.1"

| Vlastnost | Popis | Výchozí |
| -------- | ----------- | :-----: |
| [AllowSynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | Určuje, zda je povolen synchronní vstup/výstup pro `HttpContext.Request.Body` a `HttpContext.Response.Body`. | `false` |
| [Ověřování. AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | Povoluje anonymní požadavky. | `true` |
| [Ověřování. schémata](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | Určete povolená schémata ověřování. Může být kdykoli změněno před vyřazením naslouchacího procesu. Hodnoty jsou k dispozici ve [výčtu schémata AuthenticationSchemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None` a `NTLM`. | `None` |
| [EnableResponseCaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | Pokusit se o ukládání do mezipaměti v [režimu jádra](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) pro odpovědi s oprávněnými záhlavími. Odpověď nesmí obsahovat hlavičku `Set-Cookie`, `Vary` nebo `Pragma`. Musí obsahovat hlavičku `Cache-Control`, která je `public` a buď hodnota `shared-max-age` nebo `max-age` nebo hlavička `Expires`. | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | Maximální počet souběžných přijetí. | 5 @no__t – 0 [prostředí. @no__t – 2ProcessorCount](xref:System.Environment.ProcessorCount) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | Maximální počet souběžných připojení, která se mají přijmout Pro nekonečné použijte `-1`. Pomocí `null` můžete použít nastavení pro počítač v rámci registru. | `null`<br>počet |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | Viz část <a href="#maxrequestbodysize">MaxRequestBodySize</a> . | 30000000 bajtů<br>(~ 28,6 MB) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | Maximální počet požadavků, které lze zařadit do fronty. | 1000 |
| `RequestQueueMode` | Označuje, zda je server zodpovědný za vytvoření a konfiguraci fronty požadavků nebo zda se má připojit k existující frontě.<br>Většina stávajících možností konfigurace se nedá použít při připojování k existující frontě. | `RequestQueueMode.Create` |
| `RequestQueueName` | Název fronty požadavků HTTP. sys. | `null` (anonymní fronta) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | Určuje, jestli tělo odpovědi zapisuje, které selhalo kvůli odpojení klienta, by mělo vyvolat výjimky nebo dokončit normálně. | `false`<br>(normálně dokončit) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | Vystavte konfiguraci HTTP. sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager>, která může být v registru nakonfigurovaná taky. Pomocí odkazů rozhraní API získáte další informace o jednotlivých nastaveních, včetně výchozích hodnot:<ul><li>[TimeoutManager. DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody) &ndash; čas povolený pro rozhraní API serveru http, který má vyprázdnit tělo entity u připojení Keep-Alive.</li><li>[TimeoutManager. EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody) &ndash; čas povolený pro doručení těla entity požadavku.</li><li>[TimeoutManager. HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait) &ndash; čas povolený pro rozhraní API serveru HTTP k analýze hlavičky žádosti.</li><li>[TimeoutManager. IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection) &ndash; čas povolený pro nečinné připojení.</li><li>[TimeoutManager. MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond) &ndash; minimální rychlost odesílání odpovědi.</li><li>[TimeoutManager. RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue) &ndash; čas, který může požadavek zůstat ve frontě požadavků předtím, než ji aplikace vybere.</li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | Zadejte <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> pro registraci v HTTP. sys. Nejužitečnější je [UrlPrefixCollection. Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), který se používá k přidání předpony do kolekce. Ty mohou být kdykoliv změněny předtím, než se naslouchací proces vyřazuje. |  |

::: moniker-end

::: moniker range="= aspnetcore-3.0"

| Vlastnost | Popis | Výchozí |
| -------- | ----------- | :-----: |
| [AllowSynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | Určuje, zda je povolen synchronní vstup/výstup pro `HttpContext.Request.Body` a `HttpContext.Response.Body`. | `false` |
| [Ověřování. AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | Povoluje anonymní požadavky. | `true` |
| [Ověřování. schémata](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | Určete povolená schémata ověřování. Může být kdykoli změněno před vyřazením naslouchacího procesu. Hodnoty jsou k dispozici ve [výčtu schémata AuthenticationSchemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None` a `NTLM`. | `None` |
| [EnableResponseCaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | Pokusit se o ukládání do mezipaměti v [režimu jádra](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) pro odpovědi s oprávněnými záhlavími. Odpověď nesmí obsahovat hlavičku `Set-Cookie`, `Vary` nebo `Pragma`. Musí obsahovat hlavičku `Cache-Control`, která je `public` a buď hodnota `shared-max-age` nebo `max-age` nebo hlavička `Expires`. | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | Maximální počet souběžných přijetí. | 5 @no__t – 0 [prostředí. @no__t – 2ProcessorCount](xref:System.Environment.ProcessorCount) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | Maximální počet souběžných připojení, která se mají přijmout Pro nekonečné použijte `-1`. Pomocí `null` můžete použít nastavení pro počítač v rámci registru. | `null`<br>počet |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | Viz část <a href="#maxrequestbodysize">MaxRequestBodySize</a> . | 30000000 bajtů<br>(~ 28,6 MB) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | Maximální počet požadavků, které lze zařadit do fronty. | 1000 |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | Určuje, jestli tělo odpovědi zapisuje, které selhalo kvůli odpojení klienta, by mělo vyvolat výjimky nebo dokončit normálně. | `false`<br>(normálně dokončit) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | Vystavte konfiguraci HTTP. sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager>, která může být v registru nakonfigurovaná taky. Pomocí odkazů rozhraní API získáte další informace o jednotlivých nastaveních, včetně výchozích hodnot:<ul><li>[TimeoutManager. DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody) &ndash; čas povolený pro rozhraní API serveru http, který má vyprázdnit tělo entity u připojení Keep-Alive.</li><li>[TimeoutManager. EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody) &ndash; čas povolený pro doručení těla entity požadavku.</li><li>[TimeoutManager. HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait) &ndash; čas povolený pro rozhraní API serveru HTTP k analýze hlavičky žádosti.</li><li>[TimeoutManager. IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection) &ndash; čas povolený pro nečinné připojení.</li><li>[TimeoutManager. MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond) &ndash; minimální rychlost odesílání odpovědi.</li><li>[TimeoutManager. RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue) &ndash; čas, který může požadavek zůstat ve frontě požadavků předtím, než ji aplikace vybere.</li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | Zadejte <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> pro registraci v HTTP. sys. Nejužitečnější je [UrlPrefixCollection. Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), který se používá k přidání předpony do kolekce. Ty mohou být kdykoliv změněny předtím, než se naslouchací proces vyřazuje. |  |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| Vlastnost | Popis | Výchozí |
| -------- | ----------- | :-----: |
| [AllowSynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | Určuje, zda je povolen synchronní vstup/výstup pro `HttpContext.Request.Body` a `HttpContext.Response.Body`. | `true` |
| [Ověřování. AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | Povoluje anonymní požadavky. | `true` |
| [Ověřování. schémata](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | Určete povolená schémata ověřování. Může být kdykoli změněno před vyřazením naslouchacího procesu. Hodnoty jsou k dispozici ve [výčtu schémata AuthenticationSchemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None` a `NTLM`. | `None` |
| [EnableResponseCaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | Pokusit se o ukládání do mezipaměti v [režimu jádra](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) pro odpovědi s oprávněnými záhlavími. Odpověď nesmí obsahovat hlavičku `Set-Cookie`, `Vary` nebo `Pragma`. Musí obsahovat hlavičku `Cache-Control`, která je `public` a buď hodnota `shared-max-age` nebo `max-age` nebo hlavička `Expires`. | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | Maximální počet souběžných přijetí. | 5 @no__t – 0 [prostředí. @no__t – 2ProcessorCount](xref:System.Environment.ProcessorCount) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | Maximální počet souběžných připojení, která se mají přijmout Pro nekonečné použijte `-1`. Pomocí `null` můžete použít nastavení pro počítač v rámci registru. | `null`<br>počet |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | Viz část <a href="#maxrequestbodysize">MaxRequestBodySize</a> . | 30000000 bajtů<br>(~ 28,6 MB) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | Maximální počet požadavků, které lze zařadit do fronty. | 1000 |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | Určuje, jestli tělo odpovědi zapisuje, které selhalo kvůli odpojení klienta, by mělo vyvolat výjimky nebo dokončit normálně. | `false`<br>(normálně dokončit) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | Vystavte konfiguraci HTTP. sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager>, která může být v registru nakonfigurovaná taky. Pomocí odkazů rozhraní API získáte další informace o jednotlivých nastaveních, včetně výchozích hodnot:<ul><li>[TimeoutManager. DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody) &ndash; čas povolený pro rozhraní API serveru http, který má vyprázdnit tělo entity u připojení Keep-Alive.</li><li>[TimeoutManager. EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody) &ndash; čas povolený pro doručení těla entity požadavku.</li><li>[TimeoutManager. HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait) &ndash; čas povolený pro rozhraní API serveru HTTP k analýze hlavičky žádosti.</li><li>[TimeoutManager. IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection) &ndash; čas povolený pro nečinné připojení.</li><li>[TimeoutManager. MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond) &ndash; minimální rychlost odesílání odpovědi.</li><li>[TimeoutManager. RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue) &ndash; čas, který může požadavek zůstat ve frontě požadavků předtím, než ji aplikace vybere.</li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | Zadejte <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> pro registraci v HTTP. sys. Nejužitečnější je [UrlPrefixCollection. Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), který se používá k přidání předpony do kolekce. Ty mohou být kdykoliv změněny předtím, než se naslouchací proces vyřazuje. |  |

::: moniker-end

<a name="maxrequestbodysize"></a>

**MaxRequestBodySize**

Maximální povolená velikost libovolného textu žádosti v bajtech Pokud je nastavena hodnota `null`, maximální velikost textu požadavku je neomezená. Toto omezení nemá žádný vliv na upgradovaná připojení, která jsou vždycky neomezená.

Doporučená metoda pro přepsání limitu v aplikaci ASP.NET Core MVC pro jednu `IActionResult` je použití atributu <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> u metody Action:

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

Výjimka je vyvolána, pokud se aplikace pokusí nakonfigurovat limit žádosti poté, co aplikace začne číst požadavek. Vlastnost `IsReadOnly` lze použít k označení, zda je vlastnost `MaxRequestBodySize` ve stavu jen pro čtení, což znamená, že je příliš pozdě pro konfiguraci limitu.

Pokud by měla aplikace přepsat <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> na požadavek, použijte <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

::: moniker-end

Pokud používáte Visual Studio, ujistěte se, že aplikace není nakonfigurovaná tak, aby spouštěla službu IIS nebo IIS Express.

V aplikaci Visual Studio je výchozím spouštěcím profilem IIS Express. Chcete-li spustit projekt jako konzolovou aplikaci, ručně změňte vybraný profil, jak je znázorněno na následujícím snímku obrazovky:

![Vybrat profil aplikace konzoly](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a>Konfigurace Windows serveru

1. Určete porty, které se mají otevřít pro aplikaci, a pomocí [brány Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) nebo rutiny [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) prostředí PowerShell otevřete porty brány firewall, aby bylo možné provozovat přes protokol HTTP. sys. V následujících příkazech a konfiguraci aplikace se používá port 443.

1. Při nasazování na virtuální počítač Azure otevřete porty ve [skupině zabezpečení sítě](/azure/virtual-machines/windows/nsg-quickstart-portal). V následujících příkazech a konfiguraci aplikace se používá port 443.

1. V případě potřeby Získejte a nainstalujte certifikáty X. 509.

   V systému Windows vytvořte pomocí [rutiny prostředí PowerShell New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate)certifikáty podepsané svým držitelem. Nepodporovaný příklad naleznete v tématu [UpdateIISExpressSSLForChrome. ps1](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).

   Nainstalujte certifikáty podepsané svým držitelem nebo certifikátem podepsaným certifikační autoritou do**osobního** úložiště **místního počítače**serveru  > .

1. Pokud se jedná o [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd), nainstalujte rozhraní .NET core, .NET Framework nebo obojí (Pokud je aplikace aplikace .NET Core cílící na .NET Framework).

   * **.NET core** &ndash; Pokud aplikace vyžaduje .NET Core, Získejte a spusťte instalační program **modulu runtime .NET Core** ze [souborů ke stažení pro .NET Core](https://dotnet.microsoft.com/download). Neinstalujte na server plnou sadu SDK.
   * **.NET Framework** &ndash; Pokud aplikace vyžaduje .NET Framework, přečtěte si [Průvodce instalací .NET Framework](/dotnet/framework/install/). Nainstalujte požadovanou .NET Framework. Instalační program pro nejnovější .NET Framework je k dispozici na stránce [soubory ke stažení pro .NET Core](https://dotnet.microsoft.com/download) .

   Pokud je aplikace samostatným [nasazením](/dotnet/core/deploying/#self-contained-deployments-scd), aplikace zahrne modul runtime do svého nasazení. Na serveru není nutná žádná instalace rozhraní.

1. Konfigurace adres URL a portů v aplikaci

   Ve výchozím nastavení ASP.NET Core váže `http://localhost:5000`. Pokud chcete nakonfigurovat předpony a porty adresy URL, můžete použít tyto možnosti:

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * argument příkazového řádku `urls`
   * Proměnná prostředí `ASPNETCORE_URLS`
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   Následující příklad kódu ukazuje, jak použít <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> s místní IP adresou serveru `10.0.0.4` na portu 443:

::: moniker range=">= aspnetcore-3.0"

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

::: moniker-end

   Výhodou `UrlPrefixes` je, že se okamžitě generuje chybová zpráva pro nesprávně naformátované předpony.

   Nastavení v nastavení `UrlPrefixes` přepsat `UseUrls` @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5. Výhody `UseUrls`, `urls` a proměnné prostředí `ASPNETCORE_URLS` proto usnadňují přepínání mezi Kestrel a HTTP. sys.

   HTTP. sys používá [formáty řetězce UrlPrefix HTTP serveru API](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx).

   > [!WARNING]
   > Vazby nejvyšší úrovně zástupný znak (`http://*:80/` a `http://+:80`) by měl **není** použít. Vazby na zástupné znaky na nejvyšší úrovni vytvoří chyby zabezpečení aplikace. To platí pro silné a slabé zástupné znaky. Místo zástupných znaků použijte explicitní názvy hostitelů nebo IP adresy. Vazba zástupných znaků subdomény (například `*.mysub.com`) není bezpečnostní riziko, pokud ovládáte celou nadřazenou doménu (na rozdíl od `*.com`, která je zranitelná). Další informace najdete v tématu [RFC 7230: Oddíl 5,4: Hostitel @ no__t-0.

1. Předregistrujte předpony adresy URL na serveru.

   Integrovaný nástroj pro konfiguraci souboru HTTP. sys je *netsh. exe*. pomocí *nástroje Netsh. exe* můžete rezervovat PŘEDPONY adres URL a přiřazovat certifikáty X. 509. Nástroj vyžaduje oprávnění správce.

   Registrace adres URL pro aplikaci pomocí nástroje *netsh. exe* :

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * `<URL>` &ndash; plně kvalifikované adresy URL (Uniform Resource Locator). Nepoužívejte vazbu zástupných znaků. Použijte platný název hostitele nebo místní IP adresu. *Adresa URL musí obsahovat koncové lomítko.*
   * `<USER>` &ndash; určuje jméno uživatele nebo název skupiny.

   V následujícím příkladu je místní IP adresa serveru `10.0.0.4`:

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   Když je adresa URL registrována, nástroj odpoví pomocí `URL reservation successfully added`.

   Pokud chcete odstranit registrovanou adresu URL, použijte příkaz `delete urlacl`:

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. Zaregistrujte certifikáty X. 509 na serveru.

   K registraci certifikátů pro aplikaci použijte nástroj *netsh. exe* :

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * `<IP>` &ndash; určuje místní IP adresu pro vazbu. Nepoužívejte vazbu zástupných znaků. Použijte platnou IP adresu.
   * `<PORT>` &ndash; určuje port pro vazbu.
   * `<THUMBPRINT>` &ndash; kryptografický otisk certifikátu X. 509.
   * `<GUID>` &ndash; identifikátor GUID generovaný vývojářem, který představuje aplikaci pro informační účely.

   Pro referenční účely uložte identifikátor GUID do aplikace jako značku balíčku:

   * V sadě Visual Studio:
     * Otevřete vlastnosti projektu aplikace tak, že kliknete pravým tlačítkem na aplikaci v **Průzkumník řešení** a vyberete **vlastnosti**.
     * Vyberte kartu **balíček** .
     * Zadejte identifikátor GUID, který jste vytvořili v poli **značky** .
   * Když nepoužíváte Visual Studio:
     * Otevřete soubor projektu aplikace.
     * Přidejte vlastnost `<PackageTags>` do nového nebo existujícího `<PropertyGroup>` s identifikátorem GUID, který jste vytvořili:

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   V následujícím příkladu:

   * Místní IP adresa serveru je `10.0.0.4`.
   * Generátor náhodných identifikátorů GUID online poskytuje hodnotu `appid`.

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   Když je certifikát zaregistrován, nástroj odpoví `SSL Certificate successfully added`.

   Chcete-li odstranit registraci certifikátu, použijte příkaz `delete sslcert`:

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

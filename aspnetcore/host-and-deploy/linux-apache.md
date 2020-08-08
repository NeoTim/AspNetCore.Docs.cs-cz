---
title: Hostování ASP.NET Core v systému Linux pomocí Apache
author: rick-anderson
description: Naučte se, jak nastavit Apache jako reverzní proxy server v CentOS pro přesměrování provozu HTTP do ASP.NET Core webové aplikace běžící na Kestrel.
monikerRange: '>= aspnetcore-2.1'
ms.author: shboyer
ms.custom: mvc
ms.date: 04/10/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/linux-apache
ms.openlocfilehash: 2bf5633461996bfecaaa6b730adc9a19bb2769c4
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88015553"
---
# <a name="host-aspnet-core-on-linux-with-apache"></a>Hostování ASP.NET Core v systému Linux pomocí Apache

Od [Shayne Boyer](https://github.com/spboyer)

Pomocí této příručky se naučíte, jak nastavit [Apache](https://httpd.apache.org/) jako reverzní proxy server na [CentOS 7](https://www.centos.org/) pro přesměrování provozu http do ASP.NET Core webové aplikace běžící na serveru [Kestrel](xref:fundamentals/servers/kestrel) . [Rozšíření mod_proxy](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html) a související moduly vytvoří reverzní proxy server serveru.

## <a name="prerequisites"></a>Požadavky

* Server se systémem CentOS 7 se standardním uživatelským účtem s oprávněním sudo.
* Nainstalujte modul runtime .NET Core na server.
   1. Navštivte [stránku stáhnout jádro .NET Core](https://dotnet.microsoft.com/download/dotnet-core).
   1. Vyberte nejnovější verzi rozhraní .NET Core, která není ve verzi Preview.
   1. Stáhněte si nejnovější modul runtime bez verze Preview v tabulce v části **Spustit aplikace – modul runtime**.
   1. Vyberte odkaz **pokyny správce balíčků** pro Linux a postupujte podle pokynů pro CentOS.
* Existující aplikace ASP.NET Core.

V jakémkoli okamžiku v budoucnu po upgradu sdílené architektury restartujte aplikace ASP.NET Core hostované serverem.

## <a name="publish-and-copy-over-the-app"></a>Publikování a kopírování přes aplikaci

Nakonfigurujte aplikaci pro [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd).

Pokud je aplikace spuštěná místně a není nakonfigurovaná tak, aby přijímala zabezpečené připojení (HTTPS), proveďte jednu z následujících metod:

* Nakonfigurujte aplikaci tak, aby zpracovávala Zabezpečená místní připojení. Další informace najdete v části [konfigurace https](#https-configuration) .
* Odebere `https://localhost:5001` (je-li k dispozici) z `applicationUrl` vlastnosti v souboru *Properties/launchSettings.js* .

Pokud chcete zabalit aplikaci do adresáře (například *bin/Release/ &lt; target_framework_moniker &gt; /Publish*), která se dají spustit na serveru, spusťte [dotnet Publish](/dotnet/core/tools/dotnet-publish) z vývojového prostředí:

```dotnetcli
dotnet publish --configuration Release
```

Pokud nechcete zachovat modul runtime .NET Core na serveru, můžete aplikaci publikovat také jako samostatné [nasazení](/dotnet/core/deploying/#self-contained-deployments-scd) .

Zkopírujte aplikaci ASP.NET Core na server pomocí nástroje, který se integruje do pracovního postupu organizace (například SCP, SFTP). Je běžné najít webové aplikace v adresáři *var* (například *var/www/helloapp*).

> [!NOTE]
> V rámci scénáře nasazení v produkčním prostředí provádí pracovní postup průběžné integrace publikování aplikace a zkopírování prostředků na server.

## <a name="configure-a-proxy-server"></a>Konfigurace proxy server

Reverzní proxy je běžné nastavení pro obsluhu dynamických webových aplikací. Reverzní proxy ukončí požadavek HTTP a předá ho do aplikace ASP.NET.

Proxy server je ten, který přepošle požadavky klienta na jiný server místo toho, aby splňovaly samotné požadavky. Reverzní proxy server předává pevnému cíli, obvykle jménem libovolných klientů. V této příručce je Apache nakonfigurovaná jako reverzní proxy server běžící na stejném serveru, který Kestrel obsluhuje aplikaci ASP.NET Core.

Vzhledem k tomu, že požadavky jsou předávány reverzním proxy, použijte [middleware předávaných hlaviček](xref:host-and-deploy/proxy-load-balancer) z balíčku [Microsoft. AspNetCore. HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) . Middleware aktualizuje `Request.Scheme` pomocí `X-Forwarded-Proto` hlavičky, aby identifikátory URI pro přesměrování a další zásady zabezpečení fungovaly správně.

Po vyvolání middlewaru předávaných hlaviček musí být všechny komponenty, které jsou závislé na schématu, jako je ověřování, generace odkazů, přesměrování a zeměpisná poloha, umístěny.

[!INCLUDE[](~/includes/ForwardedHeaders.md)]

Volejte <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> metodu v horní části `Startup.Configure` před voláním jiného middleware. Nakonfigurujte middleware pro přeposílání `X-Forwarded-For` `X-Forwarded-Proto` hlaviček a:

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

Pokud <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> pro middleware nejsou zadány žádné, výchozí hlavičky budou předány `None` .

Proxy servery běžící na adresách zpětné smyčky (127.0.0.0/8, [:: 1]), včetně standardní adresy localhost (127.0.0.1), jsou ve výchozím nastavení důvěryhodné. Pokud jiné důvěryhodné proxy servery nebo sítě v rámci organizace zařídí žádosti mezi Internetem a webovým serverem, přidejte je do seznamu <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> nebo <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> s <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> . Následující příklad přidá důvěryhodnou proxy server na IP adrese 10.0.0.100 do middlewaru předávaných hlaviček `KnownProxies` v `Startup.ConfigureServices` :

```csharp
// using System.Net;

services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

Další informace naleznete v tématu <xref:host-and-deploy/proxy-load-balancer>.

### <a name="install-apache"></a>Nainstalovat Apache

Aktualizujte balíčky CentOS na nejnovější stabilní verze:

```bash
sudo yum update -y
```

Nainstalujte webový server Apache na CentOS jediným `yum` příkazem:

```bash
sudo yum -y install httpd mod_ssl
```

Ukázkový výstup po spuštění příkazu:

```bash
Downloading packages:
httpd-2.4.6-40.el7.centos.4.x86_64.rpm               | 2.7 MB  00:00:01
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
Installing : httpd-2.4.6-40.el7.centos.4.x86_64      1/1 
Verifying  : httpd-2.4.6-40.el7.centos.4.x86_64      1/1 

Installed:
httpd.x86_64 0:2.4.6-40.el7.centos.4

Complete!
```

> [!NOTE]
> V tomto příkladu výstup odráží httpd. 86_64, protože verze CentOS 7 je 64 bitů. Chcete-li ověřit, kde je nainstalována Apache, spusťte `whereis httpd` příkaz z příkazového řádku.

### <a name="configure-apache"></a>Konfigurace Apache

Konfigurační soubory pro Apache se nacházejí v `/etc/httpd/conf.d/` adresáři. Libovolný soubor s příponou *. conf* je zpracováván v abecedním pořadí vedle konfiguračních souborů modulu v `/etc/httpd/conf.modules.d/` , který obsahuje všechny konfigurační soubory potřebné pro načtení modulů.

Pro aplikaci vytvořte konfigurační soubor s názvem *helloapp. conf*:

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5000/
    ServerName www.example.com
    ServerAlias *.example.com
    ErrorLog ${APACHE_LOG_DIR}helloapp-error.log
    CustomLog ${APACHE_LOG_DIR}helloapp-access.log common
</VirtualHost>
```

`VirtualHost`Blok se může objevit několikrát, v jednom nebo více souborech na serveru. V předchozím konfiguračním souboru akceptuje Apache veřejný provoz na portu 80. Doména `www.example.com` se obsluhuje a `*.example.com` alias se přeloží na stejný web. Další informace najdete v tématu [Podpora virtuálních hostitelů založených na názvech](https://httpd.apache.org/docs/current/vhosts/name-based.html) . Žádosti jsou proxy servery v kořenovém adresáři na port 5000 serveru na adrese 127.0.0.1. Pro obousměrnou komunikaci se `ProxyPass` `ProxyPassReverse` vyžadují. Pokud chcete změnit IP adresu/port Kestrel, přečtěte si téma [Kestrel: konfigurace koncového bodu](xref:fundamentals/servers/kestrel#endpoint-configuration).

> [!WARNING]
> Nepovedlo se zadat správnou [direktivu servername](https://httpd.apache.org/docs/current/mod/core.html#servername) v bloku **VirtualHost** k vystavení ohrožení zabezpečení vaší aplikace. Vazba zástupných znaků subdomény (například `*.example.com` ) nepředstavuje toto bezpečnostní riziko, pokud řídíte celou nadřazenou doménu (na rozdíl od `*.com` , která je zranitelná). Další informace najdete v [části rfc7230 část-5,4](https://tools.ietf.org/html/rfc7230#section-5.4) .

Protokolování lze nakonfigurovat na `VirtualHost` `ErrorLog` `CustomLog` direktivy using a. `ErrorLog`je umístění, kde Server protokoluje chyby, a `CustomLog` nastavuje název souboru a formát souboru protokolu. V tomto případě se jedná o případ, kdy se zaprotokolují informace o žádosti. Pro každý požadavek je k dispozici jeden řádek.

Uložte soubor a otestujte konfiguraci. Pokud vše projde, odpověď by měla být `Syntax [OK]` .

```bash
sudo service httpd configtest
```

Restartujte Apache:

```bash
sudo systemctl restart httpd
sudo systemctl enable httpd
```

## <a name="monitor-the-app"></a>Monitorování aplikace

Apache je nyní nastaveno k přeposílání požadavků do `http://localhost:80` aplikace ASP.NET Core spuštěné v Kestrel na adrese `http://127.0.0.1:5000` . Apache ale není nastavené na správu procesu Kestrel. K zahájení a monitorování základní webové aplikace můžete použít *systém* a vytvořit soubor služby. *systém* je systémem init, který poskytuje mnoho výkonných funkcí pro spouštění, zastavování a správu procesů.

### <a name="create-the-service-file"></a>Vytvoření souboru služby

Vytvořte definiční soubor služby:

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

Příklad souboru služby pro aplikaci:

```
[Unit]
Description=Example .NET Web API App running on CentOS 7

[Service]
WorkingDirectory=/var/www/helloapp
ExecStart=/usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=dotnet-example
User=apache
Environment=ASPNETCORE_ENVIRONMENT=Production 

[Install]
WantedBy=multi-user.target
```

V předchozím příkladu je uživatel, který spravuje službu, určen pomocí `User` Možnosti. Uživatel ( `apache` ) musí existovat a musí mít správné vlastnictví souborů aplikace.

Slouží `TimeoutStopSec` ke konfiguraci časového intervalu, po který se má čekat na vypnutí aplikace po přijetí počátečního signálu přerušení. Pokud se aplikace v tomto období neukončí, SIGKILL se vydá pro ukončení aplikace. Zadejte hodnotu jako nejednotkové sekundy (například `150` ), hodnotu časového rozsahu (například `2min 30s` ) nebo `infinity` zakažte časový limit. `TimeoutStopSec`ve výchozím nastavení se jedná o hodnotu `DefaultTimeoutStopSec` v konfiguračním souboru správce (*System-System. conf*, *System. conf. d*, *systemd-User. conf*, *User. conf. d*). Výchozí časový limit pro většinu distribucí je 90 sekund.

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

Některé hodnoty (například připojovací řetězce SQL) musí být uvozené řídicími znaky, aby poskytovatelé konfigurace mohli číst proměnné prostředí. Pomocí následujícího příkazu vygenerujte správně uvozenou hodnotu pro použití v konfiguračním souboru:

```console
systemd-escape "<value-to-escape>"
```

::: moniker range=">= aspnetcore-3.0"

Oddělovače dvojtečky ( `:` ) nejsou podporovány v názvech proměnných prostředí. Místo dvojtečky použijte dvojité podtržítko ( `__` ). [Poskytovatel konfigurace proměnných prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider) převádí dvojitá podtržítka na dvojtečky, když jsou proměnné prostředí čteny do konfigurace. V následujícím příkladu je klíč připojovacího řetězce `ConnectionStrings:DefaultConnection` nastaven do souboru definice služby jako `ConnectionStrings__DefaultConnection` :

::: moniker-end
::: moniker range="< aspnetcore-3.0"

Oddělovače dvojtečky ( `:` ) nejsou podporovány v názvech proměnných prostředí. Místo dvojtečky použijte dvojité podtržítko ( `__` ). [Poskytovatel konfigurace proměnných prostředí](xref:fundamentals/configuration/index#environment-variables) převádí dvojitá podtržítka na dvojtečky, když jsou proměnné prostředí čteny do konfigurace. V následujícím příkladu je klíč připojovacího řetězce `ConnectionStrings:DefaultConnection` nastaven do souboru definice služby jako `ConnectionStrings__DefaultConnection` :

::: moniker-end

```
Environment=ConnectionStrings__DefaultConnection={Connection String}
```

Uložte soubor a povolte službu:

```bash
sudo systemctl enable kestrel-helloapp.service
```

Spusťte službu a ověřte, že je spuštěná:

```bash
sudo systemctl start kestrel-helloapp.service
sudo systemctl status kestrel-helloapp.service

◝ kestrel-helloapp.service - Example .NET Web API App running on CentOS 7
    Loaded: loaded (/etc/systemd/system/kestrel-helloapp.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-helloapp.service
            └─9021 /usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
```

Pomocí systému reverzního proxy serveru nakonfigurovaného a Kestrel spravovaného přes *systém*je webová aplikace plně nakonfigurovaná a dá se k ní dostat z prohlížeče v místním počítači `http://localhost` . Při kontrole hlaviček odpovědi se v hlavičce **serveru** označuje, že ASP.NET Core aplikace obsluhuje Kestrel:

```
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a>Zobrazení protokolů

Vzhledem k tomu, že webová aplikace používající Kestrel je spravovaná pomocí *systému*, události a procesy se zaznamenávají do centralizovaného deníku. Tento deník ale obsahuje položky pro všechny služby a procesy spravované *systémem*. Chcete-li zobrazit `kestrel-helloapp.service` položky specifické pro zobrazení, použijte následující příkaz:

```bash
sudo journalctl -fu kestrel-helloapp.service
```

Pro filtrování času zadejte možnosti času pomocí příkazu. Použijte například `--since today` k filtrování aktuálního dne nebo `--until 1 hour ago` k zobrazení záznamů předchozí hodiny. Další informace najdete na [stránce muž pro journalctl](https://www.unix.com/man-page/centos/1/journalctl/).

```bash
sudo journalctl -fu kestrel-helloapp.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="data-protection"></a>Ochrana dat

[Sada ASP.NET Core Data Protection Stack](xref:security/data-protection/introduction) je používána několika ASP.NET Core [middlewary](xref:fundamentals/middleware/index), včetně middlewaru ověřování (například cookie middlewaru) a ochrany proti padělání žádostí mezi lokalitami (CSRF). I v případě, že rozhraní API ochrany dat nejsou volána uživatelským kódem, je třeba chránit data, aby bylo možné vytvořit trvalé úložiště kryptografických [klíčů](xref:security/data-protection/implementation/key-management). Pokud ochrana dat není nakonfigurovaná, klíče se uchovávají v paměti a při restartování aplikace se zahodí.

Pokud se klíčového prstence při restartu aplikace uloží do paměti:

* U cookie tokenů ověřování na základě jsou neověřené.
* Uživatelé se musí znovu přihlásit na svůj další požadavek.
* Data chráněná pomocí Key ringu už nebude možné dešifrovat. To může zahrnovat [CSRF tokeny](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) a [ASP.NET Core MVC TempData cookie s](xref:fundamentals/app-state#tempdata).

Pokud chcete nakonfigurovat ochranu dat, aby zachovala a zašifroval klíč Ring, přečtěte si:

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>

## <a name="secure-the-app"></a>Zabezpečení aplikace

### <a name="configure-firewall"></a>Konfigurace brány firewall

*Brána firewall* je dynamickým démonem pro správu brány firewall s podporou síťových zón. Filtrování portů a paketů je stále možné spravovat pomocí softwaru iptables. *Brána firewall* by měla být nainstalována ve výchozím nastavení. `yum`dá se použít k instalaci balíčku nebo ověření, jestli je nainstalovaný.

```bash
sudo yum install firewalld -y
```

Slouží `firewalld` k otevření pouze portů potřebných pro aplikaci. V tomto případě se používají porty 80 a 443. Následující příkazy trvale nastaví porty 80 a 443 na otevřené:

```bash
sudo firewall-cmd --add-port=80/tcp --permanent
sudo firewall-cmd --add-port=443/tcp --permanent
```

Znovu načtěte nastavení brány firewall. Ověřte dostupné služby a porty ve výchozí zóně. Možnosti jsou k dispozici na základě kontroly `firewall-cmd -h` .

```bash
sudo firewall-cmd --reload
sudo firewall-cmd --list-all
```

```bash
public (default, active)
interfaces: eth0
sources: 
services: dhcpv6-client
ports: 443/tcp 80/tcp
masquerade: no
forward-ports: 
icmp-blocks: 
rich rules: 
```

### <a name="https-configuration"></a>Konfigurace HTTPS

**Konfigurace místních připojení (HTTPS) aplikace pro zabezpečení**

Příkaz příkazového řádku [dotnet](/dotnet/core/tools/dotnet-run) používá *vlastnosti nebo launchSettings.jsaplikace v* souboru, který nakonfiguruje aplikaci tak, aby naslouchala adresám URL poskytnutým `applicationUrl` vlastností (například `https://localhost:5001;http://localhost:5000` ).

Nakonfigurujte aplikaci tak, aby používala certifikát ve vývoji pro `dotnet run` příkazové nebo vývojové prostředí (F5 nebo CTRL + F5 v Visual Studio Code), a to pomocí jednoho z následujících přístupů:

* [Nahradit výchozí certifikát z konfigurace](xref:fundamentals/servers/kestrel#configuration) (*doporučeno*)
* [KestrelServerOptions.ConfigureHttpsDefaults](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

**Konfigurace připojení klienta reverzního proxy serveru pro zabezpečení (HTTPS)**

Pro konfiguraci Apache pro HTTPS se používá modul *mod_ssl* . Po instalaci modulu *httpd* byl také nainstalován modul *mod_ssl* . Pokud není nainstalovaná, použijte `yum` ji k přidání do konfigurace.

```bash
sudo yum install mod_ssl
```

Pokud chcete vynutilit protokol HTTPS, nainstalujte `mod_rewrite` modul, aby se povolilo přepsání adresy URL:

```bash
sudo yum install mod_rewrite
```

Úpravou souboru *helloapp. conf* povolte přepis adresy URL a zabezpečenou komunikaci na portu 443:

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    RewriteEngine On
    RewriteCond %{HTTPS} !=on
    RewriteRule ^/?(.*) https://%{SERVER_NAME}/$1 [R,L]
</VirtualHost>

<VirtualHost *:443>
    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5000/
    ErrorLog /var/log/httpd/helloapp-error.log
    CustomLog /var/log/httpd/helloapp-access.log common
    SSLEngine on
    SSLProtocol all -SSLv2
    SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:!RC4+RSA:+HIGH:+MEDIUM:!LOW:!RC4
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
</VirtualHost>
```

> [!NOTE]
> Tento příklad používá místně generovaný certifikát. **SSLCertificateFile** by měl být primárním souborem certifikátu pro název domény. **SSLCertificateKeyFile** by měl být soubor klíče vygenerovaný při vytvoření CSR. **SSLCertificateChainFile** by měl být soubor zprostředkujícího certifikátu (pokud existuje), který poskytla certifikační autorita.

Uložte soubor a otestujte konfiguraci:

```bash
sudo service httpd configtest
```

Restartujte Apache:

```bash
sudo systemctl restart httpd
```

## <a name="additional-apache-suggestions"></a>Další návrhy Apache

### <a name="restart-apps-with-shared-framework-updates"></a>Restartovat aplikace se sdílenými aktualizacemi rozhraní

Po upgradu sdílené architektury na serveru restartujte aplikace ASP.NET Core hostované serverem.

### <a name="additional-headers"></a>Další záhlaví

Aby bylo možné zabezpečit před škodlivými útoky, je třeba upravit nebo přidat několik hlaviček. Ujistěte se, že `mod_headers` je modul nainstalovaný:

```bash
sudo yum install mod_headers
```

#### <a name="secure-apache-from-clickjacking-attacks"></a>Zabezpečení Apache před útoky clickjacking

[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), označovaný také jako *útok s opravou uživatelského rozhraní*, je škodlivý útok, při kterém návštěvník webu získá odkaz nebo tlačítko na jiné stránce, než se právě navštíví. Slouží `X-FRAME-OPTIONS` k zabezpečení lokality.

Zmírnění útoků Clickjacking:

1. Upravte soubor *httpd. conf* :

   ```bash
   sudo nano /etc/httpd/conf/httpd.conf
   ```

   Přidejte řádek `Header append X-FRAME-OPTIONS "SAMEORIGIN"`.
1. Soubor uložte.
1. Restartujte Apache.

#### <a name="mime-type-sniffing"></a>Sledování typu MIME

`X-Content-Type-Options`Záhlaví brání aplikaci Internet Explorer ve *sledování MIME* (určení souboru `Content-Type` z obsahu souboru). Pokud server nastaví `Content-Type` hlavičku na `text/html` `nosniff` sadu možností, Internet Explorer vykreslí obsah `text/html` bez ohledu na obsah souboru.

Upravte soubor *httpd. conf* :

```bash
sudo nano /etc/httpd/conf/httpd.conf
```

Přidejte řádek `Header set X-Content-Type-Options "nosniff"`. Soubor uložte. Restartujte Apache.

### <a name="load-balancing"></a>Vyrovnávání zatížení

Tento příklad ukazuje, jak nastavit a nakonfigurovat Apache na CentOS 7 a Kestrel na stejném počítači instance. Aby nedošlo k jednomu bodu selhání; použití *mod_proxy_balancer* a úpravou **VirtualHost** by umožňovalo spravovat více instancí webových aplikací za proxy server Apache.

```bash
sudo yum install mod_proxy_balancer
```

V konfiguračním souboru uvedeném níže `helloapp` je nastavená další instance, která se spustí na portu 5001. Oddíl *proxy* je nastaven s konfigurací vyrovnání se dvěma členy pro vyrovnávání zatížení *byrequests*.

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    RewriteEngine On
    RewriteCond %{HTTPS} !=on
    RewriteRule ^/?(.*) https://%{SERVER_NAME}/$1 [R,L]
</VirtualHost>

<VirtualHost *:443>
    ProxyPass / balancer://mycluster/ 

    ProxyPassReverse / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5001/

    <Proxy balancer://mycluster>
        BalancerMember http://127.0.0.1:5000
        BalancerMember http://127.0.0.1:5001 
        ProxySet lbmethod=byrequests
    </Proxy>

    <Location />
        SetHandler balancer
    </Location>
    ErrorLog /var/log/httpd/helloapp-error.log
    CustomLog /var/log/httpd/helloapp-access.log common
    SSLEngine on
    SSLProtocol all -SSLv2
    SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:!RC4+RSA:+HIGH:+MEDIUM:!LOW:!RC4
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
</VirtualHost>
```

### <a name="rate-limits"></a>Omezení přenosové rychlosti

Pomocí *mod_ratelimit*, který je součástí modulu *httpd* , může být omezena šířka pásma klientů:

```bash
sudo nano /etc/httpd/conf.d/ratelimit.conf
```

Ukázkový soubor omezuje šířku pásma na 600 KB/s v kořenovém umístění:

```
<IfModule mod_ratelimit.c>
    <Location />
        SetOutputFilter RATE_LIMIT
        SetEnv rate-limit 600
    </Location>
</IfModule>
```

### <a name="long-request-header-fields"></a>Pole hlavičky dlouhé žádosti

Výchozí nastavení proxy serveru obvykle omezují pole hlaviček požadavku na 8 190 bajtů. Aplikace může vyžadovat pole delší než výchozí (například aplikace, které používají [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)). Pokud jsou požadována delší pole, vyžaduje direktiva [LimitRequestFieldSize](https://httpd.apache.org/docs/2.4/mod/core.html#LimitRequestFieldSize) proxy server úpravu. Hodnota, která se má použít, závisí na scénáři. Další informace najdete v dokumentaci k vašemu serveru.

> [!WARNING]
> Nerozšiřovat výchozí hodnotu, `LimitRequestFieldSize` Pokud je to nutné. Zvýšení hodnoty zvyšuje riziko přetečení vyrovnávací paměti (přetečení) a útok DoS (Denial of Service) uživateli se zlými úmysly.

## <a name="additional-resources"></a>Další zdroje

* [Předpoklady pro .NET Core v systému Linux](/dotnet/core/linux-prerequisites)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>

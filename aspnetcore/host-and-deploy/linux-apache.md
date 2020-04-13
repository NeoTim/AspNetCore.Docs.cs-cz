---
title: Host ASP.NET Core na Linuxu s Apache
author: rick-anderson
description: Naučte se nastavit Apache jako reverzní proxy server na CentOS přesměrovat HTTP provoz na ASP.NET webové aplikace Core běží na Kestrel.
monikerRange: '>= aspnetcore-2.1'
ms.author: shboyer
ms.custom: mvc
ms.date: 04/10/2020
uid: host-and-deploy/linux-apache
ms.openlocfilehash: 1256f6d21f94ef6c4baad7aae4bd0e751af5c675
ms.sourcegitcommit: 6f1b516e0c899a49afe9a29044a2383ce2ada3c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81224034"
---
# <a name="host-aspnet-core-on-linux-with-apache"></a>Host ASP.NET Core na Linuxu s Apache

Podle [Shayne Boyer](https://github.com/spboyer)

Pomocí této příručky se [dozvíte,](https://httpd.apache.org/) jak nastavit Apache jako reverzní proxy server na [CentOS 7](https://www.centos.org/) pro přesměrování http provozu na ASP.NET webovou aplikaci Core spuštěnou na serveru [Kestrel.](xref:fundamentals/servers/kestrel) [Rozšíření mod_proxy](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html) a související moduly vytvářejí reverzní proxy serveru.

## <a name="prerequisites"></a>Požadavky

* Server se systémem CentOS 7 se standardním uživatelským účtem s oprávněním sudo.
* Nainstalujte na server runtime jádra .NET.
   1. Navštivte [stránku Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core).
   1. Vyberte nejnovější verzi jádra .NET bez náhledu.
   1. Stáhněte si nejnovější runtime bez náhledu v tabulce v části **Spustit aplikace - Runtime**.
   1. Vyberte odkaz **pokyny správce balíčků** pro Linux a postupujte podle pokynů CentOS.
* Existující aplikace ASP.NET Core.

Kdykoli v budoucnu po upgradu sdíleného rozhraní restartujte aplikace ASP.NET Core hostované serverem.

## <a name="publish-and-copy-over-the-app"></a>Publikování a kopírování přes aplikaci

Nakonfigurujte aplikaci pro [nasazení závislé na rámci](/dotnet/core/deploying/#framework-dependent-deployments-fdd).

Pokud je aplikace spuštěná místně a není nakonfigurovaná pro zabezpečená připojení (HTTPS), přijměte některý z následujících přístupů:

* Nakonfigurujte aplikaci tak, aby zpracovávala zabezpečená místní připojení. Další informace naleznete v části [konfigurace protokolu HTTPS.](#https-configuration)
* Odebrat `https://localhost:5001` (pokud je `applicationUrl` k dispozici) z vlastnosti v *properties/launchSettings.json* souboru.

Spusťte [publikování dotnet](/dotnet/core/tools/dotnet-publish) z vývojového prostředí a zabalte aplikaci do adresáře (například *&lt;bin/Release/ target_framework_moniker&gt;/publish),* který lze spustit na serveru:

```dotnetcli
dotnet publish --configuration Release
```

Aplikace může být také publikována jako [samostatné nasazení,](/dotnet/core/deploying/#self-contained-deployments-scd) pokud nechcete udržovat runtime .NET Core na serveru.

Zkopírujte aplikaci ASP.NET Core na server pomocí nástroje, který se integruje do pracovního postupu organizace (například SCP, SFTP). Je běžné vyhledat webové aplikace v adresáři *var* (například *var/www/helloapp*).

> [!NOTE]
> Podle scénáře nasazení v produkčním prostředí provádí pracovní postup průběžné integrace publikování aplikace a kopírování datových zdrojů na server.

## <a name="configure-a-proxy-server"></a>Konfigurace serveru proxy

Reverzní proxy je běžné nastavení pro zobrazování dynamických webových aplikací. Reverzní proxy server ukončí požadavek HTTP a předá jej ASP.NET aplikaci.

Proxy server je server, který předává požadavky klientů na jiný server namísto plnění požadavků samotných. Reverzní proxy předá do pevného cíle, obvykle jménem libovolných klientů. V této příručce je Apache nakonfigurován jako reverzní proxy server běžící na stejném serveru, který Kestrel obsluhuje aplikaci ASP.NET Core.

Vzhledem k tomu, že požadavky jsou předávány reverzním proxy serverem, použijte [middleware s předanými hlavičkami](xref:host-and-deploy/proxy-load-balancer) z balíčku [Microsoft.AspNetCore.HttpOverrides.](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) Middleware aktualizuje `Request.Scheme`, pomocí `X-Forwarded-Proto` záhlaví, takže přesměrování IDENTIFIKÁTORŮ URI a dalších zásad zabezpečení pracovat správně.

Každá součást, která závisí na schématu, jako je ověřování, generování propojení, přesměrování a geografické umístění, musí být umístěna po vyvolání middlewaru předávaných záhlaví. Obecně platí, že middleware s předaným záhlavím by měl být spuštěn před jiným middlewarem s výjimkou diagnostiky a zpracování chyb middlewaru. Toto pořadí zajišťuje, že middleware spoléhající na předávané informace záhlaví může spotřebovat hodnoty záhlaví pro zpracování.

Vyvolat <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> metodu v `Startup.Configure` horní části před voláním jiné middleware. Nakonfigurujte middleware tak, aby předával záhlaví `X-Forwarded-For` a: `X-Forwarded-Proto`

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

Pokud <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> middlewaru nezadali, výchozí hlavičky `None`pro předávání jsou .

Proxy servery spuštěné na adresách zpětné smyčky (127.0.0.0/8, [::1]), včetně standardní adresy localhost (127.0.0.1), jsou ve výchozím nastavení důvěryhodné. Pokud jiné důvěryhodné servery proxy nebo sítě v rámci organizace zpracovávají požadavky mezi <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> Internetem a webovým serverem, přidejte je do seznamu nebo <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> s <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>aplikací . Následující příklad přidá důvěryhodný proxy server na adrese IP 10.0.0.100 `KnownProxies` do `Startup.ConfigureServices`middlewaru Předávaných záhlaví v :

```csharp
// using System.Net;

services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

Další informace naleznete v tématu <xref:host-and-deploy/proxy-load-balancer>.

### <a name="install-apache"></a>Instalace Apache

Aktualizujte balíčky CentOS na jejich nejnovější stabilní verze:

```bash
sudo yum update -y
```

Nainstalujte webový server Apache na CentOS pomocí jediného `yum` příkazu:

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
> V tomto příkladu výstup odráží httpd.86_64, protože centos 7 verze je 64 bit. Chcete-li ověřit, kde `whereis httpd` je Apache nainstalován, spusťte z příkazového řádku.

### <a name="configure-apache"></a>Konfigurace Apache

Konfigurační soubory pro `/etc/httpd/conf.d/` Apache jsou umístěny v adresáři. Každý soubor s příponou *.conf* je zpracován v abecedním pořadí kromě konfiguračních souborů modulu v `/etc/httpd/conf.modules.d/`aplikaci , který obsahuje všechny konfigurační soubory potřebné k načtení modulů.

Vytvořte pro aplikaci konfigurační soubor s názvem *helloapp.conf:*

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

Blok `VirtualHost` se může zobrazit vícekrát v jednom nebo více souborech na serveru. V předchozím konfiguračním souboru Apache přijímá veřejný provoz na portu 80. Doména `www.example.com` je obsluhována `*.example.com` a alias se překládá na stejný web. Další informace najdete [v tématu Podpora virtuálního hostitele založená na názvu.](https://httpd.apache.org/docs/current/vhosts/name-based.html) Požadavky jsou proxied na kořenový port 5000 serveru na 127.0.0.1. Pro obousměrnou komunikaci `ProxyPass` a `ProxyPassReverse` jsou vyžadovány. Chcete-li změnit protokol Kestrel IP/port, přečtěte si část [Kestrel: Konfigurace koncového bodu](xref:fundamentals/servers/kestrel#endpoint-configuration).

> [!WARNING]
> Pokud se nepodaří zadat správnou [direktivu Název_serveru](https://httpd.apache.org/docs/current/mod/core.html#servername) v bloku **VirtualHost,** aplikace bude vystavena slabým místům zabezpečení. Vazba se zástupnými znaky subdomény (například) nepředstavuje toto bezpečnostní riziko, `*.example.com` `*.com`pokud řídíte celou nadřazenou doménu (na rozdíl od , která je zranitelná). Viz [rfc7230 sekce-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) pro více informací.

Protokolování lze `VirtualHost` nakonfigurovat podle použití `ErrorLog` a `CustomLog` direktivy. `ErrorLog`je umístění, kde server zaznamenává `CustomLog` chyby a nastavuje název souboru a formát souboru protokolu. V tomto případě je zaznamenána informace požadavku. Pro každou žádost je jeden řádek.

Uložte soubor a otestujte konfiguraci. Pokud vše projde, odpověď `Syntax [OK]`by měla být .

```bash
sudo service httpd configtest
```

Restartujte Apache:

```bash
sudo systemctl restart httpd
sudo systemctl enable httpd
```

## <a name="monitor-the-app"></a>Sledování aplikace

Apache je nyní nastavena pro `http://localhost:80` předávání požadavků na ASP.NET aplikace `http://127.0.0.1:5000`Core spuštěná na Kestrel at . Apache však není nastavena pro správu procesu Kestrel. Pomocí *systemd* utvařte soubor služby pro spuštění a monitorování základní webové aplikace. *systemd* je init systém, který poskytuje mnoho výkonných funkcí pro spouštění, zastavování a správu procesů.

### <a name="create-the-service-file"></a>Vytvoření souboru služby

Vytvořte soubor definice služby:

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

Ukázkový soubor služby pro aplikaci:

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

V předchozím příkladu je uživatel, který spravuje službu, určen `User` možností. Uživatel (`apache`) musí existovat a mít správné vlastnictví souborů aplikace.

Slouží `TimeoutStopSec` ke konfiguraci doby čekání na vypnutí aplikace po přijetí signálu počátečního přerušení. Pokud se aplikace v tomto období nevypne, je vydána aplikace SIGKILL, aby aplikaci ukončila. Zadejte hodnotu jako sekundy `150`bez jednotky (například ), `2min 30s`hodnotu `infinity` časového rozpětí (například ) nebo zakázat časový rozsah. `TimeoutStopSec`výchozí hodnota v `DefaultTimeoutStopSec` konfiguračním souboru správce (*systemd-system.conf*, *system.conf.d*, *systemd-user.conf*, *user.conf.d*). Výchozí časový limit pro většinu distribucí je 90 sekund.

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

Některé hodnoty (například připojovací řetězce SQL) musí být uvozeny, aby zprostředkovatelé konfigurace mohli číst proměnné prostředí. Pomocí následujícího příkazu vygenerujte správně uvozenou hodnotu pro použití v konfiguračním souboru:

```console
systemd-escape "<value-to-escape>"
```

Oddělovače dvojtečky (`:`) nejsou v názvech proměnných prostředí podporovány. Místo dvojtečky`__`použijte dvojité podtržítko ( ). [Zprostředkovatel konfigurace proměnných prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider) převádí dvojité podtržítka na dvojtečky při čtení proměnných prostředí do konfigurace. V následujícím příkladu je `ConnectionStrings:DefaultConnection` klíč připojovacího `ConnectionStrings__DefaultConnection`řetězce nastaven do souboru definice služby jako :

```
Environment=ConnectionStrings__DefaultConnection={Connection String}
```

Uložte soubor a povolte službu:

```bash
sudo systemctl enable kestrel-helloapp.service
```

Spusťte službu a ověřte, zda je spuštěná:

```bash
sudo systemctl start kestrel-helloapp.service
sudo systemctl status kestrel-helloapp.service

● kestrel-helloapp.service - Example .NET Web API App running on CentOS 7
    Loaded: loaded (/etc/systemd/system/kestrel-helloapp.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-helloapp.service
            └─9021 /usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
```

S reverzní proxy nakonfigurován a Kestrel spravuje prostřednictvím *systemd*, webová aplikace je plně `http://localhost`nakonfigurován a lze přistupovat z prohlížeče na místním počítači na . Při kontrole záhlaví odpovědí záhlaví **serveru** označuje, že aplikace ASP.NET Core je obsluhována kestrelem:

```
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a>Zobrazení protokolů

Vzhledem k tomu, že webová aplikace pomocí Kestrel je spravována pomocí *systemd*, události a procesy jsou protokolovány do centralizovaného deníku. Tento deník však obsahuje položky pro všechny služby a procesy spravované *systemd*. Chcete-li `kestrel-helloapp.service`zobrazit specifické položky, použijte následující příkaz:

```bash
sudo journalctl -fu kestrel-helloapp.service
```

Pro filtrování času zadejte volby času pomocí příkazu. Slouží `--since today` například k filtrování pro `--until 1 hour ago` aktuální den nebo k zobrazení položek předchozí hodiny. Další informace naleznete na [stránce man pro deník .](https://www.unix.com/man-page/centos/1/journalctl/)

```bash
sudo journalctl -fu kestrel-helloapp.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="data-protection"></a>Ochrana dat

Zásobník [ASP.NET Core Data Protection](xref:security/data-protection/introduction) používá několik [middlewares](xref:fundamentals/middleware/index)ASP.NET Core , včetně autentizačního middlewaru (například middleware souborů cookie) a ochrany padělání požadavků na více webů (CSRF). I v případě, že rozhraní API ochrany dat nejsou volána uživatelským kódem, ochrana dat by měla být nakonfigurována tak, aby vytvořila trvalé úložiště kryptografických [klíčů](xref:security/data-protection/implementation/key-management). Pokud ochrana dat není nakonfigurována, klíče jsou uloženy v paměti a po restartování aplikace zahozeny.

Pokud je kroužek klíče uložen v paměti při restartování aplikace:

* Všechny ověřovací tokeny založené na souborech cookie jsou zrušeny.
* Uživatelé se musí znovu přihlásit při dalším požadavku.
* Všechna data chráněná kroužkem klíče již nelze dešifrovat. To může zahrnovat [tokeny CSRF](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) a [ASP.NET základní soubory cookie MVC TempData](xref:fundamentals/app-state#tempdata).

Chcete-li nakonfigurovat ochranu dat tak, aby byla zachována a zašifrována kroužek klíče, přečtěte si následující:

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>

## <a name="secure-the-app"></a>Zabezpečení aplikace

### <a name="configure-firewall"></a>Konfigurace brány firewall

*Firewalld* je dynamický daemon pro správu brány firewall s podporou síťových zón. Porty a filtrování paketů lze stále spravovat pomocí iptables. *Firewalld* by měl být nainstalován ve výchozím nastavení. `yum`lze použít k instalaci balíčku nebo ověření, že je nainstalován.

```bash
sudo yum install firewalld -y
```

Slouží `firewalld` k otevření pouze portů potřebných pro aplikaci. V tomto případě se používá port 80 a 443. Následující příkazy trvale nastavit porty 80 a 443 otevřít:

```bash
sudo firewall-cmd --add-port=80/tcp --permanent
sudo firewall-cmd --add-port=443/tcp --permanent
```

Znovu načtěte nastavení brány firewall. Zkontrolujte dostupné služby a porty ve výchozí zóně. Možnosti jsou k `firewall-cmd -h`dispozici kontrolou .

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

### <a name="https-configuration"></a>Konfigurace PROTOKOLU HTTPS

**Konfigurace aplikace pro zabezpečená místní připojení (HTTPS)**

Příkaz [dotnet run](/dotnet/core/tools/dotnet-run) používá soubor *Vlastnosti/launchSettings.json* aplikace, který konfiguruje aplikaci tak, aby naslouchala adresám URL poskytovaným `applicationUrl` vlastností (například ). `https://localhost:5001;http://localhost:5000`

Nakonfigurujte aplikaci tak, `dotnet run` aby používala certifikát ve vývoji pro prostředí příkazů nebo vývoje (F5 nebo Ctrl+F5 v kódu Visual Studia) pomocí jednoho z následujících přístupů:

* [Nahrazení výchozího certifikátu z konfigurace](xref:fundamentals/servers/kestrel#configuration) *(doporučeno)*
* [KestrelServerOptions.ConfigureHttpsDefaults](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

**Konfigurace reverzního proxy serveru pro zabezpečená připojení klientů (HTTPS)**

Pro konfiguraci Apache pro HTTPS se používá *modul mod_ssl.* Když byl nainstalován *modul httpd,* byl nainstalován také *modul mod_ssl.* Pokud nebyla nainstalována, `yum` použijte k jeho přidání do konfigurace.

```bash
sudo yum install mod_ssl
```

Chcete-li vynutit protokol HTTPS, nainstalujte modul a `mod_rewrite` povolte přepisování adres URL:

```bash
sudo yum install mod_rewrite
```

Upravte soubor *helloapp.conf* a povolte přepis ování adres URL a zabezpečenou komunikaci na portu 443:

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
> Tento příklad používá místně generovaný certifikát. **SSLCertificateFile** by měl být primárnísoubor certifikátu pro název domény. **SSLCertificateKeyFile** by měl být soubor klíče generovaný při vytvoření zástupce. **SSLCertificateChainFile** by měl být zprostředkující soubor certifikátu (pokud existuje), který byl dodán certifikační autoritou.

Uložte soubor a otestujte konfiguraci:

```bash
sudo service httpd configtest
```

Restartujte Apache:

```bash
sudo systemctl restart httpd
```

## <a name="additional-apache-suggestions"></a>Další návrhy Apache

### <a name="restart-apps-with-shared-framework-updates"></a>Restartování aplikací pomocí sdílených aktualizací architektury

Po upgradu sdíleného rozhraní na serveru restartujte aplikace ASP.NET Core hostované serverem.

### <a name="additional-headers"></a>Další záhlaví

Chcete-li zabezpečit proti škodlivým útokům, existuje několik záhlaví, které by měly být změněny nebo přidány. Ujistěte `mod_headers` se, že je modul nainstalován:

```bash
sudo yum install mod_headers
```

#### <a name="secure-apache-from-clickjacking-attacks"></a>Zabezpečte Apache z clickjacking útoky

[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), také známý jako *útok na nápravu ui*, je škodlivý útok, kdy je návštěvník webových stránek podveden, aby klikl na odkaz nebo tlačítko na jiné stránce, než je v současné době na návštěvě. Slouží `X-FRAME-OPTIONS` k zabezpečení webu.

Chcete-li zmírnit clickjacking útoky:

1. Upravte soubor *httpd.conf:*

   ```bash
   sudo nano /etc/httpd/conf/httpd.conf
   ```

   Přidejte řádek `Header append X-FRAME-OPTIONS "SAMEORIGIN"`.
1. Uložte soubor.
1. Restartujte Apache.

#### <a name="mime-type-sniffing"></a>Čichání typu MIME

Záhlaví `X-Content-Type-Options` zabraňuje aplikaci Internet Explorer v *tom, aby očichávání MIME* (určení souboru `Content-Type` z obsahu souboru). Pokud server nastaví `Content-Type` `text/html` záhlaví `nosniff` s nastavenou možností, aplikace `text/html` Internet Explorer vykreslí obsah bez ohledu na obsah souboru.

Upravte soubor *httpd.conf:*

```bash
sudo nano /etc/httpd/conf/httpd.conf
```

Přidejte řádek `Header set X-Content-Type-Options "nosniff"`. Uložte soubor. Restartujte Apache.

### <a name="load-balancing"></a>Vyrovnávání zatížení

Tento příklad ukazuje, jak nastavit a nakonfigurovat Apache na CentOS 7 a Kestrel na stejném počítači instance. Aby nedošlo k jedinému bodu selhání; použití *mod_proxy_balancer* a úprava **VirtualHost** by umožnilo správu více instancí webových aplikací za proxy serverapache.

```bash
sudo yum install mod_proxy_balancer
```

V konfiguračním souboru uvedeném níže `helloapp` je další instance nastavena tak, aby byla spuštěna na portu 5001. Oddíl *Proxy* je nastaven s konfigurací nástroje pro vyrovnávání zatížení se dvěma členy pro *vyrovnávání*zatížení požadavky .

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

### <a name="rate-limits"></a>Omezení sazeb

Pomocí *mod_ratelimit*, který je součástí *modulu httpd,* může být šířka pásma klientů omezena:

```bash
sudo nano /etc/httpd/conf.d/ratelimit.conf
```

Ukázkový soubor omezuje šířku pásma na 600 KB/s pod kořenovým umístěním:

```
<IfModule mod_ratelimit.c>
    <Location />
        SetOutputFilter RATE_LIMIT
        SetEnv rate-limit 600
    </Location>
</IfModule>
```

### <a name="long-request-header-fields"></a>Pole záhlaví dlouhého požadavku

Výchozí nastavení serveru proxy obvykle omezují pole hlaviček požadavku na 8 190 bajtů. Aplikace může vyžadovat pole delší než výchozí (například aplikace, které používají [Azure Active Directory).](https://azure.microsoft.com/services/active-directory/) Pokud jsou požadována delší pole, vyžaduje direktivu [LimitRequestFieldSize](https://httpd.apache.org/docs/2.4/mod/core.html#LimitRequestFieldSize) proxy serveru úpravu. Hodnota, která má být použít, závisí na scénáři. Další informace naleznete v dokumentaci k serveru.

> [!WARNING]
> Nezvyšujte výchozí hodnotu, `LimitRequestFieldSize` pokud to není nutné. Zvýšení hodnoty zvyšuje riziko přetečení vyrovnávací paměti (přetečení) a odmítnutí služby (DoS) útoky uživatelů se zlými úmysly.

## <a name="additional-resources"></a>Další zdroje

* [Předpoklady pro .NET Core na Linuxu](/dotnet/core/linux-prerequisites)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>

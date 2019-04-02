---
title: Hostitele ASP.NET Core v Linuxu pomocí Apache
author: spboyer
description: Zjistěte, jak nastavit službu Apache jako reverzní proxy server na CentOS pro přesměrování přenosu dat protokolu HTTP k webové aplikaci ASP.NET Core spuštěnou v prostředí Kestrel.
monikerRange: '>= aspnetcore-2.1'
ms.author: spboyer
ms.custom: mvc
ms.date: 03/28/2019
uid: host-and-deploy/linux-apache
ms.openlocfilehash: fbdfe9c19f3cbf6d12678187bb07e58e82395d2f
ms.sourcegitcommit: 3e9e1f6d572947e15347e818f769e27dea56b648
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2019
ms.locfileid: "58750656"
---
# <a name="host-aspnet-core-on-linux-with-apache"></a>Hostitele ASP.NET Core v Linuxu pomocí Apache

Podle [Shayne Boyer](https://github.com/spboyer)

Pomocí této příručky, zjistěte, jak nastavit [Apache](https://httpd.apache.org/) jako reverzní proxy server na [CentOS 7](https://www.centos.org/) pro přesměrování přenosu dat protokolu HTTP pro webovou aplikaci ASP.NET Core využívající [Kestrel](xref:fundamentals/servers/kestrel) serveru. [Mod_proxy rozšíření](http://httpd.apache.org/docs/2.4/mod/mod_proxy.html) a související moduly vytvořit reverzního proxy serveru.

## <a name="prerequisites"></a>Požadavky

* Server se systémem CentOS 7 pomocí standardního uživatelského účtu s oprávněními sudo.
* Nainstalujte modul runtime .NET Core na serveru.
   1. Přejděte [.NET Core všechny soubory ke stažení stránky](https://www.microsoft.com/net/download/all).
   1. Vyberte nejnovější modul runtime – ve verzi preview ze seznamu **Runtime**.
   1. Vyberte a postupujte podle pokynů pro CentOS nebo Oracle.
* Stávající aplikace ASP.NET Core.

## <a name="publish-and-copy-over-the-app"></a>Publikování a zkopírujte myší na aplikaci

Konfigurace aplikace pro [nasazení závisí na architektuře](/dotnet/core/deploying/#framework-dependent-deployments-fdd).

Pokud aplikaci spouštíte místně a není nakonfigurován tak, aby zabezpečené připojení (HTTPS), použijte jednu z následujících postupů:

* Konfigurace aplikace pro zpracování zabezpečené místní připojení. Další informace najdete v tématu [konfigurace protokolu HTTPS](#https-configuration) oddílu.
* Odebrat `https://localhost:5001` (pokud existuje) ze `applicationUrl` vlastnost *Properties/launchSettings.json* souboru.

Spustit [dotnet publikovat](/dotnet/core/tools/dotnet-publish) z vývojového prostředí pro balíček aplikace do adresáře (například *bin/Release/&lt;target_framework_moniker&gt;/ publish*), který můžete Spusťte na serveru:

```console
dotnet publish --configuration Release
```

Aplikace můžete také publikovat jako [samostatná nasazení](/dotnet/core/deploying/#self-contained-deployments-scd) Pokud nechcete zachovat modulu runtime .NET Core na serveru.

Aplikace ASP.NET Core zkopírujte na server pomocí nástroje, které se integruje do pracovního postupu organizace (třeba spojovací bod služby, SFTP). Je běžné vyhledejte webové aplikace v rámci *var* adresář (třeba *www/var/helloapp*).

> [!NOTE]
> V případě produkčního nasazení pracovního postupu průběžné integrace funguje publikování aplikace a kopírování prostředky na server.

## <a name="configure-a-proxy-server"></a>Konfigurace proxy serveru

Reverzní proxy server je společné nastavení pro poskytování dynamické webové aplikace. Reverzní proxy server ukončí požadavek HTTP a předá ji do aplikace ASP.NET.

Proxy server je znak, který se předává požadavky klientů na jiný server místo samotného plnění požadavků. Reverzní proxy server předává do pevné umístění, obvykle jménem libovolného klientů. V této příručce Apache nakonfigurovaný jako reverzní proxy server běží na stejném serveru, aby Kestrel obsluhuje aplikace ASP.NET Core.

Vzhledem k tomu, že žádosti jsou předávány podle reverzní proxy server, použít [předané Middleware záhlaví](xref:host-and-deploy/proxy-load-balancer) z [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) balíčku. Middleware aktualizace `Request.Scheme`, použije `X-Forwarded-Proto` záhlaví tak, že identifikátory URI pro přesměrování a další zásady zabezpečení pracovat správně.

Jakékoli součásti, která závisí na schéma, jako je například ověřování, generování odkazů, přesměrování a zeměpisná poloha, musí být umístěn po vyvolání Middleware předané záhlaví. Jako obecné pravidlo by měla předávat Middleware záhlaví spustit před dalším middlewarem s výjimkou diagnostiky a middleware pro zpracování chyb. Toto uspořádání zajistí, že middleware spoléhání se na informace předávané záhlaví může spotřebovat hodnoty hlavičky pro zpracování.

Vyvolat <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> metoda `Startup.Configure` před voláním <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> nebo podobné režimu middleware ověřování. Nakonfigurujte middleware předávat `X-Forwarded-For` a `X-Forwarded-Proto` hlavičky:

```csharp
app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

Pokud ne <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> jsou určené pro middleware, jsou výchozí hlavičky pro předávání `None`.

Proxy servery, které běží na adresu zpětné smyčky (127.0.0.0/8, [:: 1]), včetně standardní localhost adresu (127.0.0.1), ve výchozím nastavení jsou důvěryhodné. Pokud jiné důvěryhodné proxy nebo sítěmi v rámci popisovač požadavky organizace mezi Internetem a webový server, je přidat do seznamu <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> nebo <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> s <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>. Následující příklad přidá důvěryhodným proxy serveru na IP adrese 10.0.0.100 s Middlewarem předané záhlaví `KnownProxies` v `Startup.ConfigureServices`:

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

Další informace naleznete v tématu <xref:host-and-deploy/proxy-load-balancer>.

### <a name="install-apache"></a>Instalace Apache

CentOS balíčky aktualizací pro jejich nejnovější stabilní verze:

```bash
sudo yum update -y
```

Instalace webového serveru Apache na CentOS pomocí jediného `yum` příkaz:

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
> V tomto příkladu výstupu odráží httpd.86_64 od verze CentOS 7 je 64bitový. Chcete-li zkontrolovat, kde je nainstalován Apache, `whereis httpd` z příkazového řádku.

### <a name="configure-apache"></a>Nakonfigurovat i Apache

Konfigurační soubory pro Apache jsou umístěné v rámci `/etc/httpd/conf.d/` adresáře. Žádný soubor s *.conf* rozšíření, jsou zpracovávána v abecedním pořadí kromě souborů konfigurace modulu v `/etc/httpd/conf.modules.d/`, která obsahuje veškeré konfigurace soubory nezbytné k načtení modulů.

Vytvoření konfiguračního souboru s názvem *helloapp.conf*, pro aplikace:

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

`VirtualHost` Bloku se mohou objevit více než jednou, v jedné nebo více souborů na serveru. V předchozím konfigurační soubor Apache přijímá veřejné provozu na portu 80. Doména `www.example.com` se obsluhuje a `*.example.com` alias se překládá na stejné webové stránce. Zobrazit [podporu založené na název virtuálního hostitele](https://httpd.apache.org/docs/current/vhosts/name-based.html) Další informace. Požadavky jsou směrovány přes proxy server v kořenovém adresáři na portu 5000 serveru na 127.0.0.1. Pro obousměrnou komunikaci `ProxyPass` a `ProxyPassReverse` jsou povinné. Chcete-li změnit Kestrel jeho IP adresa/port, [Kestrel: Konfigurace koncového bodu](xref:fundamentals/servers/kestrel#endpoint-configuration).

> [!WARNING]
> Nepodařilo se určit správnou [ServerName směrnice](https://httpd.apache.org/docs/current/mod/core.html#servername) v **VirtualHost** bloku zpřístupňuje aplikaci tak, aby slabá místa zabezpečení. Vazby zástupný znak subdoménu (například `*.example.com`) nemá představovat toto bezpečnostní riziko, pokud řídíte celý nadřazené domény (nikoli `*.com`, což je ohrožené). Zobrazit [rfc7230 části-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) Další informace.

Protokolování lze nastavit za `VirtualHost` pomocí `ErrorLog` a `CustomLog` direktivy. `ErrorLog` je umístění, kde server protokoluje chyby, a `CustomLog` nastaví název souboru a formát souboru protokolu. V takovém případě je kde zaznamenané informace o žádostech. Existuje jeden řádek pro každý požadavek.

Uložte soubor a testovací konfigurace. Pokud vše projde, odpověď by měla být `Syntax [OK]`.

```bash
sudo service httpd configtest
```

Restartujte Apache:

```bash
sudo systemctl restart httpd
sudo systemctl enable httpd
```

## <a name="monitor-the-app"></a>Sledování aplikace

Apache je nyní instalačního programu předat požadavky na `http://localhost:80` pro aplikaci ASP.NET Core spuštěnou v Kestrel na `http://127.0.0.1:5000`. Apache není však nastavené ke správě procesu Kestrel. Použití *systemd* a vytvořit soubor služby a začít monitorovat základní webovou aplikaci. *systemd* je init systém, který poskytuje řadu výkonných funkcí pro spouštění, zastavování a Správa procesů.

### <a name="create-the-service-file"></a>Vytvoření souboru služby

Vytvoření definičního souboru služby:

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

Pokud uživatel *apache* nepoužívá konfigurace, musíte uživatele nejprve vytvořit a zadané správné vlastnictví souborů.

Použití `TimeoutStopSec` nakonfigurovat doba čekání na aplikaci pro vypnutí po přijetí počáteční přerušení signálu. Pokud aplikace není v tomto období vypnout, objeví se SIGKILL ukončit aplikaci. Zadejte hodnotu unitless sekund (například `150`), časový interval hodnotu (například `2min 30s`), nebo `infinity` zakázat časový limit. `TimeoutStopSec` Výchozí hodnota je hodnota `DefaultTimeoutStopSec` v konfiguračním souboru správce (*systemd system.conf*, *system.conf.d*, *systemd user.conf*, * User.conf.d*). Výchozí hodnota časového limitu pro většinu distribuce je 90 sekund.

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

Některé hodnoty (například připojovací řetězce SQL) musí být uvozena pro zprostředkovatele konfigurace pro čtení proměnných prostředí. Použijte následující příkaz k vygenerování správně uvozený uvozovacím znakem hodnoty pro použití v konfiguračním souboru:

```console
systemd-escape "<value-to-escape>"
```

Uložte soubor a povolení služby:

```bash
sudo systemctl enable kestrel-helloapp.service
```

Spusťte službu a ověřte, zda je spuštěna:

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

Reverzní proxy server nakonfigurovaný a spravované přes Kestrel *systemd*, webové aplikace plně konfigurována a je přístupný z prohlížeče na místním počítači v `http://localhost`. Kontrola hlavičky odpovědi **Server** hlavička označuje, že aplikace ASP.NET Core je poskytovaný Kestrel:

```
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a>Zobrazení protokolů

Od webové aplikace pomocí Kestrel se spravuje pomocí *systemd*, události a procesy jsou protokolovány centralizované deníku. Ale tento deník obsahuje záznamy pro všechny služby a spravuje procesy *systemd*. Chcete-li zobrazit `kestrel-helloapp.service`-konkrétní položky, použijte následující příkaz:

```bash
sudo journalctl -fu kestrel-helloapp.service
```

Pro filtrování podle času, zadejte možnosti pomocí příkazu. Například použít `--since today` filtrovat aktuální den nebo `--until 1 hour ago` zobrazíte položky do předchozí hodiny. Další informace najdete v tématu [man stránka journalctl](https://www.unix.com/man-page/centos/1/journalctl/).

```bash
sudo journalctl -fu kestrel-helloapp.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="data-protection"></a>Ochrana dat

[Ochranu dat ASP.NET Core zásobníku](xref:security/data-protection/introduction) používá několik ASP.NET Core [middlewares](xref:fundamentals/middleware/index), včetně middleware ověřování (například middlewaru souboru cookie.) a mezi weby (CSRF) proti padělání požadavků ochranu. I v případě, že Data Protection API nejsou volané kódem uživatele, ochranu dat by měl být povolen vytvořit trvalé kryptografických [úložiště klíčů](xref:security/data-protection/implementation/key-management). Pokud není nakonfigurovaná ochrana dat, jsou klíče uložené v paměti a při restartování aplikace.

Pokud kanál klíče jsou uloženy v paměti, při restartování aplikace:

* Všechny tokeny ověřování na základě souborů cookie nejsou zneplatněny.
* Uživatelé se musí znovu přihlásit v jejich další požadavek.
* Všechna data chráněná pomocí aktualizační kanál, který klíč můžete už nebude možné dešifrovat. To může zahrnovat [CSRF tokeny](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) a [soubory cookie v ASP.NET Core MVC TempData](xref:fundamentals/app-state#tempdata).

Konfigurace ochrany dat zachovat a aktualizační kanál, který klíč šifrování, najdete v tématech:

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>

## <a name="secure-the-app"></a>Zabezpečení aplikace

### <a name="configure-firewall"></a>Konfigurace brány firewall

*Firewalld* je dynamické démon ke správě brány firewall s podporou zón sítě. Porty a filtrování paketů můžete dál spravovat iptables. *Firewalld* by měl být ve výchozím nastavení nainstalovaná. `yum` slouží k instalaci balíčku nebo ověřte, že je nainstalovaná.

```bash
sudo yum install firewalld -y
```

Použití `firewalld` otevřít pouze porty potřebné pro aplikaci. V takovém případě je to port 80 a 443 jsou používány. Následující příkazy trvale nastavte porty 80 a 443. Chcete-li otevřít:

```bash
sudo firewall-cmd --add-port=80/tcp --permanent
sudo firewall-cmd --add-port=443/tcp --permanent
```

Znovu načte nastavení brány firewall. Zkontrolujte dostupné služby a porty ve výchozí zóně. Možnosti jsou k dispozici zkontrolováním `firewall-cmd -h`.

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

### <a name="https-configuration"></a>Konfigurace protokolu HTTPS

**Konfigurace aplikace pro zabezpečené místní připojení (HTTPS)**

[Dotnet spustit](/dotnet/core/tools/dotnet-run) příkaz používá aplikace *Properties/launchSettings.json* soubor, který konfiguruje aplikaci, aby naslouchala na adresy URL poskytnuté `applicationUrl` vlastnosti (například `https://localhost:5001;http://localhost:5000`) .

Konfigurace aplikace pro použití při vývoji pro certifikátu `dotnet run` příkaz nebo vývojové prostředí (F5 nebo Ctrl + F5 ve Visual Studio Code) pomocí jedné z následujících přístupů:

* [Nahraďte výchozí certifikát z konfigurace](xref:fundamentals/servers/kestrel#configuration) (*doporučená*)
* [KestrelServerOptions.ConfigureHttpsDefaults](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

**Konfigurace reverzního proxy serveru pro připojení klientů zabezpečené (HTTPS)**

Chcete-li nakonfigurovat i Apache pro protokol HTTPS, *mod_ssl* modul se používá. Když *httpd* modul byl nainstalován, *mod_ssl* také nainstalován modul. Pokud nebyla nainstalována, použijte `yum` přidejte do konfigurace.

```bash
sudo yum install mod_ssl
```

Vynucení protokolu HTTPS, nainstalujte `mod_rewrite` modulu, který chcete-li povolit přepisování adres URL:

```bash
sudo yum install mod_rewrite
```

Upravit *helloapp.conf* soubor povolit přepisování adres URL a zabezpečenou komunikaci na portu 443:

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
> Tento příklad používá místně vygeneruje certifikát. **SSLCertificateFile** by měl být soubor primárního certifikátu pro název domény. **SSLCertificateKeyFile** by měl být soubor s klíčem vygenerován při vytvoření žádosti o podepsání certifikátu. **SSLCertificateChainFile** by měl být soubor zprostředkující certifikát (pokud existuje), který byl zadán certifikační autoritou.

Uložte soubor a otestujte konfiguraci:

```bash
sudo service httpd configtest
```

Restartujte Apache:

```bash
sudo systemctl restart httpd
```

## <a name="additional-apache-suggestions"></a>Další návrhy Apache

### <a name="additional-headers"></a>Dodatečné hlavičky

Myslet při zabezpečování před škodlivými útoky, existuje pár hlavičky, které se musí buď být přidá nebo upraví. Ujistěte se, `mod_headers` je nainstalován modul:

```bash
sudo yum install mod_headers
```

#### <a name="secure-apache-from-clickjacking-attacks"></a>Zabezpečení před útoky útoků typu clickjacking Apache

[Útoků typu Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), označované také jako *uživatelského rozhraní zjednávání nápravy útoku*, je napadením se zlými úmysly, kde návštěvníků webu je nalákaní, odkaz nebo tlačítko na stránce jiné než aktuálně navštívený. Použití `X-FRAME-OPTIONS` k zabezpečení webu.

Ke zmírnění útoků typu clickjacking útoků:

1. Upravit *httpd.conf* souboru:

   ```bash
   sudo nano /etc/httpd/conf/httpd.conf
   ```

   Přidejte řádek `Header append X-FRAME-OPTIONS "SAMEORIGIN"`.
1. Uložte soubor.
1. Restartujte Apache.

#### <a name="mime-type-sniffing"></a>Typ MIME pro analýzu sítě

`X-Content-Type-Options` Záhlaví brání aplikaci Internet Explorer z *MIME pro analýzu sítě* (určení souboru `Content-Type` z obsahu souboru). Pokud server nastaví `Content-Type` záhlaví `text/html` s `nosniff` sadu možností, Internet Explorer vykreslí obsah jako `text/html` bez ohledu na jeho obsah.

Upravit *httpd.conf* souboru:

```bash
sudo nano /etc/httpd/conf/httpd.conf
```

Přidejte řádek `Header set X-Content-Type-Options "nosniff"`. Uložte soubor. Restartujte Apache.

### <a name="load-balancing"></a>Vyrovnávání zatížení

Tento příklad ukazuje, jak nainstalovat a nakonfigurovat Apache na CentOS 7 a Kestrel ve stejném počítači instance. Aby bylo možné, není nutné jediný bod selhání; pomocí *mod_proxy_balancer* a úpravy **VirtualHost** by umožňoval správu více instancí služby web apps za proxy serverem Apache.

```bash
sudo yum install mod_proxy_balancer
```

V konfiguračním souboru je znázorněno níže, další instanci `helloapp` je nastaven na spuštění port 5001. *Proxy* části je nastaven s konfigurací nástroje pro vyrovnávání se dvěma členy pro vyrovnávání zatížení *byrequests*.

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

Pomocí *mod_ratelimit*, které je součástí *httpd* modulu, šířky pásma klientů může být omezená:

```bash
sudo nano /etc/httpd/conf.d/ratelimit.conf
```

Příklad souboru omezuje šířku pásma jako 600 KB/s v části kořenový adresář:

```
<IfModule mod_ratelimit.c>
    <Location />
        SetOutputFilter RATE_LIMIT
        SetEnv rate-limit 600
    </Location>
</IfModule>
```

### <a name="long-request-header-fields"></a>Pole hlavičky dlouhou žádost

Pokud aplikace vyžaduje pole hlavičky požadavku delší než povolená ve výchozím nastavení proxy serveru, nastavení (obvykle 8,190 bajtů), upravte hodnotu [LimitRequestFieldSize](https://httpd.apache.org/docs/2.4/mod/core.html#LimitRequestFieldSize) směrnice. Použít hodnotu závisí na scénáři. Další informace najdete v dokumentaci k serveru.

> [!WARNING]
> Není výchozí hodnotu zvýšit `LimitRequestFieldSize` není-li nezbytné. Zvýšení hodnoty zvyšují riziko přetečení vyrovnávací paměti (přetečení) a uživateli se zlými úmysly útoků s cílem odepření služby (DoS).

## <a name="additional-resources"></a>Další zdroje

* [Požadavky pro .NET Core v Linuxu](/dotnet/core/linux-prerequisites)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>

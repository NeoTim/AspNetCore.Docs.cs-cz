---
title: Hostitele ASP.NET Core v Linuxu pomocí Apache
description: Zjistěte, jak nastavit službu Apache jako reverzní proxy server na CentOS pro přesměrování přenosu dat protokolu HTTP k webové aplikaci ASP.NET Core spuštěnou v prostředí Kestrel.
author: spboyer
ms.author: spboyer
ms.custom: mvc
ms.date: 03/13/2018
uid: host-and-deploy/linux-apache
ms.openlocfilehash: d02fbd82be37e6d67214a9a0bf5851662b577cb9
ms.sourcegitcommit: 18339e3cb5a891a3ca36d8146fa83cf91c32e707
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37433971"
---
# <a name="host-aspnet-core-on-linux-with-apache"></a>Hostitele ASP.NET Core v Linuxu pomocí Apache

Podle [Shayne Boyer](https://github.com/spboyer)

Pomocí této příručky, zjistěte, jak nastavit [Apache](https://httpd.apache.org/) jako reverzní proxy server na [CentOS 7](https://www.centos.org/) pro přesměrování přenosu dat protokolu HTTP pro webovou aplikaci ASP.NET Core využívající [Kestrel](xref:fundamentals/servers/kestrel). [Mod_proxy rozšíření](http://httpd.apache.org/docs/2.4/mod/mod_proxy.html) a související moduly vytvořit reverzního proxy serveru.

## <a name="prerequisites"></a>Požadavky

1. Server se systémem CentOS 7 pomocí standardního uživatelského účtu s oprávněními sudo.
1. Nainstalujte modul runtime .NET Core na serveru.
   1. Přejděte [.NET Core všechny soubory ke stažení stránky](https://www.microsoft.com/net/download/all).
   1. Vyberte nejnovější modul runtime – ve verzi preview ze seznamu **Runtime**.
   1. Vyberte a postupujte podle pokynů pro CentOS nebo Oracle.
1. Stávající aplikace ASP.NET Core.

## <a name="publish-and-copy-over-the-app"></a>Publikování a zkopírujte myší na aplikaci

Konfigurace aplikace pro [nasazení závisí na architektuře](/dotnet/core/deploying/#framework-dependent-deployments-fdd).

Spustit [dotnet publikovat](/dotnet/core/tools/dotnet-publish) z vývojového prostředí pro balíček aplikace do adresáře (například *bin/Release/&lt;target_framework_moniker&gt;/ publish*), který můžete Spusťte na serveru:

```console
dotnet publish --configuration Release
```

Aplikace můžete také publikovat jako [samostatná nasazení](/dotnet/core/deploying/#self-contained-deployments-scd) Pokud nechcete zachovat modulu runtime .NET Core na serveru.

Aplikace ASP.NET Core zkopírujte na server pomocí nástroje, které se integruje do pracovního postupu organizace (třeba spojovací bod služby, SFTP). Je běžné vyhledejte webové aplikace v rámci *var* adresář (třeba *aspnetcore/var/hellomvc*).

> [!NOTE]
> V případě produkčního nasazení pracovního postupu průběžné integrace funguje publikování aplikace a kopírování prostředky na server.

## <a name="configure-a-proxy-server"></a>Konfigurace proxy serveru

Reverzní proxy server je společné nastavení pro poskytování dynamické webové aplikace. Reverzní proxy server ukončí požadavek HTTP a předá ji do aplikace ASP.NET.

Proxy server je znak, který se předává požadavky klientů na jiný server místo samotného plnění požadavků. Reverzní proxy server předává do pevné umístění, obvykle jménem libovolného klientů. V této příručce Apache nakonfigurovaný jako reverzní proxy server běží na stejném serveru, aby Kestrel obsluhuje aplikace ASP.NET Core.

Vzhledem k tomu, že žádosti jsou předávány podle reverzní proxy server, použít [předané Middleware záhlaví](xref:host-and-deploy/proxy-load-balancer) z [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) balíčku. Middleware aktualizace `Request.Scheme`, použije `X-Forwarded-Proto` záhlaví tak, že identifikátory URI pro přesměrování a další zásady zabezpečení pracovat správně.

Jakékoli součásti, která závisí na schéma, jako je například ověřování, generování odkazů, přesměrování a zeměpisná poloha, musí být umístěn po vyvolání Middleware předané záhlaví. Jako obecné pravidlo by měla předávat Middleware záhlaví spustit před dalším middlewarem s výjimkou diagnostiky a middleware pro zpracování chyb. Toto uspořádání zajistí, že middleware spoléhání se na informace předávané záhlaví může spotřebovat hodnoty hlavičky pro zpracování.

::: moniker range=">= aspnetcore-2.0"
> [!NOTE]
> Buď konfiguraci&mdash;s nebo bez něj reverzní proxy server&mdash;je platný a podporované konfigurace pro hostování pro ASP.NET Core 2.0 nebo novější. Další informace najdete v tématu [použití Kestrel s reverzní proxy server](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).
::: moniker-end

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Vyvolat [UseForwardedHeaders](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) metoda `Startup.Configure` před voláním [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication) nebo podobné režimu middleware ověřování. Nakonfigurujte middleware předávat `X-Forwarded-For` a `X-Forwarded-Proto` hlavičky:

```csharp
app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Vyvolat [UseForwardedHeaders](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) metoda `Startup.Configure` před voláním [UseIdentity](/dotnet/api/microsoft.aspnetcore.builder.builderextensions.useidentity) a [UseFacebookAuthentication](/dotnet/api/microsoft.aspnetcore.builder.facebookappbuilderextensions.usefacebookauthentication) nebo podobné schéma ověřování middleware. Nakonfigurujte middleware předávat `X-Forwarded-For` a `X-Forwarded-Proto` hlavičky:

```csharp
app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseIdentity();
app.UseFacebookAuthentication(new FacebookOptions()
{
    AppId = Configuration["Authentication:Facebook:AppId"],
    AppSecret = Configuration["Authentication:Facebook:AppSecret"]
});
```

---

Pokud ne [ForwardedHeadersOptions](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) jsou určené pro middleware, jsou výchozí hlavičky pro předávání `None`.

Další konfigurace může být nezbytný pro aplikací hostovaných za službou proxy servery a nástroje pro vyrovnávání zatížení. Další informace najdete v tématu [konfigurace ASP.NET Core práci se servery proxy a nástroje pro vyrovnávání zatížení](xref:host-and-deploy/proxy-load-balancer).

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

Vytvoření konfiguračního souboru s názvem *hellomvc.conf*, pro aplikace:

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
    ErrorLog ${APACHE_LOG_DIR}hellomvc-error.log
    CustomLog ${APACHE_LOG_DIR}hellomvc-access.log common
</VirtualHost>
```

`VirtualHost` Bloku se mohou objevit více než jednou, v jedné nebo více souborů na serveru. V předchozím konfigurační soubor Apache přijímá veřejné provozu na portu 80. Doména `www.example.com` se obsluhuje a `*.example.com` alias se překládá na stejné webové stránce. Zobrazit [podporu založené na název virtuálního hostitele](https://httpd.apache.org/docs/current/vhosts/name-based.html) Další informace. Požadavky jsou směrovány přes proxy server v kořenovém adresáři na portu 5000 serveru na 127.0.0.1. Pro obousměrnou komunikaci `ProxyPass` a `ProxyPassReverse` jsou povinné. Chcete-li změnit Kestrel jeho IP adresa/port, [Kestrel: konfigurace koncového bodu](xref:fundamentals/servers/kestrel#endpoint-configuration).

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

## <a name="monitoring-the-app"></a>Monitorování aplikace

Apache je nyní instalačního programu předat požadavky na `http://localhost:80` pro aplikaci ASP.NET Core spuštěnou v Kestrel na `http://127.0.0.1:5000`.  Apache není však nastavené ke správě procesu Kestrel. Použití *systemd* a vytvořit soubor služby a začít monitorovat základní webovou aplikaci. *systemd* je init systém, který poskytuje řadu výkonných funkcí pro spouštění, zastavování a Správa procesů. 

### <a name="create-the-service-file"></a>Vytvoření souboru služby

Vytvoření definičního souboru služby:

```bash
sudo nano /etc/systemd/system/kestrel-hellomvc.service
```

Příklad souboru služby pro aplikaci:

```
[Unit]
Description=Example .NET Web API App running on CentOS 7

[Service]
WorkingDirectory=/var/aspnetcore/hellomvc
ExecStart=/usr/local/bin/dotnet /var/aspnetcore/hellomvc/hellomvc.dll
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
SyslogIdentifier=dotnet-example
User=apache
Environment=ASPNETCORE_ENVIRONMENT=Production 

[Install]
WantedBy=multi-user.target
```

> [!NOTE]
> **Uživatel** &mdash; Pokud uživatel *apache* nepoužívá konfigurace, musí nejprve vytvořit uživatele a pro soubory zadané správné vlastnictví.

> [!NOTE]
> Některé hodnoty (například připojovací řetězce SQL) musí být uvozena pro zprostředkovatele konfigurace pro čtení proměnných prostředí. Použijte následující příkaz k vygenerování správně uvozený uvozovacím znakem hodnoty pro použití v konfiguračním souboru:
>
> ```console
> systemd-escape "<value-to-escape>"
> ```

Uložte soubor a povolení služby:

```bash
systemctl enable kestrel-hellomvc.service
```

Spusťte službu a ověřte, zda je spuštěna:

```bash
systemctl start kestrel-hellomvc.service
systemctl status kestrel-hellomvc.service

● kestrel-hellomvc.service - Example .NET Web API App running on CentOS 7
    Loaded: loaded (/etc/systemd/system/kestrel-hellomvc.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-hellomvc.service
            └─9021 /usr/local/bin/dotnet /var/aspnetcore/hellomvc/hellomvc.dll
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

### <a name="viewing-logs"></a>Zobrazení protokolů

Od webové aplikace pomocí Kestrel se spravuje pomocí *systemd*, události a procesy jsou protokolovány centralizované deníku. Ale tento deník obsahuje záznamy pro všechny služby a spravuje procesy *systemd*. Chcete-li zobrazit `kestrel-hellomvc.service`-konkrétní položky, použijte následující příkaz:

```bash
sudo journalctl -fu kestrel-hellomvc.service
```

Pro filtrování podle času, zadejte možnosti pomocí příkazu. Například použít `--since today` filtrovat aktuální den nebo `--until 1 hour ago` zobrazíte položky do předchozí hodiny. Další informace najdete v tématu [man stránka journalctl](https://www.unix.com/man-page/centos/1/journalctl/).

```bash
sudo journalctl -fu kestrel-hellomvc.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="securing-the-app"></a>Zabezpečení aplikace

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

### <a name="ssl-configuration"></a>Konfigurace SSL

Chcete-li nakonfigurovat i Apache pro protokol SSL, *mod_ssl* modul se používá. Když *httpd* modul byl nainstalován, *mod_ssl* také nainstalován modul. Pokud nebyla nainstalována, použijte `yum` přidejte do konfigurace.

```bash
sudo yum install mod_ssl
```

K vynucení SSL, nainstalujte `mod_rewrite` modulu, který chcete-li povolit přepisování adres URL:

```bash
sudo yum install mod_rewrite
```

Upravit *hellomvc.conf* soubor povolit přepisování adres URL a zabezpečenou komunikaci na portu 443:

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
    ErrorLog /var/log/httpd/hellomvc-error.log
    CustomLog /var/log/httpd/hellomvc-access.log common
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

Upravit *httpd.conf* souboru:

```bash
sudo nano /etc/httpd/conf/httpd.conf
```

Přidejte řádek `Header append X-FRAME-OPTIONS "SAMEORIGIN"`. Uložte soubor. Restartujte Apache.

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

V konfiguračním souboru je znázorněno níže, další instanci `hellomvc` aplikace je ke spuštění na portu 5001 instalace. *Proxy* části je nastaven s konfigurací nástroje pro vyrovnávání se dvěma členy pro vyrovnávání zatížení *byrequests*.

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
    ErrorLog /var/log/httpd/hellomvc-error.log
    CustomLog /var/log/httpd/hellomvc-access.log common
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

## <a name="additional-resources"></a>Další zdroje

* [Konfigurace ASP.NET Core práci se servery proxy a nástroje pro vyrovnávání zatížení](xref:host-and-deploy/proxy-load-balancer)

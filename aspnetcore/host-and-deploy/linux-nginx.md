---
title: Host ASP.NET Core na Linuxu s Nginx
author: rick-anderson
description: Naučte se, jak nastavit Nginx jako reverzní proxy na Ubuntu 16.04 pro předávání http provozu do ASP.NET webové aplikace Core běžící na Kestrelu.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/05/2020
uid: host-and-deploy/linux-nginx
ms.openlocfilehash: 320a5364efe85b06028d8e80000e3455bb8ebd18
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657911"
---
# <a name="host-aspnet-core-on-linux-with-nginx"></a>Host ASP.NET Core na Linuxu s Nginx

Podle [Sourabh Shirhatti](https://twitter.com/sshirhatti)

Tato příručka vysvětluje nastavení prostředí ASP.NET Core připraveného pro produkční prostředí na serveru Ubuntu 16.04. Tyto pokyny pravděpodobně pracují s novějšími verzemi Ubuntu, ale pokyny nebyly testovány s novějšími verzemi.

Informace o dalších linuxových distribucích podporovaných ASP.NET Core najdete [v tématu Požadavky pro .NET Core v Linuxu](/dotnet/core/linux-prerequisites).

> [!NOTE]
> Pro Ubuntu 14.04 se doporučuje *dohledu* jako řešení pro sledování procesu Kestrel. *systemd* není k dispozici na Ubuntu 14.04. Pokyny k Ubuntu 14.04 najdete v [předchozí verzi tohoto tématu](https://github.com/dotnet/AspNetCore.Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md).

Tato příručka:

* Umístí existující aplikaci ASP.NET Core za reverzní proxy server.
* Nastaví reverzní proxy server pro předávání požadavků na webový server Kestrel.
* Zajišťuje, že webová aplikace běží při spuštění jako daemon.
* Nakonfiguruje nástroj pro správu procesů, který pomáhá restartovat webovou aplikaci.

## <a name="prerequisites"></a>Požadavky

1. Přístup k serveru Ubuntu 16.04 se standardním uživatelským účtem s oprávněním sudo.
1. Nainstalujte na server runtime jádra .NET.
   1. Navštivte [stránku Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core).
   1. Vyberte nejnovější verzi jádra .NET bez náhledu.
   1. Stáhněte si nejnovější runtime bez náhledu v tabulce v části **Spustit aplikace - Runtime**.
   1. Vyberte odkaz **na pokyny správce balíčků** Linuxu a postupujte podle pokynů Ubuntu pro vaši verzi Ubuntu.
1. Existující aplikace ASP.NET Core.

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

Otestujte aplikaci:

1. Na příkazovém řádku spusťte aplikaci: `dotnet <app_assembly>.dll`.
1. V prohlížeči přejděte na `http://<serveraddress>:<port>` ověření, že aplikace funguje na Linuxu místně.

## <a name="configure-a-reverse-proxy-server"></a>Konfigurace serveru reverzního proxy serveru

Reverzní proxy je běžné nastavení pro zobrazování dynamických webových aplikací. Reverzní proxy ukončí požadavek HTTP a předá jej do aplikace ASP.NET Core.

### <a name="use-a-reverse-proxy-server"></a>Použití reverzního proxy serveru

Kestrel je skvělý pro podávání dynamického obsahu z ASP.NET Core. Možnosti webové hosvírování však nejsou tak bohaté na funkce jako servery, jako je Služba IIS, Apache nebo Nginx. Reverzní proxy server může offload práce, jako je například obsluha statického obsahu, ukládání požadavků do mezipaměti, komprese požadavků a ukončení HTTPS ze serveru HTTP. Reverzní proxy server může být umístěn na vyhrazeném počítači nebo může být nasazen společně se serverem HTTP.

Pro účely této příručky se používá jedna instance Nginx. Běží na stejném serveru, vedle http serveru. Na základě požadavků může být zvoleno jiné nastavení.

Vzhledem k tomu, že požadavky jsou předávány reverzním proxy serverem, použijte [middleware s předanými hlavičkami](xref:host-and-deploy/proxy-load-balancer) z balíčku [Microsoft.AspNetCore.HttpOverrides.](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) Middleware aktualizuje `Request.Scheme`, pomocí `X-Forwarded-Proto` záhlaví, takže přesměrování IDENTIFIKÁTORŮ URI a dalších zásad zabezpečení pracovat správně.

Každá součást, která závisí na schématu, jako je ověřování, generování propojení, přesměrování a geografické umístění, musí být umístěna po vyvolání middlewaru předávaných záhlaví. Obecně platí, že middleware s předaným záhlavím by měl být spuštěn před jiným middlewarem s výjimkou diagnostiky a zpracování chyb middlewaru. Toto pořadí zajišťuje, že middleware spoléhající na předávané informace záhlaví může spotřebovat hodnoty záhlaví pro zpracování.

Vyvolat <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> metodu `Startup.Configure` v <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> před voláním nebo podobné schéma ověřování middleware. Nakonfigurujte middleware tak, aby předával záhlaví `X-Forwarded-For` a: `X-Forwarded-Proto`

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

### <a name="install-nginx"></a>Instalace Nginx

Slouží `apt-get` k instalaci Nginx. Instalátor vytvoří *systemd* init skript, který běží Nginx jako daemon při spuštění systému. Postupujte podle pokynů k instalaci pro Ubuntu na [Nginx: Oficiální Debian / Ubuntu balíčky](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages).

> [!NOTE]
> Pokud jsou požadovány volitelné moduly Nginx, může být vyžadována stavba Nginx ze zdroje.

Vzhledem k tomu, že nginx byl nainstalován poprvé, explicitně spustit spuštěním:

```bash
sudo service nginx start
```

Ověřte, zda prohlížeč zobrazuje výchozí vstupní stránku nginxu. Vstupní stránka je dostupná na adrese `http://<server_IP_address>/index.nginx-debian.html`.

### <a name="configure-nginx"></a>Konfigurace služby Nginx

Chcete-li nakonfigurovat Nginx jako reverzní proxy server pro předávání požadavků na aplikaci ASP.NET Core, upravte */etc/nginx/sites-available/default*. Otevřete jej v textovém editoru a nahraďte obsah následujícím:

```nginx
server {
    listen        80;
    server_name   example.com *.example.com;
    location / {
        proxy_pass         http://localhost:5000;
        proxy_http_version 1.1;
        proxy_set_header   Upgrade $http_upgrade;
        proxy_set_header   Connection keep-alive;
        proxy_set_header   Host $host;
        proxy_cache_bypass $http_upgrade;
        proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header   X-Forwarded-Proto $scheme;
    }
}
```

Pokud je aplikace aplikace Blazor Server, která závisí na <xref:host-and-deploy/blazor/server#linux-with-nginx> SignalR WebSockets, `Connection` přečtěte si informace o tom, jak nastavit záhlaví.

Pokud `server_name` žádná shoda, Nginx používá výchozí server. Pokud není definován žádný výchozí server, je výchozím serverem první server v konfiguračním souboru. Jako osvědčený postup přidejte konkrétní výchozí server, který vrátí stavový kód 444 v konfiguračním souboru. Výchozí příklad konfigurace serveru je:

```nginx
server {
    listen   80 default_server;
    # listen [::]:80 default_server deferred;
    return   444;
}
```

S předchozím konfiguračním souborem a výchozím serverem nginx `example.com` přijímá `*.example.com`veřejný provoz na portu 80 s hlavičkou hostitele nebo . Požadavky, které neodpovídají těmto hostitelům, nebudou předány ke kestrelu. Nginx předá odpovídající požadavky Kestrel `http://localhost:5000`na . Podívejte [se, jak nginx zpracovává žádost o](https://nginx.org/docs/http/request_processing.html) další informace. Chcete-li změnit protokol Kestrel IP/port, přečtěte si část [Kestrel: Konfigurace koncového bodu](xref:fundamentals/servers/kestrel#endpoint-configuration).

> [!WARNING]
> Pokud nezadáte správnou [server_name direktivu,](https://nginx.org/docs/http/server_names.html) aplikace se zpřístupní slabým místům zabezpečení. Vazba se zástupnými znaky subdomény (například) nepředstavuje toto bezpečnostní riziko, `*.example.com` `*.com`pokud řídíte celou nadřazenou doménu (na rozdíl od , která je zranitelná). Viz [rfc7230 sekce-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) pro více informací.

Po navázání konfigurace Nginx `sudo nginx -t` spusťte ověření syntaxe konfiguračních souborů. Pokud je test konfiguračního souboru úspěšný, vynuťte nginx, aby změny zachytil spuštěním `sudo nginx -s reload`.

Přímé spuštění aplikace na serveru:

1. Přejděte do adresáře aplikace.
1. Spusťte `dotnet <app_assembly.dll>`aplikaci: , kde `app_assembly.dll` je název souboru sestavení aplikace.

Pokud aplikace běží na serveru, ale nereaguje přes Internet, zkontrolujte bránu firewall serveru a zkontrolujte, zda je otevřený port 80. Pokud používáte virtuální počítač Azure Ubuntu, přidejte pravidlo skupiny zabezpečení sítě (NSG), které umožňuje příchozí port 80 provoz. Není nutné povolit pravidlo odchozího portu 80, protože odchozí provoz je automaticky udělen, když je povoleno příchozí pravidlo.

Po dokončení testování aplikace, vypněte `Ctrl+C` aplikaci s na příkazovém řádku.

## <a name="monitor-the-app"></a>Sledování aplikace

Server je nastaven tak, aby `http://<serveraddress>:80` předával požadavky na aplikaci ASP.NET `http://127.0.0.1:5000`Core spuštěnou na kestrelu na adrese . Nginx však není nastaven pro správu procesu Kestrel. *systemd* lze použít k vytvoření souboru služby pro spuštění a sledování základní webové aplikace. *systemd* je init systém, který poskytuje mnoho výkonných funkcí pro spouštění, zastavování a správu procesů. 

### <a name="create-the-service-file"></a>Vytvoření souboru služby

Vytvořte soubor definice služby:

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

Následuje ukázkový soubor služby pro aplikaci:

```ini
[Unit]
Description=Example .NET Web API App running on Ubuntu

[Service]
WorkingDirectory=/var/www/helloapp
ExecStart=/usr/bin/dotnet /var/www/helloapp/helloapp.dll
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=dotnet-example
User=www-data
Environment=ASPNETCORE_ENVIRONMENT=Production
Environment=DOTNET_PRINT_TELEMETRY_MESSAGE=false

[Install]
WantedBy=multi-user.target
```

V předchozím příkladu je uživatel, který spravuje službu, určen `User` možností. Uživatel (`www-data`) musí existovat a mít správné vlastnictví souborů aplikace.

Slouží `TimeoutStopSec` ke konfiguraci doby čekání na vypnutí aplikace po přijetí signálu počátečního přerušení. Pokud se aplikace v tomto období nevypne, je vydána aplikace SIGKILL, aby aplikaci ukončila. Zadejte hodnotu jako sekundy `150`bez jednotky (například ), `2min 30s`hodnotu `infinity` časového rozpětí (například ) nebo zakázat časový rozsah. `TimeoutStopSec`výchozí hodnota v `DefaultTimeoutStopSec` konfiguračním souboru správce (*systemd-system.conf*, *system.conf.d*, *systemd-user.conf*, *user.conf.d*). Výchozí časový limit pro většinu distribucí je 90 sekund.

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

Linux má systém souborů rozlišující malá a velká písmena. Nastavení ASPNETCORE_ENVIRONMENT na "Produkční" má za následek hledání nastavení konfiguračního *souboru. Production.json*, ne *appsettings.production.json*.

Některé hodnoty (například připojovací řetězce SQL) musí být uvozeny, aby zprostředkovatelé konfigurace mohli číst proměnné prostředí. Pomocí následujícího příkazu vygenerujte správně uvozenou hodnotu pro použití v konfiguračním souboru:

```console
systemd-escape "<value-to-escape>"
```

Oddělovače dvojtečky (`:`) nejsou v názvech proměnných prostředí podporovány. Místo dvojtečky`__`použijte dvojité podtržítko ( ). [Zprostředkovatel konfigurace proměnných prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider) převádí dvojité podtržítka na dvojtečky při čtení proměnných prostředí do konfigurace. V následujícím příkladu je `ConnectionStrings:DefaultConnection` klíč připojovacího `ConnectionStrings__DefaultConnection`řetězce nastaven do souboru definice služby jako :

```
Environment=ConnectionStrings__DefaultConnection={Connection String}
```

Uložte soubor a povolte službu.

```bash
sudo systemctl enable kestrel-helloapp.service
```

Spusťte službu a ověřte, zda je spuštěná.

```
sudo systemctl start kestrel-helloapp.service
sudo systemctl status kestrel-helloapp.service

● kestrel-helloapp.service - Example .NET Web API App running on Ubuntu
    Loaded: loaded (/etc/systemd/system/kestrel-helloapp.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-helloapp.service
            └─9021 /usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
```

S reverzní proxy nakonfigurován a Kestrel spravuje prostřednictvím systemd, webová aplikace je plně `http://localhost`nakonfigurován a lze přistupovat z prohlížeče na místním počítači na . Je také přístupný ze vzdáleného počítače, blokování firewallu, který by mohl blokovat. Při kontrole záhlaví odpovědí `Server` se v záhlaví zobrazí aplikace ASP.NET Core obsluhovaná společností Kestrel.

```text
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a>Zobrazení protokolů

Vzhledem k tomu, že webová aplikace pomocí Kestrel je spravována pomocí `systemd`, všechny události a procesy jsou protokolovány do centralizovaného deníku. Tento deník však obsahuje všechny položky `systemd`pro všechny služby a procesy spravované společností . Chcete-li `kestrel-helloapp.service`zobrazit specifické položky, použijte následující příkaz:

```bash
sudo journalctl -fu kestrel-helloapp.service
```

Pro další filtrování mohou možnosti času, například `--since today`, `--until 1 hour ago` nebo jejich kombinace snížit množství vrácených položek.

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

## <a name="long-request-header-fields"></a>Pole záhlaví dlouhého požadavku

Výchozí nastavení proxy serveru obvykle omezují pole hlaviček požadavků na 4 K nebo 8 K v závislosti na platformě. Aplikace může vyžadovat pole delší než výchozí (například aplikace, které používají [Azure Active Directory).](https://azure.microsoft.com/services/active-directory/) Pokud jsou vyžadována delší pole, výchozí nastavení proxy serveru vyžaduje úpravy. Hodnoty, které mají být aplikovány, závisí na scénáři. Další informace naleznete v dokumentaci k serveru.

* [proxy_buffer_size](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffer_size)
* [proxy_buffers](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffers)
* [proxy_busy_buffers_size](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_busy_buffers_size)
* [large_client_header_buffers](https://nginx.org/docs/http/ngx_http_core_module.html#large_client_header_buffers)

> [!WARNING]
> Pokud to není nutné, nezvyšujte výchozí hodnoty proxy vyrovnávacích pamětí. Zvýšení těchto hodnot zvyšuje riziko přetečení vyrovnávací paměti (přetečení) a odmítnutí služby (DoS) útoky uživatelů se zlými úmysly.

## <a name="secure-the-app"></a>Zabezpečení aplikace

### <a name="enable-apparmor"></a>Povolit AppArmor

Linux Security Modules (LSM) je framework, který je součástí linuxového jádra od Linuxu 2.6. LSM podporuje různé implementace bezpečnostních modulů. [AppArmor](https://wiki.ubuntu.com/AppArmor) je LSM, který implementuje systém povinného řízení přístupu, který umožňuje omezit program na omezenou sadu prostředků. Ujistěte se, že apparmor je povolena a správně nakonfigurována.

### <a name="configure-the-firewall"></a>Konfigurace brány firewall

Zavřete všechny externí porty, které se nepoužívají. Nekomplikovaná brána firewall (ufw) poskytuje front-end `iptables` tím, že poskytuje CLI pro konfiguraci brány firewall.

> [!WARNING]
> Brána firewall zabrání přístupu k celému systému, pokud není správně nakonfigurován. Pokud nezadáte správný port SSH, efektivně vás uzamknete ze systému, pokud k němu používáte SSH. Výchozí port je 22. Další informace naleznete v [úvodu k ufw](https://help.ubuntu.com/community/UFW) a [manuálu](https://manpages.ubuntu.com/manpages/bionic/man8/ufw.8.html).

Nainstalujte `ufw` a nakonfigurujte jej tak, aby umožňoval přenos na všech potřebných portech.

```bash
sudo apt-get install ufw

sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

sudo ufw enable
```

### <a name="secure-nginx"></a>Bezpečné Nginx

#### <a name="change-the-nginx-response-name"></a>Změna názvu odpovědi Nginx

Upravit *src/http/ngx_http_header_filter_module.c*:

```
static char ngx_http_server_string[] = "Server: Web Server" CRLF;
static char ngx_http_server_full_string[] = "Server: Web Server" CRLF;
```

#### <a name="configure-options"></a>Konfigurace možností

Nakonfigurujte server pomocí dalších požadovaných modulů. Zvažte použití brány firewall webové aplikace, jako je [ModSecurity](https://www.modsecurity.org/), k posílení zabezpečení aplikace.

#### <a name="https-configuration"></a>Konfigurace PROTOKOLU HTTPS

**Konfigurace aplikace pro zabezpečená místní připojení (HTTPS)**

Příkaz [dotnet run](/dotnet/core/tools/dotnet-run) používá soubor *Vlastnosti/launchSettings.json* aplikace, který konfiguruje aplikaci tak, aby naslouchala adresám URL poskytovaným `applicationUrl` vlastností (například ). `https://localhost:5001;http://localhost:5000`

Nakonfigurujte aplikaci tak, `dotnet run` aby používala certifikát ve vývoji pro prostředí příkazů nebo vývoje (F5 nebo Ctrl+F5 v kódu Visual Studia) pomocí jednoho z následujících přístupů:

* [Nahrazení výchozího certifikátu z konfigurace](xref:fundamentals/servers/kestrel#configuration) *(doporučeno)*
* [KestrelServerOptions.ConfigureHttpsDefaults](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

**Konfigurace reverzního proxy serveru pro zabezpečená připojení klientů (HTTPS)**

* Nakonfigurujte server tak, `443` aby naslouchal přenosům HTTPS na portu zadáním platného certifikátu vydaného důvěryhodnou certifikační autoritou (CA).

* Zpevněte zabezpečení použitím některých postupů zobrazených v následujícím souboru */etc/nginx/nginx.conf.* Mezi příklady patří výběr silnější šifry a přesměrování veškerého provozu přes PROTOKOL HTTP na protokol HTTPS.

* Přidání `HTTP Strict-Transport-Security` hlavičky (HSTS) zajišťuje, že všechny následné požadavky provedené klientem jsou přes protokol HTTPS.

* Nepřidávejte hlavičku HSTS ani `max-age` nezvolte vhodné, pokud https bude zakázán v budoucnu.

Přidejte konfigurační soubor */etc/nginx/proxy.conf:*

[!code-nginx[](linux-nginx/proxy.conf)]

Upravte konfigurační soubor */etc/nginx/nginx.conf.* Příklad obsahuje `http` oddíly v `server` jednom konfiguračním souboru.

[!code-nginx[](linux-nginx/nginx.conf?highlight=2)]

#### <a name="secure-nginx-from-clickjacking"></a>Zabezpečit Nginx z clickjacking

[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), také známý jako *útok na nápravu ui*, je škodlivý útok, kdy je návštěvník webových stránek podveden, aby klikl na odkaz nebo tlačítko na jiné stránce, než je v současné době na návštěvě. Slouží `X-FRAME-OPTIONS` k zabezpečení webu.

Chcete-li zmírnit clickjacking útoky:

1. Upravte soubor *nginx.conf:*

   ```bash
   sudo nano /etc/nginx/nginx.conf
   ```

   Přidejte řádek `add_header X-Frame-Options "SAMEORIGIN";`.
1. Uložte soubor.
1. Restartujte nginx.

#### <a name="mime-type-sniffing"></a>Čichání typu MIME

Toto záhlaví zabraňuje většině prohlížečů z MIME-sniffing odpověď od deklarovaného typu obsahu, jako záhlaví pokyn prohlížeč není přepsat typ obsahu odpovědi. S `nosniff` možností, pokud server říká, že obsah je "text / html", prohlížeč vykresluje jako "text / html".

Upravte soubor *nginx.conf:*

```bash
sudo nano /etc/nginx/nginx.conf
```

Přidejte `add_header X-Content-Type-Options "nosniff";` řádek a uložte soubor a restartujte Nginx.

## <a name="additional-nginx-suggestions"></a>Další návrhy Nginx

Po upgradu sdíleného rozhraní na serveru restartujte aplikace ASP.NET Core hostované serverem.

## <a name="additional-resources"></a>Další zdroje

* [Předpoklady pro .NET Core na Linuxu](/dotnet/core/linux-prerequisites)
* [Nginx: Binární verze: Oficiální balíčky Debian/Ubuntu](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
* [NGINX: Použití předaných záhlaví](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)

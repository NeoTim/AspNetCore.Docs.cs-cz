---
title: Hostování ASP.NET Core v systému Linux pomocí Nginx
author: guardrex
description: Naučte se, jak nastavit Nginx jako reverzní proxy na Ubuntu 16,04 pro přeposílání provozu HTTP do ASP.NET Core webové aplikace běžící na Kestrel.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/31/2019
uid: host-and-deploy/linux-nginx
ms.openlocfilehash: b71bc0464892f15ef8db0324a8e66a28a6192577
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71080877"
---
# <a name="host-aspnet-core-on-linux-with-nginx"></a>Hostování ASP.NET Core v systému Linux pomocí Nginx

Od [sourabh Shirhatti](https://twitter.com/sshirhatti)

V této příručce se dozvíte, jak na serveru Ubuntu 16,04 nastavit prostředí pro ASP.NET Core připravené pro produkční prostředí. Tyto pokyny pravděpodobně fungují s novějšími verzemi Ubuntu, ale pokyny nebyly testovány s novějšími verzemi.

Informace o dalších distribucích systému Linux podporovaných nástrojem ASP.NET Core najdete v tématu [předpoklady pro .NET Core v systému Linux](/dotnet/core/linux-prerequisites).

> [!NOTE]
> Pro Ubuntu *14,04 se doporučuje* jako řešení pro monitorování procesu Kestrel. *systém* není k dispozici na Ubuntu 14,04. Pokyny pro Ubuntu 14,04 najdete v [předchozí verzi tohoto tématu](https://github.com/aspnet/AspNetCore.Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md).

Tato příručka:

* Umístí existující aplikaci ASP.NET Core za reverzní proxy server.
* Nastaví reverzní proxy server pro přeposílání požadavků na webový server Kestrel.
* Zajistí, aby se webová aplikace spouštěla při spuštění jako démon.
* Nakonfiguruje Nástroj pro správu procesů, který vám může pomáhat s restartováním webové aplikace.

## <a name="prerequisites"></a>Požadavky

1. Přístup k serveru Ubuntu 16,04 se standardním uživatelským účtem s oprávněním sudo.
1. Nainstalujte modul runtime .NET Core na server.
   1. Navštivte [stránku všechny soubory ke stažení pro .NET Core](https://www.microsoft.com/net/download/all).
   1. V seznamu pod položkou **runtime**vyberte nejnovější modul runtime, který není ve verzi Preview.
   1. Vyberte a postupujte podle pokynů pro Ubuntu, které odpovídají verzi Ubuntu serveru.
1. Existující aplikace ASP.NET Core.

## <a name="publish-and-copy-over-the-app"></a>Publikování a kopírování přes aplikaci

Nakonfigurujte aplikaci pro [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd).

Pokud je aplikace spuštěná místně a není nakonfigurovaná tak, aby přijímala zabezpečené připojení (HTTPS), proveďte jednu z následujících metod:

* Nakonfigurujte aplikaci tak, aby zpracovávala Zabezpečená místní připojení. Další informace najdete v části [konfigurace https](#https-configuration) .
* Odebere `https://localhost:5001` (je-li k dispozici) `applicationUrl` z vlastnosti v souboru *Properties/launchSettings. JSON* .

Pokud chcete zabalit aplikaci do adresáře (například *bin/Release/&lt;&gt;target_framework_moniker/Publish*), která se dají spustit na serveru, spusťte [dotnet Publish](/dotnet/core/tools/dotnet-publish) z vývojového prostředí:

```dotnetcli
dotnet publish --configuration Release
```

Pokud nechcete zachovat modul runtime .NET Core na serveru, můžete aplikaci publikovat také jako samostatné [nasazení](/dotnet/core/deploying/#self-contained-deployments-scd) .

Zkopírujte aplikaci ASP.NET Core na server pomocí nástroje, který se integruje do pracovního postupu organizace (například SCP, SFTP). Je běžné najít webové aplikace v adresáři *var* (například *var/www/helloapp*).

> [!NOTE]
> V rámci scénáře nasazení v produkčním prostředí provádí pracovní postup průběžné integrace publikování aplikace a zkopírování prostředků na server.

Testování aplikace:

1. Z příkazového řádku spusťte aplikaci: `dotnet <app_assembly>.dll`.
1. V prohlížeči přejděte na `http://<serveraddress>:<port>` adresu a ověřte, že aplikace funguje na platformě Linux místně.

## <a name="configure-a-reverse-proxy-server"></a>Konfigurace reverzního proxy server

Reverzní proxy je běžné nastavení pro obsluhu dynamických webových aplikací. Reverzní proxy ukončí požadavek HTTP a předá ho do aplikace ASP.NET Core.

### <a name="use-a-reverse-proxy-server"></a>Použít reverzní proxy server

Kestrel je ideální pro obsluhu dynamického obsahu z ASP.NET Core. Webový server, který obsluhuje možnosti, ale není jako funkce bohatě funkční jako servery jako IIS, Apache nebo Nginx. Reverzní proxy server může přesměrovat práci, jako je například obsluhující statický obsah, požadavky na ukládání do mezipaměti, komprimace požadavků a ukončení HTTPS ze serveru HTTP. Reverzní proxy server může být umístěná na vyhrazeném počítači nebo může být nasazena spolu s HTTP serverem.

Pro účely tohoto průvodce se používá jedna instance Nginx. Spouští se na stejném serveru společně se serverem HTTP. Na základě požadavků může být zvoleno jiné nastavení.

Vzhledem k tomu, že požadavky jsou předávány reverzním proxy, použijte [middleware předávaných hlaviček](xref:host-and-deploy/proxy-load-balancer) z balíčku [Microsoft. AspNetCore. HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) . Middleware aktualizuje `Request.Scheme` `X-Forwarded-Proto` pomocí hlavičky, aby identifikátory URI pro přesměrování a další zásady zabezpečení fungovaly správně.

Po vyvolání middlewaru předávaných hlaviček musí být všechny komponenty, které jsou závislé na schématu, jako je ověřování, generace odkazů, přesměrování a zeměpisná poloha, umístěny. Jako obecné pravidlo by měl middleware předaných hlaviček běžet před jiným middlewarem, kromě diagnostiky a middlewaru pro zpracování chyb. Toto řazení zajišťuje, aby middleware spoléhající se na předané informace hlaviček mohl spotřebovat hodnoty hlaviček pro zpracování.

Volejte metodu v `Startup.Configure` před voláním <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> nebo podobným middlewarem schématu ověřování. <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> Nakonfigurujte middleware pro `X-Forwarded-For` přeposílání `X-Forwarded-Proto` hlaviček a:

```csharp
app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

Pokud pro <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> middleware nejsou zadány žádné, výchozí hlavičky budou `None`předány.

Proxy servery běžící na adresách zpětné smyčky (127.0.0.0/8, [:: 1]), včetně standardní adresy localhost (127.0.0.1), jsou ve výchozím nastavení důvěryhodné. Pokud jiné důvěryhodné proxy servery nebo sítě v rámci organizace zařídí žádosti mezi Internetem a webovým serverem, přidejte je do seznamu <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> nebo <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> s <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>. Následující příklad přidá důvěryhodnou proxy server na IP adrese 10.0.0.100 do middlewaru `KnownProxies` předávaných hlaviček v: `Startup.ConfigureServices`

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

Další informace naleznete v tématu <xref:host-and-deploy/proxy-load-balancer>.

### <a name="install-nginx"></a>Nainstalovat Nginx

Použijte `apt-get` k instalaci Nginx. Instalační program vytvoří *systémový* skript init, který spustí Nginx jako démon při spuštění systému. Postupujte podle pokynů k instalaci Ubuntu na [adrese Nginx: Oficiální balíčky](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages)Debian/Ubuntu.

> [!NOTE]
> Pokud jsou vyžadovány Volitelné moduly Nginx, může být nutné sestavit Nginx ze zdroje.

Vzhledem k tomu, že se Nginx nainstaloval poprvé, explicitně ho spusťte spuštěním:

```bash
sudo service nginx start
```

Ověřte, že prohlížeč zobrazuje výchozí cílovou stránku pro Nginx. Cílová stránka je dostupná na adrese `http://<server_IP_address>/index.nginx-debian.html`.

### <a name="configure-nginx"></a>Konfigurace nginx

Pokud chcete nakonfigurovat Nginx jako reverzní proxy server pro přeposílání požadavků do vaší aplikace ASP.NET Core, upravte */etc/Nginx/sites-available/default*. Otevřete ho v textovém editoru a nahraďte jeho obsah následujícím textem:

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

Pokud se `server_name` neshodují, Nginx použije výchozí server. Pokud není definován žádný výchozí server, je první server v konfiguračním souboru výchozím serverem. Osvědčeným postupem je přidat konkrétní výchozí server, který vrátí stavový kód 444 do konfiguračního souboru. Výchozím příkladem konfigurace serveru je:

```nginx
server {
    listen   80 default_server;
    # listen [::]:80 default_server deferred;
    return   444;
}
```

Pomocí předchozího konfiguračního souboru a výchozího serveru Nginx akceptuje veřejný provoz na portu 80 s hlavičkou `example.com` hostitele nebo. `*.example.com` Požadavky, které se neshodují s těmito hostiteli, se nebudou přesílat na Kestrel. Nginx přepošle požadavky na Kestrel `http://localhost:5000`na. Další informace najdete v tématu [jak Nginx zpracovává požadavek](https://nginx.org/docs/http/request_processing.html) . Pokud chcete změnit IP adresu/port Kestrel, [Přečtěte si téma Kestrel: Konfigurace](xref:fundamentals/servers/kestrel#endpoint-configuration)koncového bodu.

> [!WARNING]
> Nepovedlo se určit správnou [direktivu název_serveru](https://nginx.org/docs/http/server_names.html) , kterou vaše aplikace vystavuje chybám zabezpečení. Vazba zástupných znaků subdomény ( `*.example.com`například) nepředstavuje toto bezpečnostní riziko `*.com`, pokud řídíte celou nadřazenou doménu (na rozdíl od, která je zranitelná). Zobrazit [rfc7230 části-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) Další informace.

Po navázání konfigurace nginx spusťte `sudo nginx -t` příkaz a ověřte syntaxi konfiguračních souborů. Pokud je test konfiguračního souboru úspěšný, vynutit Nginx změny spuštěním `sudo nginx -s reload`.

Postup při přímém spuštění aplikace na serveru:

1. Přejděte do adresáře aplikace.
1. Spusťte aplikaci: `dotnet <app_assembly.dll>`, kde `app_assembly.dll` je název souboru sestavení aplikace.

Pokud aplikace běží na serveru, ale neodpoví přes Internet, zkontrolujte bránu firewall serveru a ověřte, že je port 80 otevřený. Pokud používáte virtuální počítač Azure Ubuntu, přidejte pravidlo skupiny zabezpečení sítě (NSG), které umožňuje příchozí provoz portu 80. Není nutné povolit odchozí pravidlo portu 80, protože odchozí přenosy jsou automaticky uděleny, když je povolené příchozí pravidlo.

Po dokončení testování aplikace ukončete aplikaci `Ctrl+C` v příkazovém řádku.

## <a name="monitor-the-app"></a>Monitorování aplikace

Server je nastavený tak, aby předal požadavky `http://<serveraddress>:80` na aplikaci ASP.NET Core běžící na Kestrel v. `http://127.0.0.1:5000` Nginx ale není nastavené na správu procesu Kestrel. *systém* lze použít k vytvoření souboru služby ke spuštění a sledování základní webové aplikace. *systém* je systémem init, který poskytuje mnoho výkonných funkcí pro spouštění, zastavování a správu procesů. 

### <a name="create-the-service-file"></a>Vytvoření souboru služby

Vytvořte definiční soubor služby:

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

Následuje příklad souboru služby pro aplikaci:

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

Pokud uživatel *Webová data* nepoužívá v konfiguraci, musí se nejdřív vytvořit uživatel, který je tady definovaný, a mít k správné vlastnictví souborů.

Slouží `TimeoutStopSec` ke konfiguraci časového intervalu, po který se má čekat na vypnutí aplikace po přijetí počátečního signálu přerušení. Pokud se aplikace v tomto období neukončí, SIGKILL se vydá pro ukončení aplikace. Zadejte hodnotu jako nejednotkové sekundy (například `150`), hodnotu časového rozsahu ( `2min 30s`například) nebo `infinity` zakažte časový limit. `TimeoutStopSec``DefaultTimeoutStopSec` ve výchozím nastavení se jedná o hodnotu v konfiguračním souboru správce (*System-System. conf*, *System. conf. d*, *systemd-User. conf*, *User. conf. d*). Výchozí časový limit pro většinu distribucí je 90 sekund.

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

Linux má systém souborů s rozlišováním velkých a malých písmen. Když nanastavíte ASPNETCORE_ENVIRONMENT na "produkční", výsledky hledání konfiguračního souboru *appSettings. Produkční. JSON*, nikoli *appSettings. produkční. JSON*.

Některé hodnoty (například připojovací řetězce SQL) musí být uvozené řídicími znaky, aby poskytovatelé konfigurace mohli číst proměnné prostředí. Pomocí následujícího příkazu vygenerujte správně uvozenou hodnotu pro použití v konfiguračním souboru:

```console
systemd-escape "<value-to-escape>"
```

Oddělovače`:`dvojtečky () nejsou podporovány v názvech proměnných prostředí. Místo dvojtečky použijte dvojité podtržítko (`__`). [Poskytovatel konfigurace proměnných prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider) převádí dvojitá podtržítka na dvojtečky, když jsou proměnné prostředí čteny do konfigurace. V následujícím příkladu je klíč `ConnectionStrings:DefaultConnection` připojovacího řetězce nastaven do souboru definice služby jako: `ConnectionStrings__DefaultConnection`

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

Pomocí systému reverzního proxy serveru nakonfigurovaného a Kestrel spravovaného přes systém je webová aplikace plně nakonfigurovaná a dá se k ní dostat z prohlížeče v `http://localhost`místním počítači. Je dostupná taky ze vzdáleného počítače a znemožňuje bránu firewall, která může být zablokovaná. Při kontrole hlaviček odpovědi se v `Server` hlavičce zobrazuje ASP.NET Core aplikace, kterou obsluhuje Kestrel.

```text
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a>Zobrazení protokolů

Vzhledem k tomu, že webová aplikace využívající Kestrel `systemd`je spravovaná pomocí, jsou všechny události a procesy protokolovány do centralizovaného deníku. Tento deník ale obsahuje všechny položky pro všechny služby a procesy, které `systemd`spravuje. Chcete `kestrel-helloapp.service`-li zobrazit položky specifické pro zobrazení, použijte následující příkaz:

```bash
sudo journalctl -fu kestrel-helloapp.service
```

Pro další filtrování, časová omezení, `--since today`jako `--until 1 hour ago` například, nebo kombinace těchto možností může snížit množství vrácených položek.

```bash
sudo journalctl -fu kestrel-helloapp.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="data-protection"></a>Ochrana dat

[Sada ASP.NET Core Data Protection Stack](xref:security/data-protection/introduction) je používána několika ASP.NET Core [middlewary](xref:fundamentals/middleware/index), včetně middlewaru ověřování (například middleware souborů cookie) a ochrany proti padělání žádostí mezi weby (CSRF). I v případě, že rozhraní API ochrany dat nejsou volána uživatelským kódem, je třeba chránit data, aby bylo možné vytvořit trvalé úložiště kryptografických [klíčů](xref:security/data-protection/implementation/key-management). Pokud není nakonfigurovaná ochrana dat, jsou klíče uložené v paměti a při restartování aplikace.

Pokud kanál klíče jsou uloženy v paměti, při restartování aplikace:

* Všechny tokeny ověřování na základě souborů cookie nejsou zneplatněny.
* Uživatelé se musí znovu přihlásit v jejich další požadavek.
* Všechna data chráněná pomocí aktualizační kanál, který klíč můžete už nebude možné dešifrovat. To může zahrnovat [CSRF tokeny](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) a [soubory cookie v ASP.NET Core MVC TempData](xref:fundamentals/app-state#tempdata).

Pokud chcete nakonfigurovat ochranu dat, aby zachovala a zašifroval klíč Ring, přečtěte si:

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>

## <a name="long-request-header-fields"></a>Pole hlavičky dlouhé žádosti

Pokud aplikace vyžaduje pole hlaviček požadavku delší, než je povolené ve výchozím nastavení proxy server (obvykle se 4K nebo 8K v závislosti na platformě), vyžadují následující direktivy úpravu. Hodnoty, které se mají použít, jsou závislé na scénáři. Další informace najdete v dokumentaci k vašemu serveru.

* [proxy_buffer_size](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffer_size)
* [proxy_buffers](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffers)
* [proxy_busy_buffers_size](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_busy_buffers_size)
* [large_client_header_buffers](https://nginx.org/docs/http/ngx_http_core_module.html#large_client_header_buffers)

> [!WARNING]
> Nerozšiřujte výchozí hodnoty vyrovnávací paměti proxy, pokud je to nutné. Zvýšení těchto hodnot zvyšuje riziko přetečení vyrovnávací paměti (přetečení) a útok DoS (Denial of Service) uživateli se zlými úmysly.

## <a name="secure-the-app"></a>Zabezpečení aplikace

### <a name="enable-apparmor"></a>Povolit AppArmor

Moduly zabezpečení Linux (LSM) jsou rozhraní, které je součástí jádra Linux od verze Linux 2,6. LSM podporuje různé implementace modulů zabezpečení. [AppArmor](https://wiki.ubuntu.com/AppArmor) je lsm, který implementuje povinný Access Control systém, který umožňuje programu confining program na omezené sady prostředků. Ujistěte se, že je povolená a správně nakonfigurovaná možnost AppArmor.

### <a name="configure-the-firewall"></a>Konfigurace brány firewall

Zavřete všechny externí porty, které se nepoužívají. Nesložitá brána firewall (UFW) poskytuje front- `iptables` end pro poskytování rozhraní příkazového řádku pro konfiguraci brány firewall.

> [!WARNING]
> Brána firewall zabrání přístupu k celému systému, pokud není správně nakonfigurovaný. Pokud se k připojení použijete přes SSH, nebudete moct zadat správný port SSH. Výchozí port je 22. Další informace najdete v [úvodu k UFW](https://help.ubuntu.com/community/UFW) a [příručce](https://manpages.ubuntu.com/manpages/bionic/man8/ufw.8.html).

Nainstalujte `ufw` a nakonfigurujte ho tak, aby povoloval přenosy na všech potřebných portech.

```bash
sudo apt-get install ufw

sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

sudo ufw enable
```

### <a name="secure-nginx"></a>Zabezpečení Nginx

#### <a name="change-the-nginx-response-name"></a>Změnit název odpovědi Nginx

Edit *src/http/ngx_http_header_filter_module.c*:

```
static char ngx_http_server_string[] = "Server: Web Server" CRLF;
static char ngx_http_server_full_string[] = "Server: Web Server" CRLF;
```

#### <a name="configure-options"></a>Konfigurovat možnosti

Nakonfigurujte server s dalšími požadovanými moduly. Zvažte použití brány firewall webových aplikací, jako je například [ModSecurity](https://www.modsecurity.org/), k posílení aplikace.

#### <a name="https-configuration"></a>Konfigurace HTTPS

**Konfigurace místních připojení (HTTPS) aplikace pro zabezpečení**

Příkaz [dotnet Run](/dotnet/core/tools/dotnet-run) používá soubor *Properties/launchSettings. JSON* aplikace, který nakonfiguruje aplikaci tak, aby naslouchala adresám URL `applicationUrl` poskytnutým vlastností (například `https://localhost:5001; http://localhost:5000`).

Nakonfigurujte aplikaci tak, aby používala certifikát ve vývoji pro `dotnet run` příkazové nebo vývojové prostředí (F5 nebo CTRL + F5 v Visual Studio Code), a to pomocí jednoho z následujících přístupů:

* [Nahradit výchozí certifikát z konfigurace](xref:fundamentals/servers/kestrel#configuration) (*Doporučeno*)
* [KestrelServerOptions.ConfigureHttpsDefaults](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

**Konfigurace připojení klienta reverzního proxy serveru pro zabezpečení (HTTPS)**

* Nakonfigurujte server tak, aby naslouchal provozu protokolu HTTPS na `443` portu tím, že zadáte platný certifikát vydaný důvěryhodnou certifikační autoritou (CA).

* Posílit zabezpečení tím, že se využívaly některé postupy, které jsou znázorněné v následujícím souboru */etc/Nginx/Nginx.conf* . Mezi příklady patří výběr silnější šifry a přesměrování veškerého provozu přes protokol HTTP na HTTPS.

* Přidáním hlavičky `HTTP Strict-Transport-Security` (HSTS) se zajistí, že všechny následné požadavky, které klient provede, budou přes protokol HTTPS.

* Nepřidejte hlavičku HSTS ani zvolte vhodné `max-age` , pokud bude HTTPS v budoucnu zakázané.

Přidejte konfigurační soubor */etc/Nginx/proxy.conf* :

[!code-nginx[](linux-nginx/proxy.conf)]

Upravte konfigurační soubor */etc/Nginx/Nginx.conf* . Příklad obsahuje i `http` `server` oddíly v jednom konfiguračním souboru.

[!code-nginx[](linux-nginx/nginx.conf?highlight=2)]

#### <a name="secure-nginx-from-clickjacking"></a>Zabezpečení Nginx z clickjacking

[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), označovaný také jako *útok s opravou uživatelského rozhraní*, je škodlivý útok, při kterém návštěvník webu získá odkaz nebo tlačítko na jiné stránce, než se právě navštíví. Slouží `X-FRAME-OPTIONS` k zabezpečení lokality.

Zmírnění útoků Clickjacking:

1. Upravte soubor *Nginx. conf* :

   ```bash
   sudo nano /etc/nginx/nginx.conf
   ```

   Přidejte řádek `add_header X-Frame-Options "SAMEORIGIN";`.
1. Uložte soubor.
1. Restartujte Nginx.

#### <a name="mime-type-sniffing"></a>Sledování typu MIME

Toto záhlaví brání většině prohlížečů ze služby MIME-sledovat odpověď od deklarovaného typu obsahu, protože záhlaví instruuje prohlížeč, že nepřepisuje typ obsahu odpovědi. `nosniff` Pokud server říká, že je obsahem text/HTML, prohlížeč ho vykreslí jako text/HTML.

Upravte soubor *Nginx. conf* :

```bash
sudo nano /etc/nginx/nginx.conf
```

Přidejte řádek `add_header X-Content-Type-Options "nosniff";` a uložte soubor a pak Nginx restartujte.

## <a name="additional-resources"></a>Další zdroje

* [Předpoklady pro .NET Core v systému Linux](/dotnet/core/linux-prerequisites)
* [Nginx Binární verze: Oficiální balíčky Debian/Ubuntu](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
* [NGINX Použití předané hlavičky](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)

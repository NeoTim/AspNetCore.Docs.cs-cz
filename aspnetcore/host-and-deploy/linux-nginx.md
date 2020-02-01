---
title: Hostování ASP.NET Core v systému Linux pomocí Nginx
author: guardrex
description: Naučte se, jak nastavit Nginx jako reverzní proxy na Ubuntu 16,04 pro přeposílání provozu HTTP do ASP.NET Core webové aplikace běžící na Kestrel.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
uid: host-and-deploy/linux-nginx
ms.openlocfilehash: 1a83b7d1b211862793e3ba086234b97248f9ae70
ms.sourcegitcommit: 0b0e485a8a6dfcc65a7a58b365622b3839f4d624
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2020
ms.locfileid: "76928491"
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
   1. Navštivte [stránku stáhnout jádro .NET Core](https://dotnet.microsoft.com/download/dotnet-core).
   1. Vyberte nejnovější verzi rozhraní .NET Core, která není ve verzi Preview.
   1. Stáhněte si nejnovější modul runtime bez verze Preview v tabulce v části **Spustit aplikace – modul runtime**.
   1. Vyberte odkaz **pokyny správce balíčků** pro Linux a postupujte podle pokynů pro Ubuntu pro vaši verzi Ubuntu.
1. Existující aplikace ASP.NET Core.

V jakémkoli okamžiku v budoucnu po upgradu sdílené architektury restartujte aplikace ASP.NET Core hostované serverem.

## <a name="publish-and-copy-over-the-app"></a>Publikování a kopírování přes aplikaci

Nakonfigurujte aplikaci pro [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd).

Pokud je aplikace spuštěná místně a není nakonfigurovaná tak, aby přijímala zabezpečené připojení (HTTPS), proveďte jednu z následujících metod:

* Nakonfigurujte aplikaci tak, aby zpracovávala Zabezpečená místní připojení. Další informace najdete v části [konfigurace https](#https-configuration) .
* Z vlastnosti `applicationUrl` v souboru *Properties/launchSettings. JSON* odeberte `https://localhost:5001` (Pokud je k dispozici).

Spuštěním [dotnet Publish](/dotnet/core/tools/dotnet-publish) z vývojového prostředí zabalíte aplikaci do adresáře (například *bin/Release/&lt;target_framework_moniker&gt;/Publish*), která se dají spustit na serveru:

```dotnetcli
dotnet publish --configuration Release
```

Pokud nechcete zachovat modul runtime .NET Core na serveru, můžete aplikaci publikovat také jako samostatné [nasazení](/dotnet/core/deploying/#self-contained-deployments-scd) .

Zkopírujte aplikaci ASP.NET Core na server pomocí nástroje, který se integruje do pracovního postupu organizace (například SCP, SFTP). Je běžné najít webové aplikace v adresáři *var* (například *var/www/helloapp*).

> [!NOTE]
> V rámci scénáře nasazení v produkčním prostředí provádí pracovní postup průběžné integrace publikování aplikace a zkopírování prostředků na server.

Testování aplikace:

1. Z příkazového řádku spusťte aplikaci: `dotnet <app_assembly>.dll`.
1. V prohlížeči přejděte na `http://<serveraddress>:<port>` a ověřte, že aplikace funguje na Linux místně.

## <a name="configure-a-reverse-proxy-server"></a>Konfigurace reverzního proxy server

Reverzní proxy je běžné nastavení pro obsluhu dynamických webových aplikací. Reverzní proxy ukončí požadavek HTTP a předá ho do aplikace ASP.NET Core.

### <a name="use-a-reverse-proxy-server"></a>Použít reverzní proxy server

Kestrel je ideální pro obsluhu dynamického obsahu z ASP.NET Core. Webový server, který obsluhuje možnosti, ale není jako funkce bohatě funkční jako servery jako IIS, Apache nebo Nginx. Reverzní proxy server může přesměrovat práci, jako je například obsluhující statický obsah, požadavky na ukládání do mezipaměti, komprimace požadavků a ukončení HTTPS ze serveru HTTP. Reverzní proxy server může být umístěná na vyhrazeném počítači nebo může být nasazena spolu s HTTP serverem.

Pro účely tohoto průvodce se používá jedna instance Nginx. Spouští se na stejném serveru společně se serverem HTTP. Na základě požadavků může být zvoleno jiné nastavení.

Vzhledem k tomu, že požadavky jsou předávány reverzním proxy, použijte [middleware předávaných hlaviček](xref:host-and-deploy/proxy-load-balancer) z balíčku [Microsoft. AspNetCore. HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) . Middleware aktualizuje `Request.Scheme`pomocí hlavičky `X-Forwarded-Proto`, aby identifikátory URI pro přesměrování a další zásady zabezpečení fungovaly správně.

Po vyvolání middlewaru předávaných hlaviček musí být všechny komponenty, které jsou závislé na schématu, jako je ověřování, generace odkazů, přesměrování a zeměpisná poloha, umístěny. Jako obecné pravidlo by měl middleware předaných hlaviček běžet před jiným middlewarem, kromě diagnostiky a middlewaru pro zpracování chyb. Toto řazení zajišťuje, aby middleware spoléhající se na předané informace hlaviček mohl spotřebovat hodnoty hlaviček pro zpracování.

Vyvolejte metodu <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> v `Startup.Configure` před voláním <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> nebo podobného middlewaru schématu ověřování. Nakonfigurujte middleware pro přeposílání `X-Forwarded-For` a `X-Forwarded-Proto` hlavičky:

```csharp
app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

Pokud pro middlewari nejsou zadány žádné <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>, jsou výchozí hlavičky pro přeposílání `None`.

Proxy servery běžící na adresách zpětné smyčky (127.0.0.0/8, [:: 1]), včetně standardní adresy localhost (127.0.0.1), jsou ve výchozím nastavení důvěryhodné. Pokud jiné důvěryhodné proxy servery nebo sítě v rámci organizace zařídí žádosti mezi Internetem a webovým serverem, přidejte je do seznamu <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> nebo <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> pomocí <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>. Následující příklad přidá důvěryhodnou proxy server na IP adresu 10.0.0.100 k `KnownProxies` middlewaru s přesměrovanými hlavičkami v `Startup.ConfigureServices`:

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

Další informace najdete v tématu <xref:host-and-deploy/proxy-load-balancer>.

### <a name="install-nginx"></a>Nainstalovat Nginx

K instalaci Nginx použijte `apt-get`. Instalační program vytvoří *systémový* skript init, který spustí Nginx jako démon při spuštění systému. Postupujte podle pokynů k instalaci Ubuntu na [Nginx: oficiální balíčky Debian/Ubuntu](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages).

> [!NOTE]
> Pokud jsou vyžadovány Volitelné moduly Nginx, může být nutné sestavit Nginx ze zdroje.

Vzhledem k tomu, že se Nginx nainstaloval poprvé, explicitně ho spusťte spuštěním:

```bash
sudo service nginx start
```

Ověřte, že prohlížeč zobrazuje výchozí cílovou stránku pro Nginx. Cílová stránka je dosažitelná na `http://<server_IP_address>/index.nginx-debian.html`.

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

Pokud je aplikace Blazor serverová aplikace, která spoléhá na WebSockets signálu, přečtěte si téma <xref:host-and-deploy/blazor/server#linux-with-nginx>, kde najdete informace o tom, jak nastavit `Connection` hlavičku.

Pokud se neshodují `server_name`, Nginx použije výchozí server. Pokud není definován žádný výchozí server, je první server v konfiguračním souboru výchozím serverem. Osvědčeným postupem je přidat konkrétní výchozí server, který vrátí stavový kód 444 do konfiguračního souboru. Výchozím příkladem konfigurace serveru je:

```nginx
server {
    listen   80 default_server;
    # listen [::]:80 default_server deferred;
    return   444;
}
```

S předchozím konfiguračním souborem a výchozím serverem Nginx přijímá veřejný provoz na portu 80 s hlavičkou hostitele `example.com` nebo `*.example.com`. Požadavky, které se neshodují s těmito hostiteli, se nebudou přesílat na Kestrel. Nginx přepošle požadavky na Kestrel na `http://localhost:5000`. Další informace najdete v tématu [jak Nginx zpracovává požadavek](https://nginx.org/docs/http/request_processing.html) . Pokud chcete změnit IP adresu/port Kestrel, přečtěte si téma [Kestrel: konfigurace koncového bodu](xref:fundamentals/servers/kestrel#endpoint-configuration).

> [!WARNING]
> Nepovedlo se určit správnou [direktivu server_name](https://nginx.org/docs/http/server_names.html) , kterou vaše aplikace zpřístupňuje bezpečnostním hrozbám. Vazba zástupných znaků subdomény (například `*.example.com`) nepředstavuje toto bezpečnostní riziko, pokud ovládáte celou nadřazenou doménu (na rozdíl od `*.com`, která je zranitelná). Zobrazit [rfc7230 části-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) Další informace.

Po navázání konfigurace nginx spusťte `sudo nginx -t` a ověřte syntaxi konfiguračních souborů. Pokud je test konfiguračního souboru úspěšný, vynutí Nginx, aby se změny vybraly spuštěním `sudo nginx -s reload`.

Postup při přímém spuštění aplikace na serveru:

1. Přejděte do adresáře aplikace.
1. Spusťte aplikaci: `dotnet <app_assembly.dll>`, kde `app_assembly.dll` je název souboru sestavení aplikace.

Pokud aplikace běží na serveru, ale neodpoví přes Internet, zkontrolujte bránu firewall serveru a ověřte, že je port 80 otevřený. Pokud používáte virtuální počítač Azure Ubuntu, přidejte pravidlo skupiny zabezpečení sítě (NSG), které umožňuje příchozí provoz portu 80. Není nutné povolit odchozí pravidlo portu 80, protože odchozí přenosy jsou automaticky uděleny, když je povolené příchozí pravidlo.

Po dokončení testování aplikace ukončete aplikaci pomocí `Ctrl+C` na příkazovém řádku.

## <a name="monitor-the-app"></a>Monitorování aplikace

Server je nastavený tak, aby předal požadavky na `http://<serveraddress>:80` do ASP.NET Core aplikace běžící na Kestrel na `http://127.0.0.1:5000`. Nginx ale není nastavené na správu procesu Kestrel. *systém* lze použít k vytvoření souboru služby ke spuštění a sledování základní webové aplikace. *systém* je systémem init, který poskytuje mnoho výkonných funkcí pro spouštění, zastavování a správu procesů. 

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

V předchozím příkladu je uživatel, který spravuje službu, určen pomocí možnosti `User`. Uživatel (`www-data`) musí existovat a mít správné vlastnictví souborů aplikace.

Pomocí `TimeoutStopSec` můžete nastavit dobu, po kterou se má čekat na vypnutí aplikace po přijetí počátečního signálu přerušení. Pokud se aplikace v tomto období neukončí, SIGKILL se vydá pro ukončení aplikace. Zadejte hodnotu jako nejednotkové sekundy (například `150`), hodnotu časového rozsahu (například `2min 30s`) nebo `infinity`, aby byl časový limit zakázán. `TimeoutStopSec` výchozí hodnota `DefaultTimeoutStopSec` v konfiguračním souboru správce (*systemed-System. conf*, *System. conf. d*, *systemd-User. conf*, *User. conf. d*). Výchozí časový limit pro většinu distribucí je 90 sekund.

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

Linux má systém souborů s rozlišováním velkých a malých písmen. Když se nastaví ASPNETCORE_ENVIRONMENT k produkci, vyhledá se konfigurační soubor *appSettings. Produkční. JSON*, nikoli *appSettings. produkční. JSON*.

Některé hodnoty (například připojovací řetězce SQL) musí být uvozené řídicími znaky, aby poskytovatelé konfigurace mohli číst proměnné prostředí. Pomocí následujícího příkazu vygenerujte správně uvozenou hodnotu pro použití v konfiguračním souboru:

```console
systemd-escape "<value-to-escape>"
```

Oddělovače dvojtečky (`:`) nejsou v názvech proměnných prostředí podporovány. Místo dvojtečky použijte dvojité podtržítko (`__`). [Poskytovatel konfigurace proměnných prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider) převádí dvojitá podtržítka na dvojtečky, když jsou proměnné prostředí čteny do konfigurace. V následujícím příkladu je klíč připojovacího řetězce `ConnectionStrings:DefaultConnection` v definičním souboru služby nastaven jako `ConnectionStrings__DefaultConnection`:

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

Když je server reverzní proxy nakonfigurovaný a Kestrel spravovaný prostřednictvím systému, je webová aplikace plně nakonfigurovaná a dá se k nim dostat z prohlížeče v místním počítači na `http://localhost`. Je dostupná taky ze vzdáleného počítače a znemožňuje bránu firewall, která může být zablokovaná. Při kontrole hlaviček odpovědí se v hlavičce `Server` zobrazuje ASP.NET Core aplikace, kterou obsluhuje Kestrel.

```text
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a>Zobrazit protokoly

Vzhledem k tomu, že je webová aplikace používající Kestrel spravovaná pomocí `systemd`, všechny události a procesy se zaprotokolují do centralizovaného deníku. Tento deník ale obsahuje všechny položky pro všechny služby a procesy spravované pomocí `systemd`. Chcete-li zobrazit položky specifické pro `kestrel-helloapp.service`, použijte následující příkaz:

```bash
sudo journalctl -fu kestrel-helloapp.service
```

Pro další filtrování, časová nastavení, například `--since today`, `--until 1 hour ago` nebo kombinace těchto možností může snížit množství vrácených položek.

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

Výchozí nastavení proxy serveru obvykle omezuje pole hlaviček požadavku na 4 KB nebo 8 KB v závislosti na platformě. Aplikace může vyžadovat pole delší než výchozí (například aplikace, které používají [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)). Pokud jsou požadována delší pole, je výchozí nastavení proxy server vyžadovat úpravu. Hodnoty, které mají být použity, závisí na scénáři. Další informace najdete v dokumentaci k vašemu serveru.

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

Zavřete všechny externí porty, které se nepoužívají. Nesložitá brána firewall (UFW) poskytuje front-end pro `iptables` tím, že poskytuje rozhraní příkazového řádku pro konfiguraci brány firewall.

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

Příkaz příkazového řádku [dotnet](/dotnet/core/tools/dotnet-run) používá soubor *Properties/launchSettings. JSON* aplikace, který nakonfiguruje aplikaci tak, aby naslouchala adresám url poskytnutým vlastností `applicationUrl` (například `https://localhost:5001; http://localhost:5000`).

Nakonfigurujte aplikaci tak, aby používala certifikát ve vývoji pro příkaz `dotnet run` nebo vývojové prostředí (F5 nebo CTRL + F5 v Visual Studio Code) pomocí jednoho z následujících přístupů:

* [Nahradit výchozí certifikát z konfigurace](xref:fundamentals/servers/kestrel#configuration) (*doporučeno*)
* [KestrelServerOptions.ConfigureHttpsDefaults](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

**Konfigurace připojení klienta reverzního proxy serveru pro zabezpečení (HTTPS)**

* Nakonfigurujte server tak, aby naslouchal provozu protokolu HTTPS na portu `443` tím, že zadáte platný certifikát vydaný důvěryhodnou certifikační autoritou (CA).

* Posílit zabezpečení tím, že se využívaly některé postupy, které jsou znázorněné v následujícím souboru */etc/Nginx/Nginx.conf* . Mezi příklady patří výběr silnější šifry a přesměrování veškerého provozu přes protokol HTTP na HTTPS.

* Přidáním hlavičky `HTTP Strict-Transport-Security` (HSTS) se zajistí, že všechny následné požadavky, které klient provede, budou přes protokol HTTPS.

* Nepřidejte hlavičku HSTS ani zvolte vhodný `max-age`, pokud bude v budoucnu zakázán protokol HTTPS.

Přidejte konfigurační soubor */etc/Nginx/proxy.conf* :

[!code-nginx[](linux-nginx/proxy.conf)]

Upravte konfigurační soubor */etc/Nginx/Nginx.conf* . Příklad obsahuje oddíly `http` a `server` v jednom konfiguračním souboru.

[!code-nginx[](linux-nginx/nginx.conf?highlight=2)]

#### <a name="secure-nginx-from-clickjacking"></a>Zabezpečení Nginx z clickjacking

[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), označovaný také jako *útok s opravou uživatelského rozhraní*, je škodlivý útok, při kterém návštěvník webu získá odkaz nebo tlačítko na jiné stránce, než se právě navštíví. K zabezpečení webu použijte `X-FRAME-OPTIONS`.

Zmírnění útoků Clickjacking:

1. Upravte soubor *Nginx. conf* :

   ```bash
   sudo nano /etc/nginx/nginx.conf
   ```

   Přidejte `add_header X-Frame-Options "SAMEORIGIN";`řádku.
1. Uložte soubor.
1. Restartujte Nginx.

#### <a name="mime-type-sniffing"></a>Sledování typu MIME

Toto záhlaví brání většině prohlížečů ze služby MIME-sledovat odpověď od deklarovaného typu obsahu, protože záhlaví instruuje prohlížeč, že nepřepisuje typ obsahu odpovědi. Pokud je v případě, že server říká, že obsah je text/HTML, prohlížeč je vykreslí jako text/HTML. `nosniff`

Upravte soubor *Nginx. conf* :

```bash
sudo nano /etc/nginx/nginx.conf
```

Přidejte `add_header X-Content-Type-Options "nosniff";` řádku a uložte soubor a pak restartujte Nginx.

## <a name="additional-nginx-suggestions"></a>Další návrhy Nginx

Po upgradu sdílené architektury na serveru restartujte aplikace ASP.NET Core hostované serverem.

## <a name="additional-resources"></a>Další materiály a zdroje informací

* [Předpoklady pro .NET Core v systému Linux](/dotnet/core/linux-prerequisites)
* [Nginx: binární verze: oficiální balíčky Debian/Ubuntu](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
* [NGINX: používá se předávaná hlavička.](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)

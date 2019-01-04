---
title: Hostitele ASP.NET Core v Linuxu se serverem Nginx
author: rick-anderson
description: Další informace o nastavení serveru Nginx jako reverzní proxy server na Ubuntu 16.04 směrovat provoz protokolu HTTP k webové aplikaci ASP.NET Core spuštěnou v prostředí Kestrel.
ms.author: riande
ms.custom: mvc
ms.date: 12/20/2018
uid: host-and-deploy/linux-nginx
ms.openlocfilehash: 534c62c127e685af9c6076932943def25bd3ac06
ms.sourcegitcommit: e1cc4c1ef6c9e07918a609d5ad7fadcb6abe3e12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2019
ms.locfileid: "53997328"
---
# <a name="host-aspnet-core-on-linux-with-nginx"></a>Hostitele ASP.NET Core v Linuxu se serverem Nginx

Podle [Sourabh Shirhatti](https://twitter.com/sshirhatti)

Tato příručka vysvětluje nastavení prostředí připravené pro produkční prostředí ASP.NET Core na serveru se systémem Ubuntu 16.04. Tyto pokyny mohou pracovat s novějšími verzemi Ubuntu, ale pokynů nebyly testovány s novější verzí.

Informace v jiné distribuce Linuxu podporuje ASP.NET Core najdete v tématu [předpoklady pro .NET Core v Linuxu](/dotnet/core/linux-prerequisites).

> [!NOTE]
> Pro Ubuntu 14.04 *supervisord* jako řešení pro monitorování procesu Kestrel se doporučuje. *systemd* není k dispozici na Ubuntu 14.04. Ubuntu 14.04 pokyny najdete v tématu [předchozí verzi tohoto tématu](https://github.com/aspnet/Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md).

Tento průvodce:

* Umístí stávající aplikace ASP.NET Core za reverzní proxy server.
* Nastaví předávat požadavky na webový server Kestrel reverzního proxy serveru.
* Zajišťuje, že webová aplikace spuštěna při spuštění jako démon.
* Konfiguruje nástroj pro správu proces usnadňují, restartujte webovou aplikaci.

## <a name="prerequisites"></a>Požadavky

1. Přístup k serveru se systémem Ubuntu 16.04 pomocí standardního uživatelského účtu s oprávněními sudo.
1. Nainstalujte modul runtime .NET Core na serveru.
   1. Přejděte [.NET Core všechny soubory ke stažení stránky](https://www.microsoft.com/net/download/all).
   1. Vyberte nejnovější modul runtime – ve verzi preview ze seznamu **Runtime**.
   1. Vyberte a postupujte podle pokynů pro Ubuntu, které odpovídají verze Ubuntu serveru.
1. Stávající aplikace ASP.NET Core.

## <a name="publish-and-copy-over-the-app"></a>Publikování a zkopírujte myší na aplikaci

Konfigurace aplikace pro [nasazení závisí na architektuře](/dotnet/core/deploying/#framework-dependent-deployments-fdd).

Spustit [dotnet publikovat](/dotnet/core/tools/dotnet-publish) z vývojového prostředí pro balíček aplikace do adresáře (například *bin/Release/&lt;target_framework_moniker&gt;/ publish*), který můžete Spusťte na serveru:

```console
dotnet publish --configuration Release
```

Aplikace můžete také publikovat jako [samostatná nasazení](/dotnet/core/deploying/#self-contained-deployments-scd) Pokud nechcete zachovat modulu runtime .NET Core na serveru.

Aplikace ASP.NET Core zkopírujte na server pomocí nástroje, které se integruje do pracovního postupu organizace (třeba spojovací bod služby, SFTP). Je běžné vyhledejte webové aplikace v rámci *var* adresář (třeba *www/var/helloapp*).

> [!NOTE]
> V případě produkčního nasazení pracovního postupu průběžné integrace funguje publikování aplikace a kopírování prostředky na server.

Testování aplikace:

1. Z příkazového řádku, spusťte aplikaci: `dotnet <app_assembly>.dll`.
1. V prohlížeči přejděte na `http://<serveraddress>:<port>` k ověření, že aplikace funguje na platformě Linux místně.

## <a name="configure-a-reverse-proxy-server"></a>Konfigurace reverzního proxy serveru

Reverzní proxy server je společné nastavení pro poskytování dynamické webové aplikace. Reverzní proxy server ukončí požadavek HTTP a předá jej do aplikace ASP.NET Core.

### <a name="use-a-reverse-proxy-server"></a>Použít reverzní proxy server

Kestrel se skvěle hodí pro poskytování dynamický obsah z ASP.NET Core. Však nejsou možnosti obsluhující web jako komplexní jako servery služby IIS, Apache nebo Nginx. Reverzní proxy server může převzít práce, jako je například obsluhuje statický obsah, ukládání do mezipaměti požadavky, komprese požadavků a ukončení protokolu SSL ze serveru HTTP. Reverzní proxy server může nacházet na vyhrazený počítač nebo může být nasadí společně se službou serveru HTTP.

Pro účely tohoto průvodce se používá jednu instanci serveru Nginx. Běží na stejném serveru, spolu s HTTP serverem. Na základě požadavků, může být zvolen jiný instalační program.

Vzhledem k tomu, že žádosti jsou předávány podle reverzní proxy server, použít [předané Middleware záhlaví](xref:host-and-deploy/proxy-load-balancer) z [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) balíčku. Middleware aktualizace `Request.Scheme`, použije `X-Forwarded-Proto` záhlaví tak, že identifikátory URI pro přesměrování a další zásady zabezpečení pracovat správně.

Jakékoli součásti, která závisí na schéma, jako je například ověřování, generování odkazů, přesměrování a zeměpisná poloha, musí být umístěn po vyvolání Middleware předané záhlaví. Jako obecné pravidlo by měla předávat Middleware záhlaví spustit před dalším middlewarem s výjimkou diagnostiky a middleware pro zpracování chyb. Toto uspořádání zajistí, že middleware spoléhání se na informace předávané záhlaví může spotřebovat hodnoty hlavičky pro zpracování.

::: moniker range=">= aspnetcore-2.0"

Vyvolat [UseForwardedHeaders](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) metoda `Startup.Configure` před voláním [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication) nebo podobné režimu middleware ověřování. Nakonfigurujte middleware předávat `X-Forwarded-For` a `X-Forwarded-Proto` hlavičky:

```csharp
app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

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

::: moniker-end

Pokud ne [ForwardedHeadersOptions](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) jsou určené pro middleware, jsou výchozí hlavičky pro předávání `None`.

Pouze proxy běžící na místního hostitele (adresu 127.0.0.1, [:: 1]) ve výchozím nastavení jsou důvěryhodné. Pokud jiné důvěryhodné proxy nebo sítěmi v rámci popisovač požadavky organizace mezi Internetem a webový server, je přidat do seznamu <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> nebo <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> s <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>. Následující příklad přidá důvěryhodným proxy serveru na IP adrese 10.0.0.100 s Middlewarem předané záhlaví `KnownProxies` v `Startup.ConfigureServices`:

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

Další informace naleznete v tématu <xref:host-and-deploy/proxy-load-balancer>.

### <a name="install-nginx"></a>Instalaci serveru Nginx

Použití `apt-get` nainstaluje server Nginx. Instalační program vytvoří *systemd* init skript, který spouští server Nginx jako démon na spuštění systému. Postupujte podle pokynů pro Ubuntu na [Nginx: Oficiální Debian nebo Ubuntu balíčky](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages).

> [!NOTE]
> Pokud volitelný moduly Nginx, může být potřeba vytváření Nginx ze zdroje.

Protože server Nginx nainstaloval poprvé, explicitně spusťte ji spuštěním:

```bash
sudo service nginx start
```

Ověřte, že prohlížeč zobrazí výchozí úvodní stránka pro server Nginx. Cílová stránka je dostupný na `http://<server_IP_address>/index.nginx-debian.html`.

### <a name="configure-nginx"></a>Konfigurace serveru Nginx

Chcete-li nakonfigurovat Nginx jako reverzní proxy server pro směrování požadavků do vaší aplikace ASP.NET Core, upravte */etc/nginx/sites-available/default*. Otevřete v textovém editoru a nahraďte obsah následujícím kódem:

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

Pokud ne `server_name` shody, Nginx používá výchozí server. Pokud není definovaný žádný výchozí server, první server v konfiguračním souboru je výchozí server. Jako osvědčený postup přidáte určité výchozí server, který vrátí stavový kód 444 v konfiguračním souboru. Příklad konfigurace serveru výchozí je:

```nginx
server {
    listen   80 default_server;
    # listen [::]:80 default_server deferred;
    return   444;
}
```

S předchozím konfigurační soubor a výchozí server, server Nginx přijímá veřejné provozu na portu 80 se hlavička hostitele `example.com` nebo `*.example.com`. Požadavky nejsou odpovídající tito hostitelé se získat předány Kestrel. Nginx předává požadavky odpovídající Kestrel na `http://localhost:5000`. Zobrazit [jak nginx zpracovává žádost](https://nginx.org/docs/http/request_processing.html) Další informace. Chcete-li změnit Kestrel jeho IP adresa/port, [Kestrel: Konfigurace koncového bodu](xref:fundamentals/servers/kestrel#endpoint-configuration).

> [!WARNING]
> Nepodařilo se určit správnou [název_serveru směrnice](https://nginx.org/docs/http/server_names.html) zpřístupňuje aplikaci tak, aby slabá místa zabezpečení. Vazby zástupný znak subdoménu (například `*.example.com`) nemá představovat toto bezpečnostní riziko, pokud řídíte celý nadřazené domény (nikoli `*.com`, což je ohrožené). Zobrazit [rfc7230 části-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) Další informace.

Jakmile se naváže v konfiguraci serveru Nginx, spusťte `sudo nginx -t` syntaxi konfiguračních souborů. Pokud konfigurační soubor test je úspěšný, vynutit Nginx tak, aby získaly změn spuštěním `sudo nginx -s reload`.

Pro přímé spouštění aplikace na serveru:

1. Přejděte do adresáře aplikace.
1. Spuštění aplikace: `dotnet <app_assembly.dll>`, kde `app_assembly.dll` je název souboru sestavení aplikace.

Pokud aplikace běží na serveru, ale přestane reagovat přes Internet, zkontrolujte bránu firewall serveru a potvrďte, že je otevřený port 80. Pokud používáte virtuální počítač Azure s Ubuntu, přidejte pravidlo skupiny zabezpečení sítě (NSG), která umožní příchozí port 80 provoz. Není nutné povolit pravidlo odchozí port 80 jako odchozí provoz se automaticky udělí, když je povolený příchozí pravidlo.

Po dokončení testování aplikace vypnout aplikaci s `Ctrl+C` příkazového řádku.

## <a name="monitor-the-app"></a>Sledování aplikace

Server je instalačního programu předat požadavky na `http://<serveraddress>:80` k aplikaci ASP.NET Core spuštěnou v Kestrel na `http://127.0.0.1:5000`. Server Nginx se ale nastavit ke správě procesu Kestrel. *systemd* slouží k vytvoření souboru služby ke spuštění a monitorování základní webové aplikace. *systemd* je init systém, který poskytuje řadu výkonných funkcí pro spouštění, zastavování a Správa procesů. 

### <a name="create-the-service-file"></a>Vytvoření souboru služby

Vytvoření definičního souboru služby:

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

Pokud uživatel *www-data* nepoužívá konfigurace, musí nejprve vytvořit uživatelem definované tady a pro soubory zadané správné vlastnictví.

Použití `TimeoutStopSec` nakonfigurovat doba čekání na aplikaci pro vypnutí po přijetí počáteční přerušení signálu. Pokud aplikace není v tomto období vypnout, objeví se SIGKILL ukončit aplikaci. Zadejte hodnotu unitless sekund (například `150`), časový interval hodnotu (například `2min 30s`), nebo `infinity` zakázat časový limit. `TimeoutStopSec` Výchozí hodnota je hodnota `DefaultTimeoutStopSec` v konfiguračním souboru správce (*systemd system.conf*, *system.conf.d*, *systemd user.conf*,  *User.conf.d*). Výchozí hodnota časového limitu pro většinu distribuce je 90 sekund.

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

Linux má systém souborů s rozlišením velkých. Nastavení ASPNETCORE_ENVIRONMENT "Produkční" výsledky hledání pro konfigurační soubor *appsettings. Production.JSON*, nikoli *appsettings.production.json*.

Některé hodnoty (například připojovací řetězce SQL) musí být uvozena pro zprostředkovatele konfigurace pro čtení proměnných prostředí. Použijte následující příkaz k vygenerování správně uvozený uvozovacím znakem hodnoty pro použití v konfiguračním souboru:

```console
systemd-escape "<value-to-escape>"
```

Uložte soubor a povolení služby.

```bash
sudo systemctl enable kestrel-helloapp.service
```

Spusťte službu a ověřte, zda je spuštěna.

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

Reverzní proxy server nakonfigurovat a spravovat prostřednictvím systemd Kestrel, webové aplikace plně konfigurována a je přístupný z prohlížeče na místním počítači v `http://localhost`. Je také přístupné ze vzdáleného počítače, blokování jakoukoli jinou bránu firewall, která může blokovat. Kontrola hlavičky odpovědi `Server` záhlaví zobrazuje obsluhuje Kestrel aplikaci ASP.NET Core.

```text
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a>Zobrazení protokolů

Od webové aplikace pomocí Kestrel se spravuje pomocí `systemd`, centralizované deníku se protokolují všechny události a procesy. Ale tento deník obsahuje všechny položky pro všechny služby a spravuje procesy `systemd`. Chcete-li zobrazit `kestrel-helloapp.service`-konkrétní položky, použijte následující příkaz:

```bash
sudo journalctl -fu kestrel-helloapp.service
```

Pro další filtrování, možnosti, jak `--since today`, `--until 1 hour ago` nebo kombinaci těchto způsobů můžete omezit množství vrácených záznamů.

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

## <a name="long-request-header-fields"></a>Pole hlavičky dlouhou žádost

Pokud aplikace vyžaduje požadavek déle, než je povolený proxy server pole hlavičky výchozí nastavení (obvykle 4 kB nebo 8 kb podle platformy), následující direktivy vyžadovat úpravy. Hodnoty pro použití jsou závislé na scénáři. Další informace najdete v dokumentaci k serveru.

* [proxy_buffer_size](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffer_size)
* [proxy_buffers](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffers)
* [proxy_busy_buffers_size](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_busy_buffers_size)
* [large_client_header_buffers](https://nginx.org/docs/http/ngx_http_core_module.html#large_client_header_buffers)

> [!WARNING]
> Výchozí hodnoty proxy vyrovnávací paměti není zvýšit, není-li nezbytné. Zvýšení tyto hodnoty zvyšují riziko přetečení vyrovnávací paměti (přetečení) a uživateli se zlými úmysly útoků s cílem odepření služby (DoS).

## <a name="secure-the-app"></a>Zabezpečení aplikace

### <a name="enable-apparmor"></a>Povolit AppArmor

Linux zabezpečení moduly (LSM) je architektura, která je součástí linuxového jádra od Linux 2.6. LSM podporuje různé implementace modulů zabezpečení. [AppArmor](https://wiki.ubuntu.com/AppArmor) je LSM, která implementuje systém povinné řízení přístupu, který umožňuje uzavírání program omezenou sadu prostředků. Zajištění AppArmor je povolená a správně nakonfigurovaná.

### <a name="configure-the-firewall"></a>Konfigurace brány firewall

Zavřete vypnout všechny externí porty, které nejsou používány. Znamená přístupnější aplikaci brány firewall (ufw) poskytuje front-endu pro `iptables` tím, že poskytuje rozhraní příkazového řádku pro konfiguraci brány firewall.

> [!WARNING]
> Brána firewall brání přístupu k celému systému, pokud není nakonfigurovaná správně. Nepodařilo se určit správný port SSH bude efektivně případě k zablokování systému jsou k němu připojit pomocí SSH. Výchozí port je 22. Další informace najdete v tématu [Úvod do ufw](https://help.ubuntu.com/community/UFW) a [ruční](http://manpages.ubuntu.com/manpages/bionic/man8/ufw.8.html).

Nainstalujte `ufw` a nakonfigurujte ho chcete povolit přenosy přes všechny porty potřebné.

```bash
sudo apt-get install ufw

sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

sudo ufw enable
```

### <a name="secure-nginx"></a>Zabezpečení serveru Nginx

#### <a name="change-the-nginx-response-name"></a>Změnit název odpovědi serveru Nginx

Edit *src/http/ngx_http_header_filter_module.c*:

```
static char ngx_http_server_string[] = "Server: Web Server" CRLF;
static char ngx_http_server_full_string[] = "Server: Web Server" CRLF;
```

#### <a name="configure-options"></a>Konfigurace možností

Konfigurace serveru s další požadované moduly. Zvažte použití brány firewall webových aplikací, jako například [ModSecurity](https://www.modsecurity.org/), Posilte zabezpečení aplikace.

#### <a name="configure-ssl"></a>Konfigurace SSL

* Konfigurace serveru tak, aby naslouchala na přenosy HTTPS na portu `443` zadáním platný certifikát vydaný důvěryhodného certifikátu autority (CA).

* Posílení zabezpečení, když některé postupy uvedené v následující */etc/nginx/nginx.conf* souboru. Mezi příklady patří výběrem silnější šifrování a přesměrovat veškerý provoz prostřednictvím protokolu HTTP na HTTPS.

* Přidání `HTTP Strict-Transport-Security` záhlaví (HSTS) zajišťuje, že jsou všechny následné požadavky od klienta přes protokol HTTPS.

* Nepřidávejte HSTS záhlaví nebo zvolit odpovídající `max-age` Pokud bude v budoucnu zakázání protokolu SSL.

Přidat */etc/nginx/proxy.conf* konfiguračního souboru:

[!code-nginx[](linux-nginx/proxy.conf)]

Upravit */etc/nginx/nginx.conf* konfigurační soubor. Tento příklad obsahuje oba `http` a `server` oddílů v souboru jednu konfiguraci.

[!code-nginx[](linux-nginx/nginx.conf?highlight=2)]

#### <a name="secure-nginx-from-clickjacking"></a>Zabezpečení serveru Nginx z útoků typu clickjacking

[Útoků typu Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), označované také jako *uživatelského rozhraní zjednávání nápravy útoku*, je napadením se zlými úmysly, kde návštěvníků webu je nalákaní, odkaz nebo tlačítko na stránce jiné než aktuálně navštívený. Použití `X-FRAME-OPTIONS` k zabezpečení webu.

Ke zmírnění útoků typu clickjacking útoků:

1. Upravit *nginx.conf* souboru:

   ```bash
   sudo nano /etc/nginx/nginx.conf
   ```

   Přidejte řádek `add_header X-Frame-Options "SAMEORIGIN";`.
1. Uložte soubor.
1. Restartujte server Nginx.

#### <a name="mime-type-sniffing"></a>Typ MIME pro analýzu sítě

Tato hlavička brání většina prohlížečů z MIME pro analýzu sítě odpověď od deklarovaný typ obsahu, jako hlavičku dostane pokyn, abyste nepřepsali typ obsahu odpovědi. S `nosniff` možnost, pokud server říká, že obsah je "text/html", v prohlížeči se vykreslí jako "text/html".

Upravit *nginx.conf* souboru:

```bash
sudo nano /etc/nginx/nginx.conf
```

Přidejte řádek `add_header X-Content-Type-Options "nosniff";` a uložte soubor a pak restartujte server Nginx.

## <a name="additional-resources"></a>Další zdroje

* [Požadavky pro .NET Core v Linuxu](/dotnet/core/linux-prerequisites)
* [Server Nginx: Binární verze: Oficiální balíčky Debian nebo Ubuntu](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
* [SERVER NGINX: Pomocí hlavičky předané](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)

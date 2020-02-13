---
title: Hostování a nasazení ASP.NET Core Blazor WebAssembly
author: guardrex
description: Naučte se hostovat a nasazovat Blazor aplikace s využitím ASP.NET Core, sítí pro doručování obsahu (CDN), souborových serverů a stránek GitHubu.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/webassembly
ms.openlocfilehash: 861935ff31652f923399a8aa5ae52baa6b77fa91
ms.sourcegitcommit: 85564ee396c74c7651ac47dd45082f3f1803f7a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172399"
---
# <a name="host-and-deploy-aspnet-core-opno-locblazor-webassembly"></a>Hostování a nasazení ASP.NET Core Blazor WebAssembly

Od [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com)a [Daniel Skořepa](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Pomocí [modelu hostováníBlazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly):

* Aplikace Blazor, její závislosti a modul runtime .NET se stáhnou do prohlížeče.
* Aplikace se spustí přímo ve vlákně uživatelského rozhraní prohlížeče.

Podporují se tyto strategie nasazení:

* Aplikace Blazor obsluhuje ASP.NET Core aplikace. Tato strategie je popsaná v části [hostované nasazení s ASP.NET Core](#hosted-deployment-with-aspnet-core) .
* Aplikace Blazor je umístěna na statický hostující webový server nebo službu, kde rozhraní .NET se nepoužívá k obsluze aplikace Blazor. Tato strategie je popsaná v části [samostatné nasazení](#standalone-deployment) , která obsahuje informace o hostování aplikace Blazor WebAssembly jako dílčí aplikace IIS.

## <a name="rewrite-urls-for-correct-routing"></a>Přepište adresy URL pro správné směrování.

Žádosti o směrování pro součásti stránky v Blazor aplikaci WebAssembly nejsou tak jednoduché jako požadavky směrování na serveru Blazor a v hostované aplikaci. Zvažte Blazor aplikaci WebAssembly se dvěma komponentami:

* *Main. razor* &ndash; načíst do kořenového adresáře aplikace a obsahuje odkaz na komponentu `About` (`href="About"`).
* *O. razor* &ndash; `About` součást.

Pokud je výchozí dokument aplikace požadován pomocí panelu Adresa prohlížeče (například `https://www.contoso.com/`):

1. Prohlížeč vytvoří požadavek.
1. Vrátí se výchozí stránka, což je obvykle *index. html*.
1. *index. html* se v aplikaci zabootstrap.
1. dojde k načtení směrovače Blazora vykreslí se komponenta Razor `Main`.

Na hlavní stránce vyberte odkaz na součást `About` pracuje na klientovi, protože Blazor směrovač zastaví, aby se v prohlížeči odeslala žádost `www.contoso.com` pro `About` a spolupracuje přímo vykreslená `About` komponenta. Všechny požadavky na vnitřní koncové body *v aplikaci Blazor WebAssembly* fungují stejným způsobem: požadavky neaktivují požadavky založené na prohlížeči na prostředky hostované na serveru na internetu. Směrovač zpracovává požadavky interně.

Pokud je žádost vytvořena pomocí panelu Adresa prohlížeče pro `www.contoso.com/About`, požadavek se nezdařil. Žádný takový prostředek na internetovém hostiteli aplikace neexistuje, takže se vrátí odpověď *404 – Nenalezeno* .

Vzhledem k tomu, že prohlížeče vytvářejí požadavky na internetové hostitele pro stránky na straně klienta, webové servery a hostitelské služby musí přepsat všechny požadavky na prostředky, které nejsou fyzicky na serveru, na stránku *index. html* . Když se vrátí *index. html* , Blazor směrovač aplikace převezme a odpoví správným prostředkem.

Při nasazování na server služby IIS můžete použít modul pro přepis adres URL pomocí publikovaného souboru *Web. config* aplikace. Další informace najdete v části [IIS](#iis) .

## <a name="hosted-deployment-with-aspnet-core"></a>Hostované nasazení s ASP.NET Core

*Hostované nasazení* slouží jako aplikace Blazor WebAssembly pro prohlížeče z [aplikace ASP.NET Core](xref:index) , která běží na webovém serveru.

Aplikace Blazor je součástí ASP.NET Core aplikace v publikovaném výstupu, takže se tyto dvě aplikace nasazují dohromady. Vyžaduje se webový server, který podporuje hostování aplikace ASP.NET Core. V případě hostovaného nasazení Visual Studio zahrnuje šablonu projektu **aplikaceBlazor WebAssembly** (šablona`blazorwasm` při použití příkazu [dotnet New](/dotnet/core/tools/dotnet-new) ) s vybranou možností **Hosted** .

Další informace o ASP.NET Core hostování a nasazení aplikací najdete v článku <xref:host-and-deploy/index>.

Informace o nasazení do Azure App Service najdete v tématu <xref:tutorials/publish-to-azure-webapp-using-vs>.

## <a name="standalone-deployment"></a>Samostatné nasazení

*Samostatné nasazení* obsluhuje Blazor aplikaci WebAssembly jako sadu statických souborů, které jsou požadovány přímo klienty. Každý statický souborový server může sloužit jako aplikace Blazor.

Samostatné prostředky nasazení se publikují do složky *bin/Release/{Target Framework}/PUBLISH/{Assembly Name}/DIST* .

### <a name="iis"></a>IIS

Služba IIS je schopným statickým souborovým serverem pro Blazor aplikace. Chcete-li nakonfigurovat službu IIS na hostování Blazor, přečtěte si téma [vytvoření statického webu ve službě IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).

Publikované assety se vytvoří ve složce */bin/Release/{Target Framework}/Publish* . Hostovat obsah složky pro *publikování* na webovém serveru nebo v hostitelské službě.

#### <a name="webconfig"></a>web.config

Při publikování Blazor projektu se vytvoří soubor *Web. config* s následující konfigurací služby IIS:

* Typy MIME jsou nastaveny pro následující přípony souborů:
  * *. dll* &ndash; `application/octet-stream`
  * *. json* &ndash; `application/json`
  * *. wasm* &ndash; `application/wasm`
  * *. woff* &ndash; `application/font-woff`
  * *. woff2* &ndash; `application/font-woff`
* Pro následující typy MIME je povolena komprese protokolu HTTP:
  * `application/octet-stream`
  * `application/wasm`
* Pravidla pro přepis adres URL jsou navázána:
  * Slouží jako podadresáře, kde se nachází statické prostředky aplikace ( *{název sestavení}/DIST/{Path požádal}* ).
  * Vytvořte záložní řešení zabezpečeného hesla, aby se požadavky na nesouborové prostředky přesměrovaly do výchozího dokumentu aplikace ve složce statických prostředků ( *{název sestavení}/DIST/index.html*).

#### <a name="install-the-url-rewrite-module"></a>Instalace modulu URL pro přepis

Pro přepis adres URL je vyžadován [modul URL Rewrite](https://www.iis.net/downloads/microsoft/url-rewrite) . Modul není nainstalován ve výchozím nastavení a není k dispozici pro instalaci jako funkci služby role Webový server (IIS). Modul se musí stáhnout z webu IIS. K instalaci modulu použijte instalační program webové platformy:

1. Místně přejděte na [stránku ke stažení modulu pro přepsání adresy URL](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads). V případě anglické verze vyberte **WebPI** a Stáhněte si instalační program WebPI. Pro jiné jazyky vyberte příslušnou architekturu pro server (x86/x64) a stáhněte instalační program.
1. Zkopírujte instalační program na server. Spusťte instalační program. Vyberte tlačítko **nainstalovat** a přijměte licenční podmínky. Po dokončení instalace není restartování serveru vyžadováno.

#### <a name="configure-the-website"></a>Konfigurace webu

Nastavte **fyzickou cestu** webu na složku aplikace. Složka obsahuje:

* Soubor *Web. config* , který služba IIS používá ke konfiguraci webu, včetně požadovaných pravidel přesměrování a typů obsahu souborů.
* Složka statických prostředků aplikace

#### <a name="host-as-an-iis-sub-app"></a>Hostování jako dílčí aplikace služby IIS

Pokud je samostatná aplikace hostovaná jako dílčí aplikace služby IIS, proveďte jednu z následujících akcí:

* Zakažte zděděnou obslužnou rutinu modulu ASP.NET Core.

  Odeberte obslužnou rutinu v publikovaném souboru *Web. config* aplikace Blazor přidáním oddílu `<handlers>` do souboru:

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* Zakažte dědění `<system.webServer>` oddílu kořenové (nadřazené) aplikace pomocí elementu `<location>` s `inheritInChildApplications` nastavenou na `false`:

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <handlers>
          <add name="aspNetCore" ... />
        </handlers>
        <aspNetCore ... />
      </system.webServer>
    </location>
  </configuration>
  ```

Odebrání obslužné rutiny nebo zakázání dědičnosti se provádí kromě [Konfigurace základní cesty aplikace](xref:host-and-deploy/blazor/index#app-base-path). Nastavte základní cestu aplikace v souboru *index. html* aplikace na alias služby IIS, který se používá při konfiguraci dílčí aplikace v IIS.

#### <a name="troubleshooting"></a>Řešení potíží

Pokud dojde k *chybě 500 – interní chyba serveru* a správce služby IIS vyvolá chyby při pokusu o přístup ke konfiguraci webu, potvrďte, že je nainstalován modul URL pro přepis. Pokud modul není nainstalován, soubor *Web. config* nelze analyzovat službou IIS. Tím se zabrání tomu, aby správce služby IIS načetl konfiguraci webu a web neobsluhuje statické soubory Blazor.

Další informace o řešení potíží s nasazeními do služby IIS najdete v tématu <xref:test/troubleshoot-azure-iis>.

### <a name="azure-storage"></a>Azure Storage

Hostování [Azure Storage](/azure/storage/) statických souborů umožňuje hostování aplikace bez Blazor serveru. Podporují se názvy vlastních domén, Azure Content Delivery Network (CDN) a HTTPS.

Když je u služby BLOB Service povolené hostování statických webů v účtu úložiště:

* Nastavte **název dokumentu indexu** na `index.html`.
* Nastavte **cestu k chybovému dokumentu** na `index.html`. Komponenty Razor a jiné nesouborové koncové body se neukládají na fyzických cestách se statickým obsahem uloženým ve službě BLOB Service. Když se obdrží žádost o jeden z těchto prostředků, kterou by měl směrovač Blazor zpracovat, Chyba *404 –* chyba, kterou vygenerovala služba BLOB Service, směruje požadavek na **cestu k chybovému dokumentu**. Vrátí se objekt BLOB *index. html* a Blazor směrovač načte a zpracuje cestu.

Další informace najdete v tématu [statické hostování webů v Azure Storage](/azure/storage/blobs/storage-blob-static-website).

### <a name="nginx"></a>Nginx

Následující soubor *Nginx. conf* je zjednodušený a ukazuje, jak nakonfigurovat Nginx pro odeslání souboru *index. html* pokaždé, když nemůže najít odpovídající soubor na disku.

```
events { }
http {
    server {
        listen 80;

        location / {
            root /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

Další informace o konfiguraci webového serveru Nginx v produkčním prostředí najdete v tématu [vytváření konfiguračních souborů Nginx plus a Nginx](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).

### <a name="nginx-in-docker"></a>Nginx v Docker

Pokud chcete hostovat Blazor v Docker pomocí Nginx, nastavte souboru Dockerfile na použití Nginx image založené na Alpine. Aktualizujte souboru Dockerfile a zkopírujte soubor *Nginx. config* do kontejneru.

Do souboru Dockerfile přidejte jeden řádek, jak je znázorněno v následujícím příkladu:

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a>Apache

Nasazení aplikace Blazor WebAssembly do CentOS 7 nebo novější:

1. Vytvořte konfigurační soubor Apache. Následující příklad je zjednodušený konfigurační soubor (*blazorapp. config*):

   ```
   <VirtualHost *:80>
       ServerName www.example.com
       ServerAlias *.example.com

       DocumentRoot "/var/www/blazorapp"
       ErrorDocument 404 /index.html

       AddType aplication/wasm .wasm
       AddType application/octet-stream .dll
   
       <Directory "/var/www/blazorapp">
           Options -Indexes
           AllowOverride None
       </Directory>

       <IfModule mod_deflate.c>
           AddOutputFilterByType DEFLATE text/css
           AddOutputFilterByType DEFLATE application/javascript
           AddOutputFilterByType DEFLATE text/html
           AddOutputFilterByType DEFLATE application/octet-stream
           AddOutputFilterByType DEFLATE application/wasm
           <IfModule mod_setenvif.c>
           BrowserMatch ^Mozilla/4 gzip-only-text/html
           BrowserMatch ^Mozilla/4.0[678] no-gzip
           BrowserMatch bMSIE !no-gzip !gzip-only-text/html
       </IfModule>
       </IfModule>

       ErrorLog /var/log/httpd/blazorapp-error.log
       CustomLog /var/log/httpd/blazorapp-access.log common
   </VirtualHost>
   ```

1. Konfigurační soubor Apache umístěte do adresáře `/etc/httpd/conf.d/`, který je výchozím adresářem konfigurace Apache v CentOS 7.

1. Umístěte soubory aplikace do adresáře `/var/www/blazorapp` (umístění zadané pro `DocumentRoot` v konfiguračním souboru).

1. Restartujte službu Apache.

Další informace najdete v tématu [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) a [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).

### <a name="github-pages"></a>Stránky GitHubu

Chcete-li zpracovat přepisy adresy URL, přidejte soubor *404. html* pomocí skriptu, který zpracovává přesměrování požadavku na stránku *index. html* . Ukázkovou implementaci poskytovanou komunitou najdete v tématu [jednostránkové aplikace pro stránky GitHubu](https://spa-github-pages.rafrex.com/) ([rafrex/Spa – GitHub-Pages na GitHubu](https://github.com/rafrex/spa-github-pages#readme)). Příklad použití přístupu komunity se dá zobrazit v [blazor-demo/blazor-demo. GitHub. IO na GitHubu](https://github.com/blazor-demo/blazor-demo.github.io) ([živý web](https://blazor-demo.github.io/)).

Při použití webu projektu místo webu organizace přidejte nebo aktualizujte značku `<base>` v souboru *index. html*. Nastavte hodnotu atributu `href` na název úložiště GitHub s koncovým lomítkem (například `my-repository/`.

## <a name="host-configuration-values"></a>Hodnoty konfigurace hostitele

[aplikaceBlazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) můžou přijmout následující hodnoty konfigurace hostitele jako argumenty příkazového řádku za běhu ve vývojovém prostředí.

### <a name="content-root"></a>Kořen obsahu

Argument `--contentroot` nastaví absolutní cestu k adresáři, který obsahuje soubory obsahu aplikace ([kořen obsahu](xref:fundamentals/index#content-root)). V následujících příkladech je `/content-root-path` kořenovou cestou obsahu aplikace.

* Předejte argument při místním spuštění aplikace z příkazového řádku. Z adresáře aplikace spusťte:

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* Přidejte položku do souboru *launchSettings. JSON* aplikace v profilu **IIS Express** . Toto nastavení se používá při spuštění aplikace pomocí ladicího programu sady Visual Studio a z příkazového řádku s `dotnet run`.

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* V aplikaci Visual Studio zadejte do **vlastností** argument > **ladit** > **argumenty aplikace**. Nastavení argumentu na stránce vlastností aplikace Visual Studio přidá argument do souboru *launchSettings. JSON* .

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a>Základ cesty

Argument `--pathbase` nastaví základní cestu aplikace pro aplikaci spuštěnou místně s cestou relativní adresy URL, která není kořenem (značka `<base>` `href` je nastavená na jinou cestu než `/` pro pracovní a produkční prostředí). V následujících příkladech je `/relative-URL-path` základ cesty aplikace. Další informace najdete v tématu [základní cesta k aplikaci](xref:host-and-deploy/blazor/index#app-base-path).

> [!IMPORTANT]
> Na rozdíl od cesty `href` značky `<base>` nezahrnujte koncové lomítko (`/`) při předávání hodnoty `--pathbase`ho argumentu. Pokud je základní cesta k aplikaci k dispozici ve značce `<base>` jako `<base href="/CoolApp/">` (obsahuje koncové lomítko), předejte hodnotu argumentu příkazového řádku jako `--pathbase=/CoolApp` (žádné koncové lomítko).

* Předejte argument při místním spuštění aplikace z příkazového řádku. Z adresáře aplikace spusťte:

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* Přidejte položku do souboru *launchSettings. JSON* aplikace v profilu **IIS Express** . Toto nastavení se používá při spuštění aplikace pomocí ladicího programu sady Visual Studio a z příkazového řádku s `dotnet run`.

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* V aplikaci Visual Studio zadejte do **vlastností** argument > **ladit** > **argumenty aplikace**. Nastavení argumentu na stránce vlastností aplikace Visual Studio přidá argument do souboru *launchSettings. JSON* .

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a>URL – adresy

Argument `--urls` nastaví IP adresy nebo adresy hostitelů s porty a protokoly, které se mají na požadavky naslouchat.

* Předejte argument při místním spuštění aplikace z příkazového řádku. Z adresáře aplikace spusťte:

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* Přidejte položku do souboru *launchSettings. JSON* aplikace v profilu **IIS Express** . Toto nastavení se používá při spuštění aplikace pomocí ladicího programu sady Visual Studio a z příkazového řádku s `dotnet run`.

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* V aplikaci Visual Studio zadejte do **vlastností** argument > **ladit** > **argumenty aplikace**. Nastavení argumentu na stránce vlastností aplikace Visual Studio přidá argument do souboru *launchSettings. JSON* .

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a>Konfigurace Linkeru

Blazor provádí propojení v prostředním jazyce (IL) pro každé sestavení, aby se odebralo zbytečné IL z výstupních sestavení. Propojení sestavení lze řídit při sestavování. Další informace naleznete v tématu <xref:host-and-deploy/blazor/configure-linker>.

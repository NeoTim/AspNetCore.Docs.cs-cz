---
title: Hostování a nasazení Blazor ASP.NET Core WebAssembly
author: guardrex
description: Zjistěte, jak hostovat a nasazovat Blazor aplikaci pomocí ASP.NET, sítí pro doručování obsahu (CDN), souborových serverů a stránek GitHubu.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/webassembly
ms.openlocfilehash: f364d94085d175fde5596c222ef21852c0106ec1
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80751128"
---
# <a name="host-and-deploy-aspnet-core-opno-locblazor-webassembly"></a>Hostování a nasazení Blazor ASP.NET Core WebAssembly

[Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), a [Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

S [ Blazor modelem hostování websestavy](xref:blazor/hosting-models#blazor-webassembly):

* Aplikace, Blazor její závislosti a za běhu .NET jsou staženy do prohlížeče.
* Aplikace se spouští přímo ve vlákně uživatelského rozhraní prohlížeče.

Podporovány jsou následující strategie nasazení:

* Aplikace Blazor je obsluhována aplikací ASP.NET Core. Tato strategie je [popsána v části Hostované nasazení s ASP.NET jádrem.](#hosted-deployment-with-aspnet-core)
* Aplikace Blazor je umístěna na statickém hostingovém webovém serveru nebo Blazor službě, kde se rozhraní .NET nepoužívá k poskytování aplikace. Tato strategie je popsána v části [Samostatné nasazení,](#standalone-deployment) která obsahuje informace o hostování aplikace Blazor WebAssembly jako podaplikace služby IIS.

## <a name="rewrite-urls-for-correct-routing"></a>Přepsat adresy URL pro správné směrování

Směrování požadavků na komponenty Blazor stránky v aplikaci WebAssembly není tak Blazor jednoduché jako požadavky na směrování v hostované aplikaci server. Zvažte Blazor aplikaci WebAssembly se dvěma součástmi:

* *Main.razor* &ndash; načte v kořenovém adresáři `About` aplikace`href="About"`a obsahuje odkaz na komponentu ( ).
* *Součást About.razor.* &ndash; `About`

Pokud je požadován výchozí dokument aplikace pomocí adresního řádku `https://www.contoso.com/`prohlížeče (například ):

1. Prohlížeč podá požadavek.
1. Je vrácena výchozí stránka, která je obvykle *index.html*.
1. *index.html* bootstraps aplikace.
1. Blazornačte se směrovač a `Main` komponenta Razor je vykreslena.

Na hlavní stránce výběr odkazu na `About` komponentu funguje na Blazor straně klienta, protože směrovač zabrání `www.contoso.com` `About` prohlížeči v `About` podání požadavku na Internetu do aplikace for a slouží samotné vykreslené součásti. Všechny požadavky na interní koncové body *v aplikaci Blazor WebAssembly* fungují stejným způsobem: Požadavky neaktivují požadavky založené na prohlížeči na prostředky hostované na serveru v Internetu. Směrovač zpracovává požadavky interně.

Pokud je požadavek podán pomocí adresního řádku prohlížeče pro `www.contoso.com/About`, požadavek se nezdaří. Žádný takový prostředek neexistuje na internetovém hostiteli aplikace, takže je vrácena odpověď *404 - Not Found.*

Vzhledem k tomu, že prohlížeče pořazují internetové hostitele pro stránky na straně klienta, musí webové servery a hostitelské služby přepsat všechny požadavky na prostředky, které nejsou fyzicky na serveru, na stránku *index.html.* Po vrácení *souboru index.html* Blazor převezme směrovač aplikace a odpoví správným zdrojem.

Při nasazování na server služby IIS můžete použít modul přepisování adres URL s publikovaným souborem *web.config* aplikace. Další informace naleznete v části [IIS.](#iis)

## <a name="hosted-deployment-with-aspnet-core"></a>Hostované nasazení s ASP.NET jádrem

*Hostované nasazení* slouží Blazor aplikaci WebAssembly do prohlížečů z [aplikace ASP.NET Core,](xref:index) která běží na webovém serveru.

Klientská Blazor aplikace WebAssembly je publikována do složky */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* serverové aplikace spolu s dalšími statickými webovými prostředky serverové aplikace. Tyto dvě aplikace se nasazují společně. Webový server, který je schopen hostování aplikace ASP.NET Core je vyžadován. Pro`-ho|--hosted` hostované nasazení visual studio obsahuje šablonu `dotnet new` `blazorwasm` ** Blazor ** projektu aplikace WebAssembly App (šablona při použití [dotnet new](/dotnet/core/tools/dotnet-new) command) s vybranou volbou **Hosted** (při použití příkazu).

Další informace o hostování a nasazení <xref:host-and-deploy/index>aplikací ASP.NET Core najdete v tématu .

Informace o nasazení do služby <xref:tutorials/publish-to-azure-webapp-using-vs>Azure App Service najdete v tématu .

## <a name="standalone-deployment"></a>Samostatné nasazení

*Samostatné nasazení* slouží Blazor aplikaci WebAssembly jako sadu statických souborů, které jsou požadovány přímo klienty. Aplikace je schopna obsluhovat Blazor libovolný statický souborový server.

Prostředky samostatného nasazení jsou publikovány do složky */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot.*

### <a name="iis"></a>IIS

Služba IIS je schopný Blazor statický souborový server pro aplikace. Informace o konfiguraci Blazorslužby IIS jako hostitele naleznete [v tématu Vytvoření statického webu ve službě IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).

Publikované datové zdroje jsou vytvářeny ve složce */bin/Release/{TARGET FRAMEWORK}/publish.* Hostujte obsah složky *publikování* na webovém serveru nebo hostitelské službě.

#### <a name="webconfig"></a>Souboru web.config

Při Blazor publikování projektu je vytvořen soubor *web.config* s následující konfigurací služby IIS:

* Typy MIME jsou nastaveny pro následující přípony souborů:
  * *.dll* &ndash;`application/octet-stream`
  * *.json* &ndash;`application/json`
  * *.wasm* &ndash;`application/wasm`
  * *.woff* &ndash;`application/font-woff`
  * *.woff2* &ndash;`application/font-woff`
* Komprese HTTP je povolena pro následující typy MIME:
  * `application/octet-stream`
  * `application/wasm`
* Pravidla modulu přepisování adres URL jsou stanovena:
  * Obsluhuj podadresář, ve kterém se nacházejí statické datové zdroje aplikace *(wwwroot/{PATH REQUESTED}*).
  * Vytvořte záložní směrování spa, aby byly požadavky na nesouborové datové zdroje přesměrovány na výchozí dokument aplikace ve složce statických datových zdrojů (*wwwroot/index.html*).
  
#### <a name="use-a-custom-webconfig"></a>Použití vlastního souboru web.config

Použití vlastního souboru *web.config:*

1. Umístěte vlastní soubor *web.config* do kořenového adresáře složky projektu.
1. Přidejte do souboru projektu následující cíl (*.csproj*):

   ```xml
   <Target Name="CopyWebConfigOnPublish" AfterTargets="Publish">
     <Copy SourceFiles="web.config" DestinationFolder="$(PublishDir)" />
   </Target>
   ```
   
> [!NOTE]
> Použití nastavené vlastnosti `<IsWebConfigTransformDisabled>` MSBuild na `true` Blazor není v aplikacích WebAssembly podporováno [stejně jako pro ASP.NET aplikace Core nasazené do služby IIS](xref:host-and-deploy/iis/index#webconfig-file). Další informace naleznete v [tématu Blazor Copy target required to provide custom WASM web.config (dotnet/aspnetcore #20569)](https://github.com/dotnet/aspnetcore/issues/20569).

#### <a name="install-the-url-rewrite-module"></a>Instalace modulu přepisování adres URL

K přepsání adres URL je [nutný modul přepisování adres URL.](https://www.iis.net/downloads/microsoft/url-rewrite) Modul není ve výchozím nastavení nainstalován a není k dispozici pro instalaci jako funkce služby role Web Server (IIS). Modul musí být stažen z webových stránek iis. Modul nainstalujte pomocí Instalační služby webové platformy:

1. Místně přejděte na [stránku stažení modulu přepisování adres URL](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads). V anglické verzi vyberte **WebPI** a stáhněte instalační program WebPI. Pro ostatní jazyky vyberte příslušnou architekturu pro server (x86/x64) a stáhněte si instalační program.
1. Zkopírujte instalační program na server. Spusťte instalační program. Vyberte tlačítko **Instalovat** a přijměte licenční podmínky. Restartování serveru není po dokončení instalace nutné.

#### <a name="configure-the-website"></a>Konfigurace webu

Nastavte **fyzickou cestu** k složce aplikace. Složka obsahuje:

* Soubor *web.config,* který službu IIS používá ke konfiguraci webu, včetně požadovaných pravidel přesměrování a typů obsahu souborů.
* Složka statických datových zdrojů aplikace.

#### <a name="host-as-an-iis-sub-app"></a>Hostovat jako podaplikaci iis

Pokud je samostatná aplikace hostovaná jako podaplikace iis, proveďte jednu z následujících akcí:

* Zakažte zděděnou obslužnou rutinu ASP.NET základního modulu.

  Odeberte obslužnou rutinu v Blazor publikovaném `<handlers>` souboru *web.config* aplikace přidáním oddílu do souboru:

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* Zakažte `<system.webServer>` dědičnost oddílu kořenové `<location>` (nadřazené) aplikace pomocí prvku s `inheritInChildApplications` nastavenou na `false`:

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

Odebrání obslužné rutiny nebo zakázání dědičnosti se provádí kromě [konfigurace základní cesty aplikace](xref:host-and-deploy/blazor/index#app-base-path). Nastavte základní cestu aplikace v souboru *index.html* aplikace na alias služby IIS používaný při konfiguraci podaplikace ve službách IIS.

#### <a name="troubleshooting"></a>Řešení potíží

Pokud je *přijata interní chyba serveru 500* a Správce služby IIS vyvolá chyby při pokusu o přístup ke konfiguraci webu, zkontrolujte, zda je nainstalován modul přepisování adres URL. Není-li modul nainstalován, nemůže být soubor *web.config* službou IIS analyzován. Správce služby IIS tak nemůže načíst konfiguraci webu Blazora web ve zobrazování statických souborů webu.

Další informace o řešení potíží s nasazením ve službě IIS naleznete v tématu <xref:test/troubleshoot-azure-iis>.

### <a name="azure-storage"></a>Azure Storage

Azure [Storage](/azure/storage/) statický soubor Blazor hosting umožňuje serverbez aplikace hosting. Vlastní názvy domén, síť pro doručování obsahu Azure (CDN) a HTTPS jsou podporované.

Když je služba blob povolená pro statické hostování webových stránek na účtu úložiště:

* Nastavte **název dokumentu Rejstříku** na . `index.html`
* Nastavte **cestu dokumentu Chyby** na `index.html`. Komponenty holicího strojku a jiné koncové body mimo soubor nejsou umístěny na fyzické cesty ve statickém obsahu uloženém službou objektů blob. Pokud je přijat požadavek na jeden Blazor z těchto prostředků, který by měl směrovač zpracovat, chyba *404 - Not Found* generovaná službou objektů blob směruje požadavek na **cestu dokumentu Chyba**. Je vrácen objekt blob *index.html* a Blazor směrovač načte a zpracuje cestu.

Další informace najdete [v tématu Statický web hosting v Azure Storage](/azure/storage/blobs/storage-blob-static-website).

### <a name="nginx"></a>Nginx

Následující soubor *nginx.conf* je zjednodušen, aby ukázal, jak nakonfigurovat Nginx pro odeslání souboru *index.html,* kdykoli nemůže najít odpovídající soubor na disku.

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

Další informace o konfiguraci produkčního webového serveru Nginx naleznete v [tématu Vytváření konfiguračních souborů NGINX Plus a NGINX](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).

### <a name="nginx-in-docker"></a>Nginx v Dockeru

Chcete-li hostovat Blazor v Dockeru pomocí Nginx, nastavte Dockerfile pro použití image Nginx založené na alpském. Aktualizujte soubor Dockerfile a zkopírujte soubor *nginx.config* do kontejneru.

Přidejte jeden řádek do dockerfile, jak je znázorněno v následujícím příkladu:

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a>Apache

Nasazení aplikace Blazor WebAssembly do CentOS 7 nebo novějšího:

1. Vytvořte konfigurační soubor Apache. Následující příklad je zjednodušený konfigurační soubor (*blazorapp.config*):

   ```
   <VirtualHost *:80>
       ServerName www.example.com
       ServerAlias *.example.com

       DocumentRoot "/var/www/blazorapp"
       ErrorDocument 404 /index.html

       AddType application/wasm .wasm
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

1. Umístěte konfigurační `/etc/httpd/conf.d/` soubor Apache do adresáře, což je výchozí konfigurační adresář Apache v CentOS 7.

1. Umístěte soubory aplikace do `/var/www/blazorapp` adresáře (umístění `DocumentRoot` určené v konfiguračním souboru).

1. Restartujte službu Apache.

Další informace naleznete [v tématu mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) a [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).

### <a name="github-pages"></a>Stránky GitHubu

Chcete-li zpracovat přepsání adresy URL, přidejte soubor *404.html* se skriptem, který zpracovává přesměrování požadavku na stránku *index.html.* Příklad implementace poskytované komunitou najdete [v tématu Jednostránkové aplikace pro stránky GitHub](https://spa-github-pages.rafrex.com/) [(rafrex/spa-github-pages na GitHubu).](https://github.com/rafrex/spa-github-pages#readme) Příklad použití komunitního přístupu lze vidět na [blazor-demo/blazor-demo.github.io na GitHubu](https://github.com/blazor-demo/blazor-demo.github.io) [(live site](https://blazor-demo.github.io/)).

Pokud používáte web projektu místo webu organizace, `<base>` přidejte nebo aktualizujte značku v *souboru index.html*. Nastavte `href` hodnotu atributu na název úložiště GitHub s koncovým lomítkem (například `my-repository/`.

## <a name="host-configuration-values"></a>Hodnoty konfigurace hostitele

Aplikace WebAssembly mohou přijímat následující hodnoty konfigurace hostitele jako argumenty příkazového řádku za běhu ve vývojovém prostředí. [ Blazor ](xref:blazor/hosting-models#blazor-webassembly)

### <a name="content-root"></a>Kořen obsahu

Argument `--contentroot` nastaví absolutní cestu k adresáři, který obsahuje soubory obsahu aplikace[(kořen obsahu).](xref:fundamentals/index#content-root) V následujících příkladech `/content-root-path` je kořenová cesta obsahu aplikace.

* Předajte argument při místním spuštění aplikace na příkazovém řádku. Z adresáře aplikace spusťte:

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* Přidejte položku do souboru *launchSettings.json* aplikace v profilu **Služby IIS Express.** Toto nastavení se používá při spuštění aplikace s ladicím programem sady Visual Studio a z příkazového řádku s `dotnet run`aplikací .

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* V sadě Visual Studio zadejte argument v**argumentech aplikace****ladění** >  **vlastností** > . Nastavení argumentu na stránce vlastností sady Visual Studio přidá argument do souboru *launchSettings.json.*

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a>Základna cesty

Argument `--pathbase` nastaví základní cestu aplikace pro aplikaci spuštěnou místně s `<base>` nekořenovou relativní adresou URL (značka `href` je nastavena na jinou cestu než `/` pro pracovní a produkční). V následujících příkladech `/relative-URL-path` je základem cesty aplikace. Další informace naleznete v [tématu Základní cesta aplikace](xref:host-and-deploy/blazor/index#app-base-path).

> [!IMPORTANT]
> Na rozdíl od `href` cesty `<base>` poskytnuté ke značce nezahrnte při `--pathbase` předávání hodnoty argumentu koncové lomítko (`/`). Pokud je základní cesta aplikace `<base>` k `<base href="/CoolApp/">` dispozici ve značce jako (obsahuje koncové lomítko), předaj hodnotu argumentu příkazového řádku jako `--pathbase=/CoolApp` (žádné koncové lomítko).

* Předajte argument při místním spuštění aplikace na příkazovém řádku. Z adresáře aplikace spusťte:

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* Přidejte položku do souboru *launchSettings.json* aplikace v profilu **Služby IIS Express.** Toto nastavení se používá při spuštění aplikace s ladicím `dotnet run`programem sady Visual Studio a z příkazového řádku s aplikací .

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* V sadě Visual Studio zadejte argument v**argumentech aplikace****ladění** >  **vlastností** > . Nastavení argumentu na stránce vlastností sady Visual Studio přidá argument do souboru *launchSettings.json.*

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a>Adresy URL

Argument `--urls` nastaví adresy IP nebo hostitelské adresy s porty a protokoly pro naslouchání požadavkům.

* Předajte argument při místním spuštění aplikace na příkazovém řádku. Z adresáře aplikace spusťte:

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* Přidejte položku do souboru *launchSettings.json* aplikace v profilu **Služby IIS Express.** Toto nastavení se používá při spuštění aplikace s ladicím `dotnet run`programem sady Visual Studio a z příkazového řádku s aplikací .

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* V sadě Visual Studio zadejte argument v**argumentech aplikace****ladění** >  **vlastností** > . Nastavení argumentu na stránce vlastností sady Visual Studio přidá argument do souboru *launchSettings.json.*

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a>Konfigurace Linkeru

Blazorprovede propojení zprostředkujícíjazyk (IL) na každé sestavení verze odebrat zbytečné IL z výstupních sestavení. Další informace naleznete v tématu <xref:host-and-deploy/blazor/configure-linker>.

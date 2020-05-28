---
title: ' hostování a nasazení ASP.NET Core Blazor WebAssembly ' Autor: Description: ' Naučte se hostovat a nasazovat Blazor aplikaci pomocí ASP.NET Core, sítě pro doručování obsahu (CDN), souborové servery a stránky GitHubu.
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

---
# <a name="host-and-deploy-aspnet-core-blazor-webassembly"></a>Hostování a nasazení ASP.NET Core Blazor WebAssembly

Od [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Skořepa](https://github.com/danroth27), [Robert Adams](https://twitter.com/ben_a_adams)a [Safia Abdalla](https://safia.rocks)

S [ Blazor modelem hostování WebAssembly](xref:blazor/hosting-models#blazor-webassembly):

* BlazorAplikace, její závislosti a modul runtime .NET jsou stahovány do prohlížeče paralelně.
* Aplikace se spustí přímo ve vlákně uživatelského rozhraní prohlížeče.

Podporují se tyto strategie nasazení:

* BlazorAplikaci obsluhuje aplikace ASP.NET Core. Tato strategie je popsaná v části [hostované nasazení s ASP.NET Core](#hosted-deployment-with-aspnet-core) .
* BlazorAplikace se umístí na statický hostující webový server nebo službu, kde rozhraní .NET se k obsluze aplikace nepoužívá Blazor . Tato strategie je popsaná v části [samostatné nasazení](#standalone-deployment) , která obsahuje informace o hostování Blazor aplikace WebAssembly jako dílčí aplikace služby IIS.

## <a name="brotli-precompression"></a>Předkomprese Brotli

Při Blazor publikování aplikace WebAssembly je výstup komprimován pomocí [kompresního algoritmu Brotli](https://tools.ietf.org/html/rfc7932) na nejvyšší úrovni, aby se snížila velikost aplikace a odstranila se nutnost komprese za běhu.

Konfiguraci komprese *souboru Web. config* služby IIS najdete v části [IIS: Brotli a komprese GZip](#brotli-and-gzip-compression) .

## <a name="rewrite-urls-for-correct-routing"></a>Přepište adresy URL pro správné směrování.

Žádosti o směrování pro součásti stránky v Blazor aplikaci WebAssembly nejsou tak jednoduché jako požadavky směrování na serveru, v němž je Blazor umístěná aplikace. Zvažte Blazor aplikaci WebAssembly se dvěma součástmi:

* *Main. Razor*: načte do kořenového adresáře aplikace a obsahuje odkaz na `About` komponentu ( `href="About"` ).
* *O nástroji. Razor*: `About` Komponenta

Pokud je výchozí dokument aplikace požadován pomocí panelu Adresa prohlížeče (například `https://www.contoso.com/` ):

1. Prohlížeč vytvoří požadavek.
1. Vrátí se výchozí stránka, což je obvykle *index. html*.
1. *index. html* se v aplikaci zabootstrap.
1. Blazorse načte směrovač a Razor `Main` Komponenta se vykreslí.

Na hlavní stránce vyberte odkaz na `About` komponentu na klientovi, protože Blazor směrovač zastaví v prohlížeči, aby odeslal požadavek na Internet `www.contoso.com` pro `About` a sloužil přímo vykreslené `About` součásti. Všechny požadavky na vnitřní koncové body *v Blazor aplikaci WebAssembly* fungují stejným způsobem: požadavky neaktivují požadavky založené na prohlížeči na prostředky hostované na serveru na internetu. Směrovač zpracovává požadavky interně.

Pokud je žádost vytvořena pomocí panelu Adresa prohlížeče pro `www.contoso.com/About` , požadavek se nezdařil. Žádný takový prostředek na internetovém hostiteli aplikace neexistuje, takže se vrátí odpověď *404 – Nenalezeno* .

Vzhledem k tomu, že prohlížeče vytvářejí požadavky na internetové hostitele pro stránky na straně klienta, webové servery a hostitelské služby musí přepsat všechny požadavky na prostředky, které nejsou fyzicky na serveru, na stránku *index. html* . Když se vrátí *index. html* , Blazor směrovač aplikace převezme a odpoví správným prostředkem.

Při nasazování na server služby IIS můžete použít modul pro přepis adres URL pomocí publikovaného souboru *Web. config* aplikace. Další informace najdete v části [IIS](#iis) .

## <a name="hosted-deployment-with-aspnet-core"></a>Hostované nasazení s ASP.NET Core

*Hostované nasazení* obsluhuje Blazor aplikaci WebAssembly pro prohlížeče z [aplikace ASP.NET Core](xref:index) , která běží na webovém serveru.

Klientská Blazor aplikace WebAssembly je publikovaná do složky */bin/Release/{Target Framework}/Publish/wwwroot* aplikace v serverové aplikaci společně s dalšími statickými webovými prostředky serverové aplikace. Obě aplikace se nasazují dohromady. Vyžaduje se webový server, který podporuje hostování aplikace ASP.NET Core. V případě hostovaného nasazení Visual Studio zahrnuje šablonu projektu ** Blazor aplikace pro WebAssembly** ( `blazorwasm` Šablona při použití příkazu [dotnet New](/dotnet/core/tools/dotnet-new) ) s vybranou možností **Hosted** ( `-ho|--hosted` při použití `dotnet new` příkazu).

Další informace o ASP.NET Core hostování a nasazení aplikací najdete v tématu <xref:host-and-deploy/index> .

Informace o nasazení do Azure App Service najdete v tématu <xref:tutorials/publish-to-azure-webapp-using-vs> .

## <a name="standalone-deployment"></a>Samostatné nasazení

*Samostatné nasazení* obsluhuje Blazor aplikaci WebAssembly jako sadu statických souborů, které jsou požadovány přímo klienty. Každý statický souborový server může Blazor aplikaci zpracovat.

Samostatné prostředky nasazení se publikují do složky */bin/Release/{Target Framework}/Publish/wwwroot* .

### <a name="azure-app-service"></a>Azure App Service

BlazorAplikace WebAssembly se dají nasadit do Azure App Services v systému Windows, který hostuje aplikaci ve [službě IIS](#iis).

Nasazení samostatné Blazor aplikace WebAssembly do Azure App Service pro Linux není aktuálně podporováno. Image serveru pro Linux, která je hostitelem aplikace, není v tuto chvíli k dispozici. Pro povolení tohoto scénáře probíhá práce.

### <a name="iis"></a>IIS

Služba IIS je schopným statickým souborovým serverem pro Blazor aplikace. Chcete-li nakonfigurovat službu IIS na hostování Blazor , přečtěte si téma [vytvoření statického webu ve službě IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).

Publikované assety se vytvoří ve složce */bin/Release/{Target Framework}/Publish* . Hostovat obsah složky pro *publikování* na webovém serveru nebo v hostitelské službě.

#### <a name="webconfig"></a>Web. config

Při Blazor publikování projektu se vytvoří soubor *Web. config* s následující konfigurací služby IIS:

* Typy MIME jsou nastaveny pro následující přípony souborů:
  * *. dll*:`application/octet-stream`
  * *. JSON*:`application/json`
  * *. wasm*:`application/wasm`
  * *. woff*:`application/font-woff`
  * *. woff2*:`application/font-woff`
* Pro následující typy MIME je povolena komprese protokolu HTTP:
  * `application/octet-stream`
  * `application/wasm`
* Pravidla pro přepis adres URL jsou navázána:
  * Slouží jako podadresáře, kde se nachází statické prostředky aplikace (*wwwroot/{cesta je požadovaná}*).
  * Vytvořte záložní záložní postup, aby se požadavky na nesouborové prostředky přesměrovaly do výchozího dokumentu aplikace ve složce statických prostředků (*wwwroot/index.html*).
  
#### <a name="use-a-custom-webconfig"></a>Použít vlastní web. config

Chcete-li použít vlastní soubor *Web. config* , umístěte vlastní soubor *Web. config* do kořenové složky projektu a publikujte projekt.

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

  Odeberte obslužnou rutinu v Blazor publikovaném souboru *Web. config* aplikace přidáním `<handlers>` oddílu do souboru:

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* Zakažte dědění kořenového oddílu (nadřazené) aplikace `<system.webServer>` pomocí `<location>` elementu s `inheritInChildApplications` nastavením na `false` :

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

#### <a name="brotli-and-gzip-compression"></a>Komprese Brotli a gzip

Službu IIS lze konfigurovat pomocí *souboru Web. config* pro poskytování komprimovaných prostředků Brotli nebo gzip Blazor . Příklad konfigurace naleznete v tématu [Web. config](webassembly/_samples/web.config?raw=true).

#### <a name="troubleshooting"></a>Řešení potíží

Pokud dojde k *chybě 500 – interní chyba serveru* a správce služby IIS vyvolá chyby při pokusu o přístup ke konfiguraci webu, potvrďte, že je nainstalován modul URL pro přepis. Pokud modul není nainstalován, soubor *Web. config* nelze analyzovat službou IIS. Tím se zabrání tomu, aby správce služby IIS načetl konfiguraci webu a web ze Blazor statických souborů obsluhy.

Další informace o řešení potíží s nasazeními služby IIS najdete v tématu <xref:test/troubleshoot-azure-iis> .

### <a name="azure-storage"></a>Azure Storage

Hostování statického souboru [Azure Storage](/azure/storage/) umožňuje Blazor hostování aplikací bez serveru. Podporují se názvy vlastních domén, Azure Content Delivery Network (CDN) a HTTPS.

Když je u služby BLOB Service povolené hostování statických webů v účtu úložiště:

* Nastavte **název dokumentu indexu** na `index.html` .
* Nastavte **cestu k chybovému dokumentu** na `index.html` . Razorsoučásti a jiné koncové body, které nejsou v souboru, se neukládají na fyzických cestách ve statickém obsahu uloženém službou BLOB Service. Když se přijme žádost o jeden z těchto prostředků, kterou Blazor by měl směrovač zpracovat, chyba *404 – nenalezená* služba BLOB Service směruje požadavek na **cestu k chybovému dokumentu**. Vrátí se objekt BLOB *index. html* a Blazor směrovač načte a zpracuje cestu.

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

Nasazení Blazor aplikace WebAssembly na CentOS 7 nebo novější:

1. Vytvořte konfigurační soubor Apache. Následující příklad je zjednodušený konfigurační soubor (*blazorapp. config*):

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

1. Soubor konfigurace Apache umístěte do `/etc/httpd/conf.d/` adresáře, který je výchozím adresářem konfigurace Apache v CentOS 7.

1. Umístěte soubory aplikace do `/var/www/blazorapp` adresáře (umístění zadaného do `DocumentRoot` konfiguračního souboru).

1. Restartujte službu Apache.

Další informace najdete v tématu [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) a [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).

### <a name="github-pages"></a>Stránky GitHubu

Chcete-li zpracovat přepisy adresy URL, přidejte soubor *404. html* pomocí skriptu, který zpracovává přesměrování požadavku na stránku *index. html* . Ukázkovou implementaci poskytovanou komunitou najdete v tématu [jednostránkové aplikace pro stránky GitHubu](https://spa-github-pages.rafrex.com/) ([rafrex/Spa – GitHub-Pages na GitHubu](https://github.com/rafrex/spa-github-pages#readme)). Příklad použití přístupu komunity se dá zobrazit v [blazor-demo/blazor-demo. GitHub. IO na GitHubu](https://github.com/blazor-demo/blazor-demo.github.io) ([živý web](https://blazor-demo.github.io/)).

Při použití webu projektu místo webu organizace přidejte nebo aktualizujte `<base>` značku v souboru *index. html*. Nastavte `href` hodnotu atributu na název úložiště GitHub s koncovým lomítkem (například `my-repository/` .

## <a name="host-configuration-values"></a>Hodnoty konfigurace hostitele

[ Blazor Aplikace WebAssembly](xref:blazor/hosting-models#blazor-webassembly) můžou přijmout následující hodnoty konfigurace hostitele jako argumenty příkazového řádku za běhu ve vývojovém prostředí.

### <a name="content-root"></a>Kořen obsahu

`--contentroot`Argument nastavuje absolutní cestu k adresáři, který obsahuje soubory obsahu aplikace ([kořen obsahu](xref:fundamentals/index#content-root)). V následujících příkladech `/content-root-path` je kořenová cesta obsahu aplikace.

* Předejte argument při místním spuštění aplikace z příkazového řádku. Z adresáře aplikace spusťte:

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* Přidejte položku do souboru *launchSettings. JSON* aplikace v profilu **IIS Express** . Toto nastavení se používá při spuštění aplikace s ladicím programem sady Visual Studio a z příkazového řádku s `dotnet run` .

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* V aplikaci Visual Studio zadejte argument v části **vlastnosti**  >  **ladit**  >  **argumenty aplikace**. Nastavení argumentu na stránce vlastností aplikace Visual Studio přidá argument do souboru *launchSettings. JSON* .

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a>Základ cesty

`--pathbase`Argument nastaví základní cestu aplikace pro aplikaci spouštěnou místně s nekořenovou cestou relativní adresy URL ( `<base>` značka `href` je nastavená na jinou cestu než `/` pro pracovní a produkční). V následujících příkladech `/relative-URL-path` je základ cesty aplikace. Další informace najdete v tématu [základní cesta k aplikaci](xref:host-and-deploy/blazor/index#app-base-path).

> [!IMPORTANT]
> Na rozdíl od cesty, která je k dispozici pro `href` `<base>` značku, nezahrnujte koncové lomítko ( `/` ) při předávání `--pathbase` hodnoty argumentu. Pokud se základní cesta aplikace poskytuje ve `<base>` značce jako `<base href="/CoolApp/">` (zahrnuje koncové lomítko), předejte hodnotu argumentu příkazového řádku jako `--pathbase=/CoolApp` (žádné koncové lomítko).

* Předejte argument při místním spuštění aplikace z příkazového řádku. Z adresáře aplikace spusťte:

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* Přidejte položku do souboru *launchSettings. JSON* aplikace v profilu **IIS Express** . Toto nastavení se používá při spuštění aplikace pomocí ladicího programu sady Visual Studio a z příkazového řádku s `dotnet run` .

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* V aplikaci Visual Studio zadejte argument v části **vlastnosti**  >  **ladit**  >  **argumenty aplikace**. Nastavení argumentu na stránce vlastností aplikace Visual Studio přidá argument do souboru *launchSettings. JSON* .

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a>Adresy URL

`--urls`Argument nastaví IP adresy nebo adresy hostitelů s porty a protokoly, které se mají na požadavky naslouchat.

* Předejte argument při místním spuštění aplikace z příkazového řádku. Z adresáře aplikace spusťte:

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* Přidejte položku do souboru *launchSettings. JSON* aplikace v profilu **IIS Express** . Toto nastavení se používá při spuštění aplikace pomocí ladicího programu sady Visual Studio a z příkazového řádku s `dotnet run` .

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* V aplikaci Visual Studio zadejte argument v části **vlastnosti**  >  **ladit**  >  **argumenty aplikace**. Nastavení argumentu na stránce vlastností aplikace Visual Studio přidá argument do souboru *launchSettings. JSON* .

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a>Konfigurace Linkeru

Blazorprovede propojení s mezijazykem (IL) na každém sestavení vydaných verzí a odebere z výstupních sestavení zbytečné IL. Další informace naleznete v tématu <xref:host-and-deploy/blazor/configure-linker>.

## <a name="custom-boot-resource-loading"></a>Načítání vlastního spouštěcího prostředku

BlazorAplikaci WebAssembly lze inicializovat pomocí `loadBootResource` funkce pro přepsání vestavěného mechanismu načítání prostředků spouštění. Použijte `loadBootResource` v následujících případech:

* Umožňuje uživatelům načíst statické prostředky, jako jsou data o časovém pásmu nebo *dotnet. wasm* z CDN.
* Načtěte komprimovaná sestavení pomocí požadavku HTTP a dekomprimujte je v klientovi pro hostitele, kteří nepodporují načítání komprimovaného obsahu ze serveru.
* Aliasujte prostředky na jiný název tak, že každý požadavek přesměrujete `fetch` na nový název.

`loadBootResource`parametry jsou uvedeny v následující tabulce.

| Parametr    | Description |
| ---
title: ' hostování a nasazení ASP.NET Core Blazor WebAssembly ' Autor: Description: ' Naučte se hostovat a nasazovat Blazor aplikaci pomocí ASP.NET Core, sítě pro doručování obsahu (CDN), souborové servery a stránky GitHubu.
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
title: ' hostování a nasazení ASP.NET Core Blazor WebAssembly ' Autor: Description: ' Naučte se hostovat a nasazovat Blazor aplikaci pomocí ASP.NET Core, sítě pro doručování obsahu (CDN), souborové servery a stránky GitHubu.
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
title: ' hostování a nasazení ASP.NET Core Blazor WebAssembly ' Autor: Description: ' Naučte se hostovat a nasazovat Blazor aplikaci pomocí ASP.NET Core, sítě pro doručování obsahu (CDN), souborové servery a stránky GitHubu.
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
title: ' hostování a nasazení ASP.NET Core Blazor WebAssembly ' Autor: Description: ' Naučte se hostovat a nasazovat Blazor aplikaci pomocí ASP.NET Core, sítě pro doručování obsahu (CDN), souborové servery a stránky GitHubu.
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

------ | ---Název: ' hostitel a nasazení ASP.NET Core Blazor WebAssembly ' Autor: Description: ' Naučte se hostovat a nasazovat Blazor aplikaci pomocí ASP.NET Core, sítě pro doručování obsahu (CDN), souborové servery a stránky GitHubu.
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
title: ' hostování a nasazení ASP.NET Core Blazor WebAssembly ' Autor: Description: ' Naučte se hostovat a nasazovat Blazor aplikaci pomocí ASP.NET Core, sítě pro doručování obsahu (CDN), souborové servery a stránky GitHubu.
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
title: ' hostování a nasazení ASP.NET Core Blazor WebAssembly ' Autor: Description: ' Naučte se hostovat a nasazovat Blazor aplikaci pomocí ASP.NET Core, sítě pro doručování obsahu (CDN), souborové servery a stránky GitHubu.
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

------ | | `type`       | Typ prostředku. Permissable typy: `assembly` , `pdb` , `dotnetjs` , `dotnetwasm` , `timezonedata` | | `name`       | Název prostředku. | | `defaultUri` | Relativní nebo absolutní identifikátor URI prostředku. | | `integrity`  | Řetězec integrity představující očekávaný obsah v odpovědi |

`loadBootResource`Vrátí některou z následujících možností pro přepsání procesu načítání:

* Řetězec identifikátoru URI. V následujícím příkladu (*wwwroot/index.html*) jsou následující soubory obsluhovány ze sítě CDN v `https://my-awesome-cdn.com/` :

  * *dotnet. \* .. js*
  * *dotnet. wasm*
  * Data časového pásma

  ```html
  ...

  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        console.log(`Loading: '${type}', '${name}', '${defaultUri}', '${integrity}'`);
        switch (type) {
          case 'dotnetjs':
          case 'dotnetwasm':
          case 'timezonedata':
            return `https://my-awesome-cdn.com/blazorwebassembly/3.2.0/${name}`;
        }
      }
    });
  </script>
  ```

* `Promise<Response>`. Předejte `integrity` parametr v hlavičce pro zachování výchozího chování kontroly integrity.

  Následující příklad (*wwwroot/index.html*) přidá vlastní hlavičku protokolu HTTP do odchozích požadavků a předá `integrity` parametr prostřednictvím `fetch` volání:
  
  ```html
  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        return fetch(defaultUri, { 
          cache: 'no-cache',
          integrity: integrity,
          headers: { 'MyCustomHeader': 'My custom value' }
        });
      }
    });
  </script>
  ```

* `null`/`undefined`, což vede k výchozímu chování při načítání.

Externí zdroje musí vracet požadované hlavičky CORS pro prohlížeče, aby bylo možné načíst prostředky mezi zdroji. Sítě CDN obvykle poskytuje ve výchozím nastavení požadované hlavičky.

Stačí zadat typy pro vlastní chování. Typy neurčené pro `loadBootResource` jsou načteny rozhraním na své výchozí chování při načítání.

## <a name="change-the-filename-extension-of-dll-files"></a>Změna přípony filename souborů DLL

V případě, že potřebujete změnit přípony názvů souborů publikovaných *knihoven DLL* aplikace, postupujte podle pokynů v této části.

Po publikování aplikace použijte skript prostředí nebo kanál sestavení DevOps k přejmenování souborů *. dll* tak, aby používaly jinou příponu souboru. Zaměřte se na soubory *. dll* v adresáři *wwwroot* publikovaného výstupu aplikace (například *{root obsahu}/bin/Release/netstandard2.1/Publish/wwwroot*).

V následujících příkladech jsou soubory *. dll* přejmenovány, aby používaly příponu souboru *. bin* .

Ve Windows:

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

Pokud se také používají prostředky pracovního procesu služby, přidejte následující příkaz:

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

V systému Linux nebo macOS:

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

Pokud se také používají prostředky pracovního procesu služby, přidejte následující příkaz:

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
Chcete-li použít jinou příponu souboru než *. bin*, nahraďte *. bin* v předchozích příkazech.

Chcete-li vyřešit komprimované soubory *blazor. boot. JSON. gz* a *blazor.boot.JSON.br* , proveďte jeden z následujících přístupů:

* Odstraňte komprimované soubory *blazor. boot. JSON. gz* a *blazor.boot.JSON.br* . Komprese je u tohoto přístupu zakázána.
* Opětovná komprimace aktualizovaného souboru *blazor. boot. JSON* .

Předchozí pokyny platí i v případě, že se aktivují prostředky pracovního procesu. Odeberte nebo znovu zkomprimujete *wwwroot/Service-Worker-assets. js. br* a *wwwroot/Service-Worker-assets. js. gz*. V opačném případě kontroly integrity souborů selžou v prohlížeči.

Následující příklad Windows používá skript prostředí PowerShell umístěný v kořenovém adresáři projektu.

*ChangeDLLExtensions. ps1:*:

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

Pokud se také používají prostředky pracovního procesu služby, přidejte následující příkaz:

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

V souboru projektu se skript spustí po publikování aplikace:

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

Pokud chcete poskytnout zpětnou vazbu, navštivte [aspnetcore/problémy #5477](https://github.com/dotnet/aspnetcore/issues/5477).
 
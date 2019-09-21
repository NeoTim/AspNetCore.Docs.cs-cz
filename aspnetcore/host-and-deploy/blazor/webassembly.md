---
title: Hostování a nasazení ASP.NET Core Blazor WebAssembly
author: guardrex
description: Naučte se hostovat a nasazovat aplikaci Blazor pomocí ASP.NET Core, stránek Content Delivery Networks (CDN), souborových serverů a GitHubu.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
uid: host-and-deploy/blazor/webassembly
ms.openlocfilehash: b7de145ae9924dd405e67f60fc93d81549834164
ms.sourcegitcommit: e5a74f882c14eaa0e5639ff082355e130559ba83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/20/2019
ms.locfileid: "71168229"
---
# <a name="host-and-deploy-aspnet-core-blazor-webassembly"></a>Hostování a nasazení ASP.NET Core Blazor WebAssembly

Od [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com)a [Daniel Skořepa](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

S [modelem hostování WebAssembly Blazor](xref:blazor/hosting-models#blazor-webassembly):

* Do prohlížeče se stáhnou aplikace Blazor, její závislosti a modul runtime .NET.
* Aplikace se spustí přímo ve vlákně uživatelského rozhraní prohlížeče.

Podporují se tyto strategie nasazení:

* Aplikace Blazor obsluhuje aplikaci ASP.NET Core. Tato strategie je popsaná v části [hostované nasazení s ASP.NET Core](#hosted-deployment-with-aspnet-core) .
* Aplikace Blazor je umístěna na statický hostující webový server nebo službu, kde rozhraní .NET se nepoužívá k obsluze aplikace Blazor. Tato strategie je popsaná v části [samostatné nasazení](#standalone-deployment) , která obsahuje informace o hostování aplikace Blazor WebAssembly jako dílčí aplikace služby IIS.

## <a name="rewrite-urls-for-correct-routing"></a>Přepište adresy URL pro správné směrování.

Požadavky směrování na součásti stránky v aplikaci Blazor WebAssembly nejsou stejně jednoduché jako požadavky směrování na serveru Blazor a v hostované aplikaci. Zvažte Blazor aplikaci WebAssembly se dvěma komponentami:

* *Main. Razor* &ndash; se načte do kořenového adresáře aplikace a obsahuje odkaz na `About` komponentu (`href="About"`).
* *O komponentě. Razor* &ndash; `About` .

Pokud je výchozí dokument aplikace požadován pomocí panelu Adresa prohlížeče (například `https://www.contoso.com/`):

1. Prohlížeč vytvoří požadavek.
1. Vrátí se výchozí stránka, což je obvykle *index. html*.
1. *index. html* se v aplikaci zabootstrap.
1. Blazor směrovač se načte a komponenta Razor `Main` se vykreslí.

Na hlavní `About` stránce vyberte odkaz na komponentu na klientovi, protože směrovač Blazor zabrání v prohlížeči, aby `www.contoso.com` odeslal požadavek na Internet pro `About` a obsluhu samotné vykreslené `About` komponenty. Všechny požadavky na vnitřní koncové body *v aplikaci Blazor WebAssembly* fungují stejným způsobem: Požadavky neaktivují požadavky založené na prohlížeči na prostředky hostované na serveru na internetu. Směrovač zpracovává požadavky interně.

Pokud je žádost vytvořena pomocí panelu Adresa prohlížeče pro `www.contoso.com/About`, požadavek se nezdařil. Žádný takový prostředek na internetovém hostiteli aplikace neexistuje, takže se vrátí odpověď *404 –* Nenalezeno.

Vzhledem k tomu, že prohlížeče vytvářejí požadavky na internetové hostitele pro stránky na straně klienta, webové servery a hostitelské služby musí přepsat všechny požadavky na prostředky, které nejsou fyzicky na serveru, na stránku *index. html* . Když se vrátí *index. html* , Blazor směrovač aplikace převezme a odpoví správným prostředkem.

Při nasazování na server služby IIS můžete použít modul pro přepis adres URL pomocí publikovaného souboru *Web. config* aplikace. Další informace najdete v části [IIS](#iis) .

## <a name="hosted-deployment-with-aspnet-core"></a>Hostované nasazení s ASP.NET Core

*Hostované nasazení* obsluhuje aplikaci Blazor WebAssembly pro prohlížeče z [aplikace ASP.NET Core](xref:index) , která běží na webovém serveru.

Aplikace Blazor je součástí aplikace ASP.NET Core v publikovaném výstupu, takže se tyto dvě aplikace nasazují dohromady. Vyžaduje se webový server, který podporuje hostování aplikace ASP.NET Core. V případě hostovaného nasazení Visual Studio zahrnuje šablonu projektu **aplikace WebAssembly Blazor** (`blazorwasm` šablona při použití příkazu [dotnet New](/dotnet/core/tools/dotnet-new) ) se zvolenou možností **Hosted** .

Další informace o ASP.NET Core hostování a nasazení aplikací najdete v tématu <xref:host-and-deploy/index>.

Informace o nasazení do Azure App Service najdete v tématu <xref:tutorials/publish-to-azure-webapp-using-vs>.

## <a name="standalone-deployment"></a>Samostatné nasazení

*Samostatné nasazení* obsluhuje aplikaci Blazor WebAssembly jako sadu statických souborů, které jsou požadovány přímo klienty. Libovolný statický souborový server může obsluhovat aplikaci Blazor.

Samostatné prostředky nasazení se publikují do složky *bin/Release/{Target Framework}/PUBLISH/{Assembly Name}/DIST* .

### <a name="iis"></a>IIS

Služba IIS je schopným statickým souborovým serverem pro aplikace Blazor. Chcete-li nakonfigurovat službu IIS na hostování Blazor, přečtěte si téma [vytvoření statického webu ve službě IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).

Publikované assety se vytvoří ve složce */bin/Release/{Target Framework}/Publish* . Hostovat obsah složky pro *publikování* na webovém serveru nebo v hostitelské službě.

#### <a name="webconfig"></a>web.config

Při publikování projektu Blazor se vytvoří soubor *Web. config* s následující konfigurací služby IIS:

* Typy MIME jsou nastaveny pro následující přípony souborů:
  * *.dll* &ndash; `application/octet-stream`
  * *.json* &ndash; `application/json`
  * *.wasm* &ndash; `application/wasm`
  * *.woff* &ndash; `application/font-woff`
  * *.woff2* &ndash; `application/font-woff`
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

  Odeberte obslužnou rutinu v publikovaném souboru *Web. config* aplikace Blazor přidáním `<handlers>` oddílu do souboru:

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* Zakažte dědění kořenového `<system.webServer>` oddílu (nadřazené) aplikace `<location>` pomocí elementu s `inheritInChildApplications` nastavením na `false`:

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

#### <a name="troubleshooting"></a>Poradce při potížích

Pokud dojde k *chybě 500 – interní chyba serveru* a správce služby IIS vyvolá chyby při pokusu o přístup ke konfiguraci webu, potvrďte, že je nainstalován modul URL pro přepis. Pokud modul není nainstalován, soubor *Web. config* nelze analyzovat službou IIS. Tím se zabrání tomu, aby správce služby IIS načetl konfiguraci webu a web neobsluhuje statické soubory Blazor.

Další informace o řešení potíží s nasazeními služby IIS najdete <xref:test/troubleshoot-azure-iis>v tématu.

### <a name="azure-storage"></a>Azure Storage

Hostování statického souboru [Azure Storage](/azure/storage/) umožňuje hostování aplikace bez serveru. Podporují se názvy vlastních domén, Azure Content Delivery Network (CDN) a HTTPS.

Když je u služby BLOB Service povolené hostování statických webů v účtu úložiště:

* Nastavte **název dokumentu indexu** na `index.html`.
* Nastavte **cestu k chybovému dokumentu** na. `index.html` Komponenty Razor a jiné nesouborové koncové body se neukládají na fyzických cestách se statickým obsahem uloženým ve službě BLOB Service. Když se obdrží požadavek na jeden z těchto prostředků, který by měl směrovač Blazor zpracovat, Chyba *404 –* chyba, kterou vygenerovala služba BLOB Service, směruje požadavek na **cestu k chybovému dokumentu**. Vrátí se objekt BLOB *index. html* a Blazor směrovač načte a zpracuje cestu.

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

Pokud chcete hostovat Blazor v Docker pomocí Nginx, nastavte souboru Dockerfile pro použití image Nginx založené na Alpine. Aktualizujte souboru Dockerfile a zkopírujte soubor *Nginx. config* do kontejneru.

Do souboru Dockerfile přidejte jeden řádek, jak je znázorněno v následujícím příkladu:

```Dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="github-pages"></a>Stránky GitHubu

Chcete-li zpracovat přepisy adresy URL, přidejte soubor *404. html* pomocí skriptu, který zpracovává přesměrování požadavku na stránku *index. html* . Ukázkovou implementaci poskytovanou komunitou najdete v tématu [jednostránkové aplikace pro stránky GitHubu](https://spa-github-pages.rafrex.com/) ([rafrex/Spa – GitHub-Pages na GitHubu](https://github.com/rafrex/spa-github-pages#readme)). Příklad použití přístupu komunity se dá zobrazit v [blazor-demo/blazor-demo. GitHub. IO na GitHubu](https://github.com/blazor-demo/blazor-demo.github.io) ([živý web](https://blazor-demo.github.io/)).

Při použití webu projektu místo webu organizace přidejte nebo aktualizujte `<base>` značku v souboru *index. html*. Nastavte hodnotu `my-repository/`atributu na název úložiště GitHub s koncovým lomítkem (například. `href`

## <a name="host-configuration-values"></a>Hodnoty konfigurace hostitele

[Aplikace Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) můžou přijmout následující hodnoty konfigurace hostitele jako argumenty příkazového řádku za běhu ve vývojovém prostředí.

### <a name="content-root"></a>Kořen obsahu

`--contentroot` Argument nastavuje absolutní cestu k adresáři, který obsahuje soubory obsahu aplikace. V následujících příkladech `/content-root-path` je kořenová cesta obsahu aplikace.

* Předejte argument při místním spuštění aplikace z příkazového řádku. Z adresáře aplikace spusťte:

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* Přidejte položku do souboru *launchSettings. JSON* aplikace v profilu **IIS Express** . Toto nastavení se používá při spuštění aplikace s ladicím programem sady Visual Studio a z příkazového řádku s `dotnet run`.

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* V aplikaci Visual Studio zadejte argument v části **vlastnosti** > **ladit** > **argumenty aplikace**. Nastavení argumentu na stránce vlastností aplikace Visual Studio přidá argument do souboru *launchSettings. JSON* .

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a>Základ cesty

Argument nastaví základní cestu aplikace pro aplikaci spouštěnou místně s nekořenovou cestou relativní adresy URL `<base>` (značka `href` je nastavená na jinou cestu než `/` pro pracovní a produkční). `--pathbase` V následujících příkladech `/relative-URL-path` je základ cesty aplikace. Další informace najdete v tématu [základní cesta k aplikaci](xref:host-and-deploy/blazor/index#app-base-path).

> [!IMPORTANT]
> Na rozdíl od cesty, která `href` je k `<base>` dispozici pro značku, nezahrnujte koncové lomítko`/` `--pathbase` () při předávání hodnoty argumentu. Pokud se základní cesta aplikace poskytuje ve `<base>` značce jako `<base href="/CoolApp/">` (zahrnuje koncové lomítko), předejte hodnotu argumentu příkazového řádku jako `--pathbase=/CoolApp` (žádné koncové lomítko).

* Předejte argument při místním spuštění aplikace z příkazového řádku. Z adresáře aplikace spusťte:

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* Přidejte položku do souboru *launchSettings. JSON* aplikace v profilu **IIS Express** . Toto nastavení se používá při spuštění aplikace pomocí ladicího programu sady Visual Studio a z příkazového řádku `dotnet run`s.

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* V aplikaci Visual Studio zadejte argument v části **vlastnosti** > **ladit** > **argumenty aplikace**. Nastavení argumentu na stránce vlastností aplikace Visual Studio přidá argument do souboru *launchSettings. JSON* .

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a>URL – adresy

`--urls` Argument nastaví IP adresy nebo adresy hostitelů s porty a protokoly, které se mají na požadavky naslouchat.

* Předejte argument při místním spuštění aplikace z příkazového řádku. Z adresáře aplikace spusťte:

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* Přidejte položku do souboru *launchSettings. JSON* aplikace v profilu **IIS Express** . Toto nastavení se používá při spuštění aplikace pomocí ladicího programu sady Visual Studio a z příkazového řádku `dotnet run`s.

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* V aplikaci Visual Studio zadejte argument v části **vlastnosti** > **ladit** > **argumenty aplikace**. Nastavení argumentu na stránce vlastností aplikace Visual Studio přidá argument do souboru *launchSettings. JSON* .

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a>Konfigurace Linkeru

Blazor provádí propojení podle jazyka IL (Intermediate Language) na každém sestavení pro odebrání zbytečného IL z výstupních sestavení. Propojení sestavení lze řídit při sestavování. Další informace naleznete v tématu <xref:host-and-deploy/blazor/configure-linker>.

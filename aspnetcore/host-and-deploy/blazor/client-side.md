---
title: Hostitelství a nasazení aplikace ASP.NET Core Blazor na straně klienta
author: guardrex
description: Zjistěte, jak hostovat a nasazení Blazor aplikace pomocí ASP.NET Core, Content Delivery Network (CDN), souborové servery a stránkách Githubu.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 06/14/2019
uid: host-and-deploy/blazor/client-side
ms.openlocfilehash: 7567473ae8acd9e1072954907f0fe9c7beea29ad
ms.sourcegitcommit: 4ef0362ef8b6e5426fc5af18f22734158fe587e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2019
ms.locfileid: "67153188"
---
# <a name="host-and-deploy-aspnet-core-blazor-client-side"></a>Hostitelství a nasazení aplikace ASP.NET Core Blazor na straně klienta

Podle [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), a [Daniel Roth](https://github.com/danroth27)

## <a name="host-configuration-values"></a>Hodnoty konfigurace hostitele

Blazor aplikací, které používají [model hostingu na straně klienta](xref:blazor/hosting-models#client-side) může přijmout hodnoty následující konfigurace hostitele jako argumenty příkazového řádku za běhu ve vývojovém prostředí.

### <a name="content-root"></a>Obsah kořenové

`--contentroot` Argument Nastaví absolutní cestu k adresáři, který obsahuje soubory obsahu aplikace. V následujících příkladech `/content-root-path` je obsahu kořenovou cestu aplikace.

* Předejte argument při spuštění aplikace místně na příkazovém řádku. Z adresáře aplikace spusťte:

  ```console
  dotnet run --contentroot=/content-root-path
  ```

* Přidejte záznam do aplikace *launchSettings.json* soubor **služby IIS Express** profilu. Toto nastavení se používá při spuštění aplikace pomocí ladicího programu sady Visual Studio a z příkazového řádku s `dotnet run`.

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* V sadě Visual Studio, zadat argument v **vlastnosti** > **ladění** > **argumenty aplikace**. Nastavení argumentu na stránce vlastností Visual Studio přidá argument *launchSettings.json* souboru.

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a>Základ cesty

`--pathbase` Argument nastaví základní cesty aplikace pro aplikaci pro místní spuštění s virtuální cestou nekořenovými ( `<base>` značky `href` nastavený na cestu jiné než `/` pro pracovní a provozní). V následujících příkladech `/virtual-path` je základní cesty aplikace. Další informace najdete v tématu [základní cesty aplikace](#app-base-path) oddílu.

> [!IMPORTANT]
> Na rozdíl od zadaná cesta k `href` z `<base>` značku, nemusíte zahrnovat koncové lomítko (`/`) při předávání `--pathbase` hodnota argumentu. Pokud je základní cesta aplikace součástí `<base>` označit jako `<base href="/CoolApp/">` (včetně koncového lomítka), předejte hodnotu argument příkazového řádku jako `--pathbase=/CoolApp` (žádného koncového lomítka).

* Předejte argument při spuštění aplikace místně na příkazovém řádku. Z adresáře aplikace spusťte:

  ```console
  dotnet run --pathbase=/virtual-path
  ```

* Přidejte záznam do aplikace *launchSettings.json* soubor **služby IIS Express** profilu. Toto nastavení se používá při spuštění aplikace pomocí ladicího programu sady Visual Studio a z příkazového řádku s `dotnet run`.

  ```json
  "commandLineArgs": "--pathbase=/virtual-path"
  ```

* V sadě Visual Studio, zadat argument v **vlastnosti** > **ladění** > **argumenty aplikace**. Nastavení argumentu na stránce vlastností Visual Studio přidá argument *launchSettings.json* souboru.

  ```console
  --pathbase=/virtual-path
  ```

### <a name="urls"></a>URL – adresy

`--urls` Argument nastaví IP adres nebo adres hostitele s porty a protokoly pro naslouchání požadavkům.

* Předejte argument při spuštění aplikace místně na příkazovém řádku. Z adresáře aplikace spusťte:

  ```console
  dotnet run --urls=http://127.0.0.1:0
  ```

* Přidejte záznam do aplikace *launchSettings.json* soubor **služby IIS Express** profilu. Toto nastavení se používá při spuštění aplikace pomocí ladicího programu sady Visual Studio a z příkazového řádku s `dotnet run`.

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* V sadě Visual Studio, zadat argument v **vlastnosti** > **ladění** > **argumenty aplikace**. Nastavení argumentu na stránce vlastností Visual Studio přidá argument *launchSettings.json* souboru.

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="deployment"></a>Nasazení

S [model hostingu na straně klienta](xref:blazor/hosting-models#client-side):

* Aplikace Blazor, jeho závislosti a modul .NET runtime se stáhnou do prohlížeče.
* Aplikace je proveden přímo v prohlížeči vlákno uživatelského rozhraní. Je podporován některý z následujících strategií:
  * Aplikace Blazor obsluhují aplikace ASP.NET Core. Tato strategie je obsažen v [hostované nasazení pomocí technologie ASP.NET Core](#hosted-deployment-with-aspnet-core) oddílu.
  * Blazor aplikace je umístěn na statické hostování webového serveru nebo službě, kde není .NET používají k předávání Blazor aplikace. Tato strategie je obsažen v [samostatné nasazení](#standalone-deployment) oddílu.

## <a name="configure-the-linker"></a>Konfigurace Linkeru

Blazor provádí Intermediate Language (IL) propojení na každé sestavení odebrat nepotřebné IL z výstupního sestavení. Propojování sestavení je možné řídit na sestavení. Další informace naleznete v tématu <xref:host-and-deploy/blazor/configure-linker>.

## <a name="rewrite-urls-for-correct-routing"></a>Přepisování adres URL pro správné směrování

Směrování požadavků pro součásti stránky v aplikaci na straně klienta není snadné – stačí směrování žádostí na aplikace na straně serveru, prostředí. Vezměte v úvahu aplikace na straně klienta se dvěma stránkami:

* **_Main.Razor** &ndash; zatížení v kořenovém adresáři aplikace a obsahuje odkaz na stránku o (`href="About"`).
* **_About.Razor** &ndash; o stránce.

Pokud aplikace výchozí dokument se požaduje pomocí panelu Adresa prohlížeče (například `https://www.contoso.com/`):

1. Prohlížeč odešle požadavek.
1. Vrátí výchozí stránku, což je obvykle *index.html*.
1. *index.HTML* bootstraps aplikace.
1. Směrovač Blazor na zatížení a Razor hlavní stránky (*Main.razor*) se zobrazí.

Na hlavní stránce vyberte odkaz na stránku o načte stránku o. Vyberte odkaz na stránku o funguje na straně klienta, protože směrovače Blazor zastaví prohlížeče z požadavku na Internetu, aby `www.contoso.com` pro `About` a slouží samotná stránka o. Všechny žádosti pro interní stránky *v rámci aplikace na straně klienta* stejným způsobem fungovat: Požadavky nejsou aktivace založené na prohlížeči požadavky na prostředky serveru hostované na Internetu. Směrovač interně zpracovává požadavky.

Pokud je vytvořena pomocí panelu Adresa prohlížeče pro žádost `www.contoso.com/About`, požadavek selže. Žádný takový prostředek neexistuje na hostiteli aplikace Internet, tak *404 - Nenalezeno* vrátí odpověď.

Protože prohlížeče zasílat požadavky do Internetu na hostitele pro stránky na straně klienta, webové servery a služby hostování musí přepsat všechny požadavky na prostředky nejsou fyzicky na server, aby *index.html* stránky. Když *index.html* se vrátí, má aplikace na straně klienta směrovače a odpovídá zprávou správný zdroj.

## <a name="app-base-path"></a>Základní cesta aplikace

*Základní cesty aplikace* je kořenová cesta virtuální aplikace na serveru. Například aplikace, které se nacházejí na serveru Contoso ve virtuální složce na `/CoolApp/` je dosažena `https://www.contoso.com/CoolApp` a má virtuální základní cesta `/CoolApp/`. Nastavení základní cesty aplikace na virtuální cestu (`<base href="/CoolApp/">`), aplikace se provádí vědět, kde se prakticky nachází na serveru. Aplikace můžete použít základní cesta aplikace k vytvoření adresy URL kořeni aplikace z komponenty, která není v kořenovém adresáři. To umožňuje součásti, které existují na různých úrovních adresářovou strukturu vytvářet odkazy na další zdroje v umístěních v celé aplikaci. Základní cesta aplikace se také používá k zachycení hypertextový odkaz klikne, kde `href` cíl odkazu je v rámci základní cesty aplikace místo identifikátoru URI&mdash;směrovače Blazor zpracovává vnitřní navigační.

V mnoha scénářích hostování serveru virtuální cesta k aplikaci je kořenový adresář aplikace. V těchto případech je základní cesty aplikace lomítkem (`<base href="/" />`), což je výchozí konfigurace pro aplikaci. V jiných scénářích hostování, jako jsou virtuální adresáře stránkách Githubu a služby IIS nebo dílčí aplikace základní cesty aplikace nastavte na serveru virtuální cesta k aplikaci. Nastavení základní cesty aplikace, aktualizujte `<base>` značky v rámci `<head>` značky elementů *wwwroot/index.html* souboru. Nastavte `href` atribut hodnotu `/virtual-path/` (vyžaduje se do adresy koncové lomítko), kde `/virtual-path/` je kořenová cesta úplnou virtuální aplikace na serveru pro aplikaci. V předchozím příkladu je nastavena virtuální cesta `/CoolApp/`: `<base href="/CoolApp/">`.

Pro aplikace s virtuální cestou nekořenovými nakonfigurovali (například `<base href="/CoolApp/">`), aplikace nenajde žádné její prostředky *při spuštění místně*. Chcete-li tento problém vyřešit během místní vývoj a testování, můžete zadat *základ cesty* argument, který odpovídá `href` hodnotu `<base>` značky v době běhu.

Předat argument základní cesty s kořenovou cestou (`/`) při místním spuštění aplikace, spusťte `dotnet run` příkaz z adresáře aplikace se `--pathbase` možnost:

```console
dotnet run --pathbase=/{Virtual Path (no trailing slash)}
```

Pro aplikace s virtuální cestou základní `/CoolApp/` (`<base href="/CoolApp/">`), má příkaz tuto podobu:

```console
dotnet run --pathbase=/CoolApp
```

Reakce aplikace na místně `http://localhost:port/CoolApp`.

Další informace najdete v části na [hodnota konfigurace hostitele základní cesty](#path-base).

Pokud aplikace používá [model hostingu na straně klienta](xref:blazor/hosting-models#client-side) (na základě **Blazor** šablonu projektu; `blazor` šablony při použití [dotnet nové](/dotnet/core/tools/dotnet-new) příkaz) a je hostovaná jako dílčí aplikace služby IIS v aplikaci ASP.NET Core, je potřeba zakázat obslužnou rutinu zděděné modul ASP.NET Core nebo Ujistěte se, že aplikace root (nadřazené) `<handlers>` tématu *web.config* soubor není děděný Sub – aplikace.

Odebrat publikování obslužné rutiny v aplikaci *web.config* souboru tak, že přidáte `<handlers>` část do souboru:

```xml
<handlers>
  <remove name="aspNetCore" />
</handlers>
```

Můžete také zakázat dědičnost (nadřazené) kořenové aplikace `<system.webServer>` části pomocí `<location>` element s `inheritInChildApplications` nastavena na `false`:

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

Kromě konfigurace základní cesty aplikace, jak je popsáno v této části se provádí odebírá obslužnou rutinu nebo zakážou dědičnost. Nastavení základní cesty aplikace v aplikaci prvku *index.html* soubor do služby IIS alias používaný při konfiguraci podřízeným aplikacím ve službě IIS.

## <a name="hosted-deployment-with-aspnet-core"></a>Hostované nasazení pomocí technologie ASP.NET Core

A *hostované nasazení* slouží Blazor aplikace na straně klienta z prohlížečů [aplikace ASP.NET Core](xref:index) , který běží na serveru.

Blazor aplikace je součástí aplikace ASP.NET Core v publikované výstup tak, aby dvě aplikace se nasazují společně. Webový server, který dokáže hostovat aplikace ASP.NET Core je povinný. Hostované nasazení Visual Studio obsahuje **Blazor (ASP.NET Core hostované)** šablonu projektu (`blazorhosted` šablony při použití [dotnet nové](/dotnet/core/tools/dotnet-new) příkaz).

Další informace o nasazení a hostování aplikací ASP.NET Core najdete v tématu <xref:host-and-deploy/index>.

Informace o nasazení do služby Azure App Service najdete v tématu <xref:tutorials/publish-to-azure-webapp-using-vs>.

## <a name="standalone-deployment"></a>Samostatné nasazení

A *samostatné nasazení* slouží jako sadu statické soubory, které jsou požadovány přímo klienty Blazor aplikace na straně klienta. Žádné statický souborový server je schopná odesílat Blazor aplikace.

Jsou publikovány v samostatných nasazení prostředků *bin/Release / {TARGET FRAMEWORK} /publish/ {název sestavení} / dist* složky.

### <a name="iis"></a>IIS

Služba IIS je schopen statický souborový server pro Blazor aplikace. Můžete nakonfigurovat službu IIS do hostitele Blazor, naleznete v tématu [vytvoření statického webu ve službě IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).

Publikované prostředky vytvořené v */bin/vydání / {CÍLOVÁ ARCHITEKTURA} / publish* složky. Hostování obsahu *publikovat* složku na webovém serveru nebo hostující služby.

#### <a name="webconfig"></a>web.config

Při publikování projektu Blazor *web.config* soubor se vytvoří s následující konfigurací služby IIS:

* Typy MIME jsou nastavené pro následující přípony souborů:
  * *.dll* &ndash; `application/octet-stream`
  * *.json* &ndash; `application/json`
  * *.wasm* &ndash; `application/wasm`
  * *.woff* &ndash; `application/font-woff`
  * *.woff2* &ndash; `application/font-woff`
* Pro následující typy MIME je povolena komprese protokolu HTTP:
  * `application/octet-stream`
  * `application/wasm`
* Modul přepisování adres URL pravidla jsou vytvořeny:
  * Poskytovat dílčí adresáře, kde jsou umístěny statických prostředků aplikace ( */dist/ {název sestavení} {cesta k požadované}* ).
  * Vytvoření aplikace SPA záložní směrování tak, aby požadavky pro nesouborové prostředky se přesměrují do aplikace výchozí dokument v její složce statické prostředky ( *{sestavení NAME}/dist/index.html*).

#### <a name="install-the-url-rewrite-module"></a>Nainstalovat modul přepisování adres URL

[Modul přepisování adres URL](https://www.iis.net/downloads/microsoft/url-rewrite) se vyžaduje k přepsání adresy URL. Ve výchozím nastavení není nainstalován modul a není k dispozici pro instalaci jako funkci služby role Webový Server (IIS). Modul musí stáhnout z webu služby IIS. Použití instalačního programu webové platformy nainstalovat modul:

1. Místně, přejděte [modul přepisování adres URL stránky pro stažení](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads). Pro anglickou verzi, vyberte **instalace webové platformy** pro stažení instalačního programu Instalace webové platformy. Pro jiné jazyky vyberte příslušnou architekturu pro server (x86/x64) pro stažení instalačního programu.
1. Zkopírujte instalační program na server. Spusťte instalační program. Vyberte **nainstalovat** tlačítko a přijměte licenční podmínky. Po dokončení instalace není nutné restartovat server.

#### <a name="configure-the-website"></a>Konfiguraci webové stránky

Nastavit na webu **fyzická cesta** do složky aplikace. Složka obsahuje:

* *Web.config* soubor, který služba IIS používá pro konfiguraci webové stránky, včetně pravidel požadovaná přesměrování a typy obsahu souborů.
* Složku statických prostředků aplikace.

#### <a name="troubleshooting"></a>Poradce při potížích

Pokud *500 – Interní chyba serveru* přijetí a Správce služby IIS vyvolá chyby při pokusu o přístup ke konfiguraci příslušného webu, ověřte, že je nainstalován modul přepisování adres URL. Když není nainstalován modul, *web.config* soubor nejde parsovat službou IIS. To zabrání načítání konfigurace na webu a webu z obsluhy statických souborů pro Blazor Správce služby IIS.

Další informace o řešení potíží s nasazením do služby IIS najdete v tématu <xref:host-and-deploy/iis/troubleshoot>.

### <a name="azure-storage"></a>Azure Storage

Hostování statického souboru v Azure Storage umožňuje hostování bez serveru Blazor aplikací. Vlastní názvy domén, Azure Content Delivery Network (CDN) a protokolu HTTPS se nepodporuje.

Když je povolena služba objektů blob pro hostování statického webu v účtu úložiště:

* Nastavte **název dokumentu indexu** k `index.html`.
* Nastavte **cesta dokumentu chyby** k `index.html`. Součásti Razor a ostatní koncové body nesouborové není nachází na fyzické cesty v statického obsahu, které jsou uložené ve službě blob. Při přijetí požadavku z jednoho z těchto prostředků je, že by měl zpracovat Blazor směrovače, *404 - Nenalezeno* chyby vygenerované službou blob směruje žádosti **cesta dokumentu chyby**. *Index.html* je vrácen objekt blob a směrovač Blazor načte a zpracuje cestu.

Další informace najdete v tématu [hostoval statický web ve službě Azure Storage](/azure/storage/blobs/storage-blob-static-website).

### <a name="nginx"></a>Server Nginx

Následující *nginx.conf* souboru je zjednodušenou ukazují, jak nakonfigurovat Nginx tak, aby odeslat *index.html* souboru pokaždé, když nemůže najít odpovídající soubor na disku.

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

Další informace o konfiguraci serveru webového serveru Nginx produkčního prostředí, najdete v části [NGINX konfiguračních souborů a vytváří NGINX Plus](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).

### <a name="nginx-in-docker"></a>Server Nginx v Docker

K hostování Blazor v Dockeru pomocí serveru Nginx, instalační program soubor Dockerfile, který chcete použít image serveru Nginx Alpine na základě. Aktualizace souboru Dockerfile zkopírovat *nginx.config* souboru do kontejneru.

Přidejte jeden řádek do souboru Dockerfile, jak je znázorněno v následujícím příkladu:

```Dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="github-pages"></a>Stránkách Githubu

Pro zpracování přepisů adresu URL, přidejte *404. html* soubor skriptu, který zpracovává požadavek na přesměrování *index.html* stránky. Příklad implementace poskytované komunitou, naleznete v tématu [jedné stránky aplikace pro stránky Githubu](http://spa-github-pages.rafrex.com/) ([rafrex/spa – github stránky na Githubu](https://github.com/rafrex/spa-github-pages#readme)). Příklad použití komunity přístup, můžete zobrazit v [blazor-demo/blazor-demo.github.io na Githubu](https://github.com/blazor-demo/blazor-demo.github.io) ([živého webu](https://blazor-demo.github.io/)).

Když použijete web projektu namísto serveru organizace, přidat nebo aktualizovat `<base>` značku *index.html*. Nastavte `href` hodnotu atributu na název úložiště GitHub s koncovým lomítkem (například `my-repository/`.

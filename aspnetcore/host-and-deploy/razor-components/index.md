---
title: Hostitelství a nasazení součásti syntaxe Razor
author: guardrex
description: Objevte, jak hostovat a nasadit komponenty Razor a Blazor aplikace pomocí ASP.NET Core, Content Delivery Network (CDN), souborové servery a stránkách Githubu.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 03/22/2019
uid: host-and-deploy/razor-components/index
ms.openlocfilehash: 236e8da27b80dbdb3e0ea885413b6cfd563dde60
ms.sourcegitcommit: 7d6019f762fc5b8cbedcd69801e8310f51a17c18
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2019
ms.locfileid: "58419417"
---
# <a name="host-and-deploy-razor-components"></a>Hostitelství a nasazení součásti syntaxe Razor

Podle [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), a [Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

## <a name="publish-the-app"></a>Publikování aplikace

Aplikace budou publikovány pro nasazení v rámci konfigurace verze se [dotnet publikovat](/dotnet/core/tools/dotnet-publish) příkazu. Integrované vývojové prostředí může zpracovat provádění `dotnet publish` příkaz automaticky pomocí jeho integrované funkce pro publikování, proto nemusí být potřeba ručně spusťte příkaz z příkazového řádku v závislosti na nástroje pro vývoj v použití.

```console
dotnet publish -c Release
```

`dotnet publish` aktivační události [obnovení](/dotnet/core/tools/dotnet-restore) závislostí projektu a [sestavení](/dotnet/core/tools/dotnet-build) projektu před vytvořením prostředků pro nasazení. Jako součást procesu sestavení jsou odebrány nepoužívané metod a sestavení snížit velikost ke stažení aplikace a časy načtení. Nasazení se vytvoří v */bin/vydání/&lt;Cílová architektura&gt;/ publish* složky.

Prostředky v *publikovat* složky jsou nasazené na webovém serveru. Nasazení může být ruční nebo automatizované proces v závislosti na vývojové nástroje, které používá.

## <a name="host-configuration-values"></a>Hodnoty konfigurace hostitele

Součásti aplikace Razor, používající [model hostingu na straně serveru](xref:razor-components/hosting-models#server-side-hosting-model) může přijmout [hodnoty konfigurace webového hostitele](xref:fundamentals/host/web-host#host-configuration-values).

Blazor aplikací, které používají [model hostingu na straně klienta](xref:razor-components/hosting-models#client-side-hosting-model) může přijmout hodnoty následující konfigurace hostitele jako argumenty příkazového řádku za běhu ve vývojovém prostředí.

### <a name="content-root"></a>Obsah kořenové

`--contentroot` Argument Nastaví absolutní cestu k adresáři, který obsahuje soubory obsahu aplikace.

* Předejte argument při spuštění aplikace místně na příkazovém řádku. Z adresáře aplikace spusťte:

  ```console
  dotnet run --contentroot=/<content-root>
  ```

* Přidejte záznam do aplikace *launchSettings.json* soubor **služby IIS Express** profilu. Toto nastavení je neexistoval při spuštění aplikace pomocí ladicího programu sady Visual Studio a při spuštění aplikace z příkazového řádku s `dotnet run`.

  ```json
  "commandLineArgs": "--contentroot=/<content-root>"
  ```

* V sadě Visual Studio, zadat argument v **vlastnosti** > **ladění** > **argumenty aplikace**. Nastavení argumentu na stránce vlastností Visual Studio přidá argument *launchSettings.json* souboru.

  ```console
  --contentroot=/<content-root>
  ```

### <a name="path-base"></a>Základ cesty

`--pathbase` Argument nastaví základní cesty aplikace pro aplikaci pro místní spuštění s virtuální cestou nekořenovými ( `<base>` značky `href` nastavený na cestu jiné než `/` pro pracovní a provozní). Další informace najdete v tématu [základní cesty aplikace](#app-base-path) oddílu.

> [!IMPORTANT]
> Na rozdíl od zadaná cesta k `href` z `<base>` značku, nemusíte zahrnovat koncové lomítko (`/`) při předávání `--pathbase` hodnota argumentu. Pokud je základní cesta aplikace součástí `<base>` označit jako `<base href="/CoolApp/" />` (včetně koncového lomítka), předejte hodnotu argument příkazového řádku jako `--pathbase=/CoolApp` (žádného koncového lomítka).

* Předejte argument při spuštění aplikace místně na příkazovém řádku. Z adresáře aplikace spusťte:

  ```console
  dotnet run --pathbase=/<virtual-path>
  ```

* Přidejte záznam do aplikace *launchSettings.json* soubor **služby IIS Express** profilu. Toto nastavení je neexistoval při spuštění aplikace pomocí ladicího programu sady Visual Studio a při spuštění aplikace z příkazového řádku s `dotnet run`.

  ```json
  "commandLineArgs": "--pathbase=/<virtual-path>"
  ```

* V sadě Visual Studio, zadat argument v **vlastnosti** > **ladění** > **argumenty aplikace**. Nastavení argumentu na stránce vlastností Visual Studio přidá argument *launchSettings.json* souboru.

  ```console
  --pathbase=/<virtual-path>
  ```

### <a name="urls"></a>URL – adresy

`--urls` Argument určuje IP adresy nebo adresy hostitele s porty a protokoly pro naslouchání požadavkům.

* Předejte argument při spuštění aplikace místně na příkazovém řádku. Z adresáře aplikace spusťte:

  ```console
  dotnet run --urls=http://127.0.0.1:0
  ```

* Přidejte záznam do aplikace *launchSettings.json* soubor **služby IIS Express** profilu. Toto nastavení je neexistoval při spuštění aplikace pomocí ladicího programu sady Visual Studio a při spuštění aplikace z příkazového řádku s `dotnet run`.

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* V sadě Visual Studio, zadat argument v **vlastnosti** > **ladění** > **argumenty aplikace**. Nastavení argumentu na stránce vlastností Visual Studio přidá argument *launchSettings.json* souboru.

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="deploy-a-client-side-blazor-app"></a>Nasazení aplikace na straně klienta Blazor

S [model hostingu na straně klienta](xref:razor-components/hosting-models#client-side-hosting-model):

* Aplikace Blazor, jeho závislosti a modul .NET runtime se stáhnou do prohlížeče.
* Aplikace je proveden přímo v prohlížeči vlákno uživatelského rozhraní. Je podporován některý z následujících strategií:
  * Aplikace Blazor obsluhují aplikace ASP.NET Core. Do [Client-side Blazor hostované nasazení pomocí technologie ASP.NET Core](#client-side-blazor-hosted-deployment-with-aspnet-core) oddílu.
  * Blazor aplikace je umístěn na statické hostování webového serveru nebo službě, kde není .NET používají k předávání Blazor aplikace. Do [Client-side Blazor samostatné nasazení](#client-side-blazor-standalone-deployment) oddílu.

### <a name="configure-the-linker"></a>Konfigurace Linkeru

Blazor provádí Intermediate Language (IL) propojení na každé sestavení odebrat nepotřebné IL z výstupního sestavení. Můžete řídit propojení při sestavování sestavení. Další informace naleznete v tématu <xref:host-and-deploy/razor-components/configure-linker>.

### <a name="rewrite-urls-for-correct-routing"></a>Přepisování adres URL pro správné směrování

Směrování požadavků pro součásti stránky v aplikaci na straně klienta není snadné – stačí směrování žádostí na aplikace na straně serveru, prostředí. Vezměte v úvahu aplikace na straně klienta se dvěma stránkami:

* **_Main.cshtml_**  &ndash; zatížení v kořenovém adresáři aplikace a obsahuje odkaz na stránku o (`href="About"`).
* **_About.cshtml_**  &ndash; o stránce.

Pokud aplikace výchozí dokument se požaduje pomocí panelu Adresa prohlížeče (například `https://www.contoso.com/`):

1. Prohlížeč odešle požadavek.
1. Vrátí výchozí stránku, což je obvykle *index.html*.
1. *index.HTML* bootstraps aplikace.
1. Směrovač Blazor na zatížení a Razor hlavní stránky (*Main.cshtml*) se zobrazí.

Na hlavní stránce vyberte odkaz na stránku o načte stránku o. Vyberte odkaz na stránku o funguje na straně klienta, protože směrovače Blazor zastaví prohlížeče z požadavku na Internetu, aby `www.contoso.com` pro `About` a slouží samotná stránka o. Všechny žádosti pro interní stránky *v rámci aplikace na straně klienta* stejným způsobem fungovat: Požadavky nejsou aktivace založené na prohlížeči požadavky na prostředky serveru hostované na Internetu. Směrovač interně zpracovává požadavky.

Pokud je vytvořena pomocí panelu Adresa prohlížeče pro žádost `www.contoso.com/About`, požadavek selže. Žádný takový prostředek neexistuje na hostiteli aplikace Internet, tak *404 Nenalezeno* vrátí odpověď.

Protože prohlížeče zasílat požadavky do Internetu na hostitele pro stránky na straně klienta, webové servery a služby hostování musí přepsat všechny požadavky na prostředky nejsou fyzicky na server, aby *index.html* stránky. Když *index.html* se vrátí, má aplikace na straně klienta směrovače a odpovídá zprávou správný zdroj.

### <a name="app-base-path"></a>Základní cesta aplikace

Základní cesta aplikace je kořenová cesta virtuální aplikace na serveru. Například aplikace, které se nacházejí na serveru Contoso ve virtuální složce na `/CoolApp/` je dosažena `https://www.contoso.com/CoolApp` a má virtuální základní cesta `/CoolApp/`. Nastavením základní cesta aplikace na `CoolApp/`, aplikace se provádí vědět, kde se prakticky nachází na serveru. Aplikace můžete použít základní cesta aplikace k vytvoření adresy URL kořeni aplikace z komponenty, která není v kořenovém adresáři. To umožňuje součásti, které existují na různých úrovních adresářovou strukturu vytvářet odkazy na další zdroje v umístěních v celé aplikaci. Základní cesta aplikace se také používá k zachycení hypertextový odkaz klikne, kde `href` cíl odkazu je v rámci základní cesty aplikace místo identifikátoru URI&mdash;směrovače Blazor zpracovává vnitřní navigační.

V mnoha scénářích hostování serveru virtuální cesta k aplikaci je kořenový adresář aplikace. V těchto případech je základní cesty aplikace lomítkem (`<base href="/" />`), což je výchozí konfigurace pro aplikaci. V jiných scénářích hostování, jako jsou virtuální adresáře stránkách Githubu a služby IIS nebo dílčí aplikace základní cesty aplikace nastavte na serveru virtuální cesta k aplikaci. Nastavení základní cesty aplikace, přidat nebo aktualizovat `<base>` značku *index.html* najdete v `<head>` značky elementů. Nastavte `href` atribut hodnotu `<virtual-path>/` (vyžaduje se do adresy koncové lomítko), kde `<virtual-path>/` je kořenová cesta úplnou virtuální aplikace na serveru pro aplikaci. V předchozím příkladu je nastavena virtuální cesta `CoolApp/`: `<base href="CoolApp/" />`.

Pro aplikace s virtuální cestou nekořenovými nakonfigurovali (například `<base href="CoolApp/" />`), aplikace nenajde žádné její prostředky *při spuštění místně*. Chcete-li tento problém vyřešit během místní vývoj a testování, můžete zadat *základ cesty* argument, který odpovídá `href` hodnotu `<base>` značky v době běhu.

Předat argument základní cesty s kořenovou cestou (`/`) při místním spuštění aplikace, spusťte následující příkaz z adresáře aplikace:

```console
dotnet run --pathbase=/CoolApp
```

Reakce aplikace na místně `http://localhost:port/CoolApp`.

Další informace najdete v tématu [hodnota konfigurace hostitele základní cesty](#path-base) oddílu.

> [!IMPORTANT]
> Pokud aplikace používá [model hostingu na straně klienta](xref:razor-components/hosting-models#client-side-hosting-model) (na základě **Blazor** šablony projektu) a je hostovaný jako dílčí aplikace služby IIS v aplikaci ASP.NET Core, je potřeba zakázat zděděné ASP.NET Core Obslužná rutina modulu nebo Ujistěte se, že aplikace root (nadřazené) `<handlers>` tématu *web.config* soubor není děděný podřízeným aplikacím.
>
> Odebrat publikování obslužné rutiny v aplikaci *web.config* souboru tak, že přidáte `<handlers>` část do souboru:
>
> ```xml
> <handlers>
>   <remove name="aspNetCore" />
> </handlers>
> ```
>
> Můžete také zakázat dědičnost (nadřazené) kořenové aplikace `<system.webServer>` části pomocí `<location>` element s `inheritInChildApplications` nastavena na `false`:
>
> ```xml
> <?xml version="1.0" encoding="utf-8"?>
> <configuration>
>  <location path="." inheritInChildApplications="false">
>    <system.webServer>
>      <handlers>
>        <add name="aspNetCore" ... />
>      </handlers>
>      <aspNetCore ... />
>    </system.webServer>
>  </location>
> </configuration>
> ```
>
> Kromě konfigurace základní cesty aplikace, jak je popsáno v této části se provádí odebírá obslužnou rutinu nebo zakážou dědičnost. Nastavení základní cesty aplikace v aplikaci prvku *index.html* soubor do služby IIS alias používaný při konfiguraci podřízeným aplikacím ve službě IIS.

### <a name="client-side-blazor-hosted-deployment-with-aspnet-core"></a>Nasazení Blazor hostované na straně klienta pomocí ASP.NET Core

A *hostované nasazení* slouží Blazor aplikace na straně klienta z prohlížečů [aplikace ASP.NET Core](xref:index) , který běží na serveru.

Blazor aplikace je součástí aplikace ASP.NET Core v publikované výstup tak, aby dvě aplikace se nasazují společně. Webový server, který dokáže hostovat aplikace ASP.NET Core je povinný. Hostované nasazení Visual Studio obsahuje **Blazor (ASP.NET Core hostované)** šablonu projektu (`blazorhosted` šablony při použití [dotnet nové](/dotnet/core/tools/dotnet-new) příkaz).

Další informace o nasazení a hostování aplikací ASP.NET Core najdete v tématu <xref:host-and-deploy/index>.

Informace o nasazení do služby Azure App Service najdete v následujících tématech:

<xref:tutorials/publish-to-azure-webapp-using-vs>

Zjistěte, jak publikovat aplikace ASP.NET Core do služby Azure App Service pomocí sady Visual Studio.

### <a name="client-side-blazor-standalone-deployment"></a>Nasazení samostatného Blazor na straně klienta

A *samostatné nasazení* slouží jako sadu statické soubory, které jsou požadovány přímo klienty Blazor aplikace na straně klienta. Webový server se používají k předávání Blazor aplikace.

#### <a name="client-side-blazor-standalone-hosting-with-iis"></a>Na straně klienta Blazor samostatné hostování se službou IIS

Služba IIS je schopen statický souborový server pro Blazor aplikace. Můžete nakonfigurovat službu IIS do hostitele Blazor, naleznete v tématu [vytvoření statického webu ve službě IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).

Publikované prostředky vytvořené v  *\\bin\\vydání\\&lt;Cílová architektura&gt;\\publikovat* složky. Hostování obsahu *publikovat* složku na webovém serveru nebo hostující služby.

**web.config**

Při publikování projektu Blazor *web.config* soubor se vytvoří s následující konfigurací služby IIS:

* Typy MIME jsou nastavené pro následující přípony souborů:
  * *\*.dll*: `application/octet-stream`
  * *\*.json*: `application/json`
  * *\*.wasm*: `application/wasm`
  * *\*.woff*: `application/font-woff`
  * *\*.woff2*: `application/font-woff`
* Pro následující typy MIME je povolena komprese protokolu HTTP:
  * `application/octet-stream`
  * `application/wasm`
* Modul přepisování adres URL pravidla jsou vytvořeny:
  * Poskytovat dílčí adresáře, kde jsou umístěny statických prostředků aplikace (*&lt;název_sestavení&gt;\\dist\\&lt;path_requested&gt;*).
  * Vytvoření aplikace SPA záložní směrování tak, aby požadavky pro nesouborové prostředky se přesměrují do aplikace výchozí dokument v její složce statické prostředky (*&lt;název_sestavení&gt;\\dist\\index.html*).

**Nainstalovat modul přepisování adres URL**

[Modul přepisování adres URL](https://www.iis.net/downloads/microsoft/url-rewrite) se vyžaduje k přepsání adresy URL. Ve výchozím nastavení není nainstalován modul a není k dispozici pro instalaci jako funkce služby role Webový Server (IIS). Modul musí stáhnout z webu služby IIS. Použití instalačního programu webové platformy nainstalovat modul:

1. Místně, přejděte [modul přepisování adres URL stránky pro stažení](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads). Pro anglickou verzi, vyberte **instalace webové platformy** pro stažení instalačního programu Instalace webové platformy. Pro jiné jazyky vyberte příslušnou architekturu pro server (x86/x64) pro stažení instalačního programu.
1. Zkopírujte instalační program na server. Spusťte instalační program. Vyberte **nainstalovat** tlačítko a přijměte licenční podmínky. Po dokončení instalace není nutné restartovat server.

**Konfiguraci webové stránky**

Nastavit na webu **fyzická cesta** do složky aplikace. Složka obsahuje:

* *Web.config* soubor, který služba IIS používá pro konfiguraci webové stránky, včetně pravidel požadovaná přesměrování a typy obsahu souborů.
* Složku statických prostředků aplikace.

**Odstraňování potíží**

Pokud *500 – Interní chyba serveru* přijetí a Správce služby IIS vyvolá chyby při pokusu o přístup ke konfiguraci příslušného webu, ověřte, že je nainstalován modul přepisování adres URL. Když není nainstalován modul, *web.config* soubor nejde parsovat službou IIS. To zabrání načítání konfigurace na webu a webu z obsluhy statických souborů pro Blazor Správce služby IIS.

Další informace o řešení potíží s nasazením do služby IIS najdete v tématu <xref:host-and-deploy/iis/troubleshoot>.

#### <a name="client-side-blazor-standalone-hosting-with-nginx"></a>Na straně klienta Blazor samostatné hostování se serverem Nginx

Následující *nginx.conf* souboru je zjednodušenou ukazují, jak nakonfigurovat Nginx tak, aby odeslat *Index.html* souboru pokaždé, když nemůže najít odpovídající soubor na disku.

```
events { }
http {
    server {
        listen 80;

        location / {
            root /usr/share/nginx/html;
            try_files $uri $uri/ /Index.html =404;
        }
    }
}
```

Další informace o konfiguraci serveru webového serveru Nginx produkčního prostředí, najdete v části [NGINX konfiguračních souborů a vytváří NGINX Plus](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).

#### <a name="client-side-blazor-standalone-hosting-with-nginx-in-docker"></a>Hostování v Dockeru nginx samostatné Blazor na straně klienta

K hostování Blazor v Dockeru pomocí serveru Nginx, instalační program soubor Dockerfile, který chcete použít image serveru Nginx Alpine na základě. Aktualizace souboru Dockerfile zkopírovat *nginx.config* souboru do kontejneru.

Přidejte jeden řádek do souboru Dockerfile, jak je znázorněno v následujícím příkladu:

```Dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

#### <a name="client-side-blazor-standalone-hosting-with-github-pages"></a>Hostování s stránkách Githubu samostatné Blazor na straně klienta

Pro zpracování přepisů adresu URL, přidejte *404. html* soubor skriptu, který zpracovává požadavek na přesměrování *index.html* stránky. Příklad implementace poskytované komunitou, naleznete v tématu [jedné stránky aplikace pro stránky Githubu](http://spa-github-pages.rafrex.com/) ([rafrex/spa – github stránky na Githubu](https://github.com/rafrex/spa-github-pages#readme)). Příklad použití komunity přístup, můžete zobrazit v [blazor-demo/blazor-demo.github.io na Githubu](https://github.com/blazor-demo/blazor-demo.github.io) ([živého webu](https://blazor-demo.github.io/)).

Když použijete web projektu namísto serveru organizace, přidat nebo aktualizovat `<base>` značku *index.html*. Nastavte `href` atribut hodnotu `<repository-name>/`, kde `<repository-name>/` je název úložiště GitHub.

## <a name="deploy-a-server-side-razor-components-app"></a>Nasazení aplikace na straně serveru součásti syntaxe Razor

S [model hostingu na straně serveru](xref:razor-components/hosting-models#server-side-hosting-model), Razor komponent je provedeno na serveru z v rámci aplikace ASP.NET Core. Aktualizace uživatelského rozhraní, zpracování událostí a volání jazyka JavaScript jsou zpracovány prostřednictvím připojení SignalR.

Aplikace je součástí aplikace ASP.NET Core v publikované výstup tak, aby dvě aplikace se nasazují společně. Webový server, který dokáže hostovat aplikace ASP.NET Core je povinný. Pro nasazení na straně serveru, Visual Studio obsahuje **Razor komponenty** šablonu projektu (`razorcomponents` šablony při použití [dotnet nové](/dotnet/core/tools/dotnet-new) příkaz).

<!--

**INSERT: Concerns are the same as publishing an ASP.NET Core SignalR app**

**INSERT: Content on the Azure SignalR Service**

**INSERT: Manually turn on WebSockets support**

-->

Při publikování aplikace ASP.NET Core Razor komponenty aplikace je součástí publikovaný výstup tak, aby aplikace ASP.NET Core a Razor komponenty aplikace je možné nasadit společně. Další informace o nasazení a hostování aplikací ASP.NET Core najdete v tématu <xref:host-and-deploy/index>.

Informace o nasazení do služby Azure App Service najdete v následujících tématech:

<xref:tutorials/publish-to-azure-webapp-using-vs>

Zjistěte, jak publikovat aplikace ASP.NET Core do služby Azure App Service pomocí sady Visual Studio.

---
title: Hostování a nasazení ASP.NET Core Blazor na straně klienta
author: guardrex
description: Naučte se hostovat a nasazovat aplikaci Blazor pomocí ASP.NET Core, stránek Content Delivery Networks (CDN), souborových serverů a GitHubu.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
uid: host-and-deploy/blazor/client-side
ms.openlocfilehash: be6b6c245440cb085a1a6b115f4f087306f7cc83
ms.sourcegitcommit: b40613c603d6f0cc71f3232c16df61550907f550
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2019
ms.locfileid: "68308089"
---
# <a name="host-and-deploy-aspnet-core-blazor-client-side"></a>Hostování a nasazení ASP.NET Core Blazor na straně klienta

Od [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com)a [Daniel Skořepa](https://github.com/danroth27)

## <a name="host-configuration-values"></a>Hodnoty konfigurace hostitele

Aplikace Blazor, které používají [model hostování na straně klienta](xref:blazor/hosting-models#client-side) , můžou přijmout následující hodnoty konfigurace hostitele jako argumenty příkazového řádku za běhu ve vývojovém prostředí.

### <a name="content-root"></a>Kořen obsahu

`--contentroot` Argument nastavuje absolutní cestu k adresáři, který obsahuje soubory obsahu aplikace. V následujících příkladech `/content-root-path` je kořenová cesta obsahu aplikace.

* Předejte argument při místním spuštění aplikace z příkazového řádku. Z adresáře aplikace spusťte:

  ```console
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

Argument nastaví základní cestu aplikace pro aplikaci spuštěnou místně s nekořenovou virtuální cestou `<base>` (značka `href` je nastavená na jinou cestu než `/` pro pracovní a produkční). `--pathbase` V následujících příkladech `/virtual-path` je základ cesty aplikace. Další informace najdete v části [základní cesta aplikace](#app-base-path) .

> [!IMPORTANT]
> Na rozdíl od cesty, která `href` je k `<base>` dispozici pro značku, nezahrnujte koncové lomítko`/` `--pathbase` () při předávání hodnoty argumentu. Pokud se základní cesta aplikace poskytuje ve `<base>` značce jako `<base href="/CoolApp/">` (zahrnuje koncové lomítko), předejte hodnotu argumentu příkazového řádku jako `--pathbase=/CoolApp` (žádné koncové lomítko).

* Předejte argument při místním spuštění aplikace z příkazového řádku. Z adresáře aplikace spusťte:

  ```console
  dotnet run --pathbase=/virtual-path
  ```

* Přidejte položku do souboru *launchSettings. JSON* aplikace v profilu **IIS Express** . Toto nastavení se používá při spuštění aplikace pomocí ladicího programu sady Visual Studio a z příkazového řádku `dotnet run`s.

  ```json
  "commandLineArgs": "--pathbase=/virtual-path"
  ```

* V aplikaci Visual Studio zadejte argument v části **vlastnosti** > **ladit** > **argumenty aplikace**. Nastavení argumentu na stránce vlastností aplikace Visual Studio přidá argument do souboru *launchSettings. JSON* .

  ```console
  --pathbase=/virtual-path
  ```

### <a name="urls"></a>URL – adresy

`--urls` Argument nastaví IP adresy nebo adresy hostitelů s porty a protokoly, které se mají na požadavky naslouchat.

* Předejte argument při místním spuštění aplikace z příkazového řádku. Z adresáře aplikace spusťte:

  ```console
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

## <a name="deployment"></a>Nasazení

S [modelem hostování na straně klienta](xref:blazor/hosting-models#client-side):

* Do prohlížeče se stáhnou aplikace Blazor, její závislosti a modul runtime .NET.
* Aplikace se spustí přímo ve vlákně uživatelského rozhraní prohlížeče. Podporuje se jedna z následujících strategií:
  * Aplikace Blazor obsluhuje aplikaci ASP.NET Core. Tato strategie je popsaná v části [hostované nasazení s ASP.NET Core](#hosted-deployment-with-aspnet-core) .
  * Aplikace Blazor je umístěna na statický hostující webový server nebo službu, kde rozhraní .NET se nepoužívá k obsluze aplikace Blazor. Tato strategie je uvedena v části [samostatné nasazení](#standalone-deployment) .

## <a name="configure-the-linker"></a>Konfigurace Linkeru

Blazor provádí propojení podle jazyka IL (Intermediate Language) na každém sestavení pro odebrání zbytečného IL z výstupních sestavení. Propojení sestavení lze řídit při sestavování. Další informace naleznete v tématu <xref:host-and-deploy/blazor/configure-linker>.

## <a name="rewrite-urls-for-correct-routing"></a>Přepište adresy URL pro správné směrování.

Požadavky směrování na součásti stránky v aplikaci na straně klienta nejsou stejně jednoduché jako požadavky na směrování na straně serveru hostované aplikace. Vezměte v úvahu aplikaci na straně klienta se dvěma součástmi:

* *Main. Razor* &ndash; se načte do kořenového adresáře aplikace a obsahuje odkaz na `About` komponentu (`href="About"`).
* *O komponentě. Razor* &ndash; `About` .

Pokud je výchozí dokument aplikace požadován pomocí panelu Adresa prohlížeče (například `https://www.contoso.com/`):

1. Prohlížeč vytvoří požadavek.
1. Vrátí se výchozí stránka, což je obvykle *index. html*.
1. *index. html* se v aplikaci zabootstrap.
1. Blazor směrovač se načte a komponenta Razor `Main` se vykreslí.

Na hlavní `About` stránce vyberte odkaz na komponentu na klientovi, protože směrovač Blazor zabrání v prohlížeči, aby `www.contoso.com` odeslal požadavek na Internet pro `About` a obsluhu samotné vykreslené `About` komponenty. Všechny požadavky na vnitřní koncové body *v aplikaci na straně klienta* fungují stejným způsobem: Požadavky neaktivují požadavky založené na prohlížeči na prostředky hostované na serveru na internetu. Směrovač zpracovává požadavky interně.

Pokud je žádost vytvořena pomocí panelu Adresa prohlížeče pro `www.contoso.com/About`, požadavek se nezdařil. Žádný takový prostředek na internetovém hostiteli aplikace neexistuje, takže se vrátí odpověď *404 –* Nenalezeno.

Vzhledem k tomu, že prohlížeče vytvářejí požadavky na internetové hostitele pro stránky na straně klienta, webové servery a hostitelské služby musí přepsat všechny požadavky na prostředky, které nejsou fyzicky na serveru, na stránku *index. html* . Když se vrátí *index. html* , směrovač na straně klienta aplikace převezme a odpoví správným prostředkem.

## <a name="app-base-path"></a>Základní cesta aplikace

*Základní cesta aplikace* je kořenová cesta virtuální aplikace na serveru. Například aplikace, která se nachází na serveru contoso ve virtuální složce `/CoolApp/` v, se dosáhne v `https://www.contoso.com/CoolApp` a má virtuální základní cestu `/CoolApp/`. Když nastavíte základní cestu aplikace na virtuální cestu (`<base href="/CoolApp/">`), aplikace bude vědět, kde se prakticky nachází na serveru. Aplikace může použít základní cestu aplikace k vytvoření adres URL relativních k kořenu aplikace ze součásti, která není v kořenovém adresáři. To umožňuje komponentám, které existují na různých úrovních adresářové struktury, vytvářet odkazy na jiné prostředky v umístění v rámci aplikace. Základní cesta aplikace se také používá k zachycení hypertextového odkazu, kde `href` cíl odkazu je v rámci základní cesty k umístění&mdash;identifikátoru URI aplikace, Blazor směrovač zpracovává interní navigaci.

V mnoha hostitelských scénářích je virtuální cesta serveru k aplikaci kořenem aplikace. V těchto případech je základní cesta aplikace lomítkem (`<base href="/" />`), což je výchozí konfigurace aplikace. V jiných scénářích hostování, jako jsou stránky GitHubu a virtuální adresáře služby IIS nebo podaplikace, musí být základní cesta aplikace nastavená na virtuální cestu serveru aplikace. Chcete-li nastavit základní cestu aplikace, aktualizujte `<base>` značku `<head>` v rámci prvků značek souboru *wwwroot/index.html* . Nastavte hodnotu `/virtual-path/`atributuna (vyžaduje se koncové lomítko), kde `/virtual-path/` je úplná kořenová cesta virtuální aplikace na serveru aplikace. `href` V předchozím příkladu je virtuální cesta nastavena na `/CoolApp/`:. `<base href="/CoolApp/">`

Pro aplikaci, která má nakonfigurovanou nekořenovou virtuální cestu (například `<base href="/CoolApp/">`), aplikace *při místním spuštění*nenalezne své prostředky. Chcete-li tento problém překonat při místním vývoji a testování, můžete dodat *základní argument Path* , který odpovídá `href` hodnotě `<base>` značky za běhu.

Pokud chcete předat základní argument Path s kořenovou cestou (`/`) při místním spuštění aplikace, `dotnet run` spusťte příkaz z adresáře `--pathbase` aplikace s možností:

```console
dotnet run --pathbase=/{Virtual Path (no trailing slash)}
```

Pro aplikaci s virtuální základní cestou `/CoolApp/` (`<base href="/CoolApp/">`) je tento příkaz:

```console
dotnet run --pathbase=/CoolApp
```

Aplikace odpoví místně na adrese `http://localhost:port/CoolApp`.

Další informace najdete v části na [hodnotě základní konfigurace hostitele Path](#path-base).

Pokud aplikace používá [model hostování na straně klienta](xref:blazor/hosting-models#client-side) (na `blazor` základě šablony projektu **Blazor (na straně klienta)** , šablony při použití příkazu [dotnet New](/dotnet/core/tools/dotnet-new) ) a je hostována jako podaplikace služby IIS v aplikaci ASP.NET Core, je důležité Zakažte zděděnou obslužnou rutinu modulu ASP.NET Core nebo zajistěte, aby dílčí `<handlers>` aplikace nezdědila oddíl kořenové (nadřazené) aplikace v souboru *Web. config* .

Odeberte obslužnou rutinu v publikovaném souboru *Web. config* aplikace přidáním `<handlers>` oddílu do souboru:

```xml
<handlers>
  <remove name="aspNetCore" />
</handlers>
```

Případně zakažte `<system.webServer>` dědění kořenového oddílu (nadřazené) aplikace `<location>` pomocí elementu s `inheritInChildApplications` nastavením na `false`:

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

Odebrání obslužné rutiny nebo zakázání dědičnosti se provádí kromě konfigurace základní cesty aplikace, jak je popsáno v této části. Nastavte základní cestu aplikace v souboru *index. html* aplikace na alias služby IIS, který se používá při konfiguraci dílčí aplikace v IIS.

## <a name="hosted-deployment-with-aspnet-core"></a>Hostované nasazení s ASP.NET Core

*Hostované nasazení* obsluhuje aplikaci Blazor na straně klienta z [aplikace ASP.NET Core](xref:index) , která běží na webovém serveru.

Aplikace Blazor je součástí aplikace ASP.NET Core v publikovaném výstupu, takže se tyto dvě aplikace nasazují dohromady. Vyžaduje se webový server, který podporuje hostování aplikace ASP.NET Core. V případě hostovaného nasazení Visual Studio zahrnuje šablonu projektu **Blazor (ASP.NET Core Hosted)** (`blazorhosted` šablona při použití příkazu [dotnet New](/dotnet/core/tools/dotnet-new) ).

Další informace o ASP.NET Core hostování a nasazení aplikací najdete v tématu <xref:host-and-deploy/index>.

Informace o nasazení do Azure App Service najdete v tématu <xref:tutorials/publish-to-azure-webapp-using-vs>.

## <a name="standalone-deployment"></a>Samostatné nasazení

*Samostatné nasazení* obsluhuje aplikaci Blazor na straně klienta jako sadu statických souborů, které jsou požadovány přímo klienty. Libovolný statický souborový server může obsluhovat aplikaci Blazor.

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

#### <a name="troubleshooting"></a>Poradce při potížích

Pokud dojde k *chybě 500 – interní chyba serveru* a správce služby IIS vyvolá chyby při pokusu o přístup ke konfiguraci webu, potvrďte, že je nainstalován modul URL pro přepis. Pokud modul není nainstalován, soubor *Web. config* nelze analyzovat službou IIS. Tím se zabrání tomu, aby správce služby IIS načetl konfiguraci webu a web neobsluhuje statické soubory Blazor.

Další informace o řešení potíží s nasazeními služby IIS najdete <xref:test/troubleshoot-azure-iis>v tématu.

### <a name="azure-storage"></a>Azure Storage

Hostování statického souboru Azure Storage umožňuje hostování aplikace bez serveru. Podporují se názvy vlastních domén, Azure Content Delivery Network (CDN) a HTTPS.

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

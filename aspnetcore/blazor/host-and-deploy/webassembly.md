---
title: ASP.NET Core hostitele a nasazení Blazor WebAssembly
author: guardrex
description: Naučte se hostovat a nasazovat Blazor aplikaci pomocí ASP.NET Core, stránek Content Delivery Networks (CDN), souborových serverů a GitHubu.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/03/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/host-and-deploy/webassembly
ms.openlocfilehash: e66a470bf5bd23950bdb0ccf61c6743916ed9349
ms.sourcegitcommit: dfea24471f4f3d7904faa92fe60c000853bddc3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/18/2020
ms.locfileid: "88504551"
---
# <a name="host-and-deploy-aspnet-core-no-locblazor-webassembly"></a>ASP.NET Core hostitele a nasazení Blazor WebAssembly

Od [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Skořepa](https://github.com/danroth27), [Robert Adams](https://twitter.com/ben_a_adams)a [Safia Abdalla](https://safia.rocks)

S [ Blazor WebAssembly modelem hostování](xref:blazor/hosting-models#blazor-webassembly):

* BlazorAplikace, její závislosti a modul runtime .NET jsou stahovány do prohlížeče paralelně.
* Aplikace se spustí přímo ve vlákně uživatelského rozhraní prohlížeče.

Podporují se tyto strategie nasazení:

* BlazorAplikaci obsluhuje aplikace ASP.NET Core. Tato strategie je popsaná v části [hostované nasazení s ASP.NET Core](#hosted-deployment-with-aspnet-core) .
* BlazorAplikace se umístí na statický hostující webový server nebo službu, kde rozhraní .NET se k obsluze aplikace nepoužívá Blazor . Tato strategie je popsaná v části [samostatné nasazení](#standalone-deployment) , která obsahuje informace o hostování Blazor WebAssembly aplikace jako dílčí aplikace služby IIS.

## <a name="compression"></a>Komprese

Při Blazor WebAssembly publikování aplikace je výstup během publikování staticky komprimován, aby se snížila velikost aplikace a odstranila se režie pro kompresi za běhu. Používají se následující kompresní algoritmy:

* [Brotli](https://tools.ietf.org/html/rfc7932) (nejvyšší úroveň)
* [GZIP](https://tools.ietf.org/html/rfc1952)

Blazor spoléhá na hostitele, který obsluhuje příslušné komprimované soubory. Při použití hostovaného projektu ASP.NET Core je hostitelský projekt schopný provádět vyjednávání obsahu a obsluhovat staticky komprimované soubory. Při hostování Blazor WebAssembly samostatné aplikace může být nutné provést další práci, aby bylo zajištěno, že budou obsluhovány staticky komprimované soubory:

* `web.config`Konfiguraci komprese služby IIS najdete v části [IIS: Brotli a komprese GZip](#brotli-and-gzip-compression) . 
* Při hostování řešení statického hostování, které nepodporují vyjednávání se staticky komprimovaným souborem, jako jsou stránky GitHubu, zvažte konfiguraci aplikace pro načtení a dekódování Brotli komprimovaných souborů:

  * Získejte dekodér JavaScript Brotli z [úložiště GitHub Google/Brotli](https://github.com/google/brotli). Od července 2020 se soubor dekodéru pojmenuje `decode.min.js` a nalezne se ve [ `js` složce](https://github.com/google/brotli/tree/master/js)úložiště.
  * Aktualizujte aplikaci tak, aby používala dekodér. Změňte značku uvnitř uzavírací `<body>` značky v `wwwroot/index.html` následujícím formátu:
  
    ```html
    <script src="decode.min.js"></script>
    <script src="_framework/blazor.webassembly.js" autostart="false"></script>
    <script>
      Blazor.start({
        loadBootResource: function (type, name, defaultUri, integrity) {
          if (type !== 'dotnetjs' && location.hostname !== 'localhost') {
            return (async function () {
              const response = await fetch(defaultUri + '.br', { cache: 'no-cache' });
              if (!response.ok) {
                throw new Error(response.statusText);
              }
              const originalResponseBuffer = await response.arrayBuffer();
              const originalResponseArray = new Int8Array(originalResponseBuffer);
              const decompressedResponseArray = BrotliDecode(originalResponseArray);
              const contentType = type === 
                'dotnetwasm' ? 'application/wasm' : 'application/octet-stream';
              return new Response(decompressedResponseArray, 
                { headers: { 'content-type': contentType } });
            })();
          }
        }
      });
    </script>
    ```
 
Chcete-li vypnout kompresi, přidejte `BlazorEnableCompression` do souboru projektu aplikace vlastnost MSBuild a nastavte hodnotu na `false` :

```xml
<PropertyGroup>
  <BlazorEnableCompression>false</BlazorEnableCompression>
</PropertyGroup>
```

`BlazorEnableCompression`Vlastnost může být předána [`dotnet publish`](/dotnet/core/tools/dotnet-publish) příkazu s následující syntaxí v příkazovém prostředí:

```dotnetcli
dotnet publish -p:BlazorEnableCompression=false
```

## <a name="rewrite-urls-for-correct-routing"></a>Přepište adresy URL pro správné směrování.

Požadavky směrování na součásti stránky v Blazor WebAssembly aplikaci nejsou stejně jednoduché jako požadavky směrování v Blazor Server hostované aplikaci. Vezměte v úvahu Blazor WebAssembly aplikaci se dvěma součástmi:

* `Main.razor`: Načte do kořenového adresáře aplikace a obsahuje odkaz na `About` komponentu ( `href="About"` ).
* `About.razor`: `About` součást.

Pokud je výchozí dokument aplikace požadován pomocí panelu Adresa prohlížeče (například `https://www.contoso.com/` ):

1. Prohlížeč vytvoří požadavek.
1. Vrátí se výchozí stránka, což je obvykle `index.html` .
1. `index.html` napředá aplikaci.
1. Blazorse načte směrovač a Razor `Main` Komponenta se vykreslí.

Na hlavní stránce vyberte odkaz na `About` komponentu na klientovi, protože Blazor směrovač zastaví v prohlížeči, aby odeslal požadavek na Internet `www.contoso.com` pro `About` a sloužil přímo vykreslené `About` součásti. Všechny požadavky na vnitřní koncové body *v Blazor WebAssembly aplikaci* fungují stejným způsobem: požadavky neaktivují požadavky založené na prohlížeči na prostředky hostované na serveru na internetu. Směrovač zpracovává požadavky interně.

Pokud je žádost vytvořena pomocí panelu Adresa prohlížeče pro `www.contoso.com/About` , požadavek se nezdařil. Žádný takový prostředek na internetovém hostiteli aplikace neexistuje, takže se vrátí odpověď *404 – Nenalezeno* .

Vzhledem k tomu, že prohlížeče připravují žádosti na internetové hostitele pro stránky na straně klienta, webové servery a hostitelské služby musí přepsat všechny požadavky na prostředky, které nejsou na stránce fyzicky na serveru `index.html` . Když `index.html` se vrátí, Blazor směrovač aplikace převezme a odpoví správným prostředkem.

Při nasazování na server služby IIS můžete použít modul pro přepsání adresy URL s publikovaným `web.config` souborem aplikace. Další informace najdete v části [IIS](#iis) .

## <a name="hosted-deployment-with-aspnet-core"></a>Hostované nasazení s ASP.NET Core

*Hostované nasazení* obsluhuje Blazor WebAssembly aplikaci prohlížeči z [ASP.NET Core aplikace](xref:index) , která běží na webovém serveru.

Klientská Blazor WebAssembly aplikace se publikuje do `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` složky serverové aplikace spolu s dalšími statickými webovými prostředky serverové aplikace. Obě aplikace se nasazují dohromady. Vyžaduje se webový server, který podporuje hostování aplikace ASP.NET Core. V případě hostovaného nasazení Visual Studio zahrnuje šablonu projektu ** Blazor WebAssembly aplikace** ( `blazorwasm` Šablona při použití [`dotnet new`](/dotnet/core/tools/dotnet-new) příkazu) s **`Hosted`** vybranou možností ( `-ho|--hosted` při použití `dotnet new` příkazu).

Další informace o ASP.NET Core hostování a nasazení aplikací najdete v tématu <xref:host-and-deploy/index> .

Informace o nasazení do Azure App Service najdete v tématu <xref:tutorials/publish-to-azure-webapp-using-vs> .

## <a name="hosted-deployment-with-multiple-no-locblazor-webassembly-apps"></a>Hostované nasazení s více Blazor WebAssembly aplikacemi

### <a name="app-configuration"></a>Konfigurace aplikací

Konfigurace hostovaného Blazor řešení pro poskytování více Blazor WebAssembly aplikací:

* Použijte existující hostované Blazor řešení nebo vytvořte nové řešení ze Blazor šablony hostovaného projektu.

* V souboru projektu klientské aplikace přidejte `<StaticWebAssetBasePath>` vlastnost do pole `<PropertyGroup>` s hodnotou, `FirstApp` abyste nastavili základní cestu pro statické prostředky projektu:

  ```xml
  <PropertyGroup>
    ...
    <StaticWebAssetBasePath>FirstApp</StaticWebAssetBasePath>
  </PropertyGroup>
  ```

* Přidání druhé klientské aplikace do řešení:

  * Přidejte do složky řešení složku s názvem `SecondClient` .
  * Vytvořte Blazor WebAssembly aplikaci s názvem `SecondBlazorApp.Client` ve `SecondClient` složce ze Blazor WebAssembly šablony projektu.
  * V souboru projektu aplikace:

    * Přidejte `<StaticWebAssetBasePath>` vlastnost do pole `<PropertyGroup>` s hodnotou `SecondApp` :

      ```xml
      <PropertyGroup>
        ...
        <StaticWebAssetBasePath>SecondApp</StaticWebAssetBasePath>
      </PropertyGroup>
      ```

    * Přidat odkaz na projekt do `Shared` projektu:

      ```xml
      <ItemGroup>
        <ProjectReference Include="..\Shared\{SOLUTION NAME}.Shared.csproj" />
      </ItemGroup>
      ```

      Zástupný symbol `{SOLUTION NAME}` je název řešení.

* V souboru projektu aplikace serveru vytvořte odkaz na projekt pro přidanou klientskou aplikaci:

  ```xml
  <ItemGroup>
    ...
    <ProjectReference Include="..\SecondClient\SecondBlazorApp.Client.csproj" />
  </ItemGroup>
  ```

* V souboru serverové aplikace `Properties/launchSettings.json` Nakonfigurujte `applicationUrl` profil Kestrel ( `{SOLUTION NAME}.Server` ) pro přístup k klientským aplikacím na portech 5001 a 5002:

  ```json
  "applicationUrl": "https://localhost:5001;https://localhost:5002",
  ```

* V metodě serverové aplikace `Startup.Configure` ( `Startup.cs` ) odeberte následující řádky, které se zobrazí po volání <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A> :

  ```csharp
  app.UseBlazorFrameworkFiles();
  app.UseStaticFiles();

  app.UseRouting();

  app.UseEndpoints(endpoints =>
  {
      endpoints.MapRazorPages();
      endpoints.MapControllers();
      endpoints.MapFallbackToFile("index.html");
  });
  ```

  Přidejte middleware, který mapuje požadavky na klientské aplikace. Následující příklad konfiguruje middleware ke spuštění v následujících případech:

  * Port žádosti je buď 5001 pro původní klientskou aplikaci, nebo 5002 pro přidanou klientskou aplikaci.
  * Hostitel žádosti je buď `firstapp.com` pro původní klientskou aplikaci, nebo `secondapp.com` pro přidanou klientskou aplikaci.

    > [!NOTE]
    > Příklad uvedený v této části vyžaduje další konfiguraci pro:
    >
    > * Přístup k aplikacím v ukázkových doménách hostitelů `firstapp.com` a `secondapp.com` .
    > * Certifikáty pro klientské aplikace, které umožňují zabezpečení TLS (HTTPS).
    >
    > Požadovaná konfigurace překračuje rozsah tohoto článku a závisí na tom, jak je řešení hostované. Další informace najdete v [článcích o hostiteli a nasazení](xref:host-and-deploy/index).

  Vložte následující kód, kde byly řádky odebrány dříve:

  ```csharp
  app.MapWhen(ctx => ctx.Request.Host.Port == 5001 || 
      ctx.Request.Host.Equals("firstapp.com"), first =>
  {
      first.Use((ctx, nxt) =>
      {
          ctx.Request.Path = "/FirstApp" + ctx.Request.Path;
          return nxt();
      });

      first.UseBlazorFrameworkFiles("/FirstApp");
      first.UseStaticFiles();
      first.UseStaticFiles("/FirstApp");
      first.UseRouting();

      first.UseEndpoints(endpoints =>
      {
          endpoints.MapControllers();
          endpoints.MapFallbackToFile("/FirstApp/{*path:nonfile}", 
              "FirstApp/index.html");
      });
  });
  
  app.MapWhen(ctx => ctx.Request.Host.Port == 5002 || 
      ctx.Request.Host.Equals("secondapp.com"), second =>
  {
      second.Use((ctx, nxt) =>
      {
          ctx.Request.Path = "/SecondApp" + ctx.Request.Path;
          return nxt();
      });

      second.UseBlazorFrameworkFiles("/SecondApp");
      second.UseStaticFiles();
      second.UseStaticFiles("/SecondApp");
      second.UseRouting();

      second.UseEndpoints(endpoints =>
      {
          endpoints.MapControllers();
          endpoints.MapFallbackToFile("/SecondApp/{*path:nonfile}", 
              "SecondApp/index.html");
      });
  });
  ```

* V řadiči pro předpověď počasí aplikace serveru ( `Controllers/WeatherForecastController.cs` ) nahraďte stávající trasu ( `[Route("[controller]")]` ) následujícími trasami `WeatherForecastController` :

  ```csharp
  [Route("FirstApp/[controller]")]
  [Route("SecondApp/[controller]")]
  ```

  Middleware přidané do metody serverové aplikace `Startup.Configure` dříve mění příchozí požadavky na `/WeatherForecast` buď `/FirstApp/WeatherForecast` nebo `/SecondApp/WeatherForecast` v závislosti na portu (5001/5002) nebo doméně ( `firstapp.com` / `secondapp.com` ). Aby bylo možné vracet údaje o počasí z aplikace serveru do klientských aplikací, je nutné, aby byly předchozí trasy řadiče.

### <a name="static-assets-and-class-libraries"></a>Statické prostředky a knihovny tříd

Pro statické prostředky použijte následující přístupy:

* Pokud je prostředek ve složce klientské aplikace `wwwroot` , zadejte jejich cesty normálně:

  ```razor
  <img alt="..." src="/{ASSET FILE NAME}" />
  ```

* Pokud je Asset ve `wwwroot` složce [ Razor knihovny tříd (RCL)](xref:blazor/components/class-libraries), odkazujte na statický prostředek v klientské aplikaci podle pokynů v [článku RCL](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl):

  ```razor
  <img alt="..." src="_content/{LIBRARY NAME}/{ASSET FILE NAME}" />
  ```

::: moniker range=">= aspnetcore-5.0"

Na součásti, které jsou k klientské aplikaci poskytovány knihovny tříd, se obvykle odkazuje. Pokud některé komponenty vyžadují šablony stylů nebo soubory JavaScriptu, použijte k získání statických prostředků některý z následujících přístupů:

* Soubor klientské aplikace `wwwroot/index.html` může propojit ( `<link>` ) se statickými prostředky.
* Komponenta může použít [ `Link` komponentu](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) rozhraní pro získání statických prostředků.

Předchozí přístupy jsou znázorněné v následujících příkladech.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Na součásti, které jsou k klientské aplikaci poskytovány knihovny tříd, se obvykle odkazuje. Pokud některé komponenty vyžadují šablony stylů nebo soubory JavaScriptu, musí soubor klientské aplikace `wwwroot/index.html` zahrnovat správné propojení statických prostředků. Tyto přístupy jsou znázorněné v následujících příkladech.

::: moniker-end

Přidejte následující `Jeep` komponentu do jedné z klientských aplikací. `Jeep`Komponenta používá:

* Obrázek ze složky klientské aplikace `wwwroot` ( `jeep-cj.png` ).
* Obrázek z přidané složky [ Razor knihovny součástí](xref:blazor/components/class-libraries) () `JeepImage` `wwwroot` ( `jeep-yj.png` ).
* Ukázková součást ( `Component1` ) je vytvořena automaticky šablonou projektu RCL při `JeepImage` Přidání knihovny do řešení.

```razor
@page "/Jeep"

<h1>1979 Jeep CJ-5&trade;</h1>

<p>
    <img alt="1979 Jeep CJ-5&trade;" src="/jeep-cj.png" />
</p>

<h1>1991 Jeep YJ&trade;</h1>

<p>
    <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
</p>

<p>
    <em>Jeep CJ-5</em> and <em>Jeep YJ</em> are a trademarks of 
    <a href="https://www.fcagroup.com">Fiat Chrysler Automobiles</a>.
</p>

<JeepImage.Component1 />
```

> [!WARNING]
> **Nezveřejňujte** image vozidel veřejně, pokud Image nevlastníte. V opačném případě riskujete porušení autorských práv.

::: moniker range=">= aspnetcore-5.0"

`jeep-yj.png`Bitovou kopii knihovny lze také přidat do `Component1` komponenty knihovny ( `Component1.razor` ). Chcete-li poskytnout `my-component` třídu CSS na stránku klientské aplikace, propojte na šablonu stylů knihovny pomocí [ `Link` komponenty](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements)rozhraní:

```razor
<div class="my-component">
    <Link href="_content/JeepImage/styles.css" rel="stylesheet" />

    <h1>JeepImage.Component1</h1>

    <p>
        This Blazor component is defined in the <strong>JeepImage</strong> package.
    </p>

    <p>
        <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
    </p>
</div>
```

Alternativou k použití [ `Link` komponenty](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) je načtení šablony stylů ze souboru klientské aplikace `wwwroot/index.html` . Tento přístup zpřístupňuje šablonu stylů všem součástem v klientské aplikaci:

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

`jeep-yj.png`Bitovou kopii knihovny lze také přidat do `Component1` komponenty knihovny ( `Component1.razor` ):

```razor
<div class="my-component">
    <h1>JeepImage.Component1</h1>

    <p>
        This Blazor component is defined in the <strong>JeepImage</strong> package.
    </p>

    <p>
        <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
    </p>
</div>
```

Soubor klientské aplikace `wwwroot/index.html` požaduje šablonu stylů knihovny s následující přidanou `<link>` značkou:

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

Přidat navigaci k `Jeep` součásti v součásti klientské aplikace `NavMenu` ( `Shared/NavMenu.razor` ):

```razor
<li class="nav-item px-3">
    <NavLink class="nav-link" href="Jeep">
        <span class="oi oi-list-rich" aria-hidden="true"></span> Jeep
    </NavLink>
</li>
```

Další informace o RCLs najdete v tématech:

* <xref:blazor/components/class-libraries>
* <xref:razor-pages/ui-class>

## <a name="standalone-deployment"></a>Samostatné nasazení

*Samostatné nasazení* obsluhuje Blazor WebAssembly aplikaci jako sadu statických souborů, které jsou požadovány přímo klienty. Každý statický souborový server může Blazor aplikaci zpracovat.

Samostatné prostředky nasazení jsou publikovány do `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` složky.

### <a name="azure-app-service"></a>Azure App Service

Blazor WebAssembly aplikace se dají nasadit do Azure App Services v systému Windows, které hostují aplikaci ve [službě IIS](#iis).

Nasazení samostatné Blazor WebAssembly aplikace do Azure App Service pro Linux není aktuálně podporováno. Image serveru pro Linux, která je hostitelem aplikace, není v tuto chvíli k dispozici. Pro povolení tohoto scénáře probíhá práce.

### <a name="iis"></a>IIS

Služba IIS je schopným statickým souborovým serverem pro Blazor aplikace. Chcete-li nakonfigurovat službu IIS na hostování Blazor , přečtěte si téma [vytvoření statického webu ve službě IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).

Publikované assety se vytvoří ve `/bin/Release/{TARGET FRAMEWORK}/publish` složce. Hostovat obsah `publish` složky na webovém serveru nebo v hostitelské službě.

#### <a name="webconfig"></a>web.config

Při Blazor publikování projektu se vytvoří `web.config` soubor s následující konfigurací služby IIS:

* Typy MIME jsou nastaveny pro následující přípony souborů:
  * `.dll`: `application/octet-stream`
  * `.json`: `application/json`
  * `.wasm`: `application/wasm`
  * `.woff`: `application/font-woff`
  * `.woff2`: `application/font-woff`
* Pro následující typy MIME je povolena komprese protokolu HTTP:
  * `application/octet-stream`
  * `application/wasm`
* Pravidla pro přepis adres URL jsou navázána:
  * Slouží jako podadresáře, kde se nachází statické prostředky aplikace ( `wwwroot/{PATH REQUESTED}` ).
  * Vytvořte záložní záložní postup, aby se požadavky na nesouborové prostředky přesměrovaly do výchozího dokumentu aplikace ve složce static assets ( `wwwroot/index.html` ).
  
#### <a name="use-a-custom-webconfig"></a>Použití vlastního web.config

Chcete-li použít vlastní `web.config` soubor, umístěte vlastní `web.config` soubor do kořenové složky projektu a publikujte projekt.

#### <a name="install-the-url-rewrite-module"></a>Instalace modulu URL pro přepis

Pro přepis adres URL je vyžadován [modul URL Rewrite](https://www.iis.net/downloads/microsoft/url-rewrite) . Modul není nainstalován ve výchozím nastavení a není k dispozici pro instalaci jako funkci služby role Webový server (IIS). Modul se musí stáhnout z webu IIS. K instalaci modulu použijte instalační program webové platformy:

1. Místně přejděte na [stránku ke stažení modulu pro přepsání adresy URL](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads). V případě anglické verze vyberte **WebPI** a Stáhněte si instalační program WebPI. Pro jiné jazyky vyberte příslušnou architekturu pro server (x86/x64) a stáhněte instalační program.
1. Zkopírujte instalační program na server. Spusťte instalační program. Vyberte tlačítko **nainstalovat** a přijměte licenční podmínky. Po dokončení instalace není restartování serveru vyžadováno.

#### <a name="configure-the-website"></a>Konfigurace webu

Nastavte **fyzickou cestu** webu na složku aplikace. Složka obsahuje:

* `web.config`Soubor, který služba IIS používá ke konfiguraci webu, včetně požadovaných pravidel přesměrování a typů obsahu souborů.
* Složka statických prostředků aplikace

#### <a name="host-as-an-iis-sub-app"></a>Hostování jako dílčí aplikace služby IIS

Pokud je samostatná aplikace hostovaná jako dílčí aplikace služby IIS, proveďte jednu z následujících akcí:

* Zakažte zděděnou obslužnou rutinu modulu ASP.NET Core.

  Odeberte obslužnou rutinu v Blazor publikovaném `web.config` souboru aplikace tak, že `<handlers>` do souboru přidáte oddíl:

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

Odebrání obslužné rutiny nebo zakázání dědičnosti se provádí kromě [Konfigurace základní cesty aplikace](xref:blazor/host-and-deploy/index#app-base-path). Nastavte základní cestu aplikace v `index.html` souboru aplikace na alias služby IIS, který se používá při konfiguraci dílčí aplikace v IIS.

#### <a name="brotli-and-gzip-compression"></a>Komprese Brotli a gzip

Službu IIS je možné nakonfigurovat prostřednictvím služby `web.config` za účelem poskytování komprimovaných prostředků Brotli nebo gzip Blazor . Příklad konfigurace najdete v tématu [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true) .

#### <a name="troubleshooting"></a>Řešení potíží

Pokud dojde k *chybě 500 – interní chyba serveru* a správce služby IIS vyvolá chyby při pokusu o přístup ke konfiguraci webu, potvrďte, že je nainstalován modul URL pro přepis. Pokud modul není nainstalován, `web.config` soubor nelze analyzovat službou IIS. Tím se zabrání tomu, aby správce služby IIS načetl konfiguraci webu a web ze Blazor statických souborů obsluhy.

Další informace o řešení potíží s nasazeními služby IIS najdete v tématu <xref:test/troubleshoot-azure-iis> .

### <a name="azure-storage"></a>Azure Storage

Hostování statického souboru [Azure Storage](/azure/storage/) umožňuje Blazor hostování aplikací bez serveru. Podporují se názvy vlastních domén, Azure Content Delivery Network (CDN) a HTTPS.

Když je u služby BLOB Service povolené hostování statických webů v účtu úložiště:

* Nastavte **název dokumentu indexu** na `index.html` .
* Nastavte **cestu k chybovému dokumentu** na `index.html` . Razor součásti a jiné koncové body, které nejsou v souboru, se neukládají na fyzických cestách ve statickém obsahu uloženém službou BLOB Service. Když se přijme žádost o jeden z těchto prostředků, kterou Blazor by měl směrovač zpracovat, chyba *404 – nenalezená* služba BLOB Service směruje požadavek na **cestu k chybovému dokumentu**. `index.html`Vrátí se objekt BLOB a Blazor směrovač načte a zpracuje cestu.

Pokud nejsou za běhu načteny soubory z důvodu nevhodných typů MIME v `Content-Type` hlavičkách souborů, proveďte jednu z následujících akcí:

* Nakonfigurujte nástroje tak, aby při nasazení souborů nastavily správné typy MIME ( `Content-Type` hlavičky).
* Změňte typy MIME ( `Content-Type` hlavičky) souborů po nasazení aplikace.

  V Průzkumník služby Storage (Azure Portal) pro každý soubor:
  
  1. Klikněte na soubor pravým tlačítkem a vyberte **vlastnosti**.
  1. Nastavte **ContentType** a klikněte na tlačítko **Uložit** .

Další informace najdete v tématu [statické hostování webů v Azure Storage](/azure/storage/blobs/storage-blob-static-website).

### <a name="nginx"></a>Nginx

Následující `nginx.conf` soubor je zjednodušený a ukazuje, jak nakonfigurovat Nginx pro odeslání `index.html` souboru pokaždé, když nemůže najít odpovídající soubor na disku.

```
events { }
http {
    server {
        listen 80;

        location / {
            root      /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

Při nastavování [limitu přenosové rychlosti Nginx](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) pomocí můžou [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req) Blazor WebAssembly aplikace vyžadovat velkou `burst` hodnotu parametru, aby odpovídala poměrně velkému počtu požadavků, které aplikace vytvořila. Zpočátku nastavte hodnotu aspoň 60:

```
http {
    server {
        ...

        location / {
            ...

            limit_req zone=one burst=60 nodelay;
        }
    }
}
```

Zvyšte hodnotu, pokud nástroje pro vývojáře v prohlížeči nebo síťový provoz označují, že žádosti dostávají stavový kód *Nedostupnosti 503-Service* .

Další informace o konfiguraci webového serveru Nginx v produkčním prostředí najdete v tématu [vytváření konfiguračních souborů Nginx plus a Nginx](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).

### <a name="nginx-in-docker"></a>Nginx v Docker

Pokud chcete hostovat Blazor v Docker pomocí Nginx, nastavte souboru Dockerfile na použití Nginx image založené na Alpine. Aktualizujte souboru Dockerfile tak, aby se `nginx.config` soubor zkopíroval do kontejneru.

Do souboru Dockerfile přidejte jeden řádek, jak je znázorněno v následujícím příkladu:

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a>Apache

Nasazení Blazor WebAssembly aplikace na CentOS 7 nebo novější:

1. Vytvořte konfigurační soubor Apache. V následujícím příkladu je zjednodušený konfigurační soubor ( `blazorapp.config` ):

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

Další informace najdete v tématech [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) a [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html) .

### <a name="github-pages"></a>Stránky GitHubu

Chcete-li zpracovat přepisy adresy URL, přidejte `wwwroot/404.html` soubor se skriptem, který zpracovává přesměrování požadavku na `index.html` stránku. Příklad najdete v [ Blazor úložišti GitHub SteveSandersonMS/OnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages):

* [`wwwroot/404.html`](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/wwwroot/404.html)
* [Živý web](https://stevesandersonms.github.io/BlazorOnGitHubPages/))

Při použití webu projektu namísto webu organizace aktualizujte `<base>` značku v `wwwroot/index.html` . Nastavte `href` hodnotu atributu na název úložiště GitHub s koncovým lomítkem (například `/my-repository/` ). V [ Blazor úložišti GitHub SteveSandersonMS/OnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages)se základ `href` aktualizuje při publikování pomocí [ `.github/workflows/main.yml` konfiguračního souboru](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/.github/workflows/main.yml).

> [!NOTE]
> [ Blazor Úložiště GitHub SteveSandersonMS/OnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages) není vlastněné, udržované ani podporované rozhraním .NET Foundation ani Microsoftem.

## <a name="host-configuration-values"></a>Hodnoty konfigurace hostitele

[ Blazor WebAssembly aplikace](xref:blazor/hosting-models#blazor-webassembly) mohou přijmout následující hodnoty konfigurace hostitele jako argumenty příkazového řádku za běhu ve vývojovém prostředí.

### <a name="content-root"></a>Kořen obsahu

`--contentroot`Argument nastavuje absolutní cestu k adresáři, který obsahuje soubory obsahu aplikace ([kořen obsahu](xref:fundamentals/index#content-root)). V následujících příkladech `/content-root-path` je kořenová cesta obsahu aplikace.

* Předejte argument při místním spuštění aplikace z příkazového řádku. Z adresáře aplikace spusťte:

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* Přidejte položku do `launchSettings.json` souboru aplikace v profilu **IIS Express** . Toto nastavení se používá při spuštění aplikace s ladicím programem sady Visual Studio a z příkazového řádku s `dotnet run` .

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* V aplikaci Visual Studio zadejte argument v části **vlastnosti**  >  **ladit**  >  **argumenty aplikace**. Nastavení argumentu na stránce vlastností aplikace Visual Studio přidá do `launchSettings.json` souboru Argument.

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a>Základ cesty

`--pathbase`Argument nastaví základní cestu aplikace pro aplikaci spouštěnou místně s nekořenovou cestou relativní adresy URL ( `<base>` značka `href` je nastavená na jinou cestu než `/` pro pracovní a produkční). V následujících příkladech `/relative-URL-path` je základ cesty aplikace. Další informace najdete v tématu [základní cesta k aplikaci](xref:blazor/host-and-deploy/index#app-base-path).

> [!IMPORTANT]
> Na rozdíl od cesty, která je k dispozici pro `href` `<base>` značku, nezahrnujte koncové lomítko ( `/` ) při předávání `--pathbase` hodnoty argumentu. Pokud se základní cesta aplikace poskytuje ve `<base>` značce jako `<base href="/CoolApp/">` (zahrnuje koncové lomítko), předejte hodnotu argumentu příkazového řádku jako `--pathbase=/CoolApp` (žádné koncové lomítko).

* Předejte argument při místním spuštění aplikace z příkazového řádku. Z adresáře aplikace spusťte:

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* Přidejte položku do `launchSettings.json` souboru aplikace v profilu **IIS Express** . Toto nastavení se používá při spuštění aplikace pomocí ladicího programu sady Visual Studio a z příkazového řádku s `dotnet run` .

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* V aplikaci Visual Studio zadejte argument v části **vlastnosti**  >  **ladit**  >  **argumenty aplikace**. Nastavení argumentu na stránce vlastností aplikace Visual Studio přidá do `launchSettings.json` souboru Argument.

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a>Adresy URL

`--urls`Argument nastaví IP adresy nebo adresy hostitelů s porty a protokoly, které se mají na požadavky naslouchat.

* Předejte argument při místním spuštění aplikace z příkazového řádku. Z adresáře aplikace spusťte:

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* Přidejte položku do `launchSettings.json` souboru aplikace v profilu **IIS Express** . Toto nastavení se používá při spuštění aplikace pomocí ladicího programu sady Visual Studio a z příkazového řádku s `dotnet run` .

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* V aplikaci Visual Studio zadejte argument v části **vlastnosti**  >  **ladit**  >  **argumenty aplikace**. Nastavení argumentu na stránce vlastností aplikace Visual Studio přidá do `launchSettings.json` souboru Argument.

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a>Konfigurace Linkeru

Blazor provede propojení s mezijazykem (IL) na každém sestavení vydaných verzí a odebere z výstupních sestavení zbytečné IL. Další informace naleznete v tématu <xref:blazor/host-and-deploy/configure-linker>.

## <a name="custom-boot-resource-loading"></a>Načítání vlastního spouštěcího prostředku

Blazor WebAssemblyAplikaci lze inicializovat pomocí `loadBootResource` funkce pro přepsání integrovaného mechanismu načítání spouštěcích prostředků. Použijte `loadBootResource` v následujících případech:

* Umožňuje uživatelům načíst statické prostředky, jako jsou například data o časovém pásmu nebo `dotnet.wasm` síť CDN.
* Načtěte komprimovaná sestavení pomocí požadavku HTTP a dekomprimujte je v klientovi pro hostitele, kteří nepodporují načítání komprimovaného obsahu ze serveru.
* Aliasujte prostředky na jiný název tak, že každý požadavek přesměrujete `fetch` na nový název.

`loadBootResource` parametry jsou uvedeny v následující tabulce.

| Parametr    | Popis |
| ------------ | ----------- |
| `type`       | Typ prostředku. Permissable typy: `assembly` , `pdb` , `dotnetjs` , `dotnetwasm` , `timezonedata` |
| `name`       | Název prostředku. |
| `defaultUri` | Relativní nebo absolutní identifikátor URI prostředku. |
| `integrity`  | Řetězec integrity představující očekávaný obsah v odpovědi |

`loadBootResource` Vrátí některou z následujících možností pro přepsání procesu načítání:

* Řetězec identifikátoru URI. V následujícím příkladu ( `wwwroot/index.html` ) jsou následující soubory obsluhovány ze sítě CDN v `https://my-awesome-cdn.com/` :

  * `dotnet.*.js`
  * `dotnet.wasm`
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

  Následující příklad ( `wwwroot/index.html` ) přidá vlastní HLAVIČKU http do odchozích požadavků a předá `integrity` parametr prostřednictvím `fetch` volání:
  
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

V případě, že potřebujete změnit příponu filename pro publikované `.dll` soubory aplikace, postupujte podle pokynů v této části.

Po publikování aplikace použijte skript prostředí nebo kanál sestavení DevOps k přejmenování `.dll` souborů tak, aby používaly jinou příponu souboru. Zaměřte se na `.dll` soubory v `wwwroot` adresáři publikovaného výstupu aplikace (například `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot` ).

V následujících příkladech `.dll` jsou soubory přejmenovány, aby používaly `.bin` příponu souboru.

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
   
Chcete-li použít jinou příponu souboru než `.bin` , nahraďte `.bin` v předchozích příkazech.

Pro vyřešení komprimovaných `blazor.boot.json.gz` `blazor.boot.json.br` souborů proveďte jednu z následujících přístupů:

* Odeberte komprimované `blazor.boot.json.gz` soubory a `blazor.boot.json.br` . Komprese je u tohoto přístupu zakázána.
* Opětovná komprimace aktualizovaného `blazor.boot.json` souboru.

Předchozí pokyny platí i v případě, že se aktivují prostředky pracovního procesu. Odeberte nebo znovu proveďte komprimaci `wwwroot/service-worker-assets.js.br` a `wwwroot/service-worker-assets.js.gz` . V opačném případě kontroly integrity souborů selžou v prohlížeči.

Následující příklad Windows používá skript prostředí PowerShell umístěný v kořenovém adresáři projektu.

`ChangeDLLExtensions.ps1:`:

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

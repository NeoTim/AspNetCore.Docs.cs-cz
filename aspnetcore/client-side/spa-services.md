---
title: Použití služeb JavaScriptu k vytváření aplikací s jedním stránkou v ASP.NET Core
author: scottaddie
description: Přečtěte si o výhodách používání služeb JavaScript k vytvoření jednostránkové aplikace (SPA), kterou zajišťuje ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: H1Hack27Feb2017
ms.date: 09/06/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: client-side/spa-services
ms.openlocfilehash: 65bd5157bb3909f8352debcb1a6dfa7d888eec0e
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82769920"
---
# <a name="use-javascript-services-to-create-single-page-applications-in-aspnet-core"></a>Použití služeb JavaScriptu k vytváření aplikací s jedním stránkou v ASP.NET Core

[Scottem Addie](https://github.com/scottaddie) a [Fiyaz Hasan](https://fiyazhasan.me/)

Jediná stránková aplikace (SPA) je oblíbeným typem webové aplikace z důvodu svého vlastního prostředí pro práci s uživateli. Integrace rozhraní a knihoven SPA na straně klienta, jako je například [úhlová](https://angular.io/) nebo [reakce](https://facebook.github.io/react/), s architekturami na straně serveru, jako je například ASP.NET Core, mohou být obtížné. Služba JavaScript Services byla vyvinuta za účelem snížení tření v procesu integrace. Umožňuje bezproblémovou operaci mezi různými serverovými a technologickými zásobníky pro klienty.

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> Funkce popsané v tomto článku jsou zastaralé od ASP.NET Core 3,0. V balíčku NuGet [Microsoft. AspNetCore. SpaServices. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) je k dispozici jednodušší mechanismus pro integraci rozhraní Spa. Další informace najdete v tématech [[oznámení] obsoleting Microsoft. AspNetCore. SpaServices a Microsoft. AspNetCore. NodeServices](https://github.com/dotnet/AspNetCore/issues/12890).

::: moniker-end

## <a name="what-is-javascript-services"></a>Co je JavaScript Services

Služba JavaScript Services je kolekce technologií na straně klienta pro ASP.NET Core. Jeho cílem je umístit ASP.NET Core jako preferovanou platformu na straně serveru vývojářů pro sestavování jednostránkové.

Služby JavaScriptu se skládají ze dvou různých balíčků NuGet:

* [Microsoft. AspNetCore. NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)
* [Microsoft. AspNetCore. SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)

Tyto balíčky jsou užitečné v následujících scénářích:

* Spuštění JavaScriptu na serveru
* Použití architektury nebo knihovny SPA
* Sestavení prostředků na straně klienta pomocí webpacku

Většina zaměření v tomto článku je umístěna na používání balíčku SpaServices.

## <a name="what-is-spaservices"></a>Co je SpaServices

SpaServices byl vytvořen tak, aby bylo možné umístit ASP.NET Core jako preferovanou platformu na straně serveru vývojářů pro stavební jednostránkové. SpaServices se nevyžaduje k vývoji jednostránkové s ASP.NET Core a nezamkne vývojáře do konkrétního klientského rozhraní.

SpaServices poskytuje užitečnou infrastrukturu, jako je:

* [Předběžné vykreslování na straně serveru](#server-side-prerendering)
* [Middleware pro vývoj pro Webpack](#webpack-dev-middleware)
* [Výměna horkého modulu](#hot-module-replacement)
* [Helpdesky směrování](#routing-helpers)

Souhrnně tyto komponenty infrastruktury rozšiřují pracovní postup vývoje i prostředí za běhu. Komponenty lze jednotlivě přijmout.

## <a name="prerequisites-for-using-spaservices"></a>Předpoklady pro použití SpaServices

Pokud chcete pracovat s SpaServices, nainstalujte následující:

* [Node. js](https://nodejs.org/) (verze 6 nebo novější) s npm

  * Chcete-li ověřit, zda jsou tyto součásti nainstalovány a lze je najít, spusťte následující příkaz z příkazového řádku:

    ```console
    node -v && npm -v
    ```

  * Pokud nasazujete na web Azure, není &mdash; k dispozici žádná akce Node. js, která je k dispozici v prostředích serveru.

* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]

  * V systému Windows pomocí sady Visual Studio 2017 se sada SDK nainstaluje výběrem úlohy **vývoje .NET Core pro různé platformy** .

* Balíček NuGet [Microsoft. AspNetCore. SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/)

## <a name="server-side-prerendering"></a>Předběžné vykreslování na straně serveru

Univerzální (označovaná také jako isomorphic) aplikace je JavaScriptová aplikace schopná běžet na serveru i v klientovi. Úhlů, reakce a další oblíbená rozhraní poskytují univerzální platformu pro tento styl vývoje aplikace. Nápad je nejprve vykreslovat komponenty architektury na serveru přes Node. js a poté delegovat další provádění na klienta.

ASP.NET Core [pomocníků značek](xref:mvc/views/tag-helpers/intro) , které poskytuje SpaServices, zjednoduší implementaci předvykreslování na straně serveru vyvoláním funkcí JavaScriptu na serveru.

### <a name="server-side-prerendering-prerequisites"></a>Požadavky na předběžné vykreslování na straně serveru

Nainstalujte balíček npm [s předvykreslováním ASPNET](https://www.npmjs.com/package/aspnet-prerendering) :

```console
npm i -S aspnet-prerendering
```

### <a name="server-side-prerendering-configuration"></a>Konfigurace předvykreslování na straně serveru

Pomocníky značek jsou v souboru *_ViewImports. cshtml* projektu Zjistitelnější prostřednictvím registrace oboru názvů:

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/_ViewImports.cshtml?highlight=3)]

Tyto pomocníky se značkami abstrakce složitými rozhraními komunikaci přímo s rozhraními API nízké úrovně, a to využitím syntaxe ve formátu HTML v zobrazení Razor:

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=5)]

### <a name="asp-prerender-module-tag-helper"></a>ASP-PreRender-pomocník značek modulu

`asp-prerender-module`Pomocník značek použitý v předchozím příkladu kódu spouští *clientapp/DIST/Main-Server. js* na serveru prostřednictvím Node. js. V zájmu srozumitelnosti je soubor *Main-Server. js* artefaktem úlohy TypeScript-to-JavaScript transpilation v procesu sestavení pro [sadu Webpack](https://webpack.github.io/) . Webpack definuje alias vstupního bodu `main-server` pro. a přechod grafu závislostí pro tento alias začíná v souboru *clientapp/boot-server. TS* :

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=53)]

V následujících úhlových příkladech používá soubor *clientapp/boot-server. TS* `createServerRenderer` funkci a `RenderResult` typ `aspnet-prerendering` balíčku npm ke konfiguraci vykreslování serveru prostřednictvím Node. js. Označení HTML určené pro vykreslování na straně serveru je předáno volání funkce přeložit, které je zabaleno do silně typovaného `Promise` objektu JavaScriptu. `Promise`Hodnota významnosti objektu je, že asynchronně poskytuje kód HTML na stránce pro vložení do elementu zástupného prvku modelu DOM.

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-34,79-)]

### <a name="asp-prerender-data-tag-helper"></a>ASP-PreRender-Pomocník s datovou značkou

Při spojení s `asp-prerender-module` pomocníkem značek `asp-prerender-data` lze pomocí pomocníka značek předat kontextové informace ze zobrazení Razor do JavaScriptu na straně serveru. Například následující kód předává uživatelská data do `main-server` modulu:

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=9-12)]

Přijatý `UserName` argument je serializován pomocí integrovaného serializátoru JSON a je uložen v `params.data` objektu. V následujícím úhlovém příkladu se data používají k sestavení vlastního pozdravu v rámci `h1` elementu:

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,38-52,79-)]

Názvy vlastností předané v Pomocníkech značek jsou reprezentovány **PascalCase** Notation. Na rozdíl od jazyka JavaScript, kde jsou stejné názvy vlastností, které jsou reprezentovány s **CamelCase**. Výchozí konfigurace serializace JSON zodpovídá za tento rozdíl.

Pro rozšíření na předchozí příklad kódu mohou být data předána ze serveru do zobrazení tím, že se dobalí `globals` vlastnost poskytnutá `resolve` funkci:

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,57-77,79-)]

`postList`Pole definované uvnitř `globals` objektu je připojeno k globálnímu `window` objektu prohlížeče. Tato proměnná výtahem do globálního rozsahu eliminuje duplicity, zejména v případě, že se týká načítání stejných dat na serveru a znovu v klientovi.

![globální proměnná postList připojená k objektu window](spa-services/_static/global_variable.png)

## <a name="webpack-dev-middleware"></a>Middleware pro vývoj pro Webpack

[Middleware](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) pro vývoj pro Webpack zavádí zjednodušený vývojový pracovní postup, který v sadě Webpack vytváří prostředky na vyžádání. Middleware automaticky zkompiluje a zachová prostředky na straně klienta při opětovném načtení stránky v prohlížeči. Alternativním přístupem je ruční vyvolání sady Webpack prostřednictvím skriptu sestavení npm projektu, když se změní závislost třetí strany nebo vlastní kód. V následujícím příkladu je zobrazen skript sestavení npm v souboru *Package. JSON* :

```json
"build": "npm run build:vendor && npm run build:custom",
```

### <a name="webpack-dev-middleware-prerequisites"></a>Požadavky na middleware pro vývoj pro Webpack

Instalace balíčku [ASPNET-Webpack](https://www.npmjs.com/package/aspnet-webpack) npm:

```console
npm i -D aspnet-webpack
```

### <a name="webpack-dev-middleware-configuration"></a>Konfigurace pro vývoj middlewaru pro Webpack

Middleware pro vývoj v sadě Webpack je zaregistrovaný do kanálu požadavků HTTP prostřednictvím následujícího kódu v metodě souboru *Startup.cs* `Configure` :

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_WebpackMiddlewareRegistration&highlight=4)]

`UseWebpackDevMiddleware`Před [registrací statického souboru hostujícího](xref:fundamentals/static-files) prostřednictvím metody rozšíření je nutné volat metodu rozšíření `UseStaticFiles` . Z bezpečnostních důvodů Zaregistrujte middleware pouze v případě, že aplikace běží v režimu vývoje.

Vlastnost souboru *Webpack. config. js* `output.publicPath` oznamuje middlewari, že má sledovat změny ve `dist` složce:

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,13-16)]

## <a name="hot-module-replacement"></a>Výměna horkého modulu

Zamyslete se nad funkcí HMR ( [Hot Module nahradilo](https://webpack.js.org/concepts/hot-module-replacement/) ) sady Webpack jako vývoj middlewaru pro vývoj pro [Webpack](#webpack-dev-middleware). HMR zavádí všechny stejné výhody, ale ještě více zjednodušuje pracovní postup vývoje díky automatické aktualizaci obsahu stránky po zkompilování změn. Nepleťte si to s aktualizací prohlížeče, což by vedlo ke konfliktu s aktuálním stavem v paměti a relacemi ladění zabezpečeného hesla. Mezi službou Webpack dev middleware a prohlížečem existuje živý odkaz, který znamená, že se do prohlížeče dostanou změny.

### <a name="hot-module-replacement-prerequisites"></a>Požadavky na nahrazení horkého modulu

Nainstalujte balíček [Webpack – Hot-middleware](https://www.npmjs.com/package/webpack-hot-middleware) npm:

```console
npm i -D webpack-hot-middleware
```

### <a name="hot-module-replacement-configuration"></a>Konfigurace nahrazení horkého modulu

Komponenta HMR musí být zaregistrovaná do kanálu požadavků HTTP MVC v `Configure` metodě:

```csharp
app.UseWebpackDevMiddleware(new WebpackDevMiddlewareOptions {
    HotModuleReplacement = true
});
```

Stejně jako u rozhraní [Webpack dev middleware](#webpack-dev-middleware) `UseWebpackDevMiddleware` musí být metoda rozšíření volána před `UseStaticFiles` metodou rozšíření. Z bezpečnostních důvodů Zaregistrujte middleware pouze v případě, že aplikace běží v režimu vývoje.

Soubor *Webpack. config. js* musí definovat `plugins` pole, i když je ponecháno prázdné:

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,25)]

Po načtení aplikace v prohlížeči se na kartě konzola nástroje pro vývojáře zobrazí potvrzení aktivace HMR:

![Nepřipojená zpráva o nahrazení horkého modulu](spa-services/_static/hmr_connected.png)

## <a name="routing-helpers"></a>Helpdesky směrování

Ve většině ASP.NET Core jednostránkové na straně klienta je často žádoucí směrování na straně klienta kromě směrování na straně serveru. Systémy směrování SPA a MVC můžou pracovat nezávisle bez rušivých zásahů. Existuje ale jeden hraniční případ s výzvami k identifikaci 404 odpovědí HTTP.

Vezměte v úvahu scénář, ve kterém se používá trasa s příponou `/some/page` . Předpokládat, že požadavek neodpovídá vzorové trase na straně serveru, ale jeho vzor odpovídá trase na straně klienta. Nyní zvažte příchozí požadavek na `/images/user-512.png` , který obecně očekává, že na serveru najde soubor s obrázkem. Pokud tato požadovaná cesta prostředku neodpovídá žádné cestě na straně serveru ani ke statickému souboru, je pravděpodobné, že aplikace na straně klienta nebude obvykle zpracovávat &mdash; stavový kód protokolu HTTP 404.

### <a name="routing-helpers-prerequisites"></a>Požadavky na helpdesky směrování

Nainstalujte balíček npm pro směrování na straně klienta. Jako příklad použijte úhlové:

```console
npm i -S @angular/router
```

### <a name="routing-helpers-configuration"></a>Konfigurace pomocníků pro směrování

Metoda rozšíření s názvem `MapSpaFallbackRoute` se používá v `Configure` metodě:

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_MvcRoutingTable&highlight=7-9)]

Trasy jsou vyhodnocovány v pořadí, ve kterém jsou konfigurovány. V důsledku toho `default` je trasa v předchozím příkladu kódu použita jako první pro porovnávání vzorů.

## <a name="create-a-new-project"></a>Vytvoření nového projektu

Služby JavaScriptu poskytují předem nakonfigurované šablony aplikací. SpaServices se používá v těchto šablonách společně s různými rozhraními a knihovnami, jako je například úhlová, reakce a Redux.

Tyto šablony lze nainstalovat prostřednictvím .NET Core CLI spuštěním následujícího příkazu:

```dotnetcli
dotnet new --install Microsoft.AspNetCore.SpaTemplates::*
```

Zobrazí se seznam dostupných šablon SPA:

| Šablony                                 | Krátký název | Jazyk | Značky        |
| ------------------------------------------| :--------: | :------: | :---------: |
| ASP.NET Core MVC s úhlovým             | Angular    | Jazyk     | Web/MVC/SPA |
| MVC ASP.NET Core s reagují. js            | react      | Jazyk     | Web/MVC/SPA |
| MVC ASP.NET Core s reagují. js a Redux  | reactredux | Jazyk     | Web/MVC/SPA |

Pokud chcete vytvořit nový projekt pomocí jedné z šablon SPA, zahrňte do příkazu [dotnet New](/dotnet/core/tools/dotnet-new) **krátký název** šablony. Následující příkaz vytvoří úhlovou aplikaci s ASP.NET Core MVC nakonfigurovanou pro stranu serveru:

```dotnetcli
dotnet new angular
```

### <a name="set-the-runtime-configuration-mode"></a>Nastavení režimu konfigurace modulu runtime

Existují dva režimy konfigurace primárního modulu runtime:

* **Vývoj**:
  * Obsahuje zdrojové mapování pro usnadnění ladění.
  * Neoptimalizuje kód na straně klienta pro výkon.
* **Provozní**:
  * Vyloučí zdrojové mapy.
  * Optimalizuje kód na straně klienta prostřednictvím sdružování a minifikace.

ASP.NET Core používá `ASPNETCORE_ENVIRONMENT` k uložení režimu konfigurace proměnnou prostředí s názvem. Další informace najdete v tématu [nastavení prostředí](xref:fundamentals/environments#set-the-environment).

### <a name="run-with-net-core-cli"></a>Spustit s .NET Core CLI

Požadované balíčky NuGet a npm obnovte spuštěním následujícího příkazu v kořenu projektu:

```dotnetcli
dotnet restore && npm i
```

Sestavte a spusťte aplikaci:

```dotnetcli
dotnet run
```

Aplikace se spouští na místním hostiteli podle [režimu konfigurace modulu runtime](#set-the-runtime-configuration-mode). Navigace `http://localhost:5000` v prohlížeči zobrazí cílovou stránku.

### <a name="run-with-visual-studio-2017"></a>Spustit se sadou Visual Studio 2017

Otevřete soubor *. csproj* generovaný příkazem [dotnet New](/dotnet/core/tools/dotnet-new) . Požadované balíčky NuGet a npm se obnoví automaticky po otevření projektu. Tento proces obnovení může trvat až několik minut a aplikace je připravená ke spuštění po dokončení. Klikněte na zelené tlačítko spustit nebo stiskněte klávesu `Ctrl + F5` a otevře se prohlížeč na úvodní stránce aplikace. Aplikace běží na místním hostiteli podle [režimu konfigurace modulu runtime](#set-the-runtime-configuration-mode).

## <a name="test-the-app"></a>Otestování aplikace

Šablony SpaServices jsou předem nakonfigurované tak, aby se spouštěly testy na straně klienta pomocí [Karma](https://karma-runner.github.io/1.0/index.html) a [jednotek Jasmine](https://jasmine.github.io/). Jednotek Jasmine je oblíbená architektura pro testování částí pro JavaScript, zatímco Karma je Test Runner pro tyto testy. Karma je nakonfigurovaná tak, aby spolupracovala s rozhraním pro [vývoj pro Webpack](#webpack-dev-middleware) . to znamená, že vývojář není nutný k zastavení a spuštění testu pokaždé, když se změny provedou. Bez ohledu na to, zda se jedná o kód spuštěný proti testovacímu případu nebo samotnému testovacímu případu, se test spustí automaticky.

Použití úhlové aplikace jako příklad, `CounterComponent` v souboru *Counter. Component. spec. TS* jsou již k dispozici dva jednotek Jasmine testovací případy:

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/app/components/counter/counter.component.spec.ts?range=15-28)]

Otevřete příkazový řádek v adresáři *clientapp* . Spusťte následující příkaz:

```console
npm test
```

Skript spustí Karma Test Runner, který přečte nastavení definované v souboru *Karma. conf. js* . Kromě dalších nastavení identifikuje *Karma. conf. js* testovací soubory, které mají být provedeny prostřednictvím svého `files` pole:

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/test/karma.conf.js?range=4-5,8-11)]

## <a name="publish-the-app"></a>Publikování aplikace

Další informace o publikování do Azure najdete v [tomto problému GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/12474) .

Kombinování generovaných prostředků na straně klienta a publikovaných artefaktů ASP.NET Core do balíčku připraveného k nasazení může být náročných. Naštěstí SpaServices orchestruje, že celý proces publikace s vlastním cílem MSBuild s názvem `RunWebpack` :

[!code-xml[](../client-side/spa-services/sample/SpaServicesSampleApp/SpaServicesSampleApp.csproj?range=31-45)]

Cíl MSBuild má následující zodpovědnosti:

1. Obnovte balíčky npm.
1. Vytvoření výrobního sestavení prostředků na straně klienta.
1. Vytvořte výrobní sestavení vlastních prostředků na straně klienta.
1. Zkopírujte prostředky vygenerované v sadě Webpack do složky pro publikování.

Cíl nástroje MSBuild je vyvolán při spuštění:

```dotnetcli
dotnet publish -c Release
```

## <a name="additional-resources"></a>Další zdroje

* [Úhlové dokumenty](https://angular.io/docs)

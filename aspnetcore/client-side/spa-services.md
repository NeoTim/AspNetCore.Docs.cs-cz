---
title: Použití služeb JavaScriptu k vytváření aplikací s jedním stránkou v ASP.NET Core
author: scottaddie
description: Přečtěte si o výhodách používání služeb JavaScript k vytvoření jednostránkové aplikace (SPA), kterou zajišťuje ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: H1Hack27Feb2017
ms.date: 09/06/2019
uid: client-side/spa-services
ms.openlocfilehash: c0c73882afd579510ad9cdf5b485c1d6fbeadd1c
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78663777"
---
# <a name="use-javascript-services-to-create-single-page-applications-in-aspnet-core"></a>Použití služeb JavaScriptu k vytváření aplikací s jedním stránkou v ASP.NET Core

[Scottem Addie](https://github.com/scottaddie) a [Fiyaz Hasan](https://fiyazhasan.me/)

Jednu stránku aplikace (SPA) je typ oblíbené webové aplikace z důvodu jeho přináší bohaté uživatelské prostředí. Integrace rozhraní a knihoven SPA na straně klienta, jako je například [úhlová](https://angular.io/) nebo [reakce](https://facebook.github.io/react/), s architekturami na straně serveru, jako je například ASP.NET Core, mohou být obtížné. Služba JavaScript Services byla vyvinuta za účelem snížení tření v procesu integrace. Umožňuje bezproblémové operace mezi různých klientských a serverových sad technologie.

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> Funkce popsané v tomto článku jsou zastaralé od ASP.NET Core 3,0. V balíčku NuGet [Microsoft. AspNetCore. SpaServices. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) je k dispozici jednodušší mechanismus pro integraci rozhraní Spa. Další informace najdete v tématech [[oznámení] obsoleting Microsoft. AspNetCore. SpaServices a Microsoft. AspNetCore. NodeServices](https://github.com/dotnet/AspNetCore/issues/12890).

::: moniker-end

## <a name="what-is-javascript-services"></a>Co je JavaScript Services

Služba JavaScript Services je kolekce technologií na straně klienta pro ASP.NET Core. Jeho cílem je na pozici ASP.NET Core, jako preferovanou platformu na straně serveru vývojářů pro vytváření SPA.

Služby JavaScriptu se skládají ze dvou různých balíčků NuGet:

* [Microsoft. AspNetCore. NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)
* [Microsoft. AspNetCore. SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)

Tyto balíčky jsou užitečné v následujících scénářích:

* Spuštění JavaScriptu na serveru
* Použít SPA architektury nebo knihovny
* Vytvoření prostředků na straně klienta s Webpacku

Mnoho pozornosti v tomto článku je umístěn na použití SpaServices balíčku.

## <a name="what-is-spaservices"></a>Co je SpaServices

SpaServices byl vytvořen na pozici ASP.NET Core, jako preferovanou platformu na straně serveru vývojářů pro vytváření SPA. SpaServices se nevyžaduje k vývoji jednostránkové s ASP.NET Core a nezamkne vývojáře do konkrétního klientského rozhraní.

SpaServices infrastrukturu užitečné jako:

* [Předběžné vykreslování na straně serveru](#server-side-prerendering)
* [Middleware pro vývoj pro Webpack](#webpack-dev-middleware)
* [Výměna horkého modulu](#hot-module-replacement)
* [Helpdesky směrování](#routing-helpers)

Společně tyto součástí infrastruktury vylepšení pracovního postupu vývoje i prostředí runtime. Součásti může být přijata jednotlivě.

## <a name="prerequisites-for-using-spaservices"></a>Předpoklady pro použití SpaServices

Pro práci s SpaServices, nainstalujte následující:

* [Node. js](https://nodejs.org/) (verze 6 nebo novější) s npm

  * Ověření tyto součásti jsou nainstalovány a najdete, spusťte z příkazového řádku následující:

    ```console
    node -v && npm -v
    ```

  * Pokud nasazujete na web Azure, není nutná žádná akce&mdash;Node. js je nainstalován a k dispozici v prostředích serveru.

* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]

  * V systému Windows pomocí sady Visual Studio 2017 se sada SDK nainstaluje výběrem úlohy **vývoje .NET Core pro různé platformy** .

* Balíček NuGet [Microsoft. AspNetCore. SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/)

## <a name="server-side-prerendering"></a>Dokončení fáze před vykreslením na straně serveru

Univerzální aplikace (označované také jako isomorphic) je schopný běžet současně na serveru a klienta aplikace v jazyce JavaScript. Angular, React a dalších oblíbených architektur poskytují univerzální platformu pro tento styl vývoje aplikace. Cílem je nejprve vykreslit framework komponent na serveru pomocí Node.js a následně delegovat další spuštění klientovi.

ASP.NET Core [pomocníků značek](xref:mvc/views/tag-helpers/intro) , které poskytuje SpaServices, zjednoduší implementaci předvykreslování na straně serveru vyvoláním funkcí JavaScriptu na serveru.

### <a name="server-side-prerendering-prerequisites"></a>Požadavky na předběžné vykreslování na straně serveru

Nainstalujte balíček npm [s předvykreslováním ASPNET](https://www.npmjs.com/package/aspnet-prerendering) :

```console
npm i -S aspnet-prerendering
```

### <a name="server-side-prerendering-configuration"></a>Konfigurace předvykreslování na straně serveru

Pomocníky značek jsou v souboru *_ViewImports. cshtml* projektu Zjistitelnější prostřednictvím registrace oboru názvů:

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/_ViewImports.cshtml?highlight=3)]

Tyto pomocné rutiny značek abstrakci složitými rozhraními komunikovat přímo s nižší úrovně rozhraní API s využitím syntaxe jazyka HTML v zobrazení Razor:

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=5)]

### <a name="asp-prerender-module-tag-helper"></a>ASP-PreRender-pomocník značek modulu

Pomocník značek `asp-prerender-module` použitý v předchozím příkladu kódu provede na serveru pomocí Node. js *clientapp/DIST/Main-Server. js* . V zájmu srozumitelnosti je soubor *Main-Server. js* artefaktem úlohy TypeScript-to-JavaScript transpilation v procesu sestavení pro [sadu Webpack](https://webpack.github.io/) . Webpack definuje alias vstupního bodu `main-server`; a přechod grafu závislostí pro tento alias začíná v souboru *clientapp/boot-server. TS* :

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=53)]

V následujících úhlových příkladech používá soubor *clientapp/boot-server. TS* funkci `createServerRenderer` a `RenderResult` typ balíčku `aspnet-prerendering` npm ke konfiguraci vykreslování serveru prostřednictvím Node. js. Značka HTML určená pro vykreslování na straně serveru je předána voláním funkce Resolve, které je zabaleno do silně typovaného objektu JavaScript `Promise`. Význam `Promise`ho objektu je, že asynchronně poskytuje kód HTML na stránce pro vložení do elementu zástupného prvku modelu DOM.

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-34,79-)]

### <a name="asp-prerender-data-tag-helper"></a>ASP-PreRender-Pomocník s datovou značkou

Při spojení s pomocníkem značky `asp-prerender-module` lze pomocný objekt `asp-prerender-data` značky použít k předání kontextových informací ze zobrazení Razor na server JavaScript. Například následující kód předává uživatelská data do modulu `main-server`:

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=9-12)]

Přijatý `UserName` argument je serializován pomocí integrovaného serializátoru JSON a je uložen v objektu `params.data`. V následujícím úhlovém příkladu se data používají k sestavení vlastního pozdravu v rámci prvku `h1`:

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,38-52,79-)]

Názvy vlastností předané v Pomocníkech značek jsou reprezentovány **PascalCase** Notation. Na rozdíl od jazyka JavaScript, kde jsou stejné názvy vlastností, které jsou reprezentovány s **CamelCase**. Výchozí konfigurace serializace JSON je zodpovědná za tento rozdíl.

Pro rozšíření na předchozí příklad kódu mohou být data předána ze serveru do zobrazení tím, že se vlastnost `globals` poskytne `resolve` funkce:

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,57-77,79-)]

Pole `postList` definované uvnitř objektu `globals` je připojeno k objektu globálního `window` prohlížeče. Tato proměnná zvedání globálním rozsahem eliminuje duplikace úsilí, zvlášť, protože se týkají načítají stejná data jednou na serveru a znovu na straně klienta.

![globální proměnná postList připojen k objektu window](spa-services/_static/global_variable.png)

## <a name="webpack-dev-middleware"></a>Webpacku Dev middlewaru

[Middleware](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) pro vývoj pro Webpack zavádí zjednodušený vývojový pracovní postup, který v sadě Webpack vytváří prostředky na vyžádání. Middleware automaticky zkompiluje a slouží prostředky na straně klienta, pokud stránka opětovném načtení nástroje v prohlížeči. Alternativním přístupem je vyvolat ručně Webpacku prostřednictvím skriptu buildu projektu npm při změně závislostí třetích stran nebo vlastní kód. V následujícím příkladu je zobrazen skript sestavení npm v souboru *Package. JSON* :

```json
"build": "npm run build:vendor && npm run build:custom",
```

### <a name="webpack-dev-middleware-prerequisites"></a>Požadavky na middleware pro vývoj pro Webpack

Instalace balíčku [ASPNET-Webpack](https://www.npmjs.com/package/aspnet-webpack) npm:

```console
npm i -D aspnet-webpack
```

### <a name="webpack-dev-middleware-configuration"></a>Konfigurace pro vývoj middlewaru pro Webpack

Middleware pro vývoj v sadě Webpack je zaregistrovaný do kanálu požadavků HTTP prostřednictvím následujícího kódu v `Configure` metodě souboru *Startup.cs* :

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_WebpackMiddlewareRegistration&highlight=4)]

Před [registrací statického souboru hostujícího](xref:fundamentals/static-files) prostřednictvím metody rozšíření `UseStaticFiles` je nutné volat metodu rozšíření `UseWebpackDevMiddleware`. Z bezpečnostních důvodů se zaregistrujte middleware pouze v případě, že aplikace běží v režimu pro vývoj.

Vlastnost `output.publicPath` souboru *Webpack. config. js* oznamuje middlewari, aby sledoval `dist` složku pro změny:

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,13-16)]

## <a name="hot-module-replacement"></a>Nahrazení horké modulu

Zamyslete se nad funkcí HMR ( [Hot Module nahradilo](https://webpack.js.org/concepts/hot-module-replacement/) ) sady Webpack jako vývoj middlewaru pro vývoj pro [Webpack](#webpack-dev-middleware). HMR představuje stejné výhody, ale další zjednodušuje pracovní postup vývoje pomocí automaticky aktualizuje obsah stránky po kompilaci změn. Tento nástroj nezaměnili při aktualizaci prohlížeče, které by mohly narušit aktuálního stavu v paměti a ladicí relace tato jednostránková aplikace. Je aktivní propojení mezi službou Webpacku Dev Middleware a prohlížeče, což znamená, že změny jsou vloženy do prohlížeče.

### <a name="hot-module-replacement-prerequisites"></a>Požadavky na nahrazení horkého modulu

Nainstalujte balíček [Webpack – Hot-middleware](https://www.npmjs.com/package/webpack-hot-middleware) npm:

```console
npm i -D webpack-hot-middleware
```

### <a name="hot-module-replacement-configuration"></a>Konfigurace nahrazení horkého modulu

Komponentu HMR je nutné zaregistrovat do kanálu požadavků HTTP MVC v metodě `Configure`:

```csharp
app.UseWebpackDevMiddleware(new WebpackDevMiddlewareOptions {
    HotModuleReplacement = true
});
```

Stejně jako u rozhraní [Webpack dev middleware](#webpack-dev-middleware)musí být metoda rozšíření `UseWebpackDevMiddleware` volána před metodou rozšíření `UseStaticFiles`. Z bezpečnostních důvodů se zaregistrujte middleware pouze v případě, že aplikace běží v režimu pro vývoj.

Soubor *Webpack. config. js* musí definovat `plugins` pole, i když je ponecháno prázdné:

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,25)]

Po načtení aplikace v prohlížeči, poskytuje nástroje pro vývojáře kartu konzoly pro potvrzení HMR aktivace:

![Horké modulu nahrazení připojené zprávy](spa-services/_static/hmr_connected.png)

## <a name="routing-helpers"></a>Směrování pomocné rutiny

Ve většině ASP.NET Core jednostránkové na straně klienta je často žádoucí směrování na straně klienta kromě směrování na straně serveru. Jednostránková aplikace a MVC směrovací systémy může pracovat nezávisle na bez rušení. Existuje, ale jeden okraj případu představující vyzve: identifikace obdržíte kód odpovědi HTTP 404.

Vezměte v úvahu scénář, ve kterém se používá trasa s rozšířením `/some/page`. Předpokládejme, požadavek nebude – porovnávací trasu na straně serveru, ale jeho vzor neshoduje trasu na straně klienta. Teď zvažte příchozí požadavek na `/images/user-512.png`, který obecně očekává, že se na serveru najde soubor s obrázkem. Pokud tato požadovaná cesta prostředku neodpovídá žádné cestě na straně serveru nebo statickému souboru, není pravděpodobné, že by aplikace na straně klienta zpracovávala&mdash;obecně návratového kódu stavu HTTP 404.

### <a name="routing-helpers-prerequisites"></a>Požadavky na helpdesky směrování

Nainstalujte balíček npm pro směrování na straně klienta. Jako příklad použijeme Angular:

```console
npm i -S @angular/router
```

### <a name="routing-helpers-configuration"></a>Konfigurace pomocníků pro směrování

V metodě `Configure` se používá rozšiřující metoda s názvem `MapSpaFallbackRoute`:

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_MvcRoutingTable&highlight=7-9)]

Trasy jsou vyhodnocovány v pořadí, ve kterém jsou konfigurovány. V důsledku toho je `default` trasa v předchozím příkladu kódu použita jako první pro porovnávání vzorů.

## <a name="create-a-new-project"></a>Vytvoření nového projektu

Služby JavaScriptu poskytují předem nakonfigurované šablony aplikací. SpaServices se používá v těchto šablonách společně s různými rozhraními a knihovnami, jako je například úhlová, reakce a Redux.

Tyto šablony lze ji nainstalovat prostřednictvím rozhraní příkazového řádku .NET Core, spuštěním následujícího příkazu:

```dotnetcli
dotnet new --install Microsoft.AspNetCore.SpaTemplates::*
```

Zobrazí se seznam dostupných šablon SPA:

| Šablony                                 | Krátký název | Jazyk | Značky        |
| ------------------------------------------| :--------: | :------: | :---------: |
| ASP.NET Core MVC pomocí Angular             | Angular    | [C#]     | Web/MVC/SPA |
| MVC ASP.NET Core pomocí React.js            | react      | [C#]     | Web/MVC/SPA |
| MVC ASP.NET Core pomocí React.js a Redux  | reactredux | [C#]     | Web/MVC/SPA |

Pokud chcete vytvořit nový projekt pomocí jedné z šablon SPA, zahrňte do příkazu [dotnet New](/dotnet/core/tools/dotnet-new) **krátký název** šablony. Následující příkaz vytvoří aplikaci Angular s ASP.NET Core MVC, která je nakonfigurovaná na straně serveru:

```dotnetcli
dotnet new angular
```

### <a name="set-the-runtime-configuration-mode"></a>Nastavit režim konfigurace modulu runtime

Existují dva způsoby konfigurace primární runtime:

* **Vývoj**:
  * Zahrnuje zdrojových mapování pro usnadnění ladění.
  * Neoptimalizuje kód na straně klienta pro výkon.
* **Provozní**:
  * Vyloučí zdrojových mapování.
  * Optimalizuje kód na straně klienta prostřednictvím sdružování a minifikace.

ASP.NET Core používá k uložení režimu konfigurace proměnnou prostředí s názvem `ASPNETCORE_ENVIRONMENT`. Další informace najdete v tématu [nastavení prostředí](xref:fundamentals/environments#set-the-environment).

### <a name="run-with-net-core-cli"></a>Spustit s .NET Core CLI

Obnovte spuštěním následujícího příkazu v kořenovém adresáři projektu vyžaduje NuGet a npm, balíčků:

```dotnetcli
dotnet restore && npm i
```

Sestavte a spusťte aplikaci:

```dotnetcli
dotnet run
```

Aplikace se spouští na místním hostiteli podle [režimu konfigurace modulu runtime](#set-the-runtime-configuration-mode). Přechod na `http://localhost:5000` v prohlížeči zobrazí úvodní stránku.

### <a name="run-with-visual-studio-2017"></a>Spustit se sadou Visual Studio 2017

Otevřete soubor *. csproj* generovaný příkazem [dotnet New](/dotnet/core/tools/dotnet-new) . Obnoví požadované balíčky NuGet a npm se automaticky při otevření projektu. Tento proces obnovení může trvat několik minut a aplikace je připraven ke spuštění až po dokončení. Klikněte na zelené tlačítko spustit nebo stiskněte `Ctrl + F5`a prohlížeč se otevře na úvodní stránce aplikace. Aplikace běží na místním hostiteli podle [režimu konfigurace modulu runtime](#set-the-runtime-configuration-mode).

## <a name="test-the-app"></a>Otestování aplikace

Šablony SpaServices jsou předem nakonfigurované tak, aby se spouštěly testy na straně klienta pomocí [Karma](https://karma-runner.github.io/1.0/index.html) a [jednotek Jasmine](https://jasmine.github.io/). Jasmine je Oblíbené jednotkových testů pro jazyk JavaScript, zatímco Karma je nástroj test runner pro tyto testy. Karma je nakonfigurovaná tak, aby spolupracovala s rozhraním pro [vývoj pro Webpack](#webpack-dev-middleware) . to znamená, že vývojář není nutný k zastavení a spuštění testu pokaždé, když se změny provedou. Ať už běží s testovacím případem nebo testový případ samotný kód, test se spustí automaticky.

Použití úhlové aplikace jako příklad, jsou již pro `CounterComponent` v souboru *Counter. Component. spec. TS* zadány dva jednotek Jasmine testovací případy:

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/app/components/counter/counter.component.spec.ts?range=15-28)]

Otevřete příkazový řádek v adresáři *clientapp* . Spusťte následující příkaz:

```console
npm test
```

Skript spustí Karma Test Runner, který přečte nastavení definované v souboru *Karma. conf. js* . Kromě dalších nastavení identifikuje *Karma. conf. js* testovací soubory, které se mají provést prostřednictvím pole `files`:

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/test/karma.conf.js?range=4-5,8-11)]

## <a name="publish-the-app"></a>Publikování aplikace

Další informace o publikování do Azure najdete v [tomto problému GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/12474) .

Kombinování vygenerovaných prostředků na straně klienta a publikované artefakty ASP.NET Core do balíčku připravených k nasazení může být náročná. Naštěstí, SpaServices orchestruje, že celý proces publikace s vlastním cílem MSBuild s názvem `RunWebpack`:

[!code-xml[](../client-side/spa-services/sample/SpaServicesSampleApp/SpaServicesSampleApp.csproj?range=31-45)]

Cíl nástroje MSBuild má následující zodpovědnosti:

1. Obnovte balíčky npm.
1. Vytvoření výrobního sestavení prostředků na straně klienta.
1. Vytvořte výrobní sestavení vlastních prostředků na straně klienta.
1. Zkopírujte prostředky vygenerované v sadě Webpack do složky pro publikování.

Cíl nástroje MSBuild je vyvolána při spuštění:

```dotnetcli
dotnet publish -c Release
```

## <a name="additional-resources"></a>Další zdroje

* [Úhlové dokumenty](https://angular.io/docs)

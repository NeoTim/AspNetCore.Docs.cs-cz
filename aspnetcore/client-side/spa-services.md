---
title: Použití služeb jazyka JavaScript vytvořte jednostránkové aplikace v ASP.NET Core
author: scottaddie
description: Přečtěte si o výhodách používání služby jazyka JavaScript vytvořte jedné stránce aplikace (SPA) se opírá o ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: H1Hack27Feb2017
ms.date: 05/28/2019
uid: client-side/spa-services
ms.openlocfilehash: 19710b58bca606d21feda9069ad00edd1e4f72e9
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67813476"
---
# <a name="use-javascript-services-to-create-single-page-applications-in-aspnet-core"></a>Použití služeb jazyka JavaScript vytvořte jednostránkové aplikace v ASP.NET Core

Podle [Scott Addie](https://github.com/scottaddie) a [Fiyaz Hasan](https://fiyazhasan.me/)

Jednu stránku aplikace (SPA) je typ oblíbené webové aplikace z důvodu jeho přináší bohaté uživatelské prostředí. Integrace rozhraní jednostránková aplikace na straně klienta nebo knihovny, například [Angular](https://angular.io/) nebo [React](https://facebook.github.io/react/), pomocí rozhraní na straně serveru, jako jsou ASP.NET Core může být obtížné. Omezíte třecí plochy v procesu integrace byla vyvinuta služby jazyka JavaScript. Umožňuje bezproblémové operace mezi různých klientských a serverových sad technologie.

## <a name="what-is-javascript-services"></a>Co je služba jazyka JavaScript

Služby jazyka JavaScript je sada technologií na straně klienta pro ASP.NET Core. Jeho cílem je na pozici ASP.NET Core, jako preferovanou platformu na straně serveru vývojářů pro vytváření SPA.

Služba jazyka JavaScript se skládá ze dvou různých balíčcích NuGet:

* [Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)
* [Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)

Tyto balíčky jsou užitečné v následujících scénářích:

* Spuštění JavaScriptu na serveru
* Použít SPA architektury nebo knihovny
* Vytvoření prostředků na straně klienta s Webpacku

Mnoho pozornosti v tomto článku je umístěn na použití SpaServices balíčku.

## <a name="what-is-spaservices"></a>Co je SpaServices

SpaServices byl vytvořen na pozici ASP.NET Core, jako preferovanou platformu na straně serveru vývojářů pro vytváření SPA. SpaServices není vyžadována k vývoji SPA s ASP.NET Core a nezamyká vývojáře do rozhraní konkrétního klienta.

SpaServices infrastrukturu užitečné jako:

* [Dokončení fáze před vykreslením na straně serveru](#server-side-prerendering)
* [Webpacku Dev middlewaru](#webpack-dev-middleware)
* [Nahrazení horké modulu](#hot-module-replacement)
* [Směrování pomocné rutiny](#routing-helpers)

Společně tyto součástí infrastruktury vylepšení pracovního postupu vývoje i prostředí runtime. Součásti může být přijata jednotlivě.

## <a name="prerequisites-for-using-spaservices"></a>Předpoklady pro použití SpaServices

Pro práci s SpaServices, nainstalujte následující:

* [Node.js](https://nodejs.org/) (verze 6 nebo novější) pomocí npm

  * Ověření tyto součásti jsou nainstalovány a najdete, spusťte z příkazového řádku následující:

    ```console
    node -v && npm -v
    ```

  * Pokud nasazení na web Azure, nemusíte nic dělat&mdash;Node.js je nainstalováný a dostupný v serverových prostředích.

* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]

  * Ve Windows pomocí sady Visual Studio 2017, je nainstalován sadu SDK tak, že vyberete **vývoj pro různé platformy .NET Core** pracovního vytížení.

* [Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) balíček NuGet

## <a name="server-side-prerendering"></a>Dokončení fáze před vykreslením na straně serveru

Univerzální aplikace (označované také jako isomorphic) je schopný běžet současně na serveru a klienta aplikace v jazyce JavaScript. Angular, React a dalších oblíbených architektur poskytují univerzální platformu pro tento styl vývoje aplikace. Cílem je nejprve vykreslit framework komponent na serveru pomocí Node.js a následně delegovat další spuštění klientovi.

ASP.NET Core [pomocných rutin značek](xref:mvc/views/tag-helpers/intro) poskytované SpaServices zjednodušení implementace modulů na straně serveru dokončení fáze před vykreslením vyvoláním funkce jazyka JavaScript na serveru.

### <a name="server-side-prerendering-prerequisites"></a>Před vykreslením požadavky na straně serveru

Nainstalujte [aspnet – dokončení fáze před vykreslením](https://www.npmjs.com/package/aspnet-prerendering) balíčku npm:

```console
npm i -S aspnet-prerendering
```

### <a name="server-side-prerendering-configuration"></a>Před vykreslením konfigurace na straně serveru

Pomocné rutiny značek se provádí objevitelný prostřednictvím registrace oboru názvů v projektu *_ViewImports.cshtml* souboru:

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/_ViewImports.cshtml?highlight=3)]

Tyto pomocné rutiny značek abstrakci složitými rozhraními komunikovat přímo s nižší úrovně rozhraní API s využitím syntaxe jazyka HTML v zobrazení Razor:

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=5)]

### <a name="asp-prerender-module-tag-helper"></a>Pomocná rutina značky prostředí ASP. provedením operace prerender modulu

`asp-prerender-module` Pomocné rutiny značky použité v předchozím příkladu kód provede *ClientApp/dist/main-server.js* na serveru pomocí Node.js. Pro přehlednost společnosti saké *hlavní server.js* je artefakt úlohy transpilation TypeScript pro JavaScript v souboru [Webpacku](https://webpack.github.io/) procesu sestavení. Webpacku definuje vstupní bod aliasu `main-server`; a procházení grafu závislostí pro tento alias začíná *ClientApp/spouštěcí server.ts* souboru:

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=53)]

V následujícím příkladu Angular *ClientApp/spouštěcí server.ts* využívá soubor `createServerRenderer` funkce a `RenderResult` typ `aspnet-prerendering` balíček npm ke konfiguraci vykreslování na serveru pomocí Node.js. Značka jazyka HTML, který je určený pro vykreslování na straně serveru je předána volání funkce řešení, která je zabalena v JavaScriptu se silnými typy `Promise` objektu. `Promise` Násobek objektu je, že asynchronně poskytuje kód HTML na stránce pro vkládání v elementu modelu DOM zástupný symbol.

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-34,79-)]

### <a name="asp-prerender-data-tag-helper"></a>Pomocná rutina značky prostředí ASP. provedením operace prerender dat

Pokud spolu s `asp-prerender-module` pomocné rutiny značky `asp-prerender-data` pomocné rutiny značky lze použít k předání kontextové informace ze zobrazení syntaxe Razor pro jazyk JavaScript na straně serveru. Například následující kód předá data uživatele `main-server` modul:

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=9-12)]

Přijatého `UserName` argument serializován pomocí předdefinovaných serializátor JSON a je uložen v `params.data` objektu. V následujícím příkladu Angular, data se používají k vytvoření přizpůsobené pozdrav v rámci `h1` element:

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,38-52,79-)]

Názvy vlastností, které jsou předány v pomocných rutin značek jsou reprezentovány s **PascalCase** zápis. Kontrast, JavaScript, kde jsou reprezentovány stejné názvy vlastností **camelCase**. Výchozí konfigurace serializace JSON je zodpovědná za tento rozdíl.

Rozbalte na předchozí příklad kódu, data mohou být předána ze serveru do zobrazení podle hydrating `globals` zadanou pro vlastnost `resolve` funkce:

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,57-77,79-)]

`postList` Pole definované uvnitř `globals` objekt je připojen k prohlížeči globální `window` objektu. Tato proměnná zvedání globálním rozsahem eliminuje duplikace úsilí, zvlášť, protože se týkají načítají stejná data jednou na serveru a znovu na straně klienta.

![globální proměnná postList připojen k objektu window](spa-services/_static/global_variable.png)

## <a name="webpack-dev-middleware"></a>Webpacku Dev middlewaru

[Dev Middleware Webpacku](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) zavádí zjednodušit vývoj pracovní postup, kterým Webpacku sestavení prostředků na vyžádání. Middleware automaticky zkompiluje a slouží prostředky na straně klienta, pokud stránka opětovném načtení nástroje v prohlížeči. Alternativním přístupem je vyvolat ručně Webpacku prostřednictvím skriptu buildu projektu npm při změně závislostí třetích stran nebo vlastní kód. Npm sestavení skriptu *package.json* souboru je znázorněno v následujícím příkladu:

```json
"build": "npm run build:vendor && npm run build:custom",
```

### <a name="webpack-dev-middleware-prerequisites"></a>Middleware Webpacku vývoj požadavků

Nainstalujte [aspnet webpacku](https://www.npmjs.com/package/aspnet-webpack) balíčku npm:

```console
npm i -D aspnet-webpack
```

### <a name="webpack-dev-middleware-configuration"></a>Konfigurace Webpacku Dev middlewaru

Webpacku Dev middlewaru je zaregistrovat do kanál požadavků HTTP prostřednictvím následující kód *Startup.cs* souboru `Configure` metody:

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_WebpackMiddlewareRegistration&highlight=4)]

`UseWebpackDevMiddleware` – Metoda rozšíření musí být volána před [registrace hostování statického souboru](xref:fundamentals/static-files) prostřednictvím `UseStaticFiles` – metoda rozšíření. Z bezpečnostních důvodů se zaregistrujte middleware pouze v případě, že aplikace běží v režimu pro vývoj.

*Webpack.config.js* souboru `output.publicPath` vlastnost informuje middleware a sledujte `dist` složku pro změny:

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,13-16)]

## <a name="hot-module-replacement"></a>Nahrazení horké modulu

Představte si, že na Webpacku [horké nahrazení modulu](https://webpack.js.org/concepts/hot-module-replacement/) (HMR) funkce jako vývojem představ o [Webpacku Dev Middleware](#webpack-dev-middleware). HMR představuje stejné výhody, ale další zjednodušuje pracovní postup vývoje pomocí automaticky aktualizuje obsah stránky po kompilaci změn. Tento nástroj nezaměnili při aktualizaci prohlížeče, které by mohly narušit aktuálního stavu v paměti a ladicí relace tato jednostránková aplikace. Je aktivní propojení mezi službou Webpacku Dev Middleware a prohlížeče, což znamená, že změny jsou vloženy do prohlížeče.

### <a name="hot-module-replacement-prerequisites"></a>Aktivní požadavky modulu nahrazení

Nainstalujte [webpacku horkou middleware](https://www.npmjs.com/package/webpack-hot-middleware) balíčku npm:

```console
npm i -D webpack-hot-middleware
```

### <a name="hot-module-replacement-configuration"></a>Aktivní konfigurace modulu nahrazení

Komponenta HMR musí být zaregistrovaný do kanál požadavků HTTP pro MVC v `Configure` metody:

```csharp
app.UseWebpackDevMiddleware(new WebpackDevMiddlewareOptions {
    HotModuleReplacement = true
});
```

Stejně jako dřív platilo s [Webpacku Dev Middleware](#webpack-dev-middleware), `UseWebpackDevMiddleware` – metoda rozšíření musí být volána před `UseStaticFiles` – metoda rozšíření. Z bezpečnostních důvodů se zaregistrujte middleware pouze v případě, že aplikace běží v režimu pro vývoj.

*Webpack.config.js* soubor musí definovat `plugins` i v případě, že je ponecháno prázdné pole:

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,25)]

Po načtení aplikace v prohlížeči, poskytuje nástroje pro vývojáře kartu konzoly pro potvrzení HMR aktivace:

![Horké modulu nahrazení připojené zprávy](spa-services/_static/hmr_connected.png)

## <a name="routing-helpers"></a>Směrování pomocné rutiny

Ve většině SPA založené na ASP.NET Core směrování na straně klienta se často vyžaduje kromě směrování na straně serveru. Jednostránková aplikace a MVC směrovací systémy může pracovat nezávisle na bez rušení. Existuje, ale jeden okraj případu představující vyzve: identifikace obdržíte kód odpovědi HTTP 404.

Vezměte v úvahu scénář, ve kterém bez přípony trasu `/some/page` se používá. Předpokládejme, požadavek nebude – porovnávací trasu na straně serveru, ale jeho vzor neshoduje trasu na straně klienta. Teď se podíváme příchozí žádosti pro `/images/user-512.png`, což obecně očekává, že se najít soubor obrázku na serveru. Pokud tuto cestu požadovaného prostředku neodpovídá žádné trasu na straně serveru nebo statický soubor, je pravděpodobné, že aplikace na straně klienta by ji zpracovat&mdash;obvykle vrátí stavový kód HTTP 404 je žádoucí.

### <a name="routing-helpers-prerequisites"></a>Požadavky směrování pomocné rutiny

Nainstalujte balíček npm směrování na straně klienta. Jako příklad použijeme Angular:

```console
npm i -S @angular/router
```

### <a name="routing-helpers-configuration"></a>Konfigurace směrování pomocné rutiny

Rozšiřující metoda s názvem `MapSpaFallbackRoute` je používán `Configure` metody:

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_MvcRoutingTable&highlight=7-9)]

Trasy se vyhodnocují v pořadí, ve kterém jsou nakonfigurované. V důsledku toho `default` trasy v předchozím příkladu kódu je nejdříve nepoužije pro porovnávání vzorů.

## <a name="create-a-new-project"></a>Vytvoření nového projektu

JavaScript služby poskytují předem nakonfigurované aplikace šablony. Tyto šablony ve spojení s jinou architektury a knihovny, například Angular, React nebo Reduxem SpaServices používá.

Tyto šablony lze ji nainstalovat prostřednictvím rozhraní příkazového řádku .NET Core, spuštěním následujícího příkazu:

```console
dotnet new --install Microsoft.AspNetCore.SpaTemplates::*
```

Zobrazí se seznam dostupných šablon SPA:

| Šablony                                 | Krátký název | Jazyk | Značky        |
| ------------------------------------------| :--------: | :------: | :---------: |
| ASP.NET Core MVC pomocí Angular             | Angular    | [C#]     | Web/MVC/SPA |
| MVC ASP.NET Core pomocí React.js            | react      | [C#]     | Web/MVC/SPA |
| MVC ASP.NET Core pomocí React.js a Redux  | reactredux | [C#]     | Web/MVC/SPA |

K vytvoření nového projektu pomocí jedné z šablon SPA, zahrnout **krátký název** šablony v [dotnet nové](/dotnet/core/tools/dotnet-new) příkaz. Následující příkaz vytvoří aplikaci Angular s ASP.NET Core MVC, která je nakonfigurovaná na straně serveru:

```console
dotnet new angular
```

### <a name="set-the-runtime-configuration-mode"></a>Nastavit režim konfigurace modulu runtime

Existují dva způsoby konfigurace primární runtime:

* **Vývoj**:
  * Zahrnuje zdrojových mapování pro usnadnění ladění.
  * Neoptimalizuje kód na straně klienta pro výkon.
* **Produkční**:
  * Vyloučí zdrojových mapování.
  * Optimalizuje kód na straně klienta prostřednictvím sdružování a minifikace.

ASP.NET Core, používá proměnnou prostředí s názvem `ASPNETCORE_ENVIRONMENT` k uložení režim konfigurace arm. Další informace najdete v tématu [nastavte prostředí](xref:fundamentals/environments#set-the-environment).

### <a name="run-with-net-core-cli"></a>Spuštění pomocí .NET Core CLI

Obnovte spuštěním následujícího příkazu v kořenovém adresáři projektu vyžaduje NuGet a npm, balíčků:

```console
dotnet restore && npm i
```

Sestavte a spusťte aplikaci:

```console
dotnet run
```

Spuštění aplikace v místním hostiteli podle [režim konfigurace modulu runtime](#set-the-runtime-configuration-mode). Přejděte na `http://localhost:5000` v prohlížeči se zobrazí na úvodní stránku.

### <a name="run-with-visual-studio-2017"></a>Spuštění sady Visual Studio 2017

Otevřít *.csproj* soubor generovaný nástrojem [dotnet nové](/dotnet/core/tools/dotnet-new) příkazu. Obnoví požadované balíčky NuGet a npm se automaticky při otevření projektu. Tento proces obnovení může trvat několik minut a aplikace je připraven ke spuštění až po dokončení. Klikněte na zelené tlačítko pro spuštění nebo stisknutím klávesy `Ctrl + F5`, a v prohlížeči otevře úvodní stránku aplikace. Aplikace bude spuštěna na místním hostiteli podle [režim konfigurace modulu runtime](#set-the-runtime-configuration-mode).

## <a name="test-the-app"></a>Testování aplikace

SpaServices šablony jsou předem nakonfigurované ke spuštění testů na straně klienta pomocí [Karma](https://karma-runner.github.io/1.0/index.html) a [Jasmine](https://jasmine.github.io/). Jasmine je Oblíbené jednotkových testů pro jazyk JavaScript, zatímco Karma je nástroj test runner pro tyto testy. Karma je nakonfigurováno pro práci s [Webpacku Dev Middleware](#webpack-dev-middleware) tak, že vývojář není potřeba zastavit a spustit test pokaždé, když dojde ke změně. Ať už běží s testovacím případem nebo testový případ samotný kód, test se spustí automaticky.

Jako příklad použijeme aplikaci Angular, dva Jasmine testovací případy jsou už k dispozici pro `CounterComponent` v *counter.component.spec.ts* souboru:

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/app/components/counter/counter.component.spec.ts?range=15-28)]

Otevřete příkazový řádek v *ClientApp* adresáře. Spusťte následující příkaz:

```console
npm test
```

Tento skript spustí nástroj test runner Karma, který čte definované v nastavení *karma.conf.js* souboru. Kromě jiných nastavení *karma.conf.js* identifikuje testovací soubory, které chcete provést prostřednictvím jeho `files` pole:

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/test/karma.conf.js?range=4-5,8-11)]

## <a name="publish-the-app"></a>Publikování aplikace

Kombinování vygenerovaných prostředků na straně klienta a publikované artefakty ASP.NET Core do balíčku připravených k nasazení může být náročná. Naštěstí SpaServices orchestruje tento proces celé publikace s vlastní cíl nástroje MSBuild s názvem `RunWebpack`:

[!code-xml[](../client-side/spa-services/sample/SpaServicesSampleApp/SpaServicesSampleApp.csproj?range=31-45)]

Cíl nástroje MSBuild má následující zodpovědnosti:

1. Obnovení balíčků npm.
1. Vytvoření produkčních sestavení prostředků třetích stran, na straně klienta.
1. Vytvoření produkčních sestavení vlastních prostředků na straně klienta.
1. Kopírovat materiály generované Webpacku do složky publikovat.

Cíl nástroje MSBuild je vyvolána při spuštění:

```console
dotnet publish -c Release
```

## <a name="additional-resources"></a>Další zdroje

* [Dokumentace angular](https://angular.io/docs)

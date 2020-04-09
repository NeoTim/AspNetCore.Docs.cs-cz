---
title: Použití služeb JavaScript u vytváření jednostránkových aplikací v ASP.NET jádru
author: scottaddie
description: Seznamte se s výhodami používání služeb JavaScript services k vytvoření jednostránkové aplikace (SPA) podporované ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: H1Hack27Feb2017
ms.date: 09/06/2019
uid: client-side/spa-services
ms.openlocfilehash: c0c73882afd579510ad9cdf5b485c1d6fbeadd1c
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78663777"
---
# <a name="use-javascript-services-to-create-single-page-applications-in-aspnet-core"></a>Použití služeb JavaScript u vytváření jednostránkových aplikací v ASP.NET jádru

[Scott Addie](https://github.com/scottaddie) a [Fiyaz Hasan](https://fiyazhasan.me/)

Jednostránková aplikace (SPA) je populární typ webové aplikace díky své vlastní bohaté uživatelské zkušenosti. Integrace architektury SPA na straně klienta nebo knihovny, jako je [například Angular](https://angular.io/) nebo [React](https://facebook.github.io/react/), s rozhraními na straně serveru, jako je ASP.NET Core může být obtížné. JavaScript Services byl vyvinut pro snížení tření v integračním procesu. Umožňuje bezproblémový provoz mezi různými zásobníky klientské a serverové technologie.

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> Funkce popsané v tomto článku jsou zastaralé od ASP.NET Core 3.0. Jednodušší mechanismus integrace rozhraní SPA je k dispozici v balíčku [Microsoft.AspNetCore.SpaServices.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) NuGet. Další informace naleznete [v tématu [Announcement] Obsoleting Microsoft.AspNetCore.SpaServices a Microsoft.AspNetCore.NodeServices](https://github.com/dotnet/AspNetCore/issues/12890).

::: moniker-end

## <a name="what-is-javascript-services"></a>Co jsou služby JavaScriptu

JavaScript Services je kolekce technologií na straně klienta pro ASP.NET Core. Jeho cílem je umístit ASP.NET Core jako preferovanou platformu vývojářů na straně serveru pro vytváření certifikátů.

Služby JavaScript se skládá ze dvou odlišných balíčků NuGet:

* [Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)
* [Služby Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)

Tyto balíčky jsou užitečné v následujících scénářích:

* Spuštění JavaScriptu na serveru
* Použití spa frameworku nebo knihovny
* Vytváření prostředků na straně klienta pomocí webového balíčku

Velká část zaměření v tomto článku je umístěn na použití spaservices balíček.

## <a name="what-is-spaservices"></a>Co je SpaServices

SpaServices byl vytvořen pro pozici ASP.NET Core jako upřednostňovanou platformu vývojářů na straně serveru pro vytváření certifikátů. SpaServices není nutné vyvíjet SPA s ASP.NET Core a nezamkne vývojáře do konkrétního klientského rámce.

SpaServices poskytuje užitečnou infrastrukturu, jako jsou:

* [Předběžné vykreslování na straně serveru](#server-side-prerendering)
* [Webový balíček Dev Middleware](#webpack-dev-middleware)
* [Výměna horkého modulu](#hot-module-replacement)
* [Pomocníci směrování](#routing-helpers)

Tyto součásti infrastruktury společně vylepšují pracovní postup vývoje i prostředí runtime. Komponenty mohou být přijaty individuálně.

## <a name="prerequisites-for-using-spaservices"></a>Předpoklady pro používání SpaServices

Chcete-li pracovat se službami SpaServices, nainstalujte následující:

* [Node.js](https://nodejs.org/) (verze 6 nebo novější) s npm

  * Chcete-li ověřit, zda jsou tyto součásti nainstalovány a lze je najít, spusťte z příkazového řádku následující:

    ```console
    node -v && npm -v
    ```

  * Pokud se nasazujete na web&mdash;Azure, není vyžadována žádná akce Node.js, která je nainstalovaná a dostupná v serverových prostředích.

* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]

  * Ve Windows pomocí Visual Studia 2017 je sada SDK nainstalována výběrem **vývojovéúlohy napříč platformami .NET Core.**

* Balíček [Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) NuGet

## <a name="server-side-prerendering"></a>Předběžné vykreslování na straně serveru

Univerzální (známá také jako izomorfní) aplikace je javascriptová aplikace schopná spouštět jak na serveru, tak na straně klienta. Úhlové, Reagovat a další populární architektury poskytují univerzální platformu pro tento styl vývoje aplikací. Cílem je nejprve vykreslit součásti architektury na serveru prostřednictvím Node.js a potom delegovat další provádění klientovi.

ASP.NET [Pomocníky](xref:mvc/views/tag-helpers/intro) core tagů poskytované společností SpaServices zjednodušují implementaci předběžného vykreslování na straně serveru vyvoláním funkcí JavaScriptu na serveru.

### <a name="server-side-prerendering-prerequisites"></a>Požadavky předběžného vykreslování na straně serveru

Nainstalujte balíček npm [aspnet-prerendering:](https://www.npmjs.com/package/aspnet-prerendering)

```console
npm i -S aspnet-prerendering
```

### <a name="server-side-prerendering-configuration"></a>Konfigurace předběžného vykreslování na straně serveru

Tag Helpers jsou zjistitelné prostřednictvím registrace oboru názvů v souboru *_ViewImports.cshtml* projektu:

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/_ViewImports.cshtml?highlight=3)]

Tyto tag pomocníci abstraktní pryč složitosti komunikace přímo s nízkoúrovňové rozhraní API využitím html-jako syntaxe uvnitř zobrazení Razor:

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=5)]

### <a name="asp-prerender-module-tag-helper"></a>pomocník tagů asp-prerender-module

Pomocník `asp-prerender-module` značek použitý v předchozím příkladu kódu provede *clientapp/dist/main-server.js* na serveru prostřednictvím souboru Node.js. Z důvodu jasnosti je soubor *main-server.js* artefaktem úlohy transpichování typu Script-to-JavaScript v procesu sestavení [webpacku.](https://webpack.github.io/) Webpack definuje alias vstupního `main-server`bodu ; a procházení grafu závislostí pro tento alias začíná v souboru *ClientApp/boot-server.ts:*

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=53)]

V následujícím příkladu Úhlové používá soubor *ClientApp/boot-server.ts* `createServerRenderer` funkci a `RenderResult` typ balíčku `aspnet-prerendering` npm ke konfiguraci vykreslování serveru pomocí souboru Node.js. Značky HTML určené pro vykreslování na straně serveru jsou předány volání funkce `Promise` resolve, které je zabaleno do silně psaného objektu JavaScript. Význam `Promise` objektu spoáčuje s tím, že asynchronně poskytuje značky HTML na stránku pro vkládání v zástupném prvku dom.

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-34,79-)]

### <a name="asp-prerender-data-tag-helper"></a>pomocník s tagy asp-prerender-data

Ve spojení s `asp-prerender-module` pomocníkem tagů lze `asp-prerender-data` pomocníka tagů použít k předání kontextových informací ze zobrazení Razor do JavaScriptu na straně serveru. Například následující značky předá uživatelská data modulu: `main-server`

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=9-12)]

Přijatý `UserName` argument je serializován pomocí předdefinovaného serializátoru `params.data` JSON a je uložen v objektu. V následujícím příkladu Úhlové se data používají k vytvoření `h1` přizpůsobeného pozdravu v rámci prvku:

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,38-52,79-)]

Názvy vlastností předané v pomocné spoje tagů jsou reprezentovány zápisem **PascalCase.** Kontrast, že na JavaScript, kde jsou stejné názvy vlastností reprezentovány **camelCase**. Výchozí konfigurace serializace JSON je zodpovědný za tento rozdíl.

Chcete-li rozbalit v předchozím příkladu kódu, data mohou být `globals` předána ze `resolve` serveru do zobrazení hydratací vlastnosti poskytované funkci:

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,57-77,79-)]

Pole `postList` definované uvnitř `globals` objektu je připojeno ke `window` globálnímu objektu prohlížeče. Tato proměnná zvedání do globálního oboru eliminuje duplicitu úsilí, zejména pokud jde o načítání stejných dat jednou na serveru a znovu na straně klienta.

![globální proměnná postList připojená k objektu okna](spa-services/_static/global_variable.png)

## <a name="webpack-dev-middleware"></a>Webový balíček Dev Middleware

[Webpack Dev Middleware](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) představuje zjednodušený vývojový pracovní postup, ve kterém Webpack vytváří prostředky na vyžádání. Middleware automaticky zkompiluje a slouží prostředky na straně klienta při opětovném načtení stránky v prohlížeči. Alternativní přístup je ručně vyvolat webpack prostřednictvím projektu npm sestavení skriptu při změně závislosti třetí strany nebo vlastní kód. Skript sestavení npm v souboru *package.json* je uveden v následujícím příkladu:

```json
"build": "npm run build:vendor && npm run build:custom",
```

### <a name="webpack-dev-middleware-prerequisites"></a>Požadavky webového balíčku Dev Middleware

Nainstalujte balíček [aspnet-webpack](https://www.npmjs.com/package/aspnet-webpack) npm:

```console
npm i -D aspnet-webpack
```

### <a name="webpack-dev-middleware-configuration"></a>Konfigurace webového balíčku Dev Middleware

Webpack Dev Middleware je zaregistrován do kanálu požadavků *Startup.cs* HTTP pomocí `Configure` následujícího kódu v metodě Startup.cs souboru:

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_WebpackMiddlewareRegistration&highlight=4)]

Metoda `UseWebpackDevMiddleware` rozšíření musí být volána před [registrací statického souboru hosting](xref:fundamentals/static-files) prostřednictvím `UseStaticFiles` metody rozšíření. Z bezpečnostních důvodů zaregistrujte middleware pouze v případě, že aplikace běží v režimu vývoje.

Vlastnost souboru `output.publicPath` *webpack.config.js* říká middlewaru, `dist` aby sledoval změnu ve složce:

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,13-16)]

## <a name="hot-module-replacement"></a>Výměna horkého modulu

Představte si funkci [Webpack Hot Module Replacement](https://webpack.js.org/concepts/hot-module-replacement/) (HMR) jako evoluci [Webpack Dev Middleware](#webpack-dev-middleware). HMR zavádí všechny stejné výhody, ale dále zjednodušuje pracovní postup vývoje automatickou aktualizací obsahu stránky po kompilaci změn. Nepleťte si to s aktualizací prohlížeče, která by narušila aktuální stav v paměti a ladění relace SPA. Existuje živé spojení mezi službou Webpack Dev Middleware a prohlížečem, což znamená, že změny jsou tlačeny do prohlížeče.

### <a name="hot-module-replacement-prerequisites"></a>Předpoklady pro výměnu aktivního modulu

Nainstalujte balíček [webpack-hot-middleware](https://www.npmjs.com/package/webpack-hot-middleware) npm:

```console
npm i -D webpack-hot-middleware
```

### <a name="hot-module-replacement-configuration"></a>Konfigurace výměny aktivního modulu

HMR komponenta musí být registrována do kanálu požadavků `Configure` HTTP společnosti MVC v metodě:

```csharp
app.UseWebpackDevMiddleware(new WebpackDevMiddlewareOptions {
    HotModuleReplacement = true
});
```

Jak tomu bylo u [Webpack Dev Middleware](#webpack-dev-middleware), `UseStaticFiles` musí být před metodou rozšíření volána metoda `UseWebpackDevMiddleware` rozšíření. Z bezpečnostních důvodů zaregistrujte middleware pouze v případě, že aplikace běží v režimu vývoje.

Soubor *webpack.config.js* musí `plugins` definovat pole, i když je prázdné:

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,25)]

Po načtení aplikace v prohlížeči poskytuje karta Konzola vývojářských nástrojů potvrzení aktivace HMR:

![Zpráva o výměně připojeného modulu hot module](spa-services/_static/hmr_connected.png)

## <a name="routing-helpers"></a>Pomocníci směrování

Ve většině ASP.NET virtuálních microsofty na straně klienta směrování je často žádoucí kromě směrování na straně serveru. Směrovací systémy SPA a MVC mohou pracovat nezávisle bez rušení. Existuje však jeden okraj případu, který představuje výzvy: identifikace 404 odpovědí HTTP.

Zvažte scénář, ve kterém `/some/page` se používá trasa bez rozšíření. Předpokládejme, že požadavek neodpovídá směru, ale jeho vzor odpovídá trase na straně klienta. Nyní zvažte příchozí `/images/user-512.png`požadavek pro , který obecně očekává, že najde soubor obrázku na serveru. Pokud tato požadovaná cesta k prostředku neodpovídá žádné cestě na straně serveru nebo statickému&mdash;souboru, je nepravděpodobné, že by aplikace na straně klienta zpracovat obecně vrácení 404 HTTP stavový kód je žádoucí.

### <a name="routing-helpers-prerequisites"></a>Požadavky pomocníků směrování

Nainstalujte balíček npm směrování na straně klienta. Použití úhlu jako příklad:

```console
npm i -S @angular/router
```

### <a name="routing-helpers-configuration"></a>Konfigurace pomocných postupů směrování

Metoda rozšíření `MapSpaFallbackRoute` s názvem `Configure` se používá v metodě:

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_MvcRoutingTable&highlight=7-9)]

Trasy jsou vyhodnocovány v pořadí, ve kterém jsou nakonfigurovány. V důsledku `default` toho se trasa v předchozím příkladu kódu používá nejprve pro porovnávání vzorů.

## <a name="create-a-new-project"></a>Vytvoření nového projektu

Služby JavaScript poskytují předem nakonfigurované šablony aplikací. SpaServices se používá v těchto šablonách ve spojení s různými rámci a knihovny, jako je například Angular, React a Redux.

Tyto šablony lze nainstalovat pomocí rozhraní PŘÍKAZU .NET Core cli spuštěním následujícího příkazu:

```dotnetcli
dotnet new --install Microsoft.AspNetCore.SpaTemplates::*
```

Zobrazí se seznam dostupných šablon SPA:

| Šablony                                 | Krátký název | Jazyk | Značky        |
| ------------------------------------------| :--------: | :------: | :---------: |
| MVC ASP.NET jádro s úhlovým             | Úhlové    | [C#]     | Web/MVC/SPA |
| MVC ASP.NET jádro s React.js            | react      | [C#]     | Web/MVC/SPA |
| MVC ASP.NET Core s React.js a Redux  | reactredux | [C#]     | Web/MVC/SPA |

Chcete-li vytvořit nový projekt pomocí jedné ze šablon SPA, zadejte **krátký název** šablony do nového příkazu [dotnet.](/dotnet/core/tools/dotnet-new) Následující příkaz vytvoří úhlovou aplikaci s ASP.NET Core MVC nakonfigurovaným pro stranu serveru:

```dotnetcli
dotnet new angular
```

### <a name="set-the-runtime-configuration-mode"></a>Nastavení režimu konfigurace za běhu

Existují dva primární režimy konfigurace za běhu:

* **Vývoj**:
  * Obsahuje zdrojové mapy pro usnadnění ladění.
  * Neoptimalizuje kód na straně klienta pro výkon.
* **Výroba**:
  * Nezahrnuje zdrojové mapy.
  * Optimalizuje kód na straně klienta prostřednictvím sdružování a minifikace.

ASP.NET Core používá k `ASPNETCORE_ENVIRONMENT` uložení režimu konfigurace proměnnou prostředí s názvem. Další informace naleznete [v tématu Nastavení prostředí](xref:fundamentals/environments#set-the-environment).

### <a name="run-with-net-core-cli"></a>Spustit pomocí rozhraní CLI jádra .NET

Obnovení požadovaných balíčků NuGet a npm spuštěním následujícího příkazu v kořenovém adresáři projektu:

```dotnetcli
dotnet restore && npm i
```

Sestavení a spuštění aplikace:

```dotnetcli
dotnet run
```

Aplikace se spustí na localhost podle [režimu konfigurace runtime](#set-the-runtime-configuration-mode). Přechod na `http://localhost:5000` v prohlížeči zobrazí vstupní stránku.

### <a name="run-with-visual-studio-2017"></a>Spuštění s Visual Studio 2017

Otevřete soubor *.csproj* generovaný novým příkazem [dotnet.](/dotnet/core/tools/dotnet-new) Požadované balíčky NuGet a npm jsou obnoveny automaticky při otevření projektu. Tento proces obnovení může trvat až několik minut a aplikace je připravena ke spuštění po dokončení. Klikněte na zelené `Ctrl + F5`tlačítko spuštění nebo stiskněte tlačítko a prohlížeč se otevře na vstupní stránce aplikace. Aplikace běží na localhost podle [režimu konfigurace runtime](#set-the-runtime-configuration-mode).

## <a name="test-the-app"></a>Otestování aplikace

SpaServices šablony jsou pre-nakonfigurován tak, aby spustit klient-side testy pomocí [Karma](https://karma-runner.github.io/1.0/index.html) a [Jasmine](https://jasmine.github.io/). Jasmine je populární jednotka testování rámec pro JavaScript, zatímco Karma je test běžec pro tyto testy. Karma je nakonfigurován pro práci s [Webpack Dev Middleware](#webpack-dev-middleware) tak, že vývojář není nutné zastavit a spustit test pokaždé, když jsou provedeny změny. Ať už je kód spuštěn proti testovacího případu nebo samotného testovacího případu, test se spustí automaticky.

Použití angular aplikace jako příklad, dva jasmine testovacích `CounterComponent` případů jsou již k dispozici v *souboru counter.component.spec.ts:*

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/app/components/counter/counter.component.spec.ts?range=15-28)]

Otevřete příkazový řádek v adresáři *ClientApp.* Spusťte následující příkaz:

```console
npm test
```

Skript spustí test Karma běžec, který čte nastavení definované v *karma.conf.js* souboru. Mimo jiné nastavení *karma.conf.js* identifikuje testovací soubory, které `files` mají být provedeny prostřednictvím svého pole:

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/test/karma.conf.js?range=4-5,8-11)]

## <a name="publish-the-app"></a>Publikování aplikace

Další informace o publikování v Azure najdete v [tomto problému s GitHubem.](https://github.com/dotnet/AspNetCore.Docs/issues/12474)

Kombinace generovaných prostředků na straně klienta a publikovaných artefaktů ASP.NET Core do balíčku připraveného k nasazení může být těžkopádné. Naštěstí SpaServices orchestruje celý proces publikování s vlastním cílem `RunWebpack`MSBuild s názvem :

[!code-xml[](../client-side/spa-services/sample/SpaServicesSampleApp/SpaServicesSampleApp.csproj?range=31-45)]

Cíl MSBuild má následující povinnosti:

1. Obnovte balíčky npm.
1. Vytvořte sestavení na úrovni výroby aktiv třetích stran na straně klienta.
1. Vytvořte sestavení vlastních prostředků na straně klienta na úrovni výroby.
1. Zkopírujte datové zdroje generované webpackem do složky publikování.

Cíl MSBuild je vyvolán při spuštění:

```dotnetcli
dotnet publish -c Release
```

## <a name="additional-resources"></a>Další zdroje

* [Úhlové dokumenty](https://angular.io/docs)

---
title: Svazek a minfika statických aktiv v ASP.NET Core
author: scottaddie
description: Naučte se optimalizovat statické prostředky ve webové aplikaci ASP.NET Core použitím technik sdružování a minifikace.
ms.author: scaddie
ms.custom: mvc
ms.date: 04/15/2020
uid: client-side/bundling-and-minification
ms.openlocfilehash: 670ac6a96c3affd2b2ac699836f536aea7d85ff3
ms.sourcegitcommit: 77c046331f3d633d7cc247ba77e58b89e254f487
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81488686"
---
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a>Svazek a minfika statických aktiv v ASP.NET Core

[Scott Addie](https://twitter.com/Scott_Addie) a [David Pine](https://twitter.com/davidpine7)

Tento článek vysvětluje výhody použití sdružování a minifikace, včetně toho, jak lze tyto funkce používat s webovými aplikacemi ASP.NET Core.

## <a name="what-is-bundling-and-minification"></a>Co je svazování a minifikace

Sdružování a minifikace jsou dvě odlišné optimalizace výkonu, které můžete použít ve webové aplikaci. Při společném sdružování a minifikaci se zvyšuje výkon snížením počtu požadavků serveru a zmenšením velikosti požadovaných statických datových zdrojů.

Sdružování a minifikace především zlepšují dobu načítání požadavku na první stránku. Jakmile je webová stránka požadována, prohlížeč uloží statické datové zdroje do mezipaměti (JavaScript, CSS a obrázky). V důsledku toho svazování a minifikace nezlepšují výkon při žádosti o stejnou stránku nebo stránky na stejném webu požadujícím stejné prostředky. Pokud není záhlaví vypršení platnosti správně nastaveno na datových zdrojích a pokud se nepoužívá svazování a minifikace, heuristika čerstvosti prohlížeče označuje datové zdroje zastaralé po několika dnech. Kromě toho prohlížeč vyžaduje žádost o ověření pro každý prostředek. V tomto případě sdružování a minifikace poskytují zlepšení výkonu i po první stránce požadavku.

### <a name="bundling"></a>Svazování

Sdružování kombinuje několik souborů do jediného souboru. Sdružování snižuje počet požadavků serveru, které jsou nezbytné k vykreslení webového datového zdroje, například webové stránky. Můžete vytvořit libovolný počet jednotlivých svazků speciálně pro CSS, JavaScript, atd. Méně souborů znamená méně požadavků HTTP z prohlížeče na server nebo ze služby poskytující vaši aplikaci. To má za následek lepší výkon načítání první stránky.

### <a name="minification"></a>Minification

Minifikace odebere nepotřebné znaky z kódu bez změny funkčnosti. Výsledkem je významné zmenšení velikosti požadovaných datových zdrojů (například CSS, obrázků a souborů JavaScriptu). Časté vedlejší účinky minifikace zahrnují zkrácení názvů proměnných na jeden znak a odstranění komentářů a zbytečných mezer.

Zvažte následující funkci JavaScriptu:

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.js)]

Minifikace snižuje funkci na následující:

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.min.js)]

Kromě odebrání komentáře a zbytečné mezery, následující parametr a názvy proměnných byly přejmenovány takto:

Původní | Přejmenován
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a>Dopad spojeného prodeje a minifikace

V následující tabulce jsou uvedeny rozdíly mezi individuálním zatížením aktiv a použitím spojených prodejů a minifikace:

Akce | S B/M | Bez B/M | Změnit
--- | :---: | :---: | :---:
Žádosti o soubor  | 7   | 18     | 157%
KB Převedeno | 156 | 264.68 | 70 %
Doba načítání (ms) | 885 | 2360   | 167%

Prohlížeče jsou poměrně podrobné s ohledem na hlavičky požadavků HTTP. Celkový počet bajtů odeslaných metrika viděl významné snížení při sdružování. Doba načítání ukazuje významné zlepšení, ale tento příklad běžel místně. Vyšší nárůst y výkonu se realizuje při použití spojených prodejů a minifikace s aktivy převedenými přes síť.

## <a name="choose-a-bundling-and-minification-strategy"></a>Vyberte si strategii sdružování a minifikace

Šablony projektu MVC a Razor Pages poskytují řešení pro sdružování a minifikaci skládající se z konfiguračního souboru JSON. Nástroje třetích stran, jako je například [grunt](xref:client-side/using-grunt) úkol běžec, provádět stejné úkoly s trochu složitější. Nástroj jiného výrobce je skvělý, když váš vývojový pracovní postup&mdash;vyžaduje zpracování nad rámec sdružování a minifikace, jako je linting a optimalizace obrazu. Pomocí sdružování a minifikace v době návrhu se minified soubory vytvoří před nasazením aplikace. Sdružování a minifying před nasazením poskytuje výhodu snížení zatížení serveru. Je však důležité si uvědomit, že sdružování a minifikace v době návrhu zvyšuje složitost sestavení a funguje pouze se statickými soubory.

## <a name="configure-bundling-and-minification"></a>Konfigurace sdružování a minifikace

::: moniker range="<= aspnetcore-2.0"

V ASP.NET Core 2.0 nebo starší, šablony projektu MVC a Razor Pages poskytují konfigurační soubor *bundleconfig.json,* který definuje možnosti pro každou sadu:

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

V ASP.NET jádrem 2.1 nebo novějším přidejte do kořenového adresáře projektu MVC nebo Razor Pages nový soubor JSON s názvem *bundleconfig.json.* Jako výchozí bod zahrňte do tohoto souboru následující json:

::: moniker-end

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

Soubor *bundleconfig.json* definuje možnosti pro každou sadu. V předchozím příkladu je definována konfigurace jednoho svazku pro vlastní soubory JavaScriptu (*wwwroot/js/site.js*) a stylů *(wwwroot/css/site.css).*

Možnosti konfigurace zahrnují:

* `outputFileName`: Název souboru svazku pro výstup. Může obsahovat relativní cestu ze souboru *bundleconfig.json.* **Požadované**
* `inputFiles`: Pole souborů, které chcete spojit dohromady. Jedná se o relativní cesty ke konfiguračnímu souboru. **volitelné**, *prázdná hodnota má za následek prázdný výstupní soubor. [globbing](https://www.tldp.org/LDP/abs/html/globbingref.html) vzory jsou podporovány.
* `minify`: Možnosti minifikace pro typ výstupu. **volitelné**, *výchozí - `minify: { enabled: true }` *
  * Možnosti konfigurace jsou k dispozici pro typ výstupního souboru.
    * [CSS Minifier](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [Minipěč JavaScriptu](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [HTML Minifier](https://github.com/madskristensen/BundlerMinifier/wiki)
* `includeInProject`: Příznak označující, zda mají být do souboru projektu přidávány generované soubory. **volitelné**, *výchozí - false*
* `sourceMap`: Příznak označující, zda chcete generovat zdrojovou mapu pro přibalený soubor. **volitelné**, *výchozí - false*
* `sourceMapRootPath`: Kořenová cesta pro uložení generovaného zdrojového mapového souboru.

## <a name="add-files-to-workflow"></a>Přidání souborů do pracovního postupu

Vezměme si příklad, ve kterém je přidán další soubor *custom.css* připomínající následující:

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

Chcete-li minify *custom.css* a složka s *site.css* do souboru *site.min.css,* přidejte relativní cestu *bundleconfig.json*:

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> Alternativně by mohl být použit následující globbing vzor:
>
> ```json
> "inputFiles": ["wwwroot/**/!(*.min).css" ]
> ```
>
> Tento globbing vzor odpovídá všem souborům CSS a vylučuje minified soubor vzor.

Sestavte aplikaci. Otevřete *soubor site.min.css* a všimněte si, že obsah *souboru custom.css* je připojen na konec souboru.

## <a name="environment-based-bundling-and-minification"></a>Spojené spoje a minifikace založené na životním prostředí

Jako osvědčený postup by měly být přibalené a minifikované soubory vaší aplikace používány v produkčním prostředí. Během vývoje původní soubory usnadňují ladění aplikace.

Určete, které soubory chcete zahrnout na stránky pomocí [pomocníka pro označení prostředí](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) v zobrazeních. Pomocník značky prostředí vykreslí jeho obsah pouze při spuštění v [určitých prostředích](xref:fundamentals/environments).

Následující `environment` značka vykreslí nezpracované soubory CSS `Development` při spuštění v prostředí:

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

::: moniker-end

Následující `environment` značka vykreslí přibalené a minified CSS soubory `Development`při spuštění v jiném prostředí než . Například spuštění `Production` v `Staging` nebo aktivuje vykreslování těchto stylů:

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

::: moniker-end

## <a name="consume-bundleconfigjson-from-gulp"></a>Spotřebovávat bundleconfig.json od Gulp

Existují případy, kdy pracovní postup sdružování a minifikace aplikace vyžaduje další zpracování. Mezi příklady patří optimalizace obrazu, ukládání do mezipaměti a zpracování datových zdrojů CDN. Chcete-li splnit tyto požadavky, můžete převést svazování a minification pracovní postup použít Gulp.

### <a name="manually-convert-the-bundling-and-minification-workflow-to-use-gulp"></a>Ruční převod sdružovacího a minifikačního pracovního postupu na použití gulpu

Přidejte soubor *package.json* s `devDependencies`následujícím textem do kořenového adresáře projektu:

> [!WARNING]
> Modul `gulp-uglify` nepodporuje ECMAScript (ES) 2015 / ES6 a novější. Nainstalujte [gulp-terser](https://www.npmjs.com/package/gulp-terser) místo `gulp-uglify` použití ES2015 / ES6 nebo novější.

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

Nainstalujte závislosti spuštěním následujícího příkazu na stejné úrovni jako *package.json*:

```console
npm i
```

Nainstalujte cli Gulp jako globální závislost:

```console
npm i -g gulp-cli
```

Zkopírujte níže uvedený soubor *gulpfile.js* do kořenového adresáře projektu:

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a>Spuštění úloh Gulp

Chcete-li spustit úlohu minifikace gulpu před sestavením projektu v sadě Visual Studio, přidejte do souboru *.csproj následující [cíl MSBuild](/visualstudio/msbuild/msbuild-targets) Target:

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

V tomto příkladu všechny `MyPreCompileTarget` úkoly definované v `Build` rámci cíle spustit před předdefinovaný cíl. Výstup podobný následujícímu se zobrazí v okně Výstup sady Visual Studio:

```console
1>------ Build started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
1>[14:17:49] Using gulpfile C:\BuildBundlerMinifierApp\gulpfile.js
1>[14:17:49] Starting 'min:js'...
1>[14:17:49] Starting 'min:css'...
1>[14:17:49] Starting 'min:html'...
1>[14:17:49] Finished 'min:js' after 83 ms
1>[14:17:49] Finished 'min:css' after 88 ms
========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
```

## <a name="additional-resources"></a>Další zdroje

* [Použití nástroje Grunt](xref:client-side/using-grunt)
* [Používání více prostředí](xref:fundamentals/environments)
* [Pomocné rutiny značek](xref:mvc/views/tag-helpers/intro)

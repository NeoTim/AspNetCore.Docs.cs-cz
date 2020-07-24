---
title: Statické prostředky sady prostředků a minimalizuje v ASP.NET Core
author: scottaddie
description: Naučte se optimalizovat statické prostředky ve ASP.NET Core webové aplikaci, a to pomocí metod sdružování a minifikace.
ms.author: scaddie
ms.custom: mvc
ms.date: 07/23/2020
no-loc:
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: client-side/bundling-and-minification
ms.openlocfilehash: 5db6ab3d790257c677c0a4ed7e605eb39c2982ed
ms.sourcegitcommit: cc845634a490c49ff869c89b6e422b6d65d0e886
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2020
ms.locfileid: "87159720"
---
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a>Statické prostředky sady prostředků a minimalizuje v ASP.NET Core

Autor – [Scott Addie](https://twitter.com/Scott_Addie) a [David borovice](https://twitter.com/davidpine7)

Tento článek vysvětluje výhody použití sdružování a minifikace, včetně toho, jak se tyto funkce dají používat s ASP.NET Core Web Apps.

## <a name="what-is-bundling-and-minification"></a>Co je sdružování a minifikace

Sdružování a minifikace jsou dvě odlišná optimalizace výkonu, které můžete použít ve webové aplikaci. Společně, sdružování a minifikace vylepšit výkon snížením počtu požadavků serveru a zmenšením velikosti požadovaných statických prostředků.

Seřízení a minifikace primárně zlepšují dobu načtení první stránky. Po vyžádání webové stránky prohlížeč ukládá do mezipaměti statické prostředky (JavaScript, CSS a image). V důsledku toho sdružování a minifikace nezlepšují výkon při požadavku na stejnou stránku nebo stránky ve stejné lokalitě, která vyžaduje stejné prostředky. Pokud není hlavička Expires v prostředcích správně nastavená a pokud se nepoužívá sdružování a minifikace, budou heuristické aktuálnosti v prohlížeči označovat prostředky zastaralé po několika dnech. Kromě toho prohlížeč vyžaduje žádost o ověření pro každý Asset. V tomto případě přináší sdružování a minifikace vylepšení výkonu i po první žádosti stránky.

### <a name="bundling"></a>Sdružování

Sdružování kombinuje několik souborů do jediného souboru. Sdružování snižuje počet požadavků serveru, které jsou nezbytné k vykreslování webového prostředku, například webové stránky. Můžete vytvořit libovolný počet jednotlivých sad konkrétně pro šablony stylů CSS, JavaScript atd. Méně souborů znamená méně požadavků HTTP z prohlížeče na server nebo ze služby, která poskytuje vaši aplikaci. Výsledkem je lepší výkon při načítání první stránky.

### <a name="minification"></a>Minifikace

Minifikace odebere z kódu nadbytečné znaky bez změny funkčnosti. Výsledkem je výrazné snížení velikosti u požadovaných prostředků (například CSS, obrázky a JavaScriptové soubory). Běžné vedlejší účinky minifikace zahrnují zkrácení názvů proměnných na jeden znak a odstranění komentářů a nepotřebného prázdného místa.

Vezměte v úvahu následující funkci JavaScriptu:

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.js)]

Minifikace snižuje funkci na následující:

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.min.js)]

Kromě odebrání komentářů a nepotřebných prázdných znaků byly následující parametry a názvy proměnných přejmenovány takto:

Původně | Jmenovanou
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a>Dopad sdružování a minifikace

Následující tabulka popisuje rozdíly mezi jednotlivými nasazováním prostředků a používáním sdružování a minifikace:

Akce | S B/M | Bez B/M | Změnit
--- | :---: | :---: | :---:
Požadavky na soubory  | 7   | 18     | 157%
Přenesené KB | 156 | 264,68 | 70 %
Čas načtení (MS) | 885 | 2360   | 167%

Prohlížeče jsou poměrně podrobné, s ohledem na hlavičky požadavků HTTP. Metrika odeslaných celkových bajtů se při sdružování výrazně snížila. Doba načítání ukazuje výrazné zlepšení, ale tento příklad běžel místně. Zvýšení výkonu se využívá při použití sdružování a minifikace s prostředky přenesených přes síť.

## <a name="choose-a-bundling-and-minification-strategy"></a>Výběr strategie sdružování a minifikace

:::no-loc(Razor):::Šablony projektů MVC a stránky poskytují řešení pro sdružování a minifikace, které se skládají z konfiguračního souboru JSON. Nástroje třetích stran, jako je třeba Spouštěč úloh [grunt](xref:client-side/using-grunt) , provádějí stejné úlohy s větší složitou složitostí. Nástroj třetí strany je skvěle vhodný, když váš vývojový pracovní postup vyžaduje zpracování mimo sdružování a minifikace, jako &mdash; je linting a optimalizace obrázků. Když použijete sdružování a minifikace v době návrhu, vytvoří se soubory minifikovaného před nasazením aplikace. Sdružování a minifikace před nasazením přináší výhodu omezeného zatížení serveru. Je ale důležité pochopit, že sdružování v době návrhu a minifikace zvyšuje složitost sestavení a funguje jenom se statickými soubory.

## <a name="configure-bundling-and-minification"></a>Konfigurace sdružování a minifikace

::: moniker range="<= aspnetcore-2.0"

V ASP.NET Core 2,0 nebo starších verzích :::no-loc(Razor)::: představují šablony projektů MVC a pages *bundleconfig.jsv* konfiguračním souboru, který definuje možnosti pro jednotlivé sady prostředků:

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

V ASP.NET Core 2,1 nebo novějším přidejte nový soubor JSON s názvem *bundleconfig.js*do :::no-loc(Razor)::: kořenového adresáře projektu MVC nebo Pages. Do tohoto souboru vložte následující JSON jako výchozí bod:

::: moniker-end

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

*bundleconfig.jsv* souboru definuje možnosti pro jednotlivé sady. V předchozím příkladu je definována jedna konfigurace sady pro vlastní soubory jazyka JavaScript (*wwwroot/js/site.js*) a šablony stylů (*wwwroot/CSS/Web. CSS*).

Mezi možnosti konfigurace patří:

* `outputFileName`: Název souboru sady pro výstup. Může obsahovat relativní cestu z *bundleconfig.js* souboru. **požadovanou**
* `inputFiles`: Pole souborů, které se mají seskupit. Jedná se o relativní cesty ke konfiguračnímu souboru. **volitelné**, * prázdná hodnota vede k prázdnému výstupnímu souboru. jsou podporovány vzory [expanze](https://www.tldp.org/LDP/abs/html/globbingref.html) .
* `minify`: Možnosti minifikace pro typ výstupu. **volitelné**, *výchozí – `minify: { enabled: true }` *
  * Možnosti konfigurace jsou k dispozici pro jednotlivé typy výstupních souborů.
    * [Šablony stylů CSS Minifier](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [JavaScriptový Minifier](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [Minifier HTML](https://github.com/madskristensen/BundlerMinifier/wiki)
* `includeInProject`: Příznak označující, zda se mají přidat generované soubory do souboru projektu. **volitelné**, *výchozí – NEPRAVDA*
* `sourceMap`: Příznak označující, zda se má generovat zdrojová mapa pro soubor zahrnutý do balíčku. **volitelné**, *výchozí – NEPRAVDA*
* `sourceMapRootPath`: Kořenová cesta pro uložení vygenerovaného zdrojového souboru mapování.

## <a name="add-files-to-workflow"></a>Přidat soubory do pracovního postupu

Vezměte v úvahu příklad, ve kterém se přidá další *vlastní soubor. CSS* , který se podobá následujícímu:

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

Pokud chcete minimalizuje *vlastní soubor. CSS* a seskupit ho pomocí *site. CSS* do souboru *Web. min. CSS* , přidejte relativní cestu k *bundleconfig.js*:

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> Případně můžete použít následující vzor expanze názvů:
>
> ```json
> "inputFiles": ["wwwroot/**/!(*.min).css" ]
> ```
>
> Tento vzor expanze názvů se shoduje se všemi soubory šablon stylů CSS a vylučuje vzor souboru minifikovaného.

Sestavte aplikaci. Otevřete *Web. min. CSS* a Všimněte si, že obsah *vlastní. CSS* je připojen na konec souboru.

## <a name="environment-based-bundling-and-minification"></a>Sdružování a minifikace založené na prostředí

Jako osvědčený postup by se měly v produkčním prostředí používat balíčky a minifikovaného soubory vaší aplikace. Během vývoje se původní soubory vytvářejí pro snazší ladění aplikace.

Určete soubory, které chcete zahrnout na vaše stránky, pomocí [pomocné rutiny tag prostředí](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) v zobrazeních. Pomocník značek prostředí vykresluje obsah pouze při spuštění v konkrétních [prostředích](xref:fundamentals/environments).

Následující `environment` značka vykresluje nezpracované soubory šablon stylů CSS při spuštění v `Development` prostředí:

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

::: moniker-end

Následující `environment` značka vykresluje soubory šablon stylů CSS a minifikovaného při spuštění v jiném prostředí než `Development` . Například spuštění v `Production` nebo `Staging` spustí vykreslování těchto šablon stylů:

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

::: moniker-end

## <a name="consume-bundleconfigjson-from-gulp"></a>Využívání bundleconfig.jsod Gulp

Existují případy, kdy pracovní postup sdružování aplikace a minifikace vyžaduje další zpracování. Mezi příklady patří Optimalizace obrázků, busting mezipaměti a zpracování prostředků CDN. Aby bylo možné tyto požadavky splnit, můžete převést pracovní postup sdružování a minifikace na použití Gulp.

### <a name="manually-convert-the-bundling-and-minification-workflow-to-use-gulp"></a>Ručně převeďte pracovní postup sdružování a minifikace na použití Gulp

*package.json* `devDependencies` Do kořenového adresáře projektu přidejtepackage.jsdo souboru s následujícím:

> [!WARNING]
> `gulp-uglify`Modul nepodporuje ECMAScript (ES) 2015/ES6 a novější. Nainstalujte [Gulp-terser](https://www.npmjs.com/package/gulp-terser) namísto `gulp-uglify` použití ES2015/ES6 nebo novějšího.

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

Nainstalujte závislosti spuštěním následujícího příkazu na stejné úrovni jako *package.jsna*:

```bash
npm i
```

Nainstalujte rozhraní příkazového řádku Gulp jako globální závislost:

```bash
npm i -g gulp-cli
```

Zkopírujte soubor *gulpfile.js* níže do kořenového adresáře projektu:

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a>Spuštění úloh Gulp

Aktivace úlohy minifikace Gulp před sestavením projektu v aplikaci Visual Studio:

1. Nainstalujte balíček NuGet [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier) .
1. Do souboru projektu přidejte následující [cíl nástroje MSBuild](/visualstudio/msbuild/msbuild-targets) :

    [!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

V tomto příkladu všechny úlohy, které jsou definovány v rámci cílového prostředí, `MyPreCompileTarget` před předdefinovaným `Build` cílem. Výstup podobný následujícímu se zobrazí v okně výstupu sady Visual Studio:

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

## <a name="additional-resources"></a>Další zdroje informací

* [Použití nástroje Grunt](xref:client-side/using-grunt)
* [Používání více prostředí](xref:fundamentals/environments)
* [Pomocné rutiny značek](xref:mvc/views/tag-helpers/intro)

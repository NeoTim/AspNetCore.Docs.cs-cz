---
title: Statické prostředky sady prostředků a minimalizuje v ASP.NET Core
author: scottaddie
description: Naučte se optimalizovat statické prostředky ve ASP.NET Core webové aplikaci, a to pomocí metod sdružování a minifikace.
ms.author: scaddie
ms.custom: mvc
ms.date: 06/17/2019
uid: client-side/bundling-and-minification
ms.openlocfilehash: 7499381a24a2513a4fbd1205f245e624c86647c3
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71080562"
---
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a>Statické prostředky sady prostředků a minimalizuje v ASP.NET Core

Autor – [Scott Addie](https://twitter.com/Scott_Addie) a [David borovice](https://twitter.com/davidpine7)

Tento článek vysvětluje výhody použití sdružování a minifikace, včetně toho, jak se tyto funkce dají používat s ASP.NET Core Web Apps.

## <a name="what-is-bundling-and-minification"></a>Co je sdružování a minifikace

Sdružování a minifikace jsou dvě odlišná optimalizace výkonu, které můžete použít ve webové aplikaci. Společně, sdružování a minifikace vylepšit výkon snížením počtu požadavků serveru a zmenšením velikosti požadovaných statických prostředků.

Seřízení a minifikace primárně zlepšují dobu načtení první stránky. Po vyžádání webové stránky prohlížeč ukládá do mezipaměti statické prostředky (JavaScript, CSS a image). V důsledku toho sdružování a minifikace nezlepšují výkon při požadavku na stejnou stránku nebo stránky ve stejné lokalitě, která vyžaduje stejné prostředky. Pokud není hlavička Expires v prostředcích správně nastavená a pokud se nepoužívá sdružování a minifikace, budou heuristické aktuálnosti v prohlížeči označovat prostředky zastaralé po několika dnech. Kromě toho prohlížeč vyžaduje žádost o ověření pro každý Asset. V tomto případě přináší sdružování a minifikace vylepšení výkonu i po první žádosti stránky.

### <a name="bundling"></a>sdružování

Sdružování kombinuje více souborů do jednoho souboru. Sdružování snižuje počet požadavků serveru, které jsou nezbytné k vykreslování webového prostředku, například webové stránky. Můžete vytvořit libovolný počet jednotlivých sad konkrétně pro šablony stylů CSS, JavaScript atd. Méně souborů znamená méně požadavků HTTP z prohlížeče na server nebo ze služby, která poskytuje vaši aplikaci. Výsledkem je lepší výkon při načítání první stránky.

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

Akce | S B/M | Bez B/M | Změna
--- | :---: | :---: | :---:
Požadavky na soubory  | 7   | 18     | 157%
Přenesené KB | 156 | 264.68 | 70%
Čas načtení (MS) | 885 | 2360   | 167%

Prohlížeče jsou poměrně podrobné, s ohledem na hlavičky požadavků HTTP. Metrika odeslaných celkových bajtů se při sdružování výrazně snížila. Doba načítání ukazuje výrazné zlepšení, ale tento příklad běžel místně. Zvýšení výkonu se využívá při použití sdružování a minifikace s prostředky přenesených přes síť.

## <a name="choose-a-bundling-and-minification-strategy"></a>Výběr strategie sdružování a minifikace

Šablony projektů MVC a Razor Pages poskytují předem připravené řešení pro sdružování a minifikace, které se skládají z konfiguračního souboru JSON. Nástroje třetích stran, jako je třeba Spouštěč úloh [grunt](xref:client-side/using-grunt) , provádějí stejné úlohy s větší složitou složitostí. Nástroj třetí strany je skvěle vhodný, když váš vývojový pracovní postup vyžaduje zpracování mimo sdružování a minifikace&mdash;, jako je linting a optimalizace obrázků. Když použijete sdružování a minifikace v době návrhu, vytvoří se soubory minifikovaného před nasazením aplikace. Sdružování a minifikace před nasazením přináší výhodu omezeného zatížení serveru. Je ale důležité pochopit, že sdružování v době návrhu a minifikace zvyšuje složitost sestavení a funguje jenom se statickými soubory.

## <a name="configure-bundling-and-minification"></a>Konfigurace sdružování a minifikace

::: moniker range="<= aspnetcore-2.0"

V ASP.NET Core 2,0 nebo starších šablon projektů MVC a Razor Pages poskytují konfigurační soubor *bundleconfig. JSON* , který definuje možnosti pro jednotlivé sady prostředků:

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

V ASP.NET Core 2,1 nebo novějším přidejte nový soubor JSON s názvem *bundleconfig. JSON*do kořenového adresáře projektu MVC nebo Razor Pages. Do tohoto souboru vložte následující JSON jako výchozí bod:

::: moniker-end

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

Soubor *bundleconfig. JSON* definuje možnosti pro jednotlivé sady. V předchozím příkladu je definována jedna konfigurace sady pro vlastní soubory jazyka JavaScript (*wwwroot/js, site. js*) a StyleSheet (*wwwroot/CSS/Web. CSS*).

Mezi možnosti konfigurace patří:

* `outputFileName`: Název souboru sady pro výstup. Může obsahovat relativní cestu ze souboru *bundleconfig. JSON* . **požadovanou**
* `inputFiles`: Pole souborů, které se mají seskupit Jedná se o relativní cesty ke konfiguračnímu souboru. **volitelné**, * prázdná hodnota vede k prázdnému výstupnímu souboru. jsou podporovány vzory [expanze](https://www.tldp.org/LDP/abs/html/globbingref.html) .
* `minify`: Možnosti minifikace pro typ výstupu. **volitelné**, *výchozí – `minify: { enabled: true }`*
  * Možnosti konfigurace jsou k dispozici pro jednotlivé typy výstupních souborů.
    * [Šablony stylů CSS Minifier](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [JavaScriptový Minifier](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [Minifier HTML](https://github.com/madskristensen/BundlerMinifier/wiki)
* `includeInProject`: Příznak označující, zda se mají přidat generované soubory do souboru projektu **volitelné**, *výchozí – NEPRAVDA*
* `sourceMap`: Příznak označující, zda se má generovat zdrojová mapa pro soubor v balíčku **volitelné**, *výchozí – NEPRAVDA*
* `sourceMapRootPath`: Kořenová cesta pro uložení vygenerovaného zdrojového souboru mapování

## <a name="build-time-execution-of-bundling-and-minification"></a>Provádění sdružování a minifikace při sestavení

Balíček NuGet [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier/) umožňuje spuštění sdružování a minifikace v době sestavení. Balíček vloží [cíle MSBuild](/visualstudio/msbuild/msbuild-targets) , které běží při sestavování a čištění. Soubor *bundleconfig. JSON* se analyzuje procesem sestavení, aby vytvořil výstupní soubory založené na definované konfiguraci.

> [!NOTE]
> BuildBundlerMinifier patří do projektu založeného na komunitě na GitHubu, pro který Microsoft neposkytuje žádnou podporu. [Zde](https://github.com/madskristensen/BundlerMinifier/issues)by měly být uloženy problémy.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Přidejte do projektu balíček *BuildBundlerMinifier* .

Sestavte projekt. V okně výstup se zobrazí následující:

```console
1>------ Build started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>
1>Bundler: Begin processing bundleconfig.json
1>  Minified wwwroot/css/site.min.css
1>  Minified wwwroot/js/site.min.js
1>Bundler: Done processing bundleconfig.json
1>BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
```

Vyčistěte projekt. V okně výstup se zobrazí následující:

```console
1>------ Clean started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>
1>Bundler: Cleaning output from bundleconfig.json
1>Bundler: Done cleaning output file from bundleconfig.json
========== Clean: 1 succeeded, 0 failed, 0 skipped ==========
```

# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

Přidejte do projektu balíček *BuildBundlerMinifier* :

```dotnetcli
dotnet add package BuildBundlerMinifier
```

Pokud používáte ASP.NET Core 1. x, obnovte nově přidaný balíček:

```dotnetcli
dotnet restore
```

Sestavit projekt:

```dotnetcli
dotnet build
```

Zobrazí se následující:

```console
Microsoft (R) Build Engine version 15.4.8.50001 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.


    Bundler: Begin processing bundleconfig.json
    Bundler: Done processing bundleconfig.json
    BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
```

Vyčistit projekt:

```dotnetcli
dotnet clean
```

Zobrazí se následující výstup:

```console
Microsoft (R) Build Engine version 15.4.8.50001 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.


  Bundler: Cleaning output from bundleconfig.json
  Bundler: Done cleaning output file from bundleconfig.json
```

---

## <a name="ad-hoc-execution-of-bundling-and-minification"></a>Ad hoc provádění sdružování a minifikace

Je možné spouštět úlohy sdružování a minifikace na základě ad hoc, aniž byste museli projekt sestavovat. Přidejte do projektu balíček NuGet [BundlerMinifier. Core](https://www.nuget.org/packages/BundlerMinifier.Core/) :

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=10)]

> [!NOTE]
> BundlerMinifier. Core patří do komunitního projektu na GitHubu, pro který Microsoft neposkytuje žádnou podporu. [Zde](https://github.com/madskristensen/BundlerMinifier/issues)by měly být uloženy problémy.

Tento balíček rozšiřuje .NET Core CLI tak, aby zahrnoval nástroj *dotnet-komplet* . Následující příkaz se dá provést v okně konzoly Správce balíčků (PMC) nebo v příkazovém prostředí:

```dotnetcli
dotnet bundle
```

> [!IMPORTANT]
> Správce balíčků NuGet přidává do souboru *. csproj závislosti jako `<PackageReference />` uzly. Příkaz je zaregistrován v .NET Core CLI pouze v `<DotNetCliToolReference />` případě, že je uzel použit. `dotnet bundle` Upravte soubor *. csproj odpovídajícím způsobem.

## <a name="add-files-to-workflow"></a>Přidat soubory do pracovního postupu

Vezměte v úvahu příklad, ve kterém se přidá další *vlastní soubor. CSS* , který se podobá následujícímu:

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

Pokud chcete minimalizuje *vlastní soubor. CSS* a seskupit ho pomocí *site. CSS* do souboru *Web. min. CSS* , přidejte relativní cestu k souboru *bundleconfig. JSON*:

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> Případně můžete použít následující vzor expanze názvů:
>
> ```json
> "inputFiles": ["wwwroot/**/*(*.css|!(*.min.css))"]
> ```
>
> Tento vzor expanze názvů se shoduje se všemi soubory šablon stylů CSS a vylučuje vzor souboru minifikovaného.

Sestavte aplikaci. Otevřete *Web. min. CSS* a Všimněte si, že obsah *vlastní. CSS* je připojen na konec souboru.

## <a name="environment-based-bundling-and-minification"></a>Sdružování a minifikace založené na prostředí

Jako osvědčený postup by se měly v produkčním prostředí používat balíčky a minifikovaného soubory vaší aplikace. Během vývoje se původní soubory vytvářejí pro snazší ladění aplikace.

Určete soubory, které chcete zahrnout na vaše stránky, pomocí [pomocné rutiny tag prostředí](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) v zobrazeních. Pomocník značek prostředí vykresluje obsah pouze při spuštění v konkrétních [prostředích](xref:fundamentals/environments).

Následující `environment` značka vykresluje nezpracované soubory šablon stylů CSS při spuštění `Development` v prostředí:

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

::: moniker-end

Následující `environment` značka vykresluje soubory šablon stylů CSS a minifikovaného při spuštění v jiném prostředí než `Development`. Například spuštění v `Production` nebo `Staging` spustí vykreslování těchto šablon stylů:

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

::: moniker-end

## <a name="consume-bundleconfigjson-from-gulp"></a>Využití bundleconfig. JSON z Gulp

Existují případy, kdy pracovní postup sdružování aplikace a minifikace vyžaduje další zpracování. Mezi příklady patří Optimalizace obrázků, busting mezipaměti a zpracování prostředků CDN. Aby bylo možné tyto požadavky splnit, můžete převést pracovní postup sdružování a minifikace na použití Gulp.

### <a name="use-the-bundler--minifier-extension"></a>Použití rozšíření & Minifier rozšíření pro svazek

Rozšíření sady Visual Studio [& Minifier](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.BundlerMinifier) zpracovává převod na Gulp.

> [!NOTE]
> Rozšíření & rozšíření Minifier patří do komunitního projektu na GitHubu, pro který Microsoft neposkytuje žádnou podporu. [Zde](https://github.com/madskristensen/BundlerMinifier/issues)by měly být uloženy problémy.

V Průzkumník řešení klikněte pravým tlačítkem na soubor *bundleconfig. JSON* a vyberte možnost **& Minifier** > **převést na Gulp...** :

![Převést na položku kontextové nabídky Gulp](../client-side/bundling-and-minification/_static/convert-to-gulp.png)

Soubory *gulpfile. js* a *Package. JSON* jsou přidány do projektu. Jsou nainstalovány podpůrné balíčky [npm](https://www.npmjs.com/) uvedené v `devDependencies` oddílu souboru *Package. JSON* .

Spusťte následující příkaz v okně PMC a nainstalujte Gulp CLI jako globální závislost:

```console
npm i -g gulp-cli
```

Soubor *gulpfile. js* přečte soubor *bundleconfig. JSON* pro vstupy, výstupy a nastavení.

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-12&highlight=10)]

### <a name="convert-manually"></a>Převést ručně

Pokud není k dispozici sada Visual Studio nebo rozšíření Minifier &, proveďte převod ručně.

Do kořenového adresáře projektu přidejte soubor *Package. JSON* s následujícím `devDependencies`kódem:

> [!WARNING]
> `gulp-uglify` Modul nepodporuje ECMAScript (ES) 2015/ES6 a novější. Nainstalujte [Gulp-terser](https://www.npmjs.com/package/gulp-terser) namísto `gulp-uglify` použití ES2015/ES6 nebo novějšího.

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

Nainstalujte závislosti spuštěním následujícího příkazu na stejné úrovni jako *Package. JSON*:

```console
npm i
```

Nainstalujte rozhraní příkazového řádku Gulp jako globální závislost:

```console
npm i -g gulp-cli
```

Zkopírujte soubor *gulpfile. js* níže do kořenového adresáře projektu:

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a>Spuštění úloh Gulp

Chcete-li aktivovat úlohu Gulp minifikace před sestavením projektu v aplikaci Visual Studio, přidejte následující [cíl nástroje MSBuild](/visualstudio/msbuild/msbuild-targets) do souboru *. csproj:

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

V tomto příkladu všechny úlohy, které jsou `MyPreCompileTarget` definovány v rámci cílového prostředí, před předdefinovaným `Build` cílem. Výstup podobný následujícímu se zobrazí v okně výstupu sady Visual Studio:

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

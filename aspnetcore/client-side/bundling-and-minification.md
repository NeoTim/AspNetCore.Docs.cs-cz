---
title: Vytvoření balíčku a minifikace statické prostředky v ASP.NET Core
author: scottaddie
description: Zjistěte, jak optimalizovat statické prostředky ve webové aplikaci ASP.NET Core s použitím technik sdružování a minifikace.
ms.author: scaddie
ms.custom: mvc
ms.date: 11/20/2018
uid: client-side/bundling-and-minification
ms.openlocfilehash: 5d5f0aadb7740c9b2b959d12a585cd8c91758ce8
ms.sourcegitcommit: 4225e2c49a0081e6ac15acff673587201f54b4aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2018
ms.locfileid: "52282134"
---
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a>Vytvoření balíčku a minifikace statické prostředky v ASP.NET Core

Podle [Scott Addie](https://twitter.com/Scott_Addie) a [David borovice](https://twitter.com/davidpine7)

Tento článek vysvětluje výhody použití sdružování a minifikace, včetně použití těchto funkcí s webovými aplikacemi ASP.NET Core.

## <a name="what-is-bundling-and-minification"></a>Co je sdružování a minifikace

Sdružování a minifikace jsou dvě optimalizace odlišné výkonu, které můžete použít ve webové aplikaci. Použijí společně, sdružování a minifikace zvýšit výkon snížením počtu požadavků serveru a snížením velikosti požadovaných prostředků statické.

Sdružování a minifikace především zvýšení první čas načítání stránky požadavku. Po požádal webovou stránku v prohlížeči ukládá do mezipaměti statické prostředky (jazyk JavaScript, CSS a imagí). V důsledku toho sdružování a minifikace není výkon při vyžadování stejnou stránku nebo stránky, na stejném místě požaduje stejné prostředky. Pokud vypršení platnosti záhlaví není správně nastaven na prostředky a pokud se nepoužívá sdružování a minifikace, prohlížeče aktuálnosti heuristiky označit prostředky zastaralé po několika dnech. Kromě toho v prohlížeči vyžaduje žádost o ověření pro každý prostředek. V takovém případě sdružování a minifikace poskytují vylepšení výkonu i po prvním požadavku na stránku.

### <a name="bundling"></a>Sdružování

Sdružování kombinuje několik souborů do jediného souboru. Sdružování snižuje počet požadavků serveru, které jsou potřebné k vykreslení webové aktivem, například na webové stránce. Můžete vytvořit libovolný počet jednotlivých sad speciálně pro šablony stylů CSS, JavaScript, atd. Menší počet souborů znamená méně požadavky HTTP z prohlížeče na server nebo službu poskytující vaší aplikace. Výsledkem Vylepšený výkon načítání první stránky.

### <a name="minification"></a>Připravenost k minifikaci

Minifikace odebírá nepotřebné znaky z kódu bez změnila jejich funkce. Výsledkem je snížení významnou velikostí požadovaných prostředků (jako jsou šablony stylů CSS, obrázky a Javascriptové soubory). Běžné vedlejším účinkům připravenost k minifikaci zahrnují zkrácení názvy proměnných jeden znak a odebírání komentářů a zbytečné mezery.

Vezměte v úvahu následující funkce jazyka JavaScript:

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.js)]

Připravenost k minifikaci snižuje funkce takto:

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.min.js)]

Kromě odebrání komentáře a zbytečné mezery, byly přejmenovány následující názvy parametrů a proměnné následujícím způsobem:

Původní | Přejmenovat
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a>Dopad sdružování a minifikace

Následující tabulka popisuje rozdíly mezi jednotlivě načte prostředky a pomocí sdružování a minifikace:

Akce | S B/M | Bez B/M | Změna
--- | :---: | :---: | :---:
Požadavky na soubory  | 7   | 18     | 157%
KB přenosu | 156 | 264.68 | 70%
Čas načítání (ms) | 885 | 2360   | 167%

Prohlížeče jsou poměrně podrobné s ohledem na hlavičky požadavků HTTP. Celkový počet bajtů odeslané metrika viděli k výraznému snížení při vytváření sady. Doba načtení ukazuje výrazným vylepšením, ale v tomto příkladu byl spuštěn místně. Větší zvýšení výkonu jsou realizované při použití sdružování a minifikace s prostředky přenést přes síť.

## <a name="choose-a-bundling-and-minification-strategy"></a>Volba strategie sdružování a minifikace

Šablony projektů MVC a stránky Razor poskytují out-of-the-box řešení pro sdružování a minifikace skládající se z konfiguračního souboru JSON. Nástroje třetích stran, jako [Gulp](xref:client-side/using-gulp) a [Grunt](xref:client-side/using-grunt) spouštěčů úloh, provádět stejné úlohy s o něco složitější. Nástroj třetí strany je skvěle hodí, když pracovního postupu vývoje vyžaduje zpracování nad rámec sdružování a minifikace&mdash;jako je optimalizace linting a image. Pomocí sdružování a minifikace návrhu minifikovaný soubory vytvořené před nasazením aplikace. Sdružování a minifikace před nasazením poskytuje výhodu v podobě zatížení serveru. Ale je důležité uvědomit si tohoto návrhu sdružování a minifikace zvyšuje složitost sestavení a funguje jenom se statickými soubory.

## <a name="configure-bundling-and-minification"></a>Konfigurace sdružování a minifikace

::: moniker range="<= aspnetcore-2.0"

V technologii ASP.NET Core 2.0 nebo starší, poskytují šablon projektu MVC a Razor Pages *bundleconfig.json* konfigurační soubor, který definuje možnosti pro každou sadu:

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

V ASP.NET Core 2.1 nebo novější, přidejte nový soubor JSON s názvem *bundleconfig.json*, do kořenu projektu MVC nebo stránky Razor. Zahrnout následující kód JSON do tohoto souboru jako výchozí bod:

::: moniker-end

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

*Bundleconfig.json* soubor definuje možnosti pro každý svazek. V předchozím příkladu je definován konfigurace jedné sady pro vlastní jazyk JavaScript (*wwwroot/js/site.js*) a šablony stylů (*wwwroot/css/site.css*) soubory.

Možnosti konfigurace patří:

* `outputFileName`: Název souboru svazku do výstupu. Může obsahovat relativní cestu z *bundleconfig.json* souboru. **Vyžaduje**
* `inputFiles`: Pole souborů, které mají spojit dohromady. Jedná se o relativní cesty ke konfiguračnímu souboru. **volitelné**, * prázdnou hodnotu výsledkem prázdná výstupní soubor. [podpory zástupných znaků](http://www.tldp.org/LDP/abs/html/globbingref.html) vzory jsou podporovány.
* `minify`Možnosti výstupního typu: připravenost k minifikaci. **volitelné**, *výchozí – `minify: { enabled: true }`*
  * Možnosti konfigurace jsou k dispozici na typ výstupního souboru.
    * [Minifier šablon stylů CSS](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [Minifier jazyka JavaScript](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [HTML Minifier](https://github.com/madskristensen/BundlerMinifier/wiki)
* `includeInProject`: Příznak označující, jestli se má přidat generované soubory do souboru projektu. **volitelné**, *výchozí – false*
* `sourceMap`: Příznak označující, jestli se má generovat mapy zdrojového souboru jako součást balíčku. **volitelné**, *výchozí – false*
* `sourceMapRootPath`: Kořenová cesta pro ukládání vytvořeném zdrojovém souboru mapy.

## <a name="build-time-execution-of-bundling-and-minification"></a>Čas sestavení provádění sdružování a minifikace

[BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier/) balíček NuGet umožňuje provádění sdružování a minifikace v okamžiku sestavení. Balíček vkládá [cíle nástroje MSBuild](/visualstudio/msbuild/msbuild-targets) kterého se spouští v sestavení a vyčištění čas. *Bundleconfig.json* soubor analyzován pomocí procesu sestavení pro vytvoření výstupních souborů na základě definované konfigurace.

> [!NOTE]
> BuildBundlerMinifier patří do komunitní projekt na Githubu, pro které společnost Microsoft poskytuje bez podpory. Problémy měli archivované [tady](https://github.com/madskristensen/BundlerMinifier/issues).

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Přidat *BuildBundlerMinifier* do svého projektu balíček.

Sestavte projekt. Tímto se zobrazí v okně výstupu:

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

Vyčistěte projekt. Tímto se zobrazí v okně výstupu:

```console
1>------ Clean started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>
1>Bundler: Cleaning output from bundleconfig.json
1>Bundler: Done cleaning output file from bundleconfig.json
========== Clean: 1 succeeded, 0 failed, 0 skipped ==========
```

# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

Přidat *BuildBundlerMinifier* do svého projektu balíček:

```console
dotnet add package BuildBundlerMinifier
```

Pokud používáte ASP.NET Core 1.x, obnovit nově přidané balíček:

```console
dotnet restore
```

Sestavte projekt:

```console
dotnet build
```

Zobrazí se následující možnosti:

```console
Microsoft (R) Build Engine version 15.4.8.50001 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.


    Bundler: Begin processing bundleconfig.json
    Bundler: Done processing bundleconfig.json
    BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
```

Vyčistěte projekt:

```console
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

Je možné ke spouštění úloh sdružování a minifikace na základě ad hoc bez sestavení projektu. Přidat [BundlerMinifier.Core](https://www.nuget.org/packages/BundlerMinifier.Core/) do svého projektu balíček NuGet:

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=10)]

> [!NOTE]
> BundlerMinifier.Core patří do komunitní projekt na Githubu, pro které společnost Microsoft poskytuje bez podpory. Problémy měli archivované [tady](https://github.com/madskristensen/BundlerMinifier/issues).

Tento balíček rozšiřuje rozhraní příkazového řádku .NET Core mají zahrnout *dotnet sady* nástroj. V okně konzoly Správce balíčků (PMC) nebo v příkazovém řádku, mohou být provedeny následující příkaz:

```console
dotnet bundle
```

> [!IMPORTANT]
> Správce balíčků NuGet přidá závislosti jako soubory *.csproj `<PackageReference />` uzly. `dotnet bundle` Příkaz je registrovaný pomocí rozhraní příkazového řádku .NET Core pouze tehdy, když `<DotNetCliToolReference />` uzel se používá. Upravte soubor *.csproj odpovídajícím způsobem.

## <a name="add-files-to-workflow"></a>Přidání souborů do pracovního postupu

Vezměte v úvahu příklad, ve kterém Další *custom.css* přidá soubor podobné následující:

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

K minifikaci *custom.css* a zabalí jej *site.css* do *site.min.css* přidejte relativní cesta k *bundleconfig.json*:

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> Případně může použít následující vzor podpory zástupných znaků:
>
> ```json
> "inputFiles": ["wwwroot/**/*(*.css|!(*.min.css))"]
> ```
>
> Tento model podpory zástupných znaků vyhledá všechny soubory šablon stylů CSS a vyloučí vzor minifikovaný souboru.

Sestavení aplikace. Otevřít *site.min.css* a Všimněte si, že obsah *custom.css* je připojen na konec souboru.

## <a name="environment-based-bundling-and-minification"></a>Prostředí založené na sdružování a minifikace

Jako osvědčený postup by měla sloužit jako součást balíčku a minifikovaný soubory vaší aplikace v produkčním prostředí. Během vývoje zkontrolujte původní soubory pro snazší ladění aplikace.

Zadejte soubory, které chcete zahrnout do stránky s použitím [pomocná rutina značky prostředí](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) v zobrazení. Pomocná rutina značky prostředí pouze vykreslí obsah při spuštění v konkrétní [prostředí](xref:fundamentals/environments).

Následující `environment` značky vykreslí nezpracovaných souborů CSS, při spuštění v `Development` prostředí:

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

::: moniker-end

Následující `environment` značky vykreslí soubory šablon stylů CSS jako součást balíčku a minifikovaný při spuštění v prostředí než `Development`. Například používané `Production` nebo `Staging` aktivuje vykreslování tyto šablony stylů:

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

::: moniker-end

## <a name="consume-bundleconfigjson-from-gulp"></a>Využívání bundleconfig.json z Gulp

Existují případy, ve kterých aplikace sdružování a minifikace pracovní postup vyžaduje další zpracování. Mezi příklady patří optimalizovat image, nejnovějších mezipaměti a CDN asset zpracování. Chcete-li tyto požadavky splňují, můžete převést sdružování a minifikace pracovní postup použití nástroje Gulp.

### <a name="use-the-bundler--minifier-extension"></a>Použití rozšíření například položky Bundler & Minifier

Visual Studio [například položky Bundler & Minifier](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.BundlerMinifier) rozšíření zpracovává server převod na Gulp.

> [!NOTE]
> Rozšíření například položky Bundler & Minifier patří do komunitní projekt na Githubu, pro které společnost Microsoft poskytuje bez podpory. Problémy měli archivované [tady](https://github.com/madskristensen/BundlerMinifier/issues).

Klikněte pravým tlačítkem myši *bundleconfig.json* v Průzkumníku řešení a vyberte možnost **například položky Bundler & Minifier** > **převést na Gulp...** :

![Převést na Gulp položka kontextové nabídky](../client-side/bundling-and-minification/_static/convert-to-gulp.png)

*Gulpfile.js* a *package.json* soubory budou přidány do projektu. Podpůrné [npm](https://www.npmjs.com/) balíčky uvedené v *package.json* souboru `devDependencies` bodu jsou nainstalovány.

Spusťte následující příkaz v okně konzole PMC k instalaci rozhraní příkazového řádku Gulp jako globální závislost:

```console
npm i -g gulp-cli
```

*Gulpfile.js* čtení souborů *bundleconfig.json* soubor pro vstupy, výstupy a nastavení.

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-12&highlight=10)]

### <a name="convert-manually"></a>Převést ručně

Pokud nejsou k dispozici sady Visual Studio nebo rozšíření například položky Bundler & Minifier, převeďte ručně.

Přidat *package.json* souboru následujícím kódem `devDependencies`, do kořenového adresáře projektu:

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

Nainstalovat závislosti spuštěním následujícího příkazu na stejné úrovni jako *package.json*:

```console
npm i
```

Instalace rozhraní příkazového řádku Gulp jako globální závislost:

```console
npm i -g gulp-cli
```

Kopírovat *gulpfile.js* souboru pod do kořenového adresáře projektu:

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a>Spuštění úlohy Gulp

K aktivaci úlohy Gulp připravenost k minifikaci, než sestavení projektu v sadě Visual Studio, přidejte následující [cíl nástroje MSBuild](/visualstudio/msbuild/msbuild-targets) *.csproj souboru:

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

V tomto příkladu všechny úkoly definované v rámci `MyPreCompileTarget` cíl spuštění před předdefinovaného `Build` cíl. V okně výstupu sady Visual Studio se zobrazí výstup podobný následujícímu:

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

Visual Studio Task Runner Explorer lze také vázat úlohy Gulp k určité události aplikace Visual Studio. Zobrazit [spuštění úlohy výchozí](xref:client-side/using-gulp#running-default-tasks) pokyny týkající se toho.

## <a name="additional-resources"></a>Další zdroje

* [Použití nástroje Gulp](xref:client-side/using-gulp)
* [Použití nástroje Grunt](xref:client-side/using-grunt)
* [Používání více prostředí](xref:fundamentals/environments)
* [Pomocné rutiny značek](xref:mvc/views/tag-helpers/intro)

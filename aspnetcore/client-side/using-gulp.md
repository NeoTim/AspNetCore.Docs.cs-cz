---
title: Použití nástroje Gulp v ASP.NET Core
author: rick-anderson
description: Další informace o použití nástroje Gulp v ASP.NET Core.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 10/04/2018
uid: client-side/using-gulp
ms.openlocfilehash: 9f6d03a1e8a81bceca15cb1e1aa664c22c31e1d3
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58209869"
---
# <a name="use-gulp-in-aspnet-core"></a>Použití nástroje Gulp v ASP.NET Core

Podle [Scott Addie](https://scottaddie.com), [Shayne Boyer](https://twitter.com/spboyer), a [David borovice](https://twitter.com/davidpine7)

V typické moderní webové aplikace může proces sestavení:

* Vytvoření balíčku a minifikace souborů JavaScriptu a CSS.
* Spuštění nástrojů pro volání úlohy sdružování a minifikace před každým sestavením.
* Kompilace méně nebo SASS soubory šablon stylů CSS.
* Kompilovat soubory CoffeeScript nebo TypeScript pro JavaScript.

A *Spouštěč úloh* je nástroj, který automatizuje tyto běžné vývojářské úlohy a další. Visual Studio poskytuje integrovanou podporu pro dva spouštěčů oblíbených úloh založené na jazyce JavaScript: [Gulp](https://gulpjs.com/) a [Grunt](using-grunt.md).

## <a name="gulp"></a>Gulp

Gulp je sada založené na jazyce JavaScript streamování sestavení nástrojů pro kód na straně klienta. To se běžně používá ke streamování soubory na straně klienta pomocí několika procesů, když konkrétní událost se aktivuje v prostředí sestavení. Například Gulp slouží k automatizaci [sdružování a minifikace](bundling-and-minification.md) nebo čištění vývojového prostředí před nové sestavení.

Sada úlohy Gulp je definována v *gulpfile.js*. Následující JavaScript zahrnuje moduly Gulp a určuje cesty k souborům se nesmí odkazovat v budoucích úlohách:

```javascript
/// <binding Clean='clean' />
"use strict";

var gulp = require("gulp"),
    rimraf = require("rimraf"),
    concat = require("gulp-concat"),
    cssmin = require("gulp-cssmin"),
    uglify = require("gulp-uglify");

var paths = {
  webroot: "./wwwroot/"
};

paths.js = paths.webroot + "js/**/*.js";
paths.minJs = paths.webroot + "js/**/*.min.js";
paths.css = paths.webroot + "css/**/*.css";
paths.minCss = paths.webroot + "css/**/*.min.css";
paths.concatJsDest = paths.webroot + "js/site.min.js";
paths.concatCssDest = paths.webroot + "css/site.min.css";
```

Ve výše uvedeném kódu určuje, které moduly uzlu se vyžadují. `require` Funkce importuje každého modulu, tak, aby závislé úlohy se můžou využívat jejich funkce. Každá z importované moduly je přiřazen proměnné. Moduly lze najít tak, že název nebo cesta. V tomto příkladu s názvem moduly `gulp`, `rimraf`, `gulp-concat`, `gulp-cssmin`, a `gulp-uglify` se načítají podle názvu. Kromě toho jsou řadu cesty vytvoří, umístění souborů CSS a JavaScriptu mohli znovu použít a odkazované v rámci úlohy. Následující tabulka obsahuje popisy modulů, které jsou součástí *gulpfile.js*.

| Název modulu | Popis |
| ----------- | ----------- |
| Gulp        | Streamování Gulp sestavovací systém. Další informace najdete v tématu [gulp](https://www.npmjs.com/package/gulp). |
| rimraf      | Modul odstranění uzlu. Další informace najdete v tématu [rimraf](https://www.npmjs.com/package/rimraf). |
| gulp concat | Modul, který zřetězí soubory podle operačního systému znak nového řádku. Další informace najdete v tématu [gulp concat](https://www.npmjs.com/package/gulp-concat). |
| gulp-cssmin | Modul, který minifikuje soubory šablon stylů CSS. Další informace najdete v tématu [gulp cssmin](https://www.npmjs.com/package/gulp-cssmin). |
| gulp uglify | Modul, který minifikuje *js* soubory. Další informace najdete v tématu [gulp uglify](https://www.npmjs.com/package/gulp-uglify). |

Jakmile požadované moduly importují, je možné zadat úkoly. Zde jsou šesti úkolů zaregistrovaný, reprezentovaný následující kód:

```javascript
gulp.task("clean:js", done => rimraf(paths.concatJsDest, done));
gulp.task("clean:css", done => rimraf(paths.concatCssDest, done));
gulp.task("clean", gulp.series(["clean:js", "clean:css"]));

gulp.task("min:js", () => {
  return gulp.src([paths.js, "!" + paths.minJs], { base: "." })
    .pipe(concat(paths.concatJsDest))
    .pipe(uglify())
    .pipe(gulp.dest("."));
});

gulp.task("min:css", () => {
  return gulp.src([paths.css, "!" + paths.minCss])
    .pipe(concat(paths.concatCssDest))
    .pipe(cssmin())
    .pipe(gulp.dest("."));
});

gulp.task("min", gulp.series(["min:js", "min:css"]));

// A 'default' task is required by Gulp v4
gulp.task("default", gulp.series(["min"]));
```

---

Následující tabulka obsahuje vysvětlení úlohy určené ve výše uvedeném kódu:

|Název úlohy|Popis|
|--- |--- |
|vyčištění: js|Úloha, která používá modul odstranění uzlu rimraf minifikovaný verzi souboru site.js odebrat.|
|clean:css|Úloha, která používá modul odstranění uzlu rimraf minifikovaný verzi souboru site.css odebrat.|
|Vyčistit|Úloha, která volá `clean:js` úkolu, za nímž následuje `clean:css` úloh.|
|min:js|Úloha, která minifikuje a zřetězí všechny soubory JS v rámci složky js. . Min.js soubory jsou vyloučeny.|
|min:css|Úloha, která minifikuje a zřetězí všechny soubory CSS ve složce šablon stylů css. . Min.css soubory jsou vyloučeny.|
|min|Úloha, která volá `min:js` úkolu, za nímž následuje `min:css` úloh.|

## <a name="running-default-tasks"></a>Spuštění úlohy výchozí

Pokud jste ještě nevytvořili novou webovou aplikaci, vytvořte nový projekt webové aplikace ASP.NET v sadě Visual Studio.

1. Otevřít *package.json* souboru (Přidat ověřte, zda existuje) a přidejte následující.

    ```json
    {
      "devDependencies": {
        "gulp": "^4.0.0",
        "gulp-concat": "2.6.1",
        "gulp-cssmin": "0.2.0",
        "gulp-uglify": "3.0.0",
        "rimraf": "2.6.1"
      }
    }
    ```

2. Přidejte nový soubor JavaScript do vašeho projektu a pojmenujte ho *gulpfile.js*, zkopírujte následující kód.

    ```javascript
    /// <binding Clean='clean' />
    "use strict";

    const gulp = require("gulp"),
          rimraf = require("rimraf"),
          concat = require("gulp-concat"),
          cssmin = require("gulp-cssmin"),
          uglify = require("gulp-uglify");

    const paths = {
      webroot: "./wwwroot/"
    };

    paths.js = paths.webroot + "js/**/*.js";
    paths.minJs = paths.webroot + "js/**/*.min.js";
    paths.css = paths.webroot + "css/**/*.css";
    paths.minCss = paths.webroot + "css/**/*.min.css";
    paths.concatJsDest = paths.webroot + "js/site.min.js";
    paths.concatCssDest = paths.webroot + "css/site.min.css";

    gulp.task("clean:js", done => rimraf(paths.concatJsDest, done));
    gulp.task("clean:css", done => rimraf(paths.concatCssDest, done));
    gulp.task("clean", gulp.series(["clean:js", "clean:css"]));

    gulp.task("min:js", () => {
      return gulp.src([paths.js, "!" + paths.minJs], { base: "." })
      .pipe(concat(paths.concatJsDest))
      .pipe(uglify())
      .pipe(gulp.dest("."));
    });

    gulp.task("min:css", () => {
      return gulp.src([paths.css, "!" + paths.minCss])
      .pipe(concat(paths.concatCssDest))
      .pipe(cssmin())
      .pipe(gulp.dest("."));
    });

    gulp.task("min", gulp.series(["min:js", "min:css"]));

    // A 'default' task is required by Gulp v4
    gulp.task("default", gulp.series(["min"]));
    ```

3. V **Průzkumníka řešení**, klikněte pravým tlačítkem na *gulpfile.js*a vyberte **Task Runner Explorer**.

    ![V Průzkumníku řešení otevřete Průzkumník Spouštěče úloh](using-gulp/_static/02-SolutionExplorer-TaskRunnerExplorer.png)

    **Task Runner Explorer** zobrazuje seznam Gulp úlohy. (Možná budete muset kliknout **aktualizovat** tlačítko, které se zobrazí nalevo od názvu projektu.)

    ![Task Runner Explorer](using-gulp/_static/03-TaskRunnerExplorer.png)

    > [!IMPORTANT]
    > **Task Runner Explorer** položka kontextové nabídky se zobrazí pouze v případě *gulpfile.js* je v kořenovém adresáři projektu.

4. Pod tím **úlohy** v **Task Runner Explorer**, klikněte pravým tlačítkem na **čisté**a vyberte **spustit** v místní nabídce.

    ![Úkolu vyčisti Průzkumník Spouštěče úloh](using-gulp/_static/04-TaskRunner-clean.png)

    **Task Runner Explorer** se vytvoří nová karta s názvem **čisté** a provedení čisté úkolu, jak jsou definovány v *gulpfile.js*.

5. Klikněte pravým tlačítkem myši **čisté** úloh a pak vyberte **vazby** > **před sestavení**.

    ![Task Runner Explorer vazby BeforeBuild](using-gulp/_static/05-TaskRunner-BeforeBuild.png)

    **Před sestavení** vazby nakonfiguruje úkolu vyčisti pro automatické spuštění před každým sestavením projektu.

Tyto vazby se nastavuje pomocí **Task Runner Explorer** jsou uložené ve formě komentářů v horní části vaší *gulpfile.js* a platí jenom v sadě Visual Studio. Alternativu, která nevyžaduje, aby Visual Studio se konfigurace automatického spouštění úkolů gulp ve vašich *.csproj* souboru. Například jako umístění vašeho *.csproj* souboru:

```xml
<Target Name="MyPreCompileTarget" BeforeTargets="Build">
  <Exec Command="gulp clean" />
</Target>
```

Nyní úkolu vyčisti se spustí při spuštění projektu v sadě Visual Studio nebo z příkazového řádku pomocí [dotnet spustit](/dotnet/core/tools/dotnet-run) příkazu (Spustit `npm install` první).

## <a name="defining-and-running-a-new-task"></a>Definování a spouštění nového úkolu

Chcete-li definovat nové úlohy Gulp, upravte *gulpfile.js*.

1. Následující jazyka JavaScript přidejte na konec *gulpfile.js*:

    ```javascript
    gulp.task('first', done => {
      console.log('first task! <-----');
      done(); // signal completion
    });
    ```

    Tento úkol má název `first`, a jednoduše zobrazí řetězec.

2. Uložit *gulpfile.js*.

3. V **Průzkumníka řešení**, klikněte pravým tlačítkem na *gulpfile.js*a vyberte *Task Runner Explorer*.

4. V **Task Runner Explorer**, klikněte pravým tlačítkem na **první**a vyberte **spustit**.

    ![Task Runner Explorer spusťte první úlohu](using-gulp/_static/06-TaskRunner-First.png)

    Zobrazí se text výstupu. Příklady založené na běžných scénářů, najdete v sekci [Gulp recepty](#gulp-recipes).

## <a name="defining-and-running-tasks-in-a-series"></a>Definování a spouštění úloh v řadě

Při spuštění více úloh, úkolů současně ve výchozím nastavení spouští. Ale pokud potřebujete ke spouštění úloh v určitém pořadí, musíte zadat každý úkol po dokončení, stejně jako úkoly, které závisí na dokončení jiného úkolu.

1. Chcete-li definovat řadu úkolů ke spuštění v pořadí, nahraďte `first` úkol, který jste přidali výše v *gulpfile.js* následujícím kódem:

    ```javascript
    gulp.task('series:first', done => {
      console.log('first task! <-----');
      done(); // signal completion
    });
    gulp.task('series:second', done => {
      console.log('second task! <-----');
        done(); // signal completion
    });

    gulp.task('series', gulp.series(['series:first', 'series:second']), () => { });

    // A 'default' task is required by Gulp v4
      gulp.task('default', gulp.series('series'));
    ```

    Teď máte tři úkoly: `series:first`, `series:second`, a `series`. `series:second` Úkol obsahuje druhý parametr, který určuje pole úkolů ke spuštění a dokončení před `series:second` bude úloha spuštěna. Jak je uvedeno v kódu výše, pouze `series:first` úkolu musí splnit, aby `series:second` bude úloha spuštěna.

2. Uložit *gulpfile.js*.

3. V **Průzkumníka řešení**, klikněte pravým tlačítkem na *gulpfile.js* a vyberte **Task Runner Explorer** Pokud ještě není otevřeno.

4. V **Task Runner Explorer**, klikněte pravým tlačítkem na **řady** a vyberte **spustit**.

    ![Task Runner Explorer spusťte úlohu řady](using-gulp/_static/07-TaskRunner-Series.png)

## <a name="intellisense"></a>IntelliSense

Technologie IntelliSense poskytuje doplňování kódu, popisy parametrů a další funkce pro zvýšení produktivity a snížení chyby. Úlohy gulp jsou napsané v jazyce JavaScript; technologie IntelliSense může proto poskytnout pomoc při vývoji. Při práci s použitím jazyka JavaScript, IntelliSense vypíše objekty, funkce, vlastnosti a parametry, které jsou k dispozici podle aktuálního kontextu. Vyberte možnost kódování a ze seznamu místní nabídky technologie IntelliSense pro dokončení kódu.

![gulp technologie IntelliSense](using-gulp/_static/08-IntelliSense.png)

Další informace o technologii IntelliSense, najdete v části [technologie IntelliSense jazyka JavaScript](/visualstudio/ide/javascript-intellisense).

## <a name="development-staging-and-production-environments"></a>Vývoj, přípravného a produkčního prostředí

Při použití nástroje Gulp optimalizovat soubory na straně klienta pro pracovní a provozní zpracované soubory se uloží do místního umístění pracovního a produkčního prostředí. *_Layout.cshtml* souboru používá **prostředí** označit pomocné rutiny poskytnout dvě různé verze souborů šablon stylů CSS. Jedna verze souborů šablon stylů CSS je pro vývoj a jiné verze je optimalizovaná pro přípravným a produkčním prostředím. V sadě Visual Studio 2017, když změníte **ASPNETCORE_ENVIRONMENT** proměnnou prostředí, aby `Production`, Visual Studio vytvoří webovou aplikaci a odkaz na minimalizované soubory šablon stylů CSS. Následující kód ukazuje **prostředí** obsahující značky odkazu pro pomocné rutiny značky `Development` šablon stylů CSS souborů a minifikovaný `Staging, Production` soubory šablon stylů CSS.

```html
<environment names="Development">
    <script src="~/lib/jquery/dist/jquery.js"></script>
    <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
    <script src="~/js/site.js" asp-append-version="true"></script>
</environment>
<environment names="Staging,Production">
    <script src="https://ajax.aspnetcdn.com/ajax/jquery/jquery-2.2.0.min.js"
            asp-fallback-src="~/lib/jquery/dist/jquery.min.js"
            asp-fallback-test="window.jQuery"
            crossorigin="anonymous"
            integrity="sha384-K+ctZQ+LL8q6tP7I94W+qzQsfRV2a+AfHIi9k8z8l9ggpc8X+Ytst4yBo/hH+8Fk">
    </script>
    <script src="https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.7/bootstrap.min.js"
            asp-fallback-src="~/lib/bootstrap/dist/js/bootstrap.min.js"
            asp-fallback-test="window.jQuery && window.jQuery.fn && window.jQuery.fn.modal"
            crossorigin="anonymous"
            integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa">
    </script>
    <script src="~/js/site.min.js" asp-append-version="true"></script>
</environment>
```

## <a name="switching-between-environments"></a>Přepínání mezi prostředími

Chcete-li přepnout mezi kompilace pro různá prostředí, upravte **ASPNETCORE_ENVIRONMENT** hodnoty proměnné prostředí.

1. V **Task Runner Explorer**, ověřte, že **min** úloha byla nastavena pro spuštění **před sestavení**.

2. V **Průzkumníka řešení**, klikněte pravým tlačítkem na název projektu a vyberte **vlastnosti**.

    Zobrazí se seznam vlastností pro webové aplikace.

3. Klikněte na tlačítko **ladění** kartu.

4. Nastavte hodnotu **Hosting: prostředí** proměnnou prostředí, aby `Production`.

5. Stisknutím klávesy **F5** ke spuštění aplikace v prohlížeči.

6. V okně prohlížeče, klikněte pravým tlačítkem na stránku a vybrat **zobrazit zdroj** Chcete-li zobrazit kód HTML stránky.

    Všimněte si, že šablona stylů odkazy odkazují na minifikovaný soubory šablon stylů CSS.

7. Zavřete prohlížeč a zastavte tak webové aplikace.

8. V sadě Visual Studio, vraťte se do seznamu vlastností pro webové aplikace a změňte **Hosting: prostředí** proměnnou prostředí zpět do `Development`.

9. Stisknutím klávesy **F5** a znovu spusťte aplikaci v prohlížeči.

10. V okně prohlížeče, klikněte pravým tlačítkem na stránku a vybrat **zobrazit zdroj** zobrazíte kód HTML stránky.

    Všimněte si, že šablona stylů odkazují na nezmenšenou verze souborů šablon stylů CSS.

Další informace týkající se prostředí v ASP.NET Core najdete v tématu [používání více prostředí](../fundamentals/environments.md).

## <a name="task-and-module-details"></a>Podrobnosti úlohy a modul

Úlohy Gulp je registrovaná pro název funkce. Závislosti můžete zadat, pokud jiné úlohy musí být spuštěny před aktuální úlohu. Další funkce umožňují spouštět a sledovat úlohy Gulp, také nastavit zdroj (*src*) a cíl (*dest*) souborů právě upravuje. Toto jsou primární funkce Gulp rozhraní API:

|Gulp – funkce|Syntaxe|Popis|
|---   |--- |--- |
|– úloha  |`gulp.task(name[, deps], fn) { }`|`task` Funkce vytvoří úkol. `name` Parametr definuje název úkolu. `deps` Parametr obsahuje celou řadu úloh dokončit dříve, než se tato úloha spuštěna. `fn` Parametr představuje funkci zpětného volání, které provádí operace úlohy.|
|Sledování |`gulp.watch(glob [, opts], tasks) { }`|`watch` Funkce monitoruje soubory a spuštění úlohy, když dojde ke změně souboru. `glob` Parametr je `string` nebo `array` , který určuje, které soubory Pokud chcete sledovat. `opts` Parametr poskytuje další možnosti sledování souboru.|
|src   |`gulp.src(globs[, options]) { }`|`src` Soubory, které odpovídají glob hodnoty, které poskytuje funkce. `glob` Parametr je `string` nebo `array` , který určuje, které soubory číst. `options` Parametr poskytuje možnosti pro další soubor.|
|dest  |`gulp.dest(path[, options]) { }`|`dest` Funkce definuje umístění, ke kterému lze zapisovat soubory. `path` Parametr je řetězec nebo funkci, která určuje cílovou složku. `options` Parametru je objekt, který určuje možnosti výstupní složky.|

Další referenční informace Gulp rozhraní API najdete v tématu [Gulp dokumentace API](https://gulpjs.org/API.html).

## <a name="gulp-recipes"></a>Recepty gulp

Komunita Gulp poskytuje Gulp [recepty](https://github.com/gulpjs/gulp/blob/master/docs/recipes/README.md). Tyto recepty se skládají z úlohy Gulp vztahují k běžným scénářům.

## <a name="additional-resources"></a>Další zdroje

* [Dokumentace ke službě gulp](https://github.com/gulpjs/gulp/blob/master/docs/README.md)
* [Sdružování a minifikace v ASP.NET Core](bundling-and-minification.md)
* [Použití nástroje Grunt v ASP.NET Core](using-grunt.md)

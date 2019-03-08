---
title: Použití nástroje Gulp v ASP.NET Core
author: rick-anderson
description: Další informace o použití nástroje Gulp v ASP.NET Core.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 10/04/2018
uid: client-side/using-gulp
ms.openlocfilehash: 43277dc5910971374187f49031e74769c9e29e1f
ms.sourcegitcommit: 191d21c1e37b56f0df0187e795d9a56388bbf4c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2019
ms.locfileid: "57665623"
---
# <a name="use-gulp-in-aspnet-core"></a><span data-ttu-id="94ef3-103">Použití nástroje Gulp v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="94ef3-103">Use Gulp in ASP.NET Core</span></span>

<span data-ttu-id="94ef3-104">Podle [Scott Addie](https://scottaddie.com), [Shayne Boyer](https://twitter.com/spboyer), a [David borovice](https://twitter.com/davidpine7)</span><span class="sxs-lookup"><span data-stu-id="94ef3-104">By [Scott Addie](https://scottaddie.com), [Shayne Boyer](https://twitter.com/spboyer), and [David Pine](https://twitter.com/davidpine7)</span></span>

<span data-ttu-id="94ef3-105">V typické moderní webové aplikace může proces sestavení:</span><span class="sxs-lookup"><span data-stu-id="94ef3-105">In a typical modern web app, the build process might:</span></span>

* <span data-ttu-id="94ef3-106">Vytvoření balíčku a minifikace souborů JavaScriptu a CSS.</span><span class="sxs-lookup"><span data-stu-id="94ef3-106">Bundle and minify JavaScript and CSS files.</span></span>
* <span data-ttu-id="94ef3-107">Spuštění nástrojů pro volání úlohy sdružování a minifikace před každým sestavením.</span><span class="sxs-lookup"><span data-stu-id="94ef3-107">Run tools to call the bundling and minification tasks before each build.</span></span>
* <span data-ttu-id="94ef3-108">Kompilace méně nebo SASS soubory šablon stylů CSS.</span><span class="sxs-lookup"><span data-stu-id="94ef3-108">Compile LESS or SASS files to CSS.</span></span>
* <span data-ttu-id="94ef3-109">Kompilovat soubory CoffeeScript nebo TypeScript pro JavaScript.</span><span class="sxs-lookup"><span data-stu-id="94ef3-109">Compile CoffeeScript or TypeScript files to JavaScript.</span></span>

<span data-ttu-id="94ef3-110">A *Spouštěč úloh* je nástroj, který automatizuje tyto běžné vývojářské úlohy a další.</span><span class="sxs-lookup"><span data-stu-id="94ef3-110">A *task runner* is a tool which automates these routine development tasks and more.</span></span> <span data-ttu-id="94ef3-111">Visual Studio poskytuje integrovanou podporu pro dva spouštěčů oblíbených úloh založené na jazyce JavaScript: [Gulp](https://gulpjs.com/) a [Grunt](using-grunt.md).</span><span class="sxs-lookup"><span data-stu-id="94ef3-111">Visual Studio provides built-in support for two popular JavaScript-based task runners: [Gulp](https://gulpjs.com/) and [Grunt](using-grunt.md).</span></span>

## <a name="gulp"></a><span data-ttu-id="94ef3-112">Gulp</span><span class="sxs-lookup"><span data-stu-id="94ef3-112">Gulp</span></span>

<span data-ttu-id="94ef3-113">Gulp je sada založené na jazyce JavaScript streamování sestavení nástrojů pro kód na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="94ef3-113">Gulp is a JavaScript-based streaming build toolkit for client-side code.</span></span> <span data-ttu-id="94ef3-114">To se běžně používá ke streamování soubory na straně klienta pomocí několika procesů, když konkrétní událost se aktivuje v prostředí sestavení.</span><span class="sxs-lookup"><span data-stu-id="94ef3-114">It's commonly used to stream client-side files through a series of processes when a specific event is triggered in a build environment.</span></span> <span data-ttu-id="94ef3-115">Například Gulp slouží k automatizaci [sdružování a minifikace](bundling-and-minification.md) nebo čištění vývojového prostředí před nové sestavení.</span><span class="sxs-lookup"><span data-stu-id="94ef3-115">For instance, Gulp can be used to automate [bundling and minification](bundling-and-minification.md) or the cleaning of a development environment before a new build.</span></span>

<span data-ttu-id="94ef3-116">Sada úlohy Gulp je definována v *gulpfile.js*.</span><span class="sxs-lookup"><span data-stu-id="94ef3-116">A set of Gulp tasks is defined in *gulpfile.js*.</span></span> <span data-ttu-id="94ef3-117">Následující JavaScript zahrnuje moduly Gulp a určuje cesty k souborům se nesmí odkazovat v budoucích úlohách:</span><span class="sxs-lookup"><span data-stu-id="94ef3-117">The following JavaScript includes Gulp modules and specifies file paths to be referenced within the forthcoming tasks:</span></span>

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

<span data-ttu-id="94ef3-118">Ve výše uvedeném kódu určuje, které moduly uzlu se vyžadují.</span><span class="sxs-lookup"><span data-stu-id="94ef3-118">The above code specifies which Node modules are required.</span></span> <span data-ttu-id="94ef3-119">`require` Funkce importuje každého modulu, tak, aby závislé úlohy se můžou využívat jejich funkce.</span><span class="sxs-lookup"><span data-stu-id="94ef3-119">The `require` function imports each module so that the dependent tasks can utilize their features.</span></span> <span data-ttu-id="94ef3-120">Každá z importované moduly je přiřazen proměnné.</span><span class="sxs-lookup"><span data-stu-id="94ef3-120">Each of the imported modules is assigned to a variable.</span></span> <span data-ttu-id="94ef3-121">Moduly lze najít tak, že název nebo cesta.</span><span class="sxs-lookup"><span data-stu-id="94ef3-121">The modules can be located either by name or path.</span></span> <span data-ttu-id="94ef3-122">V tomto příkladu s názvem moduly `gulp`, `rimraf`, `gulp-concat`, `gulp-cssmin`, a `gulp-uglify` se načítají podle názvu.</span><span class="sxs-lookup"><span data-stu-id="94ef3-122">In this example, the modules named `gulp`, `rimraf`, `gulp-concat`, `gulp-cssmin`, and `gulp-uglify` are retrieved by name.</span></span> <span data-ttu-id="94ef3-123">Kromě toho jsou řadu cesty vytvoří, umístění souborů CSS a JavaScriptu mohli znovu použít a odkazované v rámci úlohy.</span><span class="sxs-lookup"><span data-stu-id="94ef3-123">Additionally, a series of paths are created so that the locations of CSS and JavaScript files can be reused and referenced within the tasks.</span></span> <span data-ttu-id="94ef3-124">Následující tabulka obsahuje popisy modulů, které jsou součástí *gulpfile.js*.</span><span class="sxs-lookup"><span data-stu-id="94ef3-124">The following table provides descriptions of the modules included in *gulpfile.js*.</span></span>

| <span data-ttu-id="94ef3-125">Název modulu</span><span class="sxs-lookup"><span data-stu-id="94ef3-125">Module Name</span></span> | <span data-ttu-id="94ef3-126">Popis</span><span class="sxs-lookup"><span data-stu-id="94ef3-126">Description</span></span> |
| ----------- | ----------- |
| <span data-ttu-id="94ef3-127">Gulp</span><span class="sxs-lookup"><span data-stu-id="94ef3-127">gulp</span></span>        | <span data-ttu-id="94ef3-128">Streamování Gulp sestavovací systém.</span><span class="sxs-lookup"><span data-stu-id="94ef3-128">The Gulp streaming build system.</span></span> <span data-ttu-id="94ef3-129">Další informace najdete v tématu [gulp](https://www.npmjs.com/package/gulp).</span><span class="sxs-lookup"><span data-stu-id="94ef3-129">For more information, see [gulp](https://www.npmjs.com/package/gulp).</span></span> |
| <span data-ttu-id="94ef3-130">rimraf</span><span class="sxs-lookup"><span data-stu-id="94ef3-130">rimraf</span></span>      | <span data-ttu-id="94ef3-131">Modul odstranění uzlu.</span><span class="sxs-lookup"><span data-stu-id="94ef3-131">A Node deletion module.</span></span> <span data-ttu-id="94ef3-132">Další informace najdete v tématu [rimraf](https://www.npmjs.com/package/rimraf).</span><span class="sxs-lookup"><span data-stu-id="94ef3-132">For more information, see [rimraf](https://www.npmjs.com/package/rimraf).</span></span> |
| <span data-ttu-id="94ef3-133">gulp concat</span><span class="sxs-lookup"><span data-stu-id="94ef3-133">gulp-concat</span></span> | <span data-ttu-id="94ef3-134">Modul, který zřetězí soubory podle operačního systému znak nového řádku.</span><span class="sxs-lookup"><span data-stu-id="94ef3-134">A module that concatenates files based on the operating system's newline character.</span></span> <span data-ttu-id="94ef3-135">Další informace najdete v tématu [gulp concat](https://www.npmjs.com/package/gulp-concat).</span><span class="sxs-lookup"><span data-stu-id="94ef3-135">For more information, see [gulp-concat](https://www.npmjs.com/package/gulp-concat).</span></span> |
| <span data-ttu-id="94ef3-136">gulp-cssmin</span><span class="sxs-lookup"><span data-stu-id="94ef3-136">gulp-cssmin</span></span> | <span data-ttu-id="94ef3-137">Modul, který minifikuje soubory šablon stylů CSS.</span><span class="sxs-lookup"><span data-stu-id="94ef3-137">A module that minifies CSS files.</span></span> <span data-ttu-id="94ef3-138">Další informace najdete v tématu [gulp cssmin](https://www.npmjs.com/package/gulp-cssmin).</span><span class="sxs-lookup"><span data-stu-id="94ef3-138">For more information, see [gulp-cssmin](https://www.npmjs.com/package/gulp-cssmin).</span></span> |
| <span data-ttu-id="94ef3-139">gulp uglify</span><span class="sxs-lookup"><span data-stu-id="94ef3-139">gulp-uglify</span></span> | <span data-ttu-id="94ef3-140">Modul, který minifikuje *js* soubory.</span><span class="sxs-lookup"><span data-stu-id="94ef3-140">A module that minifies *.js* files.</span></span> <span data-ttu-id="94ef3-141">Další informace najdete v tématu [gulp uglify](https://www.npmjs.com/package/gulp-uglify).</span><span class="sxs-lookup"><span data-stu-id="94ef3-141">For more information, see [gulp-uglify](https://www.npmjs.com/package/gulp-uglify).</span></span> |

<span data-ttu-id="94ef3-142">Jakmile požadované moduly importují, je možné zadat úkoly.</span><span class="sxs-lookup"><span data-stu-id="94ef3-142">Once the requisite modules are imported, the tasks can be specified.</span></span> <span data-ttu-id="94ef3-143">Zde jsou šesti úkolů zaregistrovaný, reprezentovaný následující kód:</span><span class="sxs-lookup"><span data-stu-id="94ef3-143">Here there are six tasks registered, represented by the following code:</span></span>

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

<span data-ttu-id="94ef3-144">Následující tabulka obsahuje vysvětlení úlohy určené ve výše uvedeném kódu:</span><span class="sxs-lookup"><span data-stu-id="94ef3-144">The following table provides an explanation of the tasks specified in the code above:</span></span>

|<span data-ttu-id="94ef3-145">Název úlohy</span><span class="sxs-lookup"><span data-stu-id="94ef3-145">Task Name</span></span>|<span data-ttu-id="94ef3-146">Popis</span><span class="sxs-lookup"><span data-stu-id="94ef3-146">Description</span></span>|
|--- |--- |
|<span data-ttu-id="94ef3-147">vyčištění: js</span><span class="sxs-lookup"><span data-stu-id="94ef3-147">clean:js</span></span>|<span data-ttu-id="94ef3-148">Úloha, která používá modul odstranění uzlu rimraf minifikovaný verzi souboru site.js odebrat.</span><span class="sxs-lookup"><span data-stu-id="94ef3-148">A task that uses the rimraf Node deletion module to remove the minified version of the site.js file.</span></span>|
|<span data-ttu-id="94ef3-149">clean:css</span><span class="sxs-lookup"><span data-stu-id="94ef3-149">clean:css</span></span>|<span data-ttu-id="94ef3-150">Úloha, která používá modul odstranění uzlu rimraf minifikovaný verzi souboru site.css odebrat.</span><span class="sxs-lookup"><span data-stu-id="94ef3-150">A task that uses the rimraf Node deletion module to remove the minified version of the site.css file.</span></span>|
|<span data-ttu-id="94ef3-151">Vyčistit</span><span class="sxs-lookup"><span data-stu-id="94ef3-151">clean</span></span>|<span data-ttu-id="94ef3-152">Úloha, která volá `clean:js` úkolu, za nímž následuje `clean:css` úloh.</span><span class="sxs-lookup"><span data-stu-id="94ef3-152">A task that calls the `clean:js` task, followed by the `clean:css` task.</span></span>|
|<span data-ttu-id="94ef3-153">min:js</span><span class="sxs-lookup"><span data-stu-id="94ef3-153">min:js</span></span>|<span data-ttu-id="94ef3-154">Úloha, která minifikuje a zřetězí všechny soubory JS v rámci složky js.</span><span class="sxs-lookup"><span data-stu-id="94ef3-154">A task that minifies and concatenates all .js files within the js folder.</span></span> <span data-ttu-id="94ef3-155">. Min.js soubory jsou vyloučeny.</span><span class="sxs-lookup"><span data-stu-id="94ef3-155">The .min.js files are excluded.</span></span>|
|<span data-ttu-id="94ef3-156">min:css</span><span class="sxs-lookup"><span data-stu-id="94ef3-156">min:css</span></span>|<span data-ttu-id="94ef3-157">Úloha, která minifikuje a zřetězí všechny soubory CSS ve složce šablon stylů css.</span><span class="sxs-lookup"><span data-stu-id="94ef3-157">A task that minifies and concatenates all .css files within the css folder.</span></span> <span data-ttu-id="94ef3-158">. Min.css soubory jsou vyloučeny.</span><span class="sxs-lookup"><span data-stu-id="94ef3-158">The .min.css files are excluded.</span></span>|
|<span data-ttu-id="94ef3-159">min</span><span class="sxs-lookup"><span data-stu-id="94ef3-159">min</span></span>|<span data-ttu-id="94ef3-160">Úloha, která volá `min:js` úkolu, za nímž následuje `min:css` úloh.</span><span class="sxs-lookup"><span data-stu-id="94ef3-160">A task that calls the `min:js` task, followed by the `min:css` task.</span></span>|

## <a name="running-default-tasks"></a><span data-ttu-id="94ef3-161">Spuštění úlohy výchozí</span><span class="sxs-lookup"><span data-stu-id="94ef3-161">Running default tasks</span></span>

<span data-ttu-id="94ef3-162">Pokud jste ještě nevytvořili novou webovou aplikaci, vytvořte nový projekt webové aplikace ASP.NET v sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="94ef3-162">If you haven't already created a new Web app, create a new ASP.NET Web Application project in Visual Studio.</span></span>

1.  <span data-ttu-id="94ef3-163">Otevřít *package.json* souboru (Přidat ověřte, zda existuje) a přidejte následující.</span><span class="sxs-lookup"><span data-stu-id="94ef3-163">Open the *package.json* file (add if not there) and add the following.</span></span>

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

2.  <span data-ttu-id="94ef3-164">Přidejte nový soubor JavaScript do vašeho projektu a pojmenujte ho *gulpfile.js*, zkopírujte následující kód.</span><span class="sxs-lookup"><span data-stu-id="94ef3-164">Add a new JavaScript file to your project and name it *gulpfile.js*, then copy the following code.</span></span>

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

3.  <span data-ttu-id="94ef3-165">V **Průzkumníka řešení**, klikněte pravým tlačítkem na *gulpfile.js*a vyberte **Task Runner Explorer**.</span><span class="sxs-lookup"><span data-stu-id="94ef3-165">In **Solution Explorer**, right-click *gulpfile.js*, and select **Task Runner Explorer**.</span></span>
    
    ![V Průzkumníku řešení otevřete Průzkumník Spouštěče úloh](using-gulp/_static/02-SolutionExplorer-TaskRunnerExplorer.png)
    
    <span data-ttu-id="94ef3-167">**Task Runner Explorer** zobrazuje seznam Gulp úlohy.</span><span class="sxs-lookup"><span data-stu-id="94ef3-167">**Task Runner Explorer** shows the list of Gulp tasks.</span></span> <span data-ttu-id="94ef3-168">(Možná budete muset kliknout **aktualizovat** tlačítko, které se zobrazí nalevo od názvu projektu.)</span><span class="sxs-lookup"><span data-stu-id="94ef3-168">(You might have to click the **Refresh** button that appears to the left of the project name.)</span></span>
    
    ![Task Runner Explorer](using-gulp/_static/03-TaskRunnerExplorer.png)
    
    > [!IMPORTANT]
    > <span data-ttu-id="94ef3-170">**Task Runner Explorer** položka kontextové nabídky se zobrazí pouze v případě *gulpfile.js* je v kořenovém adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="94ef3-170">The **Task Runner Explorer** context menu item appears only if *gulpfile.js* is in the root project directory.</span></span>

4.  <span data-ttu-id="94ef3-171">Pod tím **úlohy** v **Task Runner Explorer**, klikněte pravým tlačítkem na **čisté**a vyberte **spustit** v místní nabídce.</span><span class="sxs-lookup"><span data-stu-id="94ef3-171">Underneath **Tasks** in **Task Runner Explorer**, right-click **clean**, and select **Run** from the pop-up menu.</span></span>

    ![Úkolu vyčisti Průzkumník Spouštěče úloh](using-gulp/_static/04-TaskRunner-clean.png)

    <span data-ttu-id="94ef3-173">**Task Runner Explorer** se vytvoří nová karta s názvem **čisté** a provedení čisté úkolu, jak jsou definovány v *gulpfile.js*.</span><span class="sxs-lookup"><span data-stu-id="94ef3-173">**Task Runner Explorer** will create a new tab named **clean** and execute the clean task as it's defined in *gulpfile.js*.</span></span>

5.  <span data-ttu-id="94ef3-174">Klikněte pravým tlačítkem myši **čisté** úloh a pak vyberte **vazby** > **před sestavení**.</span><span class="sxs-lookup"><span data-stu-id="94ef3-174">Right-click the **clean** task, then select **Bindings** > **Before Build**.</span></span>

    ![Task Runner Explorer vazby BeforeBuild](using-gulp/_static/05-TaskRunner-BeforeBuild.png)

    <span data-ttu-id="94ef3-176">**Před sestavení** vazby nakonfiguruje úkolu vyčisti pro automatické spuštění před každým sestavením projektu.</span><span class="sxs-lookup"><span data-stu-id="94ef3-176">The **Before Build** binding configures the clean task to run automatically before each build of the project.</span></span>

<span data-ttu-id="94ef3-177">Tyto vazby se nastavuje pomocí **Task Runner Explorer** jsou uložené ve formě komentářů v horní části vaší *gulpfile.js* a platí jenom v sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="94ef3-177">The bindings you set up with **Task Runner Explorer** are stored in the form of a comment at the top of your *gulpfile.js* and are effective only in Visual Studio.</span></span> <span data-ttu-id="94ef3-178">Alternativu, která nevyžaduje, aby Visual Studio se konfigurace automatického spouštění úkolů gulp ve vašich *.csproj* souboru.</span><span class="sxs-lookup"><span data-stu-id="94ef3-178">An alternative that doesn't require Visual Studio is to configure automatic execution of gulp tasks in your *.csproj* file.</span></span> <span data-ttu-id="94ef3-179">Například jako umístění vašeho *.csproj* souboru:</span><span class="sxs-lookup"><span data-stu-id="94ef3-179">For example, put this in your *.csproj* file:</span></span>

```xml
<Target Name="MyPreCompileTarget" BeforeTargets="Build">
  <Exec Command="gulp clean" />
</Target>
```

<span data-ttu-id="94ef3-180">Nyní úkolu vyčisti se spustí při spuštění projektu v sadě Visual Studio nebo z příkazového řádku pomocí [dotnet spustit](/dotnet/core/tools/dotnet-run) příkazu (Spustit `npm install` první).</span><span class="sxs-lookup"><span data-stu-id="94ef3-180">Now the clean task is executed when you run the project in Visual Studio or from a command prompt using the [dotnet run](/dotnet/core/tools/dotnet-run) command (run `npm install` first).</span></span>

## <a name="defining-and-running-a-new-task"></a><span data-ttu-id="94ef3-181">Definování a spouštění nového úkolu</span><span class="sxs-lookup"><span data-stu-id="94ef3-181">Defining and running a new task</span></span>

<span data-ttu-id="94ef3-182">Chcete-li definovat nové úlohy Gulp, upravte *gulpfile.js*.</span><span class="sxs-lookup"><span data-stu-id="94ef3-182">To define a new Gulp task, modify *gulpfile.js*.</span></span>

1.  <span data-ttu-id="94ef3-183">Následující jazyka JavaScript přidejte na konec *gulpfile.js*:</span><span class="sxs-lookup"><span data-stu-id="94ef3-183">Add the following JavaScript to the end of *gulpfile.js*:</span></span>

    ```javascript
    gulp.task('first', done => {
      console.log('first task! <-----');
      done(); // signal completion
    });
    ```

    <span data-ttu-id="94ef3-184">Tento úkol má název `first`, a jednoduše zobrazí řetězec.</span><span class="sxs-lookup"><span data-stu-id="94ef3-184">This task is named `first`, and it simply displays a string.</span></span>

2.  <span data-ttu-id="94ef3-185">Uložit *gulpfile.js*.</span><span class="sxs-lookup"><span data-stu-id="94ef3-185">Save *gulpfile.js*.</span></span>

3.  <span data-ttu-id="94ef3-186">V **Průzkumníka řešení**, klikněte pravým tlačítkem na *gulpfile.js*a vyberte *Task Runner Explorer*.</span><span class="sxs-lookup"><span data-stu-id="94ef3-186">In **Solution Explorer**, right-click *gulpfile.js*, and select *Task Runner Explorer*.</span></span>

4.  <span data-ttu-id="94ef3-187">V **Task Runner Explorer**, klikněte pravým tlačítkem na **první**a vyberte **spustit**.</span><span class="sxs-lookup"><span data-stu-id="94ef3-187">In **Task Runner Explorer**, right-click **first**, and select **Run**.</span></span>

    ![Task Runner Explorer spusťte první úlohu](using-gulp/_static/06-TaskRunner-First.png)

    <span data-ttu-id="94ef3-189">Zobrazí se text výstupu.</span><span class="sxs-lookup"><span data-stu-id="94ef3-189">The output text is displayed.</span></span> <span data-ttu-id="94ef3-190">Příklady založené na běžných scénářů, najdete v sekci [Gulp recepty](#gulp-recipes).</span><span class="sxs-lookup"><span data-stu-id="94ef3-190">To see examples based on common scenarios, see [Gulp Recipes](#gulp-recipes).</span></span>

## <a name="defining-and-running-tasks-in-a-series"></a><span data-ttu-id="94ef3-191">Definování a spouštění úloh v řadě</span><span class="sxs-lookup"><span data-stu-id="94ef3-191">Defining and running tasks in a series</span></span>

<span data-ttu-id="94ef3-192">Při spuštění více úloh, úkolů současně ve výchozím nastavení spouští.</span><span class="sxs-lookup"><span data-stu-id="94ef3-192">When you run multiple tasks, the tasks run concurrently by default.</span></span> <span data-ttu-id="94ef3-193">Ale pokud potřebujete ke spouštění úloh v určitém pořadí, musíte zadat každý úkol po dokončení, stejně jako úkoly, které závisí na dokončení jiného úkolu.</span><span class="sxs-lookup"><span data-stu-id="94ef3-193">However, if you need to run tasks in a specific order, you must specify when each task is complete, as well as which tasks depend on the completion of another task.</span></span>

1.  <span data-ttu-id="94ef3-194">Chcete-li definovat řadu úkolů ke spuštění v pořadí, nahraďte `first` úkol, který jste přidali výše v *gulpfile.js* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="94ef3-194">To define a series of tasks to run in order, replace the `first` task that you added above in *gulpfile.js* with the following:</span></span>

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
 
    <span data-ttu-id="94ef3-195">Teď máte tři úkoly: `series:first`, `series:second`, a `series`.</span><span class="sxs-lookup"><span data-stu-id="94ef3-195">You now have three tasks: `series:first`, `series:second`, and `series`.</span></span> <span data-ttu-id="94ef3-196">`series:second` Úkol obsahuje druhý parametr, který určuje pole úkolů ke spuštění a dokončení před `series:second` bude úloha spuštěna.</span><span class="sxs-lookup"><span data-stu-id="94ef3-196">The `series:second` task includes a second parameter which specifies an array of tasks to be run and completed before the `series:second` task will run.</span></span> <span data-ttu-id="94ef3-197">Jak je uvedeno v kódu výše, pouze `series:first` úkolu musí splnit, aby `series:second` bude úloha spuštěna.</span><span class="sxs-lookup"><span data-stu-id="94ef3-197">As specified in the code above, only the `series:first` task must be completed before the `series:second` task will run.</span></span>

2.  <span data-ttu-id="94ef3-198">Uložit *gulpfile.js*.</span><span class="sxs-lookup"><span data-stu-id="94ef3-198">Save *gulpfile.js*.</span></span>

3.  <span data-ttu-id="94ef3-199">V **Průzkumníka řešení**, klikněte pravým tlačítkem na *gulpfile.js* a vyberte **Task Runner Explorer** Pokud ještě není otevřeno.</span><span class="sxs-lookup"><span data-stu-id="94ef3-199">In **Solution Explorer**, right-click *gulpfile.js* and select **Task Runner Explorer** if it isn't already open.</span></span>

4.  <span data-ttu-id="94ef3-200">V **Task Runner Explorer**, klikněte pravým tlačítkem na **řady** a vyberte **spustit**.</span><span class="sxs-lookup"><span data-stu-id="94ef3-200">In **Task Runner Explorer**, right-click **series** and select **Run**.</span></span>

    ![Task Runner Explorer spusťte úlohu řady](using-gulp/_static/07-TaskRunner-Series.png)

## <a name="intellisense"></a><span data-ttu-id="94ef3-202">IntelliSense</span><span class="sxs-lookup"><span data-stu-id="94ef3-202">IntelliSense</span></span>

<span data-ttu-id="94ef3-203">Technologie IntelliSense poskytuje doplňování kódu, popisy parametrů a další funkce pro zvýšení produktivity a snížení chyby.</span><span class="sxs-lookup"><span data-stu-id="94ef3-203">IntelliSense provides code completion, parameter descriptions, and other features to boost productivity and to decrease errors.</span></span> <span data-ttu-id="94ef3-204">Úlohy gulp jsou napsané v jazyce JavaScript; technologie IntelliSense může proto poskytnout pomoc při vývoji.</span><span class="sxs-lookup"><span data-stu-id="94ef3-204">Gulp tasks are written in JavaScript; therefore, IntelliSense can provide assistance while developing.</span></span> <span data-ttu-id="94ef3-205">Při práci s použitím jazyka JavaScript, IntelliSense vypíše objekty, funkce, vlastnosti a parametry, které jsou k dispozici podle aktuálního kontextu.</span><span class="sxs-lookup"><span data-stu-id="94ef3-205">As you work with JavaScript, IntelliSense lists the objects, functions, properties, and parameters that are available based on your current context.</span></span> <span data-ttu-id="94ef3-206">Vyberte možnost kódování a ze seznamu místní nabídky technologie IntelliSense pro dokončení kódu.</span><span class="sxs-lookup"><span data-stu-id="94ef3-206">Select a coding option from the pop-up list provided by IntelliSense to complete the code.</span></span>

![gulp technologie IntelliSense](using-gulp/_static/08-IntelliSense.png)

<span data-ttu-id="94ef3-208">Další informace o technologii IntelliSense, najdete v části [technologie IntelliSense jazyka JavaScript](/visualstudio/ide/javascript-intellisense).</span><span class="sxs-lookup"><span data-stu-id="94ef3-208">For more information about IntelliSense, see [JavaScript IntelliSense](/visualstudio/ide/javascript-intellisense).</span></span>

## <a name="development-staging-and-production-environments"></a><span data-ttu-id="94ef3-209">Vývoj, přípravného a produkčního prostředí</span><span class="sxs-lookup"><span data-stu-id="94ef3-209">Development, staging, and production environments</span></span>

<span data-ttu-id="94ef3-210">Při použití nástroje Gulp optimalizovat soubory na straně klienta pro pracovní a provozní zpracované soubory se uloží do místního umístění pracovního a produkčního prostředí.</span><span class="sxs-lookup"><span data-stu-id="94ef3-210">When Gulp is used to optimize client-side files for staging and production, the processed files are saved to a local staging and production location.</span></span> <span data-ttu-id="94ef3-211">*_Layout.cshtml* souboru používá **prostředí** označit pomocné rutiny poskytnout dvě různé verze souborů šablon stylů CSS.</span><span class="sxs-lookup"><span data-stu-id="94ef3-211">The *_Layout.cshtml* file uses the **environment** tag helper to provide two different versions of CSS files.</span></span> <span data-ttu-id="94ef3-212">Jedna verze souborů šablon stylů CSS je pro vývoj a jiné verze je optimalizovaná pro přípravným a produkčním prostředím.</span><span class="sxs-lookup"><span data-stu-id="94ef3-212">One version of CSS files is for development and the other version is optimized for both staging and production.</span></span> <span data-ttu-id="94ef3-213">V sadě Visual Studio 2017, když změníte **ASPNETCORE_ENVIRONMENT** proměnnou prostředí, aby `Production`, Visual Studio vytvoří webovou aplikaci a odkaz na minimalizované soubory šablon stylů CSS.</span><span class="sxs-lookup"><span data-stu-id="94ef3-213">In Visual Studio 2017, when you change the **ASPNETCORE_ENVIRONMENT** environment variable to `Production`, Visual Studio will build the Web app and link to the minimized CSS files.</span></span> <span data-ttu-id="94ef3-214">Následující kód ukazuje **prostředí** obsahující značky odkazu pro pomocné rutiny značky `Development` šablon stylů CSS souborů a minifikovaný `Staging, Production` soubory šablon stylů CSS.</span><span class="sxs-lookup"><span data-stu-id="94ef3-214">The following markup shows the **environment** tag helpers containing link tags to the `Development` CSS files and the minified `Staging, Production` CSS files.</span></span>

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

## <a name="switching-between-environments"></a><span data-ttu-id="94ef3-215">Přepínání mezi prostředími</span><span class="sxs-lookup"><span data-stu-id="94ef3-215">Switching between environments</span></span>

<span data-ttu-id="94ef3-216">Chcete-li přepnout mezi kompilace pro různá prostředí, upravte **ASPNETCORE_ENVIRONMENT** hodnoty proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="94ef3-216">To switch between compiling for different environments, modify the **ASPNETCORE_ENVIRONMENT** environment variable's value.</span></span>

1.  <span data-ttu-id="94ef3-217">V **Task Runner Explorer**, ověřte, že **min** úloha byla nastavena pro spuštění **před sestavení**.</span><span class="sxs-lookup"><span data-stu-id="94ef3-217">In **Task Runner Explorer**, verify that the **min** task has been set to run **Before Build**.</span></span>

2.  <span data-ttu-id="94ef3-218">V **Průzkumníka řešení**, klikněte pravým tlačítkem na název projektu a vyberte **vlastnosti**.</span><span class="sxs-lookup"><span data-stu-id="94ef3-218">In **Solution Explorer**, right-click the project name and select **Properties**.</span></span>

    <span data-ttu-id="94ef3-219">Zobrazí se seznam vlastností pro webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="94ef3-219">The property sheet for the Web app is displayed.</span></span>

3.  <span data-ttu-id="94ef3-220">Klikněte na tlačítko **ladění** kartu.</span><span class="sxs-lookup"><span data-stu-id="94ef3-220">Click the **Debug** tab.</span></span>

4.  <span data-ttu-id="94ef3-221">Nastavte hodnotu **Hosting: prostředí** proměnnou prostředí, aby `Production`.</span><span class="sxs-lookup"><span data-stu-id="94ef3-221">Set the value of the **Hosting:Environment** environment variable to `Production`.</span></span>

5.  <span data-ttu-id="94ef3-222">Stisknutím klávesy **F5** ke spuštění aplikace v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="94ef3-222">Press **F5** to run the application in a browser.</span></span>

6.  <span data-ttu-id="94ef3-223">V okně prohlížeče, klikněte pravým tlačítkem na stránku a vybrat **zobrazit zdroj** Chcete-li zobrazit kód HTML stránky.</span><span class="sxs-lookup"><span data-stu-id="94ef3-223">In the browser window, right-click the page and select **View Source** to view the HTML for the page.</span></span>

    <span data-ttu-id="94ef3-224">Všimněte si, že šablona stylů odkazy odkazují na minifikovaný soubory šablon stylů CSS.</span><span class="sxs-lookup"><span data-stu-id="94ef3-224">Notice that the stylesheet links point to the minified CSS files.</span></span>

7.  <span data-ttu-id="94ef3-225">Zavřete prohlížeč a zastavte tak webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="94ef3-225">Close the browser to stop the Web app.</span></span>

8.  <span data-ttu-id="94ef3-226">V sadě Visual Studio, vraťte se do seznamu vlastností pro webové aplikace a změňte **Hosting: prostředí** proměnnou prostředí zpět do `Development`.</span><span class="sxs-lookup"><span data-stu-id="94ef3-226">In Visual Studio, return to the property sheet for the Web app and change the **Hosting:Environment** environment variable back to `Development`.</span></span>

9.  <span data-ttu-id="94ef3-227">Stisknutím klávesy **F5** a znovu spusťte aplikaci v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="94ef3-227">Press **F5** to run the application in a browser again.</span></span>

10. <span data-ttu-id="94ef3-228">V okně prohlížeče, klikněte pravým tlačítkem na stránku a vybrat **zobrazit zdroj** zobrazíte kód HTML stránky.</span><span class="sxs-lookup"><span data-stu-id="94ef3-228">In the browser window, right-click the page and select **View Source** to see the HTML for the page.</span></span>

    <span data-ttu-id="94ef3-229">Všimněte si, že šablona stylů odkazují na nezmenšenou verze souborů šablon stylů CSS.</span><span class="sxs-lookup"><span data-stu-id="94ef3-229">Notice that the stylesheet links point to the unminified versions of the CSS files.</span></span>

<span data-ttu-id="94ef3-230">Další informace týkající se prostředí v ASP.NET Core najdete v tématu [používání více prostředí](../fundamentals/environments.md).</span><span class="sxs-lookup"><span data-stu-id="94ef3-230">For more information related to environments in ASP.NET Core, see [Use multiple environments](../fundamentals/environments.md).</span></span>

## <a name="task-and-module-details"></a><span data-ttu-id="94ef3-231">Podrobnosti úlohy a modul</span><span class="sxs-lookup"><span data-stu-id="94ef3-231">Task and module details</span></span>

<span data-ttu-id="94ef3-232">Úlohy Gulp je registrovaná pro název funkce.</span><span class="sxs-lookup"><span data-stu-id="94ef3-232">A Gulp task is registered with a function name.</span></span> <span data-ttu-id="94ef3-233">Závislosti můžete zadat, pokud jiné úlohy musí být spuštěny před aktuální úlohu.</span><span class="sxs-lookup"><span data-stu-id="94ef3-233">You can specify dependencies if other tasks must run before the current task.</span></span> <span data-ttu-id="94ef3-234">Další funkce umožňují spouštět a sledovat úlohy Gulp, také nastavit zdroj (*src*) a cíl (*dest*) souborů právě upravuje.</span><span class="sxs-lookup"><span data-stu-id="94ef3-234">Additional functions allow you to run and watch the Gulp tasks, as well as set the source (*src*) and destination (*dest*) of the files being modified.</span></span> <span data-ttu-id="94ef3-235">Toto jsou primární funkce Gulp rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="94ef3-235">The following are the primary Gulp API functions:</span></span>

|<span data-ttu-id="94ef3-236">Gulp – funkce</span><span class="sxs-lookup"><span data-stu-id="94ef3-236">Gulp Function</span></span>|<span data-ttu-id="94ef3-237">Syntaxe</span><span class="sxs-lookup"><span data-stu-id="94ef3-237">Syntax</span></span>|<span data-ttu-id="94ef3-238">Popis</span><span class="sxs-lookup"><span data-stu-id="94ef3-238">Description</span></span>|
|---   |--- |--- |
|<span data-ttu-id="94ef3-239">– úloha</span><span class="sxs-lookup"><span data-stu-id="94ef3-239">task</span></span>  |`gulp.task(name[, deps], fn) { }`|<span data-ttu-id="94ef3-240">`task` Funkce vytvoří úkol.</span><span class="sxs-lookup"><span data-stu-id="94ef3-240">The `task` function creates a task.</span></span> <span data-ttu-id="94ef3-241">`name` Parametr definuje název úkolu.</span><span class="sxs-lookup"><span data-stu-id="94ef3-241">The `name` parameter defines the name of the task.</span></span> <span data-ttu-id="94ef3-242">`deps` Parametr obsahuje celou řadu úloh dokončit dříve, než se tato úloha spuštěna.</span><span class="sxs-lookup"><span data-stu-id="94ef3-242">The `deps` parameter contains an array of tasks to be completed before this task runs.</span></span> <span data-ttu-id="94ef3-243">`fn` Parametr představuje funkci zpětného volání, které provádí operace úlohy.</span><span class="sxs-lookup"><span data-stu-id="94ef3-243">The `fn` parameter represents a callback function which performs the operations of the task.</span></span>|
|<span data-ttu-id="94ef3-244">Sledování</span><span class="sxs-lookup"><span data-stu-id="94ef3-244">watch</span></span> |`gulp.watch(glob [, opts], tasks) { }`|<span data-ttu-id="94ef3-245">`watch` Funkce monitoruje soubory a spuštění úlohy, když dojde ke změně souboru.</span><span class="sxs-lookup"><span data-stu-id="94ef3-245">The `watch` function monitors files and runs tasks when a file change occurs.</span></span> <span data-ttu-id="94ef3-246">`glob` Parametr je `string` nebo `array` , který určuje, které soubory Pokud chcete sledovat.</span><span class="sxs-lookup"><span data-stu-id="94ef3-246">The `glob` parameter is a `string` or `array` that determines which files to watch.</span></span> <span data-ttu-id="94ef3-247">`opts` Parametr poskytuje další možnosti sledování souboru.</span><span class="sxs-lookup"><span data-stu-id="94ef3-247">The `opts` parameter provides additional file watching options.</span></span>|
|<span data-ttu-id="94ef3-248">src</span><span class="sxs-lookup"><span data-stu-id="94ef3-248">src</span></span>   |`gulp.src(globs[, options]) { }`|<span data-ttu-id="94ef3-249">`src` Soubory, které odpovídají glob hodnoty, které poskytuje funkce.</span><span class="sxs-lookup"><span data-stu-id="94ef3-249">The `src` function provides files that match the glob value(s).</span></span> <span data-ttu-id="94ef3-250">`glob` Parametr je `string` nebo `array` , který určuje, které soubory číst.</span><span class="sxs-lookup"><span data-stu-id="94ef3-250">The `glob` parameter is a `string` or `array` that determines which files to read.</span></span> <span data-ttu-id="94ef3-251">`options` Parametr poskytuje možnosti pro další soubor.</span><span class="sxs-lookup"><span data-stu-id="94ef3-251">The `options` parameter provides additional file options.</span></span>|
|<span data-ttu-id="94ef3-252">dest</span><span class="sxs-lookup"><span data-stu-id="94ef3-252">dest</span></span>  |`gulp.dest(path[, options]) { }`|<span data-ttu-id="94ef3-253">`dest` Funkce definuje umístění, ke kterému lze zapisovat soubory.</span><span class="sxs-lookup"><span data-stu-id="94ef3-253">The `dest` function defines a location to which files can be written.</span></span> <span data-ttu-id="94ef3-254">`path` Parametr je řetězec nebo funkci, která určuje cílovou složku.</span><span class="sxs-lookup"><span data-stu-id="94ef3-254">The `path` parameter is a string or function that determines the destination folder.</span></span> <span data-ttu-id="94ef3-255">`options` Parametru je objekt, který určuje možnosti výstupní složky.</span><span class="sxs-lookup"><span data-stu-id="94ef3-255">The `options` parameter is an object that specifies output folder options.</span></span>|

<span data-ttu-id="94ef3-256">Další referenční informace Gulp rozhraní API najdete v tématu [Gulp dokumentace API](https://gulpjs.org/API.html).</span><span class="sxs-lookup"><span data-stu-id="94ef3-256">For additional Gulp API reference information, see [Gulp Docs API](https://gulpjs.org/API.html).</span></span>

## <a name="gulp-recipes"></a><span data-ttu-id="94ef3-257">Recepty gulp</span><span class="sxs-lookup"><span data-stu-id="94ef3-257">Gulp recipes</span></span>

<span data-ttu-id="94ef3-258">Komunita Gulp poskytuje Gulp [recepty](https://github.com/gulpjs/gulp/blob/master/docs/recipes/README.md).</span><span class="sxs-lookup"><span data-stu-id="94ef3-258">The Gulp community provides Gulp [Recipes](https://github.com/gulpjs/gulp/blob/master/docs/recipes/README.md).</span></span> <span data-ttu-id="94ef3-259">Tyto recepty se skládají z úlohy Gulp vztahují k běžným scénářům.</span><span class="sxs-lookup"><span data-stu-id="94ef3-259">These recipes consist of Gulp tasks to address common scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="94ef3-260">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="94ef3-260">Additional resources</span></span>

* [<span data-ttu-id="94ef3-261">Dokumentace ke službě gulp</span><span class="sxs-lookup"><span data-stu-id="94ef3-261">Gulp documentation</span></span>](https://github.com/gulpjs/gulp/blob/master/docs/README.md)
* [<span data-ttu-id="94ef3-262">Sdružování a minifikace v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="94ef3-262">Bundling and minification in ASP.NET Core</span></span>](bundling-and-minification.md)
* [<span data-ttu-id="94ef3-263">Použití nástroje Grunt v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="94ef3-263">Use Grunt in ASP.NET Core</span></span>](using-grunt.md)

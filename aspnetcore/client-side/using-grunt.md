---
title: Použití nástroje Grunt v ASP.NET Core
author: rick-anderson
description: ''
ms.author: riande
ms.date: 05/10/2019
uid: client-side/using-grunt
ms.openlocfilehash: 73b7704726db9d93588dddd3f3b05a23fb3425b3
ms.sourcegitcommit: ffe3ed7921ec6c7c70abaac1d10703ec9a43374c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2019
ms.locfileid: "65535941"
---
# <a name="use-grunt-in-aspnet-core"></a><span data-ttu-id="de30f-102">Použití nástroje Grunt v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="de30f-102">Use Grunt in ASP.NET Core</span></span>

<span data-ttu-id="de30f-103">Podle [Noel rýže](https://blog.falafel.com/falafel-software-recognized-sitefinity-website-year/)</span><span class="sxs-lookup"><span data-stu-id="de30f-103">By [Noel Rice](https://blog.falafel.com/falafel-software-recognized-sitefinity-website-year/)</span></span>

<span data-ttu-id="de30f-104">Grunt je Spouštěče úloh JavaScriptu, který automatizuje skript připravenost k minifikaci, TypeScript kompilace, nástroji "lint" kvality kódu, šablony stylů CSS předběžné procesory a téměř všechny opakované případě vyžadující způsobem pro podporu vývoje klienta.</span><span class="sxs-lookup"><span data-stu-id="de30f-104">Grunt is a JavaScript task runner that automates script minification, TypeScript compilation, code quality "lint" tools, CSS pre-processors, and just about any repetitive chore that needs doing to support client development.</span></span> <span data-ttu-id="de30f-105">Grunt je plně podporováno v sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="de30f-105">Grunt is fully supported in Visual Studio.</span></span>

<span data-ttu-id="de30f-106">Tento příklad používá prázdný projekt ASP.NET Core jako výchozí bod, jak automatizovat proces sestavení klienta od začátku.</span><span class="sxs-lookup"><span data-stu-id="de30f-106">This example uses an empty ASP.NET Core project as its starting point, to show how to automate the client build process from scratch.</span></span>

<span data-ttu-id="de30f-107">Dokončení příkladu vyčistí cílový adresář nasazení, kombinuje Javascriptové soubory, zkontroluje kvalitu kódu, zestruční obsah souboru jazyka JavaScript a nasadí do kořenového adresáře webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="de30f-107">The finished example cleans the target deployment directory, combines JavaScript files, checks code quality, condenses JavaScript file content and deploys to the root of your web application.</span></span> <span data-ttu-id="de30f-108">Použijeme následující balíčky:</span><span class="sxs-lookup"><span data-stu-id="de30f-108">We will use the following packages:</span></span>

* <span data-ttu-id="de30f-109">**grunt**: Balíček nástroje Grunt úloh runner.</span><span class="sxs-lookup"><span data-stu-id="de30f-109">**grunt**: The Grunt task runner package.</span></span>

* <span data-ttu-id="de30f-110">**grunt-contrib-clean**: Modul plug-in, který odstraní soubory nebo adresáře.</span><span class="sxs-lookup"><span data-stu-id="de30f-110">**grunt-contrib-clean**: A plugin that removes files or directories.</span></span>

* <span data-ttu-id="de30f-111">**grunt-contrib-jshint**: Modul plug-in kontroly kvality kódu jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="de30f-111">**grunt-contrib-jshint**: A plugin that reviews JavaScript code quality.</span></span>

* <span data-ttu-id="de30f-112">**grunt-contrib-concat**: Modul plug-in, který spojuje soubory do jediného souboru.</span><span class="sxs-lookup"><span data-stu-id="de30f-112">**grunt-contrib-concat**: A plugin that joins files into a single file.</span></span>

* <span data-ttu-id="de30f-113">**grunt-contrib-uglify**: Modul plug-in minifikuje zmenšit velikost kódu jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="de30f-113">**grunt-contrib-uglify**: A plugin that minifies JavaScript to reduce size.</span></span>

* <span data-ttu-id="de30f-114">**grunt-contrib-watch**: Modul plug-in, které sleduje aktivity soubor.</span><span class="sxs-lookup"><span data-stu-id="de30f-114">**grunt-contrib-watch**: A plugin that watches file activity.</span></span>

## <a name="preparing-the-application"></a><span data-ttu-id="de30f-115">Příprava aplikace</span><span class="sxs-lookup"><span data-stu-id="de30f-115">Preparing the application</span></span>

<span data-ttu-id="de30f-116">Pokud chcete začít, nastavte novou prázdnou webovou aplikaci a přidejte soubory TypeScript příklad.</span><span class="sxs-lookup"><span data-stu-id="de30f-116">To begin, set up a new empty web application and add TypeScript example files.</span></span> <span data-ttu-id="de30f-117">Soubory TypeScript automaticky kompilovány do jazyka JavaScript pomocí výchozího nastavení sady Visual Studio a bude náš suroviny proces použití nástroje Grunt.</span><span class="sxs-lookup"><span data-stu-id="de30f-117">TypeScript files are automatically compiled into JavaScript using default Visual Studio settings and will be our raw material to process using Grunt.</span></span>

1. <span data-ttu-id="de30f-118">V sadě Visual Studio vytvořte nový `ASP.NET Web Application`.</span><span class="sxs-lookup"><span data-stu-id="de30f-118">In Visual Studio, create a new `ASP.NET Web Application`.</span></span>

2. <span data-ttu-id="de30f-119">V **nový projekt ASP.NET** dialogového okna, vyberte ASP.NET Core **prázdný** šablonu a klikněte na tlačítko OK.</span><span class="sxs-lookup"><span data-stu-id="de30f-119">In the **New ASP.NET Project** dialog, select the ASP.NET Core **Empty** template and click the OK button.</span></span>

3. <span data-ttu-id="de30f-120">V Průzkumníku řešení zkontrolujte strukturu projektu.</span><span class="sxs-lookup"><span data-stu-id="de30f-120">In the Solution Explorer, review the project structure.</span></span> <span data-ttu-id="de30f-121">`\src` Složka obsahuje prázdný `wwwroot` a `Dependencies` uzly.</span><span class="sxs-lookup"><span data-stu-id="de30f-121">The `\src` folder includes empty `wwwroot` and `Dependencies` nodes.</span></span>

    ![prázdný webový řešení](using-grunt/_static/grunt-solution-explorer.png)

4. <span data-ttu-id="de30f-123">Přidat novou složku s názvem `TypeScript` k adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="de30f-123">Add a new folder named `TypeScript` to your project directory.</span></span>

5. <span data-ttu-id="de30f-124">Před přidáním všech souborů, ujistěte se, že Visual Studio poskytuje možnost "zkompilovat při uložení" pro TypeScript soubory se změnami.</span><span class="sxs-lookup"><span data-stu-id="de30f-124">Before adding any files, make sure that Visual Studio has the option 'compile on save' for TypeScript files checked.</span></span> <span data-ttu-id="de30f-125">Přejděte do **nástroje** > **možnosti** > **textový Editor** > **Typescript**  >  **Projektu**:</span><span class="sxs-lookup"><span data-stu-id="de30f-125">Navigate to **Tools** > **Options** > **Text Editor** > **Typescript** > **Project**:</span></span>

    ![Možnosti nastavení automatické kompilaci souborů TypeScript](using-grunt/_static/typescript-options.png)

6. <span data-ttu-id="de30f-127">Klikněte pravým tlačítkem myši `TypeScript` adresář a zaškrtnout možnost **Přidat > Nová položka** v místní nabídce.</span><span class="sxs-lookup"><span data-stu-id="de30f-127">Right-click the `TypeScript` directory and select **Add > New Item** from the context menu.</span></span> <span data-ttu-id="de30f-128">Vyberte **soubor JavaScript** položku a zadejte název souboru *Tastes.ts* (Poznámka: \*.ts rozšíření).</span><span class="sxs-lookup"><span data-stu-id="de30f-128">Select the **JavaScript file** item and name the file *Tastes.ts* (note the \*.ts extension).</span></span> <span data-ttu-id="de30f-129">Zkopírovat řádek níže uvedený kód TypeScript do souboru (po uložení, nový *Tastes.js* souboru se zobrazí s zdroji JavaScript).</span><span class="sxs-lookup"><span data-stu-id="de30f-129">Copy the line of TypeScript code below into the file (when you save, a new *Tastes.js* file will appear with the JavaScript source).</span></span>

    ```typescript
    enum Tastes { Sweet, Sour, Salty, Bitter }
    ```

7. <span data-ttu-id="de30f-130">Přidat druhý soubor **TypeScript** adresáře a pojmenujte ho `Food.ts`.</span><span class="sxs-lookup"><span data-stu-id="de30f-130">Add a second file to the **TypeScript** directory and name it `Food.ts`.</span></span> <span data-ttu-id="de30f-131">Zkopírujte následující kód do souboru.</span><span class="sxs-lookup"><span data-stu-id="de30f-131">Copy the code below into the file.</span></span>

    ```typescript
    class Food {
      constructor(name: string, calories: number) {
        this._name = name;
        this._calories = calories;
      }

      private _name: string;
      get Name() {
        return this._name;
      }

      private _calories: number;
      get Calories() {
        return this._calories;
      }

      private _taste: Tastes;
      get Taste(): Tastes { return this._taste }
      set Taste(value: Tastes) {
        this._taste = value;
      }
    }
    ```

## <a name="configuring-npm"></a><span data-ttu-id="de30f-132">Konfigurace NPM</span><span class="sxs-lookup"><span data-stu-id="de30f-132">Configuring NPM</span></span>

<span data-ttu-id="de30f-133">V dalším kroku nakonfigurujte NPM pro stažení nástroje grunt a grunt úlohy.</span><span class="sxs-lookup"><span data-stu-id="de30f-133">Next, configure NPM to download grunt and grunt-tasks.</span></span>

1. <span data-ttu-id="de30f-134">V Průzkumníku řešení klikněte pravým tlačítkem myši na projekt a vyberte **Přidat > Nová položka** v místní nabídce.</span><span class="sxs-lookup"><span data-stu-id="de30f-134">In the Solution Explorer, right-click the project and select **Add > New Item** from the context menu.</span></span> <span data-ttu-id="de30f-135">Vyberte **konfigurační soubor NPM** položky, ponechte výchozí název *package.json*a klikněte na tlačítko **přidat** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="de30f-135">Select the **NPM configuration file** item, leave the default name, *package.json*, and click the **Add** button.</span></span>

2. <span data-ttu-id="de30f-136">V *package.json* uvnitř souboru `devDependencies` objektu složené závorky, zadejte "grunt".</span><span class="sxs-lookup"><span data-stu-id="de30f-136">In the *package.json* file, inside the `devDependencies` object braces, enter "grunt".</span></span> <span data-ttu-id="de30f-137">Vyberte `grunt` z technologie Intellisense seznam a stiskněte klávesu Enter.</span><span class="sxs-lookup"><span data-stu-id="de30f-137">Select `grunt` from the Intellisense list and press the Enter key.</span></span> <span data-ttu-id="de30f-138">Visual Studio se nabídka grunt název balíčku a přidejte dvojtečku.</span><span class="sxs-lookup"><span data-stu-id="de30f-138">Visual Studio will quote the grunt package name, and add a colon.</span></span> <span data-ttu-id="de30f-139">Napravo od dvojtečku, vyberte nejnovější stabilní verze balíčku z horní části seznamu technologie Intellisense (stiskněte `Ctrl-Space` Pokud technologie Intellisense se nezobrazí).</span><span class="sxs-lookup"><span data-stu-id="de30f-139">To the right of the colon, select the latest stable version of the package from the top of the Intellisense list (press `Ctrl-Space` if Intellisense doesn't appear).</span></span>

    ![grunt technologie Intellisense](using-grunt/_static/devdependencies-grunt.png)

    > [!NOTE]
    > <span data-ttu-id="de30f-141">NPM používá [sémantické správy verzí](http://semver.org/) k uspořádání závislosti.</span><span class="sxs-lookup"><span data-stu-id="de30f-141">NPM uses [semantic versioning](http://semver.org/) to organize dependencies.</span></span> <span data-ttu-id="de30f-142">Sémantické správy verzí, označované také jako SemVer identifikuje balíčky se schéma číslování \<hlavní >.\< podverze >. \<opravy >.</span><span class="sxs-lookup"><span data-stu-id="de30f-142">Semantic versioning, also known as SemVer, identifies packages with the numbering scheme \<major>.\<minor>.\<patch>.</span></span> <span data-ttu-id="de30f-143">Technologie IntelliSense zjednodušuje tím, že zobrazuje pouze několik běžné volby sémantické správy verzí.</span><span class="sxs-lookup"><span data-stu-id="de30f-143">Intellisense simplifies semantic versioning by showing only a few common choices.</span></span> <span data-ttu-id="de30f-144">Horní položku v seznamu technologie Intellisense (0.4.5 v příkladu výše) se považuje za nejnovější stabilní verze balíčku.</span><span class="sxs-lookup"><span data-stu-id="de30f-144">The top item in the Intellisense list (0.4.5 in the example above) is considered the latest stable version of the package.</span></span> <span data-ttu-id="de30f-145">Symbol stříšky (^) odpovídá nejnovější hlavní verzi a tilda (~) odpovídá nejnovější dílčí verzi.</span><span class="sxs-lookup"><span data-stu-id="de30f-145">The caret (^) symbol matches the most recent major version and the tilde (~) matches the most recent minor version.</span></span> <span data-ttu-id="de30f-146">Najdete v článku [odkaz analyzátoru verzi semver NPM](https://www.npmjs.com/package/semver) jako průvodce k úplné expressivity poskytující SemVer.</span><span class="sxs-lookup"><span data-stu-id="de30f-146">See the [NPM semver version parser reference](https://www.npmjs.com/package/semver) as a guide to the full expressivity that SemVer provides.</span></span>

3. <span data-ttu-id="de30f-147">Přidat další závislosti načíst grunt-contrib -\* balíčky *čisté*, *jshint*, *concat*, *uglify*a *watch* jak je znázorněno v následujícím příkladu.</span><span class="sxs-lookup"><span data-stu-id="de30f-147">Add more dependencies to load grunt-contrib-\* packages for *clean*, *jshint*, *concat*, *uglify*, and *watch* as shown in the example below.</span></span> <span data-ttu-id="de30f-148">Verze nemusejí být stejné v příkladu.</span><span class="sxs-lookup"><span data-stu-id="de30f-148">The versions don't need to match the example.</span></span>

    ```json
    "devDependencies": {
      "grunt": "0.4.5",
      "grunt-contrib-clean": "0.6.0",
      "grunt-contrib-jshint": "0.11.0",
      "grunt-contrib-concat": "0.5.1",
      "grunt-contrib-uglify": "0.8.0",
      "grunt-contrib-watch": "0.6.1"
    }
    ```

4. <span data-ttu-id="de30f-149">Uložit *package.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="de30f-149">Save the *package.json* file.</span></span>

<span data-ttu-id="de30f-150">Balíčky pro každou položku devDependencies stáhne společně s všechny soubory, které vyžaduje každý balíček.</span><span class="sxs-lookup"><span data-stu-id="de30f-150">The packages for each devDependencies item will download, along with any files that each package requires.</span></span> <span data-ttu-id="de30f-151">Můžete najít soubory v balíčku `node_modules` adresáře tím, že **zobrazit všechny soubory** tlačítko v Průzkumníku řešení.</span><span class="sxs-lookup"><span data-stu-id="de30f-151">You can find the package files in the `node_modules` directory by enabling the **Show All Files** button in the Solution Explorer.</span></span>

![grunt node_modules](using-grunt/_static/node-modules.png)

> [!NOTE]
> <span data-ttu-id="de30f-153">Pokud je potřeba je ručně obnovit závislosti v Průzkumníkovi řešení pravým tlačítkem myši na `Dependencies\NPM` a vyberete **obnovit balíčky** nabídky.</span><span class="sxs-lookup"><span data-stu-id="de30f-153">If you need to, you can manually restore dependencies in Solution Explorer by right-clicking on `Dependencies\NPM` and selecting the **Restore Packages** menu option.</span></span>

![Obnovení balíčků](using-grunt/_static/restore-packages.png)

## <a name="configuring-grunt"></a><span data-ttu-id="de30f-155">Konfigurace nástroje Grunt</span><span class="sxs-lookup"><span data-stu-id="de30f-155">Configuring Grunt</span></span>

<span data-ttu-id="de30f-156">Grunt je nakonfigurovaný nástrojem manifestu s názvem *soubor Gruntfile.js* , který definuje, načítá a registruje úlohy, které lze spustit ručně nebo konfigurované pro běh automaticky na základě událostí v sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="de30f-156">Grunt is configured using a manifest named *Gruntfile.js* that defines, loads and registers tasks that can be run manually or configured to run automatically based on events in Visual Studio.</span></span>

1. <span data-ttu-id="de30f-157">Klikněte pravým tlačítkem na projekt a vyberte **Přidat > Nová položka**.</span><span class="sxs-lookup"><span data-stu-id="de30f-157">Right-click the project and select **Add > New Item**.</span></span> <span data-ttu-id="de30f-158">Vyberte **Grunt konfigurační soubor** možnost, ponechte výchozí název *soubor Gruntfile.js*a klikněte na tlačítko **přidat** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="de30f-158">Select the **Grunt Configuration file** option, leave the default name, *Gruntfile.js*, and click the **Add** button.</span></span>

   <span data-ttu-id="de30f-159">Počáteční kód obsahuje definici modulu a `grunt.initConfig()` metoda.</span><span class="sxs-lookup"><span data-stu-id="de30f-159">The initial code includes a module definition and the `grunt.initConfig()` method.</span></span> <span data-ttu-id="de30f-160">`initConfig()` Slouží k nastavení možností pro každý balíček a zbytek modulu se načtou a zaregistrovat úlohy.</span><span class="sxs-lookup"><span data-stu-id="de30f-160">The `initConfig()` is used to set options for each package, and the remainder of the module will load and register tasks.</span></span>

   ```javascript
   module.exports = function (grunt) {
     grunt.initConfig({
     });
   };
   ```

2. <span data-ttu-id="de30f-161">Uvnitř `initConfig()` metody přidat možnosti pro `clean` úloh, jak je znázorněno v příkladu *soubor Gruntfile.js* níže.</span><span class="sxs-lookup"><span data-stu-id="de30f-161">Inside the `initConfig()` method, add options for the `clean` task as shown in the example *Gruntfile.js* below.</span></span> <span data-ttu-id="de30f-162">Úkolu vyčisti přijímá pole řetězců adresářů.</span><span class="sxs-lookup"><span data-stu-id="de30f-162">The clean task accepts an array of directory strings.</span></span> <span data-ttu-id="de30f-163">Tento úkol odstraní soubory z wwwroot/lib a odstraní celý/dočasný adresář.</span><span class="sxs-lookup"><span data-stu-id="de30f-163">This task removes files from wwwroot/lib and removes the entire /temp directory.</span></span>

    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
        clean: ["wwwroot/lib/*", "temp/"],
      });
    };
    ```

3. <span data-ttu-id="de30f-164">Pod initConfig() metodu, přidejte volání do `grunt.loadNpmTasks()`.</span><span class="sxs-lookup"><span data-stu-id="de30f-164">Below the initConfig() method, add a call to `grunt.loadNpmTasks()`.</span></span> <span data-ttu-id="de30f-165">To způsobí, že úloha spustitelných ze sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="de30f-165">This will make the task runnable from Visual Studio.</span></span>

    ```javascript
    grunt.loadNpmTasks("grunt-contrib-clean");
    ```

4. <span data-ttu-id="de30f-166">Uložit *soubor Gruntfile.js*.</span><span class="sxs-lookup"><span data-stu-id="de30f-166">Save *Gruntfile.js*.</span></span> <span data-ttu-id="de30f-167">Soubor by měl vypadat přibližně jako na následujícím snímku obrazovky.</span><span class="sxs-lookup"><span data-stu-id="de30f-167">The file should look something like the screenshot below.</span></span>

    ![Počáteční gruntfile](using-grunt/_static/gruntfile-js-initial.png)

5. <span data-ttu-id="de30f-169">Klikněte pravým tlačítkem na *soubor Gruntfile.js* a vyberte **Task Runner Explorer** v místní nabídce.</span><span class="sxs-lookup"><span data-stu-id="de30f-169">Right-click *Gruntfile.js* and select **Task Runner Explorer** from the context menu.</span></span> <span data-ttu-id="de30f-170">Otevře se okno Task Runner Explorer.</span><span class="sxs-lookup"><span data-stu-id="de30f-170">The Task Runner Explorer window will open.</span></span>

    ![Nabídka Průzkumníka Spouštěče úloh](using-grunt/_static/task-runner-explorer-menu.png)

6. <span data-ttu-id="de30f-172">Ověřte, že `clean` zobrazí v části **úlohy** v Task Runner Explorer.</span><span class="sxs-lookup"><span data-stu-id="de30f-172">Verify that `clean` shows under **Tasks** in the Task Runner Explorer.</span></span>

    ![Seznam úkolů Průzkumníka Spouštěče úloh](using-grunt/_static/task-runner-explorer-tasks.png)

7. <span data-ttu-id="de30f-174">Klikněte pravým tlačítkem na úkolu vyčisti a vyberte **spustit** v místní nabídce.</span><span class="sxs-lookup"><span data-stu-id="de30f-174">Right-click the clean task and select **Run** from the context menu.</span></span> <span data-ttu-id="de30f-175">Příkazové okno zobrazí průběh úlohy.</span><span class="sxs-lookup"><span data-stu-id="de30f-175">A command window displays progress of the task.</span></span>

    ![runner explorer spusťte čisté úkol](using-grunt/_static/task-runner-explorer-run-clean.png)

    > [!NOTE]
    > <span data-ttu-id="de30f-177">Neexistují žádné soubory nebo adresáře zatím vyčistit.</span><span class="sxs-lookup"><span data-stu-id="de30f-177">There are no files or directories to clean yet.</span></span> <span data-ttu-id="de30f-178">Pokud chcete můžete je vytvořit ručně v Průzkumníku řešení a potom spustit úkolu vyčisti jako test.</span><span class="sxs-lookup"><span data-stu-id="de30f-178">If you like, you can manually create them in the Solution Explorer and then run the clean task as a test.</span></span>

8. <span data-ttu-id="de30f-179">V metodě initConfig() přidat položku pro `concat` pomocí níže uvedeného kódu.</span><span class="sxs-lookup"><span data-stu-id="de30f-179">In the initConfig() method, add an entry for `concat` using the code below.</span></span>

    <span data-ttu-id="de30f-180">`src` Pole vlastnosti obsahuje soubory zkombinovat v pořadí, by měly být kombinované.</span><span class="sxs-lookup"><span data-stu-id="de30f-180">The `src` property array lists files to combine, in the order that they should be combined.</span></span> <span data-ttu-id="de30f-181">`dest` Vlastnost přiřadí cestu k souboru, který je vytvořen kombinované.</span><span class="sxs-lookup"><span data-stu-id="de30f-181">The `dest` property assigns the path to the combined file that's produced.</span></span>

    ```javascript
    concat: {
      all: {
        src: ['TypeScript/Tastes.js', 'TypeScript/Food.js'],
        dest: 'temp/combined.js'
      }
    },
    ```

    > [!NOTE]
    > <span data-ttu-id="de30f-182">`all` Vlastnost ve výše uvedeném kódu je název cíle.</span><span class="sxs-lookup"><span data-stu-id="de30f-182">The `all` property in the code above is the name of a target.</span></span> <span data-ttu-id="de30f-183">Cíle se používají v některých úkolů Grunt umožňuje prostředí s více sestavení.</span><span class="sxs-lookup"><span data-stu-id="de30f-183">Targets are used in some Grunt tasks to allow multiple build environments.</span></span> <span data-ttu-id="de30f-184">Můžete zobrazit předdefinované cíle pomocí technologie Intellisense nebo přiřadit vlastní.</span><span class="sxs-lookup"><span data-stu-id="de30f-184">You can view the built-in targets using Intellisense or assign your own.</span></span>

9. <span data-ttu-id="de30f-185">Přidat `jshint` úloh, pomocí níže uvedeného kódu.</span><span class="sxs-lookup"><span data-stu-id="de30f-185">Add the `jshint` task using the code below.</span></span>

    <span data-ttu-id="de30f-186">Spustí nástroj jshint kvality kódu pro každý soubor JavaScript najde v adresáři temp.</span><span class="sxs-lookup"><span data-stu-id="de30f-186">The jshint code-quality utility is run against every JavaScript file found in the temp directory.</span></span>

    ```javascript
    jshint: {
      files: ['temp/*.js'],
      options: {
        '-W069': false,
      }
    },
    ```

    > [!NOTE]
    > <span data-ttu-id="de30f-187">Možnost "-W069" chybu vytvořil jshint při párování JavaScript používá syntaxi přiřazení vlastnosti namísto zápisu s tečkou, to znamená `Tastes["Sweet"]` místo `Tastes.Sweet`.</span><span class="sxs-lookup"><span data-stu-id="de30f-187">The option "-W069" is an error produced by jshint when JavaScript uses bracket syntax to assign a property instead of dot notation, i.e. `Tastes["Sweet"]` instead of `Tastes.Sweet`.</span></span> <span data-ttu-id="de30f-188">Možnost vypne upozornění umožňující zbytek procesu pokračovat.</span><span class="sxs-lookup"><span data-stu-id="de30f-188">The option turns off the warning to allow the rest of the process to continue.</span></span>

10. <span data-ttu-id="de30f-189">Přidat `uglify` úloh, pomocí níže uvedeného kódu.</span><span class="sxs-lookup"><span data-stu-id="de30f-189">Add the `uglify` task using the code below.</span></span>

    <span data-ttu-id="de30f-190">Minifikuje úlohy *combined.js* soubor najde v adresáři temp a vytvoří soubor s výsledky v wwwroot/lib podle standardní konvence  *\<název_souboru\>. min.js*.</span><span class="sxs-lookup"><span data-stu-id="de30f-190">The task minifies the *combined.js* file found in the temp directory and creates the result file in wwwroot/lib following the standard naming convention *\<file name\>.min.js*.</span></span>

    ```javascript
    uglify: {
     all: {
       src: ['temp/combined.js'],
       dest: 'wwwroot/lib/combined.min.js'
     }
    },
    ```

11. <span data-ttu-id="de30f-191">V části grunt.loadNpmTasks() volání, která načte vyčistit contrib grunt patří stejné volání pro jshint, funkce concat a uglify pomocí níže uvedeného kódu.</span><span class="sxs-lookup"><span data-stu-id="de30f-191">Under the call grunt.loadNpmTasks() that loads grunt-contrib-clean, include the same call for jshint, concat and uglify using the code below.</span></span>

    ```javascript
    grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    ```

12. <span data-ttu-id="de30f-192">Uložit *soubor Gruntfile.js*.</span><span class="sxs-lookup"><span data-stu-id="de30f-192">Save *Gruntfile.js*.</span></span> <span data-ttu-id="de30f-193">Soubor by měl vypadat přibližně jako v příkladu níže.</span><span class="sxs-lookup"><span data-stu-id="de30f-193">The file should look something like the example below.</span></span>

    ![Příklad souboru kompletní grunt](using-grunt/_static/gruntfile-js-complete.png)

13. <span data-ttu-id="de30f-195">Všimněte si, že se seznamu úkolů Průzkumníka Spouštěče úloh zahrnuje `clean`, `concat`, `jshint` a `uglify` úlohy.</span><span class="sxs-lookup"><span data-stu-id="de30f-195">Notice that the Task Runner Explorer Tasks list includes `clean`, `concat`, `jshint` and `uglify` tasks.</span></span> <span data-ttu-id="de30f-196">Spusťte každý úkol v pořadí a sledujte výsledky v Průzkumníku řešení.</span><span class="sxs-lookup"><span data-stu-id="de30f-196">Run each task in order and observe the results in Solution Explorer.</span></span> <span data-ttu-id="de30f-197">Každý úkol by měl spustit bez chyb.</span><span class="sxs-lookup"><span data-stu-id="de30f-197">Each task should run without errors.</span></span>

    ![Průzkumník Spouštěče úloh spouštět každý úkol](using-grunt/_static/task-runner-explorer-run-each-task.png)

    <span data-ttu-id="de30f-199">Vytvoří nový úkol concat *combined.js* soubor a umístí ji do dočasného adresáře.</span><span class="sxs-lookup"><span data-stu-id="de30f-199">The concat task creates a new *combined.js* file and places it into the temp directory.</span></span> <span data-ttu-id="de30f-200">Úloha jshint jednoduše spustí a nevytvoří výstup.</span><span class="sxs-lookup"><span data-stu-id="de30f-200">The jshint task simply runs and doesn't produce output.</span></span> <span data-ttu-id="de30f-201">Vytvoří nový úkol uglify *combined.min.js* soubor a umístí jej do wwwroot/lib.</span><span class="sxs-lookup"><span data-stu-id="de30f-201">The uglify task creates a new *combined.min.js* file and places it into wwwroot/lib.</span></span> <span data-ttu-id="de30f-202">Po dokončení, řešení by mělo vypadat jako na následujícím snímku obrazovky:</span><span class="sxs-lookup"><span data-stu-id="de30f-202">On completion, the solution should look something like the screenshot below:</span></span>

    ![Průzkumník řešení po všech úloh.](using-grunt/_static/solution-explorer-after-all-tasks.png)

    > [!NOTE]
    > <span data-ttu-id="de30f-204">Další informace o možnostech pro každý balíček [ https://www.npmjs.com/ ](https://www.npmjs.com/) a vyhledávací název balíčku do vyhledávacího pole na hlavní stránce.</span><span class="sxs-lookup"><span data-stu-id="de30f-204">For more information on the options for each package, visit [https://www.npmjs.com/](https://www.npmjs.com/) and lookup the package name in the search box on the main page.</span></span> <span data-ttu-id="de30f-205">Můžete například vyhledat balíček grunt contrib vyčistit získat odkaz na dokumentaci, který vysvětluje všechny její parametry.</span><span class="sxs-lookup"><span data-stu-id="de30f-205">For example, you can look up the grunt-contrib-clean package to get a documentation link that explains all of its parameters.</span></span>

### <a name="all-together-now"></a><span data-ttu-id="de30f-206">Teď všechno dohromady</span><span class="sxs-lookup"><span data-stu-id="de30f-206">All together now</span></span>

<span data-ttu-id="de30f-207">Použít Grunt `registerTask()` způsob spuštění řadu úkolů v určitém pořadí.</span><span class="sxs-lookup"><span data-stu-id="de30f-207">Use the Grunt `registerTask()` method to run a series of tasks in a particular sequence.</span></span> <span data-ttu-id="de30f-208">Například pro spuštění v příkladu výše uvedené kroky v pořadí, vyčistit -> concat -> jshint -> uglify, přidejte následující kód do modulu.</span><span class="sxs-lookup"><span data-stu-id="de30f-208">For example, to run the example steps above in the order clean -> concat -> jshint -> uglify, add the code below to the module.</span></span> <span data-ttu-id="de30f-209">Na stejné úrovni jako loadNpmTasks() volání mimo initConfig, měli byste přidat kód.</span><span class="sxs-lookup"><span data-stu-id="de30f-209">The code should be added to the same level as the loadNpmTasks() calls, outside initConfig.</span></span>

```javascript
grunt.registerTask("all", ['clean', 'concat', 'jshint', 'uglify']);
```

<span data-ttu-id="de30f-210">Nová úloha se zobrazí v Task Runner Explorer v části úlohy Alias.</span><span class="sxs-lookup"><span data-stu-id="de30f-210">The new task shows up in Task Runner Explorer under Alias Tasks.</span></span> <span data-ttu-id="de30f-211">Pravým tlačítkem a jej spustit stejně jako další úkoly.</span><span class="sxs-lookup"><span data-stu-id="de30f-211">You can right-click and run it just as you would other tasks.</span></span> <span data-ttu-id="de30f-212">`all` Úloha poběží `clean`, `concat`, `jshint` a `uglify`, v pořadí.</span><span class="sxs-lookup"><span data-stu-id="de30f-212">The `all` task will run `clean`, `concat`, `jshint` and `uglify`, in order.</span></span>

![úlohy alias grunt](using-grunt/_static/alias-tasks.png)

## <a name="watching-for-changes"></a><span data-ttu-id="de30f-214">Sledování změn</span><span class="sxs-lookup"><span data-stu-id="de30f-214">Watching for changes</span></span>

<span data-ttu-id="de30f-215">A `watch` úkolů udržuje přehled o soubory a adresáře.</span><span class="sxs-lookup"><span data-stu-id="de30f-215">A `watch` task keeps an eye on files and directories.</span></span> <span data-ttu-id="de30f-216">Hodinkami aktivuje úlohy automaticky, pokud zjistí změny.</span><span class="sxs-lookup"><span data-stu-id="de30f-216">The watch triggers tasks automatically if it detects changes.</span></span> <span data-ttu-id="de30f-217">Přidejte následující kód k initConfig a sledujte změny \*soubory JS v adresáři TypeScript.</span><span class="sxs-lookup"><span data-stu-id="de30f-217">Add the code below to initConfig to watch for changes to \*.js files in the TypeScript directory.</span></span> <span data-ttu-id="de30f-218">Pokud změníte soubor jazyka JavaScript, `watch` poběží `all` úloh.</span><span class="sxs-lookup"><span data-stu-id="de30f-218">If a JavaScript file is changed, `watch` will run the `all` task.</span></span>

```javascript
watch: {
  files: ["TypeScript/*.js"],
  tasks: ["all"]
}
```

<span data-ttu-id="de30f-219">Přidejte volání do `loadNpmTasks()` zobrazíte `watch` úkol v Task Runner Explorer.</span><span class="sxs-lookup"><span data-stu-id="de30f-219">Add a call to `loadNpmTasks()` to show the `watch` task in Task Runner Explorer.</span></span>

```javascript
grunt.loadNpmTasks('grunt-contrib-watch');
```

<span data-ttu-id="de30f-220">Klikněte pravým tlačítkem na sledování úkolů v Task Runner Explorer a v místní nabídce vyberte příkaz spustit.</span><span class="sxs-lookup"><span data-stu-id="de30f-220">Right-click the watch task in Task Runner Explorer and select Run from the context menu.</span></span> <span data-ttu-id="de30f-221">Příkazové okno zobrazující sledování úloha spuštěná se zobrazí "Čekání..." zpráva.</span><span class="sxs-lookup"><span data-stu-id="de30f-221">The command window that shows the watch task running will display a "Waiting…" message.</span></span> <span data-ttu-id="de30f-222">Otevřete jeden ze souborů TypeScript, přidejte mezeru a pak soubor uložte.</span><span class="sxs-lookup"><span data-stu-id="de30f-222">Open one of the TypeScript files, add a space, and then save the file.</span></span> <span data-ttu-id="de30f-223">To provede aktivaci úlohy sledování a aktivuje dalších úkolů ke spuštění v pořadí.</span><span class="sxs-lookup"><span data-stu-id="de30f-223">This will trigger the watch task and trigger the other tasks to run in order.</span></span> <span data-ttu-id="de30f-224">Následující snímek obrazovky ukazuje ukázkové spuštění.</span><span class="sxs-lookup"><span data-stu-id="de30f-224">The screenshot below shows a sample run.</span></span>

![spuštění výstupu úlohy](using-grunt/_static/watch-running.png)

## <a name="binding-to-visual-studio-events"></a><span data-ttu-id="de30f-226">Vazba na Visual Studio události</span><span class="sxs-lookup"><span data-stu-id="de30f-226">Binding to Visual Studio events</span></span>

<span data-ttu-id="de30f-227">Pokud chcete ručně spustit vaše úlohy pokaždé, když pracujete v sadě Visual Studio, můžete vytvořit vazbu na úloh **před sestavení**, **po sestavení**, **Vyčistit**, a  **Projekt Open** události.</span><span class="sxs-lookup"><span data-stu-id="de30f-227">Unless you want to manually start your tasks every time you work in Visual Studio, you can bind tasks to **Before Build**, **After Build**, **Clean**, and **Project Open** events.</span></span>

<span data-ttu-id="de30f-228">Pojďme vytvořit vazbu mezi `watch` tak, aby se spustí pokaždé, když se otevře v sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="de30f-228">Let’s bind `watch` so that it runs every time Visual Studio opens.</span></span> <span data-ttu-id="de30f-229">V Task Runner Explorer, klikněte pravým tlačítkem na sledování úkolů a vyberte **vazby > Otevřít projekt** v místní nabídce.</span><span class="sxs-lookup"><span data-stu-id="de30f-229">In Task Runner Explorer, right-click the watch task and select **Bindings > Project Open** from the context menu.</span></span>

![vázat úlohy k otevření projektu](using-grunt/_static/bindings-project-open.png)

<span data-ttu-id="de30f-231">Odebrat a znovu načíst projekt.</span><span class="sxs-lookup"><span data-stu-id="de30f-231">Unload and reload the project.</span></span> <span data-ttu-id="de30f-232">Když projekt znovu načten, spuštění úkolu watch automaticky spuštěn.</span><span class="sxs-lookup"><span data-stu-id="de30f-232">When the project loads again, the watch task will start running automatically.</span></span>

## <a name="summary"></a><span data-ttu-id="de30f-233">Souhrn</span><span class="sxs-lookup"><span data-stu-id="de30f-233">Summary</span></span>

<span data-ttu-id="de30f-234">Grunt je Spouštěč výkonné úloh, která umožňuje automatizovat většinu úloh sestavení klienta.</span><span class="sxs-lookup"><span data-stu-id="de30f-234">Grunt is a powerful task runner that can be used to automate most client-build tasks.</span></span> <span data-ttu-id="de30f-235">Grunt využívá NPM doručil příslušné balíčky a funkce nástroje integraci se sadou Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="de30f-235">Grunt leverages NPM to deliver its packages, and features tooling integration with Visual Studio.</span></span> <span data-ttu-id="de30f-236">Visual Studio Task Runner Explorer zjišťuje změny konfiguračních souborů a poskytuje pohodlné rozhraní pro spouštění úloh, zobrazit spuštěné úlohy a vázat úlohy k události aplikace Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="de30f-236">Visual Studio's Task Runner Explorer detects changes to configuration files and provides a convenient interface to run tasks, view running tasks, and bind tasks to Visual Studio events.</span></span>

---
title: Použití grunt v ASP.NET Core
author: rick-anderson
description: Použití grunt v ASP.NET Core
ms.author: riande
ms.date: 12/05/2019
uid: client-side/using-grunt
ms.openlocfilehash: e516b85da7e94d0c93be642086fede0a11fea3c2
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2019
ms.locfileid: "74879795"
---
# <a name="use-grunt-in-aspnet-core"></a><span data-ttu-id="9fdea-103">Použití grunt v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9fdea-103">Use Grunt in ASP.NET Core</span></span>

<span data-ttu-id="9fdea-104">Grunt je Spouštěč úloh JavaScriptu, který automatizuje skripty minifikace, kompilaci TypeScript, kvality kódu "Lint", pre-procesory šablon stylů CSS a stejně jako všechny opakující se Chore, které jsou potřeba k podpoře vývoje klienta.</span><span class="sxs-lookup"><span data-stu-id="9fdea-104">Grunt is a JavaScript task runner that automates script minification, TypeScript compilation, code quality "lint" tools, CSS pre-processors, and just about any repetitive chore that needs doing to support client development.</span></span> <span data-ttu-id="9fdea-105">Grunt je plně podporovaný v aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="9fdea-105">Grunt is fully supported in Visual Studio.</span></span>

<span data-ttu-id="9fdea-106">V tomto příkladu se jako výchozí bod používá prázdný projekt ASP.NET Core, který ukazuje, jak automatizovat proces sestavení klienta od začátku.</span><span class="sxs-lookup"><span data-stu-id="9fdea-106">This example uses an empty ASP.NET Core project as its starting point, to show how to automate the client build process from scratch.</span></span>

<span data-ttu-id="9fdea-107">Příklad dokončení čistí cílový adresář nasazení, kombinuje soubory jazyka JavaScript, kontroluje kvalitu kódu, zúžení obsahu souboru JavaScriptu a nasadí do kořenového adresáře vaší webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="9fdea-107">The finished example cleans the target deployment directory, combines JavaScript files, checks code quality, condenses JavaScript file content and deploys to the root of your web application.</span></span> <span data-ttu-id="9fdea-108">Budeme používat tyto balíčky:</span><span class="sxs-lookup"><span data-stu-id="9fdea-108">We will use the following packages:</span></span>

* <span data-ttu-id="9fdea-109">**grunt**: balíček Spouštěče úloh grunt.</span><span class="sxs-lookup"><span data-stu-id="9fdea-109">**grunt**: The Grunt task runner package.</span></span>

* <span data-ttu-id="9fdea-110">**grunt-contrib-Clean**: modul plug-in, který odebírá soubory nebo adresáře.</span><span class="sxs-lookup"><span data-stu-id="9fdea-110">**grunt-contrib-clean**: A plugin that removes files or directories.</span></span>

* <span data-ttu-id="9fdea-111">**grunt-contrib-jshint**: modul plug-in, který kontroluje kvalitu kódu JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="9fdea-111">**grunt-contrib-jshint**: A plugin that reviews JavaScript code quality.</span></span>

* <span data-ttu-id="9fdea-112">**grunt-contrib-Concat**: modul plug-in, který spojuje soubory do jednoho souboru.</span><span class="sxs-lookup"><span data-stu-id="9fdea-112">**grunt-contrib-concat**: A plugin that joins files into a single file.</span></span>

* <span data-ttu-id="9fdea-113">**grunt-contrib-uglify**: modul plug-in, který minifies JavaScript, aby se snížila velikost.</span><span class="sxs-lookup"><span data-stu-id="9fdea-113">**grunt-contrib-uglify**: A plugin that minifies JavaScript to reduce size.</span></span>

* <span data-ttu-id="9fdea-114">**grunt-contrib-Watch**: modul plug-in, který sleduje aktivitu souboru.</span><span class="sxs-lookup"><span data-stu-id="9fdea-114">**grunt-contrib-watch**: A plugin that watches file activity.</span></span>

## <a name="preparing-the-application"></a><span data-ttu-id="9fdea-115">Příprava aplikace</span><span class="sxs-lookup"><span data-stu-id="9fdea-115">Preparing the application</span></span>

<span data-ttu-id="9fdea-116">Začněte tím, že nastavíte novou prázdnou webovou aplikaci a přidáte ukázkové soubory TypeScript.</span><span class="sxs-lookup"><span data-stu-id="9fdea-116">To begin, set up a new empty web application and add TypeScript example files.</span></span> <span data-ttu-id="9fdea-117">Soubory TypeScript jsou automaticky zkompilovány do JavaScriptu pomocí výchozího nastavení sady Visual Studio a bude se jednat o náš nezpracovaný materiál pro zpracování pomocí grunt.</span><span class="sxs-lookup"><span data-stu-id="9fdea-117">TypeScript files are automatically compiled into JavaScript using default Visual Studio settings and will be our raw material to process using Grunt.</span></span>

1. <span data-ttu-id="9fdea-118">V aplikaci Visual Studio vytvořte novou `ASP.NET Web Application`.</span><span class="sxs-lookup"><span data-stu-id="9fdea-118">In Visual Studio, create a new `ASP.NET Web Application`.</span></span>

2. <span data-ttu-id="9fdea-119">V dialogovém okně **Nový projekt ASP.NET** vyberte ASP.NET Core **prázdnou** šablonu a klikněte na tlačítko OK.</span><span class="sxs-lookup"><span data-stu-id="9fdea-119">In the **New ASP.NET Project** dialog, select the ASP.NET Core **Empty** template and click the OK button.</span></span>

3. <span data-ttu-id="9fdea-120">V Průzkumník řešení zkontrolujte strukturu projektu.</span><span class="sxs-lookup"><span data-stu-id="9fdea-120">In the Solution Explorer, review the project structure.</span></span> <span data-ttu-id="9fdea-121">Složka `\src` obsahuje prázdné uzly `wwwroot` a `Dependencies`.</span><span class="sxs-lookup"><span data-stu-id="9fdea-121">The `\src` folder includes empty `wwwroot` and `Dependencies` nodes.</span></span>

    ![prázdné webové řešení](using-grunt/_static/grunt-solution-explorer.png)

4. <span data-ttu-id="9fdea-123">Do adresáře projektu přidejte novou složku s názvem `TypeScript`.</span><span class="sxs-lookup"><span data-stu-id="9fdea-123">Add a new folder named `TypeScript` to your project directory.</span></span>

5. <span data-ttu-id="9fdea-124">Před přidáním všech souborů se ujistěte, že má Visual Studio zaškrtnutou možnost kompilovat při uložení pro soubory TypeScript.</span><span class="sxs-lookup"><span data-stu-id="9fdea-124">Before adding any files, make sure that Visual Studio has the option 'compile on save' for TypeScript files checked.</span></span> <span data-ttu-id="9fdea-125">Přejděte na **nástroje** > **Možnosti** > **textový editor** > **TypeScript** > **Project**:</span><span class="sxs-lookup"><span data-stu-id="9fdea-125">Navigate to **Tools** > **Options** > **Text Editor** > **Typescript** > **Project**:</span></span>

    ![možnosti nastavení automatické kompilace souborů TypeScriptu](using-grunt/_static/typescript-options.png)

6. <span data-ttu-id="9fdea-127">Klikněte pravým tlačítkem na adresář `TypeScript` a v místní nabídce vyberte **přidat > nová položka** .</span><span class="sxs-lookup"><span data-stu-id="9fdea-127">Right-click the `TypeScript` directory and select **Add > New Item** from the context menu.</span></span> <span data-ttu-id="9fdea-128">Vyberte položku **souboru JavaScriptu** a pojmenujte soubor *chuti. TS* (poznamenejte si příponu \*. TS).</span><span class="sxs-lookup"><span data-stu-id="9fdea-128">Select the **JavaScript file** item and name the file *Tastes.ts* (note the \*.ts extension).</span></span> <span data-ttu-id="9fdea-129">Zkopírujte řádek kódu TypeScript níže do souboru (při uložení se zobrazí nový soubor *. js* se zdrojem JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="9fdea-129">Copy the line of TypeScript code below into the file (when you save, a new *Tastes.js* file will appear with the JavaScript source).</span></span>

    ```typescript
    enum Tastes { Sweet, Sour, Salty, Bitter }
    ```

7. <span data-ttu-id="9fdea-130">Přidejte do adresáře **TypeScript** druhý soubor a pojmenujte ho `Food.ts`.</span><span class="sxs-lookup"><span data-stu-id="9fdea-130">Add a second file to the **TypeScript** directory and name it `Food.ts`.</span></span> <span data-ttu-id="9fdea-131">Zkopírujte následující kód do souboru.</span><span class="sxs-lookup"><span data-stu-id="9fdea-131">Copy the code below into the file.</span></span>

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

## <a name="configuring-npm"></a><span data-ttu-id="9fdea-132">Konfigurace NPM</span><span class="sxs-lookup"><span data-stu-id="9fdea-132">Configuring NPM</span></span>

<span data-ttu-id="9fdea-133">Dále nakonfigurujte NPM tak, aby stahoval grunt a Grunt-Tasks.</span><span class="sxs-lookup"><span data-stu-id="9fdea-133">Next, configure NPM to download grunt and grunt-tasks.</span></span>

1. <span data-ttu-id="9fdea-134">V Průzkumník řešení klikněte pravým tlačítkem myši na projekt a vyberte **přidat > novou položku** z kontextové nabídky.</span><span class="sxs-lookup"><span data-stu-id="9fdea-134">In the Solution Explorer, right-click the project and select **Add > New Item** from the context menu.</span></span> <span data-ttu-id="9fdea-135">Vyberte položku **konfiguračního souboru npm** , ponechejte výchozí název, *Package. JSON*a klikněte na tlačítko **Přidat** .</span><span class="sxs-lookup"><span data-stu-id="9fdea-135">Select the **NPM configuration file** item, leave the default name, *package.json*, and click the **Add** button.</span></span>

2. <span data-ttu-id="9fdea-136">V souboru *Package. JSON* uvnitř `devDependencies` složených závorkách objektů zadejte "grunt".</span><span class="sxs-lookup"><span data-stu-id="9fdea-136">In the *package.json* file, inside the `devDependencies` object braces, enter "grunt".</span></span> <span data-ttu-id="9fdea-137">V seznamu IntelliSense vyberte `grunt` a stiskněte klávesu ENTER.</span><span class="sxs-lookup"><span data-stu-id="9fdea-137">Select `grunt` from the Intellisense list and press the Enter key.</span></span> <span data-ttu-id="9fdea-138">Sada Visual Studio nabídne název balíčku grunt a přidá dvojtečku.</span><span class="sxs-lookup"><span data-stu-id="9fdea-138">Visual Studio will quote the grunt package name, and add a colon.</span></span> <span data-ttu-id="9fdea-139">Napravo od dvojtečky vyberte nejnovější stabilní verzi balíčku z horní části seznamu technologie IntelliSense (stiskněte `Ctrl-Space`, pokud se IntelliSense nezobrazí).</span><span class="sxs-lookup"><span data-stu-id="9fdea-139">To the right of the colon, select the latest stable version of the package from the top of the Intellisense list (press `Ctrl-Space` if Intellisense doesn't appear).</span></span>

    ![grunt IntelliSense](using-grunt/_static/devdependencies-grunt.png)

    > [!NOTE]
    > <span data-ttu-id="9fdea-141">NPM používá [sémantickou správu verzí](https://semver.org/) k uspořádání závislostí.</span><span class="sxs-lookup"><span data-stu-id="9fdea-141">NPM uses [semantic versioning](https://semver.org/) to organize dependencies.</span></span> <span data-ttu-id="9fdea-142">Sémantická verze, označovaná také jako SemVer, identifikuje balíčky pomocí schématu číslování \<hlavní >.\<vedlejší >. >\<patch.</span><span class="sxs-lookup"><span data-stu-id="9fdea-142">Semantic versioning, also known as SemVer, identifies packages with the numbering scheme \<major>.\<minor>.\<patch>.</span></span> <span data-ttu-id="9fdea-143">Technologie IntelliSense zjednodušuje sémantickou správu verzí zobrazením pouze několika běžných možností.</span><span class="sxs-lookup"><span data-stu-id="9fdea-143">Intellisense simplifies semantic versioning by showing only a few common choices.</span></span> <span data-ttu-id="9fdea-144">Horní položka v seznamu IntelliSense (0.4.5 v příkladu výše) je považována za nejnovější stabilní verzi balíčku.</span><span class="sxs-lookup"><span data-stu-id="9fdea-144">The top item in the Intellisense list (0.4.5 in the example above) is considered the latest stable version of the package.</span></span> <span data-ttu-id="9fdea-145">Symbol stříšky (^) odpovídá nejnovější hlavní verzi a znak tilda (~) odpovídá nejnovější dílčí verzi.</span><span class="sxs-lookup"><span data-stu-id="9fdea-145">The caret (^) symbol matches the most recent major version and the tilde (~) matches the most recent minor version.</span></span> <span data-ttu-id="9fdea-146">Podívejte se na referenční informace o [analyzátoru npm semver verze](https://www.npmjs.com/package/semver) jako průvodce úplným expressivity, které poskytuje semver.</span><span class="sxs-lookup"><span data-stu-id="9fdea-146">See the [NPM semver version parser reference](https://www.npmjs.com/package/semver) as a guide to the full expressivity that SemVer provides.</span></span>

3. <span data-ttu-id="9fdea-147">Přidejte další závislosti pro načtení balíčků grunt-contrib-\* pro *Vyčištění*, *jshint*, *Concat*, *uglify*a *Sledujte* , jak je znázorněno v následujícím příkladu.</span><span class="sxs-lookup"><span data-stu-id="9fdea-147">Add more dependencies to load grunt-contrib-\* packages for *clean*, *jshint*, *concat*, *uglify*, and *watch* as shown in the example below.</span></span> <span data-ttu-id="9fdea-148">Verze se nemusí shodovat s příkladem.</span><span class="sxs-lookup"><span data-stu-id="9fdea-148">The versions don't need to match the example.</span></span>

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

4. <span data-ttu-id="9fdea-149">Uložte soubor *Package. JSON* .</span><span class="sxs-lookup"><span data-stu-id="9fdea-149">Save the *package.json* file.</span></span>

<span data-ttu-id="9fdea-150">Balíčky pro každou položku `devDependencies` se stáhnou spolu se všemi soubory, které každý balíček vyžaduje.</span><span class="sxs-lookup"><span data-stu-id="9fdea-150">The packages for each `devDependencies` item will download, along with any files that each package requires.</span></span> <span data-ttu-id="9fdea-151">Soubory balíčku můžete najít v adresáři *node_modules* tím, že v **Průzkumník řešení**povolíte tlačítko **Zobrazit všechny soubory** .</span><span class="sxs-lookup"><span data-stu-id="9fdea-151">You can find the package files in the *node_modules* directory by enabling the **Show All Files** button in **Solution Explorer**.</span></span>

![grunt node_modules](using-grunt/_static/node-modules.png)

> [!NOTE]
> <span data-ttu-id="9fdea-153">Pokud potřebujete, můžete závislosti v **Průzkumník řešení** ručně obnovit kliknutím pravým tlačítkem na `Dependencies\NPM` a výběrem možnosti nabídky **obnovit balíčky** .</span><span class="sxs-lookup"><span data-stu-id="9fdea-153">If you need to, you can manually restore dependencies in **Solution Explorer** by right-clicking on `Dependencies\NPM` and selecting the **Restore Packages** menu option.</span></span>

![obnovit balíčky](using-grunt/_static/restore-packages.png)

## <a name="configuring-grunt"></a><span data-ttu-id="9fdea-155">Konfigurace grunt</span><span class="sxs-lookup"><span data-stu-id="9fdea-155">Configuring Grunt</span></span>

<span data-ttu-id="9fdea-156">Grunt je nakonfigurován pomocí manifestu s názvem *Gruntfile. js* , který definuje, načítá a registruje úlohy, které lze spustit ručně nebo které jsou nakonfigurovány pro automatické spouštění na základě událostí v aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="9fdea-156">Grunt is configured using a manifest named *Gruntfile.js* that defines, loads and registers tasks that can be run manually or configured to run automatically based on events in Visual Studio.</span></span>

1. <span data-ttu-id="9fdea-157">Klikněte pravým tlačítkem na projekt a vyberte **přidat** > **Nová položka**.</span><span class="sxs-lookup"><span data-stu-id="9fdea-157">Right-click the project and select **Add** > **New Item**.</span></span> <span data-ttu-id="9fdea-158">Vyberte šablonu položky **soubor JavaScriptu** , změňte název na *Gruntfile. js*a klikněte na tlačítko **Přidat** .</span><span class="sxs-lookup"><span data-stu-id="9fdea-158">Select the **JavaScript File** item template, change the name to *Gruntfile.js*, and click the **Add** button.</span></span>

1. <span data-ttu-id="9fdea-159">Do *Gruntfile. js*přidejte následující kód.</span><span class="sxs-lookup"><span data-stu-id="9fdea-159">Add the following code to *Gruntfile.js*.</span></span> <span data-ttu-id="9fdea-160">Funkce `initConfig` nastaví možnosti pro každý balíček a zbytek modulu načítá a registruje úlohy.</span><span class="sxs-lookup"><span data-stu-id="9fdea-160">The `initConfig` function sets options for each package, and the remainder of the module loads and register tasks.</span></span>

   ```javascript
   module.exports = function (grunt) {
     grunt.initConfig({
     });
   };
   ```

1. <span data-ttu-id="9fdea-161">Uvnitř funkce `initConfig` přidejte možnosti úlohy `clean`, jak je znázorněno v příkladu *Gruntfile. js* níže.</span><span class="sxs-lookup"><span data-stu-id="9fdea-161">Inside the `initConfig` function, add options for the `clean` task as shown in the example *Gruntfile.js* below.</span></span> <span data-ttu-id="9fdea-162">Úkol `clean` přijímá pole řetězců adresáře.</span><span class="sxs-lookup"><span data-stu-id="9fdea-162">The `clean` task accepts an array of directory strings.</span></span> <span data-ttu-id="9fdea-163">Tato úloha odebere soubory z *wwwroot/lib* a odebere celý adresář */TEMP* .</span><span class="sxs-lookup"><span data-stu-id="9fdea-163">This task removes files from *wwwroot/lib* and removes the entire */temp* directory.</span></span>

    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
        clean: ["wwwroot/lib/*", "temp/"],
      });
    };
    ```

1. <span data-ttu-id="9fdea-164">Pod funkcí `initConfig` přidejte volání `grunt.loadNpmTasks`.</span><span class="sxs-lookup"><span data-stu-id="9fdea-164">Below the `initConfig` function, add a call to `grunt.loadNpmTasks`.</span></span> <span data-ttu-id="9fdea-165">Tím se úkol spustitelný ze sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="9fdea-165">This will make the task runnable from Visual Studio.</span></span>

    ```javascript
    grunt.loadNpmTasks("grunt-contrib-clean");
    ```

1. <span data-ttu-id="9fdea-166">Uložte *Gruntfile. js*.</span><span class="sxs-lookup"><span data-stu-id="9fdea-166">Save *Gruntfile.js*.</span></span> <span data-ttu-id="9fdea-167">Soubor by měl vypadat přibližně jako snímek obrazovky níže.</span><span class="sxs-lookup"><span data-stu-id="9fdea-167">The file should look something like the screenshot below.</span></span>

    ![počáteční gruntfile](using-grunt/_static/gruntfile-js-initial.png)

1. <span data-ttu-id="9fdea-169">Klikněte pravým tlačítkem na *Gruntfile. js* a v místní nabídce vyberte **Průzkumník Spouštěče úloh** .</span><span class="sxs-lookup"><span data-stu-id="9fdea-169">Right-click *Gruntfile.js* and select **Task Runner Explorer** from the context menu.</span></span> <span data-ttu-id="9fdea-170">Otevře se okno **Průzkumník Spouštěče úloh** .</span><span class="sxs-lookup"><span data-stu-id="9fdea-170">The **Task Runner Explorer** window will open.</span></span>

    ![Nabídka Průzkumníka Spouštěče úloh](using-grunt/_static/task-runner-explorer-menu.png)

1. <span data-ttu-id="9fdea-172">Ověřte, že `clean` zobrazuje v části **úlohy** v **Průzkumníkovi Spouštěče úloh**.</span><span class="sxs-lookup"><span data-stu-id="9fdea-172">Verify that `clean` shows under **Tasks** in the **Task Runner Explorer**.</span></span>

    ![seznam úkolů v Průzkumníkovi Spouštěče úloh](using-grunt/_static/task-runner-explorer-tasks.png)

1. <span data-ttu-id="9fdea-174">Klikněte pravým tlačítkem na úlohu vyčistit a v místní nabídce vyberte **Spustit** .</span><span class="sxs-lookup"><span data-stu-id="9fdea-174">Right-click the clean task and select **Run** from the context menu.</span></span> <span data-ttu-id="9fdea-175">V příkazovém okně se zobrazuje průběh úkolu.</span><span class="sxs-lookup"><span data-stu-id="9fdea-175">A command window displays progress of the task.</span></span>

    ![Průzkumník Spouštěče úloh – spustit čištění úlohy](using-grunt/_static/task-runner-explorer-run-clean.png)

    > [!NOTE]
    > <span data-ttu-id="9fdea-177">Neexistují žádné soubory ani adresáře k vyčištění.</span><span class="sxs-lookup"><span data-stu-id="9fdea-177">There are no files or directories to clean yet.</span></span> <span data-ttu-id="9fdea-178">Pokud chcete, můžete je ručně vytvořit v Průzkumník řešení a pak spustit čistou úlohu jako test.</span><span class="sxs-lookup"><span data-stu-id="9fdea-178">If you like, you can manually create them in the Solution Explorer and then run the clean task as a test.</span></span>

1. <span data-ttu-id="9fdea-179">Ve funkci `initConfig` přidejte položku pro `concat` pomocí kódu níže.</span><span class="sxs-lookup"><span data-stu-id="9fdea-179">In the `initConfig` function, add an entry for `concat` using the code below.</span></span>

    <span data-ttu-id="9fdea-180">Pole vlastností `src` obsahuje seznam souborů, které mají být kombinovány, v pořadí, ve kterém by měly být kombinovány.</span><span class="sxs-lookup"><span data-stu-id="9fdea-180">The `src` property array lists files to combine, in the order that they should be combined.</span></span> <span data-ttu-id="9fdea-181">Vlastnost `dest` přiřadí cestu k kombinovanému souboru, který je vytvořen.</span><span class="sxs-lookup"><span data-stu-id="9fdea-181">The `dest` property assigns the path to the combined file that's produced.</span></span>

    ```javascript
    concat: {
      all: {
        src: ['TypeScript/Tastes.js', 'TypeScript/Food.js'],
        dest: 'temp/combined.js'
      }
    },
    ```

    > [!NOTE]
    > <span data-ttu-id="9fdea-182">Vlastnost `all` v kódu výše je název cíle.</span><span class="sxs-lookup"><span data-stu-id="9fdea-182">The `all` property in the code above is the name of a target.</span></span> <span data-ttu-id="9fdea-183">Cíle se používají v některých úlohách grunt k povolení více prostředí sestavení.</span><span class="sxs-lookup"><span data-stu-id="9fdea-183">Targets are used in some Grunt tasks to allow multiple build environments.</span></span> <span data-ttu-id="9fdea-184">Můžete zobrazit předdefinované cíle pomocí technologie IntelliSense nebo přiřadit vlastní.</span><span class="sxs-lookup"><span data-stu-id="9fdea-184">You can view the built-in targets using IntelliSense or assign your own.</span></span>

1. <span data-ttu-id="9fdea-185">Pomocí níže uvedeného kódu přidejte úlohu `jshint`.</span><span class="sxs-lookup"><span data-stu-id="9fdea-185">Add the `jshint` task using the code below.</span></span>

    <span data-ttu-id="9fdea-186">Nástroj jshint `code-quality` se spustí pro všechny soubory JavaScriptu nalezené v *dočasném* adresáři.</span><span class="sxs-lookup"><span data-stu-id="9fdea-186">The jshint `code-quality` utility is run against every JavaScript file found in the *temp* directory.</span></span>

    ```javascript
    jshint: {
      files: ['temp/*.js'],
      options: {
        '-W069': false,
      }
    },
    ```

    > [!NOTE]
    > <span data-ttu-id="9fdea-187">Možnost "-W069" je chyba vytvořená jshint, když JavaScript používá syntaxi závorky k přiřazení vlastnosti namísto zápisu tečk, tj. `Tastes["Sweet"]` místo `Tastes.Sweet`.</span><span class="sxs-lookup"><span data-stu-id="9fdea-187">The option "-W069" is an error produced by jshint when JavaScript uses bracket syntax to assign a property instead of dot notation, i.e. `Tastes["Sweet"]` instead of `Tastes.Sweet`.</span></span> <span data-ttu-id="9fdea-188">Možnost vypne upozornění, aby zbytek procesu mohl pokračovat.</span><span class="sxs-lookup"><span data-stu-id="9fdea-188">The option turns off the warning to allow the rest of the process to continue.</span></span>

1. <span data-ttu-id="9fdea-189">Pomocí níže uvedeného kódu přidejte úlohu `uglify`.</span><span class="sxs-lookup"><span data-stu-id="9fdea-189">Add the `uglify` task using the code below.</span></span>

    <span data-ttu-id="9fdea-190">Úloha minifies soubor *kombinovaného. js* , který se nachází v dočasném adresáři, a vytvoří soubor výsledku v wwwroot/lib za standardní konvencí pojmenování *\<název souboru\>. min. js*.</span><span class="sxs-lookup"><span data-stu-id="9fdea-190">The task minifies the *combined.js* file found in the temp directory and creates the result file in wwwroot/lib following the standard naming convention *\<file name\>.min.js*.</span></span>

    ```javascript
    uglify: {
     all: {
       src: ['temp/combined.js'],
       dest: 'wwwroot/lib/combined.min.js'
     }
    },
    ```

1. <span data-ttu-id="9fdea-191">Pod voláním `grunt.loadNpmTasks`, které načítá `grunt-contrib-clean`, zahrňte stejné volání pro jshint, Concat a uglify pomocí níže uvedeného kódu.</span><span class="sxs-lookup"><span data-stu-id="9fdea-191">Under the call to `grunt.loadNpmTasks` that loads `grunt-contrib-clean`, include the same call for jshint, concat, and uglify using the code below.</span></span>

    ```javascript
    grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    ```

1. <span data-ttu-id="9fdea-192">Uložte *Gruntfile. js*.</span><span class="sxs-lookup"><span data-stu-id="9fdea-192">Save *Gruntfile.js*.</span></span> <span data-ttu-id="9fdea-193">Soubor by měl vypadat nějak podobně jako v následujícím příkladu.</span><span class="sxs-lookup"><span data-stu-id="9fdea-193">The file should look something like the example below.</span></span>

    ![Příklad úplného souboru grunt](using-grunt/_static/gruntfile-js-complete.png)

1. <span data-ttu-id="9fdea-195">Všimněte si, že seznam úkolů v **Průzkumníkovi Spouštěče úloh** obsahuje `clean`, `concat`, `jshint` a `uglify` úkoly.</span><span class="sxs-lookup"><span data-stu-id="9fdea-195">Notice that the **Task Runner Explorer** Tasks list includes `clean`, `concat`, `jshint` and `uglify` tasks.</span></span> <span data-ttu-id="9fdea-196">Spusťte jednotlivé úlohy v daném pořadí a sledujte výsledky v **Průzkumník řešení**.</span><span class="sxs-lookup"><span data-stu-id="9fdea-196">Run each task in order and observe the results in **Solution Explorer**.</span></span> <span data-ttu-id="9fdea-197">Každý úkol by měl běžet bez chyb.</span><span class="sxs-lookup"><span data-stu-id="9fdea-197">Each task should run without errors.</span></span>

    ![Průzkumník Spouštěče úloh spouští jednotlivé úlohy.](using-grunt/_static/task-runner-explorer-run-each-task.png)

    <span data-ttu-id="9fdea-199">Úkol Concat vytvoří nový kombinovaný soubor *. js* a umístí ho do dočasného adresáře.</span><span class="sxs-lookup"><span data-stu-id="9fdea-199">The concat task creates a new *combined.js* file and places it into the temp directory.</span></span> <span data-ttu-id="9fdea-200">Úloha `jshint` se jednoduše spustí a nevyprodukuje výstup.</span><span class="sxs-lookup"><span data-stu-id="9fdea-200">The `jshint` task simply runs and doesn't produce output.</span></span> <span data-ttu-id="9fdea-201">Úloha `uglify` vytvoří nový soubor s *příponou. min. js* a umístí jej do *wwwroot/lib*.</span><span class="sxs-lookup"><span data-stu-id="9fdea-201">The `uglify` task creates a new *combined.min.js* file and places it into *wwwroot/lib*.</span></span> <span data-ttu-id="9fdea-202">Po dokončení by řešení mělo vypadat přibližně jako snímek obrazovky níže:</span><span class="sxs-lookup"><span data-stu-id="9fdea-202">On completion, the solution should look something like the screenshot below:</span></span>

    ![Průzkumník řešení po všech úlohách](using-grunt/_static/solution-explorer-after-all-tasks.png)

    > [!NOTE]
    > <span data-ttu-id="9fdea-204">Další informace o možnostech jednotlivých balíčků najdete v [https://www.npmjs.com/](https://www.npmjs.com/) a v poli hledání na hlavní stránce vyhledejte název balíčku.</span><span class="sxs-lookup"><span data-stu-id="9fdea-204">For more information on the options for each package, visit [https://www.npmjs.com/](https://www.npmjs.com/) and lookup the package name in the search box on the main page.</span></span> <span data-ttu-id="9fdea-205">Můžete například vyhledat balíček grunt-contrib-Clean a získat odkaz na dokumentaci, který vysvětluje všechny jeho parametry.</span><span class="sxs-lookup"><span data-stu-id="9fdea-205">For example, you can look up the grunt-contrib-clean package to get a documentation link that explains all of its parameters.</span></span>

### <a name="all-together-now"></a><span data-ttu-id="9fdea-206">Teď vše pohromadě</span><span class="sxs-lookup"><span data-stu-id="9fdea-206">All together now</span></span>

<span data-ttu-id="9fdea-207">Pomocí metody grunt `registerTask()` můžete v určité sekvenci spustit řadu úkolů.</span><span class="sxs-lookup"><span data-stu-id="9fdea-207">Use the Grunt `registerTask()` method to run a series of tasks in a particular sequence.</span></span> <span data-ttu-id="9fdea-208">Pokud například chcete spustit příklad výše uvedených kroků v pořadí vyčistit-> Concat-> jshint-> uglify, přidejte kód níže do modulu.</span><span class="sxs-lookup"><span data-stu-id="9fdea-208">For example, to run the example steps above in the order clean -> concat -> jshint -> uglify, add the code below to the module.</span></span> <span data-ttu-id="9fdea-209">Kód by měl být přidán na stejnou úroveň jako volání loadNpmTasks (), mimo initConfig.</span><span class="sxs-lookup"><span data-stu-id="9fdea-209">The code should be added to the same level as the loadNpmTasks() calls, outside initConfig.</span></span>

```javascript
grunt.registerTask("all", ['clean', 'concat', 'jshint', 'uglify']);
```

<span data-ttu-id="9fdea-210">Nový úkol se zobrazí v Průzkumníku Spouštěče úloh v části úkoly s aliasem.</span><span class="sxs-lookup"><span data-stu-id="9fdea-210">The new task shows up in Task Runner Explorer under Alias Tasks.</span></span> <span data-ttu-id="9fdea-211">Můžete kliknout pravým tlačítkem a spustit stejným způsobem jako jiné úkoly.</span><span class="sxs-lookup"><span data-stu-id="9fdea-211">You can right-click and run it just as you would other tasks.</span></span> <span data-ttu-id="9fdea-212">Úloha `all` spustí `clean`, `concat`, `jshint` a `uglify`v uvedeném pořadí.</span><span class="sxs-lookup"><span data-stu-id="9fdea-212">The `all` task will run `clean`, `concat`, `jshint` and `uglify`, in order.</span></span>

![úkoly grunt aliasu](using-grunt/_static/alias-tasks.png)

## <a name="watching-for-changes"></a><span data-ttu-id="9fdea-214">Sledování změn</span><span class="sxs-lookup"><span data-stu-id="9fdea-214">Watching for changes</span></span>

<span data-ttu-id="9fdea-215">`watch` úkol udržuje oči pro soubory a adresáře.</span><span class="sxs-lookup"><span data-stu-id="9fdea-215">A `watch` task keeps an eye on files and directories.</span></span> <span data-ttu-id="9fdea-216">Pokud se změny detekuje, sledování se automaticky aktivuje.</span><span class="sxs-lookup"><span data-stu-id="9fdea-216">The watch triggers tasks automatically if it detects changes.</span></span> <span data-ttu-id="9fdea-217">Přidáním níže uvedeného kódu můžete initConfig sledovat změny v souborech. js \*. js v adresáři TypeScript.</span><span class="sxs-lookup"><span data-stu-id="9fdea-217">Add the code below to initConfig to watch for changes to \*.js files in the TypeScript directory.</span></span> <span data-ttu-id="9fdea-218">Pokud dojde ke změně souboru JavaScriptu, `watch` spustí úlohu `all`.</span><span class="sxs-lookup"><span data-stu-id="9fdea-218">If a JavaScript file is changed, `watch` will run the `all` task.</span></span>

```javascript
watch: {
  files: ["TypeScript/*.js"],
  tasks: ["all"]
}
```

<span data-ttu-id="9fdea-219">Přidejte volání `loadNpmTasks()` pro zobrazení úlohy `watch` v Průzkumníkovi Spouštěče úloh.</span><span class="sxs-lookup"><span data-stu-id="9fdea-219">Add a call to `loadNpmTasks()` to show the `watch` task in Task Runner Explorer.</span></span>

```javascript
grunt.loadNpmTasks('grunt-contrib-watch');
```

<span data-ttu-id="9fdea-220">V Průzkumníku Spouštěče úloh klikněte pravým tlačítkem myši na úlohu kukátka a v místní nabídce vyberte spustit.</span><span class="sxs-lookup"><span data-stu-id="9fdea-220">Right-click the watch task in Task Runner Explorer and select Run from the context menu.</span></span> <span data-ttu-id="9fdea-221">Příkazové okno, které zobrazuje spuštěný úkol kukátka, zobrazí "čekání..." Zpráva.</span><span class="sxs-lookup"><span data-stu-id="9fdea-221">The command window that shows the watch task running will display a "Waiting…" message.</span></span> <span data-ttu-id="9fdea-222">Otevřete jeden ze souborů TypeScriptu, přidejte mezeru a pak soubor uložte.</span><span class="sxs-lookup"><span data-stu-id="9fdea-222">Open one of the TypeScript files, add a space, and then save the file.</span></span> <span data-ttu-id="9fdea-223">Tím se aktivuje úloha kukátka a spustí se další úlohy, které se spustí v daném pořadí.</span><span class="sxs-lookup"><span data-stu-id="9fdea-223">This will trigger the watch task and trigger the other tasks to run in order.</span></span> <span data-ttu-id="9fdea-224">Níže uvedený snímek obrazovky ukazuje vzorový běh.</span><span class="sxs-lookup"><span data-stu-id="9fdea-224">The screenshot below shows a sample run.</span></span>

![výstup spuštěných úloh](using-grunt/_static/watch-running.png)

## <a name="binding-to-visual-studio-events"></a><span data-ttu-id="9fdea-226">Vytvoření vazby na události sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9fdea-226">Binding to Visual Studio events</span></span>

<span data-ttu-id="9fdea-227">Pokud nechcete spouštět úlohy ručně při každé práci v aplikaci Visual Studio, navažte úkoly na **před sestavením**, **po sestavení**, **Vyčištění**a otevřených událostech **projektu** .</span><span class="sxs-lookup"><span data-stu-id="9fdea-227">Unless you want to manually start your tasks every time you work in Visual Studio, bind tasks to **Before Build**, **After Build**, **Clean**, and **Project Open** events.</span></span>

<span data-ttu-id="9fdea-228">Vytvoří vazby `watch` tak, že se spustí při každém otevření sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="9fdea-228">Bind `watch` so that it runs every time Visual Studio opens.</span></span> <span data-ttu-id="9fdea-229">V Průzkumníku Spouštěče úloh klikněte pravým tlačítkem myši na úlohu kukátka a vyberte **vazby** > **projekt otevřen** z kontextové nabídky.</span><span class="sxs-lookup"><span data-stu-id="9fdea-229">In Task Runner Explorer, right-click the watch task and select **Bindings** > **Project Open** from the context menu.</span></span>

![navázání úlohy k otevření projektu](using-grunt/_static/bindings-project-open.png)

<span data-ttu-id="9fdea-231">Uvolněte a znovu načtěte projekt.</span><span class="sxs-lookup"><span data-stu-id="9fdea-231">Unload and reload the project.</span></span> <span data-ttu-id="9fdea-232">Po opětovném načtení projektu se úloha kukátka začne automaticky spouštět.</span><span class="sxs-lookup"><span data-stu-id="9fdea-232">When the project loads again, the watch task starts running automatically.</span></span>

## <a name="summary"></a><span data-ttu-id="9fdea-233">Přehled</span><span class="sxs-lookup"><span data-stu-id="9fdea-233">Summary</span></span>

<span data-ttu-id="9fdea-234">Grunt je výkonný Spouštěč úloh, který se dá použít k automatizaci většiny úloh sestavení klientů.</span><span class="sxs-lookup"><span data-stu-id="9fdea-234">Grunt is a powerful task runner that can be used to automate most client-build tasks.</span></span> <span data-ttu-id="9fdea-235">Grunt využívá NPM k doručování svých balíčků a funkcí pro integraci nástrojů se sadou Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="9fdea-235">Grunt leverages NPM to deliver its packages, and features tooling integration with Visual Studio.</span></span> <span data-ttu-id="9fdea-236">Průzkumník Spouštěče úloh sady Visual Studio detekuje změny konfiguračních souborů a poskytuje praktické rozhraní pro spouštění úkolů, zobrazení spuštěných úloh a vázání úloh k událostem sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="9fdea-236">Visual Studio's Task Runner Explorer detects changes to configuration files and provides a convenient interface to run tasks, view running tasks, and bind tasks to Visual Studio events.</span></span>

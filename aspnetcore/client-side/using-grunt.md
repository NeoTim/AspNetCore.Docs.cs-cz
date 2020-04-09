---
title: Použití Grunt v ASP.NET Core
author: rick-anderson
description: Použití Grunt v ASP.NET Core
ms.author: riande
ms.date: 12/05/2019
uid: client-side/using-grunt
ms.openlocfilehash: e516b85da7e94d0c93be642086fede0a11fea3c2
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657589"
---
# <a name="use-grunt-in-aspnet-core"></a><span data-ttu-id="2b512-103">Použití Grunt v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2b512-103">Use Grunt in ASP.NET Core</span></span>

<span data-ttu-id="2b512-104">Grunt je javascriptový plánovač úloh, který automatizuje minifikaci skriptů, kompilaci TypeScript, nástroje pro kvalitu kódu "lint", předprocesory CSS a téměř jakoukoli opakující se práci, která potřebuje dělat, aby podpořila vývoj klientů.</span><span class="sxs-lookup"><span data-stu-id="2b512-104">Grunt is a JavaScript task runner that automates script minification, TypeScript compilation, code quality "lint" tools, CSS pre-processors, and just about any repetitive chore that needs doing to support client development.</span></span> <span data-ttu-id="2b512-105">Grunt je plně podporován v sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="2b512-105">Grunt is fully supported in Visual Studio.</span></span>

<span data-ttu-id="2b512-106">Tento příklad používá prázdný ASP.NET core projektu jako výchozí bod, ukázat, jak automatizovat proces sestavení klienta od začátku.</span><span class="sxs-lookup"><span data-stu-id="2b512-106">This example uses an empty ASP.NET Core project as its starting point, to show how to automate the client build process from scratch.</span></span>

<span data-ttu-id="2b512-107">Hotový příklad čistí cílový adresář nasazení, kombinuje soubory JavaScriptu, kontroluje kvalitu kódu, kondenzuje obsah souboru JavaScript a nasazuje se do kořenového adresáře webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="2b512-107">The finished example cleans the target deployment directory, combines JavaScript files, checks code quality, condenses JavaScript file content and deploys to the root of your web application.</span></span> <span data-ttu-id="2b512-108">Použijeme následující balíčky:</span><span class="sxs-lookup"><span data-stu-id="2b512-108">We will use the following packages:</span></span>

* <span data-ttu-id="2b512-109">**grunt**: Grunt úkol běžec balíček.</span><span class="sxs-lookup"><span data-stu-id="2b512-109">**grunt**: The Grunt task runner package.</span></span>

* <span data-ttu-id="2b512-110">**grunt-contrib-clean**: Plugin, který odstraňuje soubory nebo adresáře.</span><span class="sxs-lookup"><span data-stu-id="2b512-110">**grunt-contrib-clean**: A plugin that removes files or directories.</span></span>

* <span data-ttu-id="2b512-111">**grunt-contrib-jshint**: plugin, který rekapituluje kvalitu kódu JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="2b512-111">**grunt-contrib-jshint**: A plugin that reviews JavaScript code quality.</span></span>

* <span data-ttu-id="2b512-112">**grunt-contrib-concat**: Plugin, který spojuje soubory do jednoho souboru.</span><span class="sxs-lookup"><span data-stu-id="2b512-112">**grunt-contrib-concat**: A plugin that joins files into a single file.</span></span>

* <span data-ttu-id="2b512-113">**grunt-contrib-uglify**: Plugin, který minifies JavaScript ke snížení velikosti.</span><span class="sxs-lookup"><span data-stu-id="2b512-113">**grunt-contrib-uglify**: A plugin that minifies JavaScript to reduce size.</span></span>

* <span data-ttu-id="2b512-114">**grunt-contrib-watch**: Plugin, který sleduje aktivitu souborů.</span><span class="sxs-lookup"><span data-stu-id="2b512-114">**grunt-contrib-watch**: A plugin that watches file activity.</span></span>

## <a name="preparing-the-application"></a><span data-ttu-id="2b512-115">Příprava aplikace</span><span class="sxs-lookup"><span data-stu-id="2b512-115">Preparing the application</span></span>

<span data-ttu-id="2b512-116">Chcete-li začít, nastavte novou prázdnou webovou aplikaci a přidejte ukázkové soubory jazyka TypeScript.</span><span class="sxs-lookup"><span data-stu-id="2b512-116">To begin, set up a new empty web application and add TypeScript example files.</span></span> <span data-ttu-id="2b512-117">Soubory TypeScript ukládají se automaticky do JavaScriptu pomocí výchozího nastavení sady Visual Studio a budou naší surovinou pro zpracování pomocí Grunt.</span><span class="sxs-lookup"><span data-stu-id="2b512-117">TypeScript files are automatically compiled into JavaScript using default Visual Studio settings and will be our raw material to process using Grunt.</span></span>

1. <span data-ttu-id="2b512-118">V sadě Visual Studio `ASP.NET Web Application`vytvořte nový .</span><span class="sxs-lookup"><span data-stu-id="2b512-118">In Visual Studio, create a new `ASP.NET Web Application`.</span></span>

2. <span data-ttu-id="2b512-119">V dialogovém **okně Nový ASP.NET projekt** vyberte šablonu ASP.NET Jádro **prázdné** a klepněte na tlačítko OK.</span><span class="sxs-lookup"><span data-stu-id="2b512-119">In the **New ASP.NET Project** dialog, select the ASP.NET Core **Empty** template and click the OK button.</span></span>

3. <span data-ttu-id="2b512-120">V Průzkumníku řešení zkontrolujte strukturu projektu.</span><span class="sxs-lookup"><span data-stu-id="2b512-120">In the Solution Explorer, review the project structure.</span></span> <span data-ttu-id="2b512-121">Složka `\src` obsahuje `wwwroot` prázdné `Dependencies` a uzly.</span><span class="sxs-lookup"><span data-stu-id="2b512-121">The `\src` folder includes empty `wwwroot` and `Dependencies` nodes.</span></span>

    ![prázdné webové řešení](using-grunt/_static/grunt-solution-explorer.png)

4. <span data-ttu-id="2b512-123">Přidejte do `TypeScript` adresáře projektu novou složku s názvem.</span><span class="sxs-lookup"><span data-stu-id="2b512-123">Add a new folder named `TypeScript` to your project directory.</span></span>

5. <span data-ttu-id="2b512-124">Před přidáním souborů se ujistěte, že visual studio má možnost "kompilace při uložení" pro soubory Typu Script zaškrtnuta.</span><span class="sxs-lookup"><span data-stu-id="2b512-124">Before adding any files, make sure that Visual Studio has the option 'compile on save' for TypeScript files checked.</span></span> <span data-ttu-id="2b512-125">Přejděte na**možnosti** >  **nástrojů** > **Textový editor** > **typescript** > **projektu**:</span><span class="sxs-lookup"><span data-stu-id="2b512-125">Navigate to **Tools** > **Options** > **Text Editor** > **Typescript** > **Project**:</span></span>

    ![volby nastavení automatické kompilace souborů jazyka TypeScript](using-grunt/_static/typescript-options.png)

6. <span data-ttu-id="2b512-127">Klepněte pravým `TypeScript` tlačítkem myši na adresář a z kontextové nabídky vyberte **Přidat > novou položku.**</span><span class="sxs-lookup"><span data-stu-id="2b512-127">Right-click the `TypeScript` directory and select **Add > New Item** from the context menu.</span></span> <span data-ttu-id="2b512-128">Vyberte položku **souboru JavaScript** a pojmenujte \*soubor *Tastes.ts* (všimněte si přípony .ts).</span><span class="sxs-lookup"><span data-stu-id="2b512-128">Select the **JavaScript file** item and name the file *Tastes.ts* (note the \*.ts extension).</span></span> <span data-ttu-id="2b512-129">Zkopírujte řádek kódu TypeScript u níže do souboru (při uložení se se zdrojem JavaScriptu zobrazí nový soubor *Tastes.js).*</span><span class="sxs-lookup"><span data-stu-id="2b512-129">Copy the line of TypeScript code below into the file (when you save, a new *Tastes.js* file will appear with the JavaScript source).</span></span>

    ```typescript
    enum Tastes { Sweet, Sour, Salty, Bitter }
    ```

7. <span data-ttu-id="2b512-130">Přidejte druhý soubor do **adresáře TypeScript** a pojmenujte jej `Food.ts`.</span><span class="sxs-lookup"><span data-stu-id="2b512-130">Add a second file to the **TypeScript** directory and name it `Food.ts`.</span></span> <span data-ttu-id="2b512-131">Zkopírujte níže uvedený kód do souboru.</span><span class="sxs-lookup"><span data-stu-id="2b512-131">Copy the code below into the file.</span></span>

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

## <a name="configuring-npm"></a><span data-ttu-id="2b512-132">Konfigurace npm</span><span class="sxs-lookup"><span data-stu-id="2b512-132">Configuring NPM</span></span>

<span data-ttu-id="2b512-133">Dále nakonfigurujte NPM ke stažení grunt a grunt-úkoly.</span><span class="sxs-lookup"><span data-stu-id="2b512-133">Next, configure NPM to download grunt and grunt-tasks.</span></span>

1. <span data-ttu-id="2b512-134">V Průzkumníku řešení klikněte pravým tlačítkem myši na projekt a z kontextové nabídky vyberte **Přidat > novou položku.**</span><span class="sxs-lookup"><span data-stu-id="2b512-134">In the Solution Explorer, right-click the project and select **Add > New Item** from the context menu.</span></span> <span data-ttu-id="2b512-135">Vyberte položku **konfiguračního souboru NPM,** ponechte výchozí název *package.json*a klepněte na tlačítko **Přidat.**</span><span class="sxs-lookup"><span data-stu-id="2b512-135">Select the **NPM configuration file** item, leave the default name, *package.json*, and click the **Add** button.</span></span>

2. <span data-ttu-id="2b512-136">V souboru *package.json* `devDependencies` zadejte uvnitř závorek objektu "grunt".</span><span class="sxs-lookup"><span data-stu-id="2b512-136">In the *package.json* file, inside the `devDependencies` object braces, enter "grunt".</span></span> <span data-ttu-id="2b512-137">Vyberte `grunt` ze seznamu Intellisense a stiskněte klávesu Enter.</span><span class="sxs-lookup"><span data-stu-id="2b512-137">Select `grunt` from the Intellisense list and press the Enter key.</span></span> <span data-ttu-id="2b512-138">Visual Studio bude citovat grunt název balíčku a přidat dvojtečku.</span><span class="sxs-lookup"><span data-stu-id="2b512-138">Visual Studio will quote the grunt package name, and add a colon.</span></span> <span data-ttu-id="2b512-139">Napravo od dvojtečky vyberte nejnovější stabilní verzi balíčku z horní části seznamu `Ctrl-Space` Intellisense (stiskněte, pokud se nezobrazí Intellisense).</span><span class="sxs-lookup"><span data-stu-id="2b512-139">To the right of the colon, select the latest stable version of the package from the top of the Intellisense list (press `Ctrl-Space` if Intellisense doesn't appear).</span></span>

    ![grunt Intellisense](using-grunt/_static/devdependencies-grunt.png)

    > [!NOTE]
    > <span data-ttu-id="2b512-141">NPM používá [sémantické správu verzí](https://semver.org/) k uspořádání závislostí.</span><span class="sxs-lookup"><span data-stu-id="2b512-141">NPM uses [semantic versioning](https://semver.org/) to organize dependencies.</span></span> <span data-ttu-id="2b512-142">Sémantické správu verzí, označované také jako SemVer, identifikuje balíčky s schématem \<číslování hlavní>. \<menší>. \<> náplasti.</span><span class="sxs-lookup"><span data-stu-id="2b512-142">Semantic versioning, also known as SemVer, identifies packages with the numbering scheme \<major>.\<minor>.\<patch>.</span></span> <span data-ttu-id="2b512-143">Technologie Intellisense zjednodušuje sémantickou správu verzí tím, že zobrazuje pouze několik běžných možností.</span><span class="sxs-lookup"><span data-stu-id="2b512-143">Intellisense simplifies semantic versioning by showing only a few common choices.</span></span> <span data-ttu-id="2b512-144">Horní položka v seznamu Intellisense (0.4.5 v příkladu výše) je považována za nejnovější stabilní verzi balíčku.</span><span class="sxs-lookup"><span data-stu-id="2b512-144">The top item in the Intellisense list (0.4.5 in the example above) is considered the latest stable version of the package.</span></span> <span data-ttu-id="2b512-145">Symbol stříšky (^) odpovídá nejnovější hlavní verzi a vlnovce (~) odpovídá nejnovější dílčí verzi.</span><span class="sxs-lookup"><span data-stu-id="2b512-145">The caret (^) symbol matches the most recent major version and the tilde (~) matches the most recent minor version.</span></span> <span data-ttu-id="2b512-146">Podívejte se na [npm semver verze analyzátor odkaz](https://www.npmjs.com/package/semver) jako vodítko k plné expresivity, které poskytuje SemVer.</span><span class="sxs-lookup"><span data-stu-id="2b512-146">See the [NPM semver version parser reference](https://www.npmjs.com/package/semver) as a guide to the full expressivity that SemVer provides.</span></span>

3. <span data-ttu-id="2b512-147">Přidejte další závislosti pro načtení balíčků\* grunt-contrib- pro *clean*, *jshint*, *concat*, *uglify*a *sledujte,* jak je znázorněno v příkladu níže.</span><span class="sxs-lookup"><span data-stu-id="2b512-147">Add more dependencies to load grunt-contrib-\* packages for *clean*, *jshint*, *concat*, *uglify*, and *watch* as shown in the example below.</span></span> <span data-ttu-id="2b512-148">Verze nemusí odpovídat příkladu.</span><span class="sxs-lookup"><span data-stu-id="2b512-148">The versions don't need to match the example.</span></span>

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

4. <span data-ttu-id="2b512-149">Uložte soubor *package.json.*</span><span class="sxs-lookup"><span data-stu-id="2b512-149">Save the *package.json* file.</span></span>

<span data-ttu-id="2b512-150">Balíčky pro `devDependencies` každou položku se stáhnou spolu se všemi soubory, které každý balíček vyžaduje.</span><span class="sxs-lookup"><span data-stu-id="2b512-150">The packages for each `devDependencies` item will download, along with any files that each package requires.</span></span> <span data-ttu-id="2b512-151">Soubory balíčků můžete najít v *node_modules* adresáři povolením tlačítka **Zobrazit všechny soubory** v **Průzkumníku řešení**.</span><span class="sxs-lookup"><span data-stu-id="2b512-151">You can find the package files in the *node_modules* directory by enabling the **Show All Files** button in **Solution Explorer**.</span></span>

![chrochtat node_modules](using-grunt/_static/node-modules.png)

> [!NOTE]
> <span data-ttu-id="2b512-153">Pokud potřebujete, můžete ručně obnovit závislosti v **Průzkumníku řešení** `Dependencies\NPM` kliknutím pravým tlačítkem myši na a výběrem možnosti nabídky **Obnovit balíčky.**</span><span class="sxs-lookup"><span data-stu-id="2b512-153">If you need to, you can manually restore dependencies in **Solution Explorer** by right-clicking on `Dependencies\NPM` and selecting the **Restore Packages** menu option.</span></span>

![obnovení balíčků](using-grunt/_static/restore-packages.png)

## <a name="configuring-grunt"></a><span data-ttu-id="2b512-155">Konfigurace Grunt</span><span class="sxs-lookup"><span data-stu-id="2b512-155">Configuring Grunt</span></span>

<span data-ttu-id="2b512-156">Grunt je konfigurován pomocí manifestu s názvem *Gruntfile.js,* který definuje, načte a registruje úlohy, které lze spustit ručně nebo nakonfigurovat tak, aby se spouštěly automaticky na základě událostí v sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="2b512-156">Grunt is configured using a manifest named *Gruntfile.js* that defines, loads and registers tasks that can be run manually or configured to run automatically based on events in Visual Studio.</span></span>

1. <span data-ttu-id="2b512-157">Klepněte pravým tlačítkem myši na projekt a vyberte **přidat** > **novou položku**.</span><span class="sxs-lookup"><span data-stu-id="2b512-157">Right-click the project and select **Add** > **New Item**.</span></span> <span data-ttu-id="2b512-158">Vyberte šablonu **položky souboru JavaScriptu,** změňte název na *Gruntfile.js*a klepněte na tlačítko **Přidat.**</span><span class="sxs-lookup"><span data-stu-id="2b512-158">Select the **JavaScript File** item template, change the name to *Gruntfile.js*, and click the **Add** button.</span></span>

1. <span data-ttu-id="2b512-159">Přidejte následující kód do *souboru Gruntfile.js*.</span><span class="sxs-lookup"><span data-stu-id="2b512-159">Add the following code to *Gruntfile.js*.</span></span> <span data-ttu-id="2b512-160">Funkce `initConfig` nastaví možnosti pro každý balíček a zbytek modulu načte a zaregistruje úkoly.</span><span class="sxs-lookup"><span data-stu-id="2b512-160">The `initConfig` function sets options for each package, and the remainder of the module loads and register tasks.</span></span>

   ```javascript
   module.exports = function (grunt) {
     grunt.initConfig({
     });
   };
   ```

1. <span data-ttu-id="2b512-161">Uvnitř `initConfig` funkce přidejte možnosti pro úlohu, `clean` jak je znázorněno v příkladu *Gruntfile.js* níže.</span><span class="sxs-lookup"><span data-stu-id="2b512-161">Inside the `initConfig` function, add options for the `clean` task as shown in the example *Gruntfile.js* below.</span></span> <span data-ttu-id="2b512-162">Úloha `clean` přijímá pole adresářových řetězců.</span><span class="sxs-lookup"><span data-stu-id="2b512-162">The `clean` task accepts an array of directory strings.</span></span> <span data-ttu-id="2b512-163">Tato úloha odebere soubory z *wwwroot/lib* a odebere celý *adresář /temp.*</span><span class="sxs-lookup"><span data-stu-id="2b512-163">This task removes files from *wwwroot/lib* and removes the entire */temp* directory.</span></span>

    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
        clean: ["wwwroot/lib/*", "temp/"],
      });
    };
    ```

1. <span data-ttu-id="2b512-164">Pod `initConfig` funkci přidejte volání `grunt.loadNpmTasks`do aplikace .</span><span class="sxs-lookup"><span data-stu-id="2b512-164">Below the `initConfig` function, add a call to `grunt.loadNpmTasks`.</span></span> <span data-ttu-id="2b512-165">Tím bude úloha spustitelné z visual studia.</span><span class="sxs-lookup"><span data-stu-id="2b512-165">This will make the task runnable from Visual Studio.</span></span>

    ```javascript
    grunt.loadNpmTasks("grunt-contrib-clean");
    ```

1. <span data-ttu-id="2b512-166">Uložit *Gruntfile.js*.</span><span class="sxs-lookup"><span data-stu-id="2b512-166">Save *Gruntfile.js*.</span></span> <span data-ttu-id="2b512-167">Soubor by měl vypadat podobně jako snímek obrazovky níže.</span><span class="sxs-lookup"><span data-stu-id="2b512-167">The file should look something like the screenshot below.</span></span>

    ![počáteční gruntfile](using-grunt/_static/gruntfile-js-initial.png)

1. <span data-ttu-id="2b512-169">Klikněte pravým tlačítkem myši na *soubor Gruntfile.js* a z kontextové nabídky vyberte **Průzkumníka průzkumníka úloh.**</span><span class="sxs-lookup"><span data-stu-id="2b512-169">Right-click *Gruntfile.js* and select **Task Runner Explorer** from the context menu.</span></span> <span data-ttu-id="2b512-170">Otevře se okno **Průzkumníka plánovače úloh.**</span><span class="sxs-lookup"><span data-stu-id="2b512-170">The **Task Runner Explorer** window will open.</span></span>

    ![nabídka průzkumníka posuzujícíúlohy úloh](using-grunt/_static/task-runner-explorer-menu.png)

1. <span data-ttu-id="2b512-172">Ověřte, že `clean` se zobrazuje v části **Úkoly** v **Průzkumníku průzkumníka plánovače úloh**.</span><span class="sxs-lookup"><span data-stu-id="2b512-172">Verify that `clean` shows under **Tasks** in the **Task Runner Explorer**.</span></span>

    ![seznam úloh průzkumníka posuzování úloh](using-grunt/_static/task-runner-explorer-tasks.png)

1. <span data-ttu-id="2b512-174">Klepněte pravým tlačítkem myši na čistou úlohu a z kontextové nabídky vyberte **Spustit.**</span><span class="sxs-lookup"><span data-stu-id="2b512-174">Right-click the clean task and select **Run** from the context menu.</span></span> <span data-ttu-id="2b512-175">Příkazové okno zobrazuje průběh úkolu.</span><span class="sxs-lookup"><span data-stu-id="2b512-175">A command window displays progress of the task.</span></span>

    ![průzkumník úloh spustit čistou úlohu](using-grunt/_static/task-runner-explorer-run-clean.png)

    > [!NOTE]
    > <span data-ttu-id="2b512-177">Zatím nejsou k dispozici žádné soubory ani adresáře, které by bylo možné vyčistit.</span><span class="sxs-lookup"><span data-stu-id="2b512-177">There are no files or directories to clean yet.</span></span> <span data-ttu-id="2b512-178">Pokud chcete, můžete je ručně vytvořit v Průzkumníku řešení a potom spustit čistou úlohu jako test.</span><span class="sxs-lookup"><span data-stu-id="2b512-178">If you like, you can manually create them in the Solution Explorer and then run the clean task as a test.</span></span>

1. <span data-ttu-id="2b512-179">Do `initConfig` funkce přidejte položku pro `concat` použití níže uvedeného kódu.</span><span class="sxs-lookup"><span data-stu-id="2b512-179">In the `initConfig` function, add an entry for `concat` using the code below.</span></span>

    <span data-ttu-id="2b512-180">Pole `src` vlastností obsahuje seznam souborů, které mají být kombinovány, v pořadí, v jakém by měly být kombinovány.</span><span class="sxs-lookup"><span data-stu-id="2b512-180">The `src` property array lists files to combine, in the order that they should be combined.</span></span> <span data-ttu-id="2b512-181">Vlastnost `dest` přiřadí cestu k kombinovanému souboru, který je vytvořen.</span><span class="sxs-lookup"><span data-stu-id="2b512-181">The `dest` property assigns the path to the combined file that's produced.</span></span>

    ```javascript
    concat: {
      all: {
        src: ['TypeScript/Tastes.js', 'TypeScript/Food.js'],
        dest: 'temp/combined.js'
      }
    },
    ```

    > [!NOTE]
    > <span data-ttu-id="2b512-182">Vlastnost `all` ve výše uvedeném kódu je název cíle.</span><span class="sxs-lookup"><span data-stu-id="2b512-182">The `all` property in the code above is the name of a target.</span></span> <span data-ttu-id="2b512-183">Cíle se používají v některých úlohách Grunt povolit více prostředí sestavení.</span><span class="sxs-lookup"><span data-stu-id="2b512-183">Targets are used in some Grunt tasks to allow multiple build environments.</span></span> <span data-ttu-id="2b512-184">Integrované cíle můžete zobrazit pomocí technologie IntelliSense nebo přiřadit vlastní.</span><span class="sxs-lookup"><span data-stu-id="2b512-184">You can view the built-in targets using IntelliSense or assign your own.</span></span>

1. <span data-ttu-id="2b512-185">Přidejte `jshint` úkol pomocí níže uvedeného kódu.</span><span class="sxs-lookup"><span data-stu-id="2b512-185">Add the `jshint` task using the code below.</span></span>

    <span data-ttu-id="2b512-186">Nástroj jshint `code-quality` je spuštěn proti každému souboru JavaScriptu, který se nachází v *dočasném* adresáři.</span><span class="sxs-lookup"><span data-stu-id="2b512-186">The jshint `code-quality` utility is run against every JavaScript file found in the *temp* directory.</span></span>

    ```javascript
    jshint: {
      files: ['temp/*.js'],
      options: {
        '-W069': false,
      }
    },
    ```

    > [!NOTE]
    > <span data-ttu-id="2b512-187">Možnost "-W069" je chyba vytvořená jshintem, když JavaScript používá syntaxi závorek `Tastes["Sweet"]` k `Tastes.Sweet`přiřazení vlastnosti namísto tečkového zápisu, tj.</span><span class="sxs-lookup"><span data-stu-id="2b512-187">The option "-W069" is an error produced by jshint when JavaScript uses bracket syntax to assign a property instead of dot notation, i.e. `Tastes["Sweet"]` instead of `Tastes.Sweet`.</span></span> <span data-ttu-id="2b512-188">Tato možnost vypne upozornění, aby zbytek procesu mohl pokračovat.</span><span class="sxs-lookup"><span data-stu-id="2b512-188">The option turns off the warning to allow the rest of the process to continue.</span></span>

1. <span data-ttu-id="2b512-189">Přidejte `uglify` úkol pomocí níže uvedeného kódu.</span><span class="sxs-lookup"><span data-stu-id="2b512-189">Add the `uglify` task using the code below.</span></span>

    <span data-ttu-id="2b512-190">Úloha minifikuje soubor *combined.js* nalezený v dočasném adresáři a vytvoří výsledný soubor v souboru wwwroot/lib podle standardního \* \<názvu\>souboru konvence _min.js\*.</span><span class="sxs-lookup"><span data-stu-id="2b512-190">The task minifies the *combined.js* file found in the temp directory and creates the result file in wwwroot/lib following the standard naming convention *\<file name\>.min.js*.</span></span>

    ```javascript
    uglify: {
     all: {
       src: ['temp/combined.js'],
       dest: 'wwwroot/lib/combined.min.js'
     }
    },
    ```

1. <span data-ttu-id="2b512-191">Pod volání, `grunt.loadNpmTasks` které `grunt-contrib-clean`načte , zahrnout stejné volání pro jshint, concat a uglify pomocí níže uvedeného kódu.</span><span class="sxs-lookup"><span data-stu-id="2b512-191">Under the call to `grunt.loadNpmTasks` that loads `grunt-contrib-clean`, include the same call for jshint, concat, and uglify using the code below.</span></span>

    ```javascript
    grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    ```

1. <span data-ttu-id="2b512-192">Uložit *Gruntfile.js*.</span><span class="sxs-lookup"><span data-stu-id="2b512-192">Save *Gruntfile.js*.</span></span> <span data-ttu-id="2b512-193">Soubor by měl vypadat podobně jako v níže uvedeném příkladu.</span><span class="sxs-lookup"><span data-stu-id="2b512-193">The file should look something like the example below.</span></span>

    ![kompletní příklad grunt souboru](using-grunt/_static/gruntfile-js-complete.png)

1. <span data-ttu-id="2b512-195">Všimněte si, že seznam `clean` `concat`Úlohy `uglify` **průzkumníka plánovače úloh** obsahuje aplikace , `jshint` a úkoly.</span><span class="sxs-lookup"><span data-stu-id="2b512-195">Notice that the **Task Runner Explorer** Tasks list includes `clean`, `concat`, `jshint` and `uglify` tasks.</span></span> <span data-ttu-id="2b512-196">Spusťte každou úlohu v pořadí a sledujte výsledky v **Průzkumníku řešení**.</span><span class="sxs-lookup"><span data-stu-id="2b512-196">Run each task in order and observe the results in **Solution Explorer**.</span></span> <span data-ttu-id="2b512-197">Každá úloha by měla být spuštěna bez chyb.</span><span class="sxs-lookup"><span data-stu-id="2b512-197">Each task should run without errors.</span></span>

    ![Průzkumník průzkumníka úloh spustit každou úlohu](using-grunt/_static/task-runner-explorer-run-each-task.png)

    <span data-ttu-id="2b512-199">Úloha concat vytvoří nový soubor *combined.js* a umístí jej do dočasného adresáře.</span><span class="sxs-lookup"><span data-stu-id="2b512-199">The concat task creates a new *combined.js* file and places it into the temp directory.</span></span> <span data-ttu-id="2b512-200">Úloha `jshint` se jednoduše spustí a nevytváří výstup.</span><span class="sxs-lookup"><span data-stu-id="2b512-200">The `jshint` task simply runs and doesn't produce output.</span></span> <span data-ttu-id="2b512-201">Úloha `uglify` vytvoří nový soubor *combined.min.js* a umístí jej do *wwwroot/lib*.</span><span class="sxs-lookup"><span data-stu-id="2b512-201">The `uglify` task creates a new *combined.min.js* file and places it into *wwwroot/lib*.</span></span> <span data-ttu-id="2b512-202">Po dokončení by řešení mělo vypadat podobně jako na následujícím snímku obrazovky:</span><span class="sxs-lookup"><span data-stu-id="2b512-202">On completion, the solution should look something like the screenshot below:</span></span>

    ![průzkumník řešení po všech úkolech](using-grunt/_static/solution-explorer-after-all-tasks.png)

    > [!NOTE]
    > <span data-ttu-id="2b512-204">Další informace o možnostech každého [https://www.npmjs.com/](https://www.npmjs.com/) balíčku naleznete a vyhledejte název balíčku ve vyhledávacím poli na hlavní stránce.</span><span class="sxs-lookup"><span data-stu-id="2b512-204">For more information on the options for each package, visit [https://www.npmjs.com/](https://www.npmjs.com/) and lookup the package name in the search box on the main page.</span></span> <span data-ttu-id="2b512-205">Můžete například vyhledat balíček grunt-contrib-clean a získat odkaz na dokumentaci, který vysvětluje všechny jeho parametry.</span><span class="sxs-lookup"><span data-stu-id="2b512-205">For example, you can look up the grunt-contrib-clean package to get a documentation link that explains all of its parameters.</span></span>

### <a name="all-together-now"></a><span data-ttu-id="2b512-206">Teď všechno dohromady</span><span class="sxs-lookup"><span data-stu-id="2b512-206">All together now</span></span>

<span data-ttu-id="2b512-207">Pomocí metody Grunt `registerTask()` spusťte řadu úloh v určitém pořadí.</span><span class="sxs-lookup"><span data-stu-id="2b512-207">Use the Grunt `registerTask()` method to run a series of tasks in a particular sequence.</span></span> <span data-ttu-id="2b512-208">Chcete-li například spustit výše uvedené příkladové kroky v pořadí clean -> concat -> jshint -> uglify, přidejte níže uvedený kód do modulu.</span><span class="sxs-lookup"><span data-stu-id="2b512-208">For example, to run the example steps above in the order clean -> concat -> jshint -> uglify, add the code below to the module.</span></span> <span data-ttu-id="2b512-209">Kód by měl být přidán na stejnou úroveň jako volání loadNpmTasks() mimo initConfig.</span><span class="sxs-lookup"><span data-stu-id="2b512-209">The code should be added to the same level as the loadNpmTasks() calls, outside initConfig.</span></span>

```javascript
grunt.registerTask("all", ['clean', 'concat', 'jshint', 'uglify']);
```

<span data-ttu-id="2b512-210">Nový úkol se zobrazí v Průzkumníkovi úloh v části Alias úkoly.</span><span class="sxs-lookup"><span data-stu-id="2b512-210">The new task shows up in Task Runner Explorer under Alias Tasks.</span></span> <span data-ttu-id="2b512-211">Můžete klepnout pravým tlačítkem myši a spustit jej stejně jako ostatní úkoly.</span><span class="sxs-lookup"><span data-stu-id="2b512-211">You can right-click and run it just as you would other tasks.</span></span> <span data-ttu-id="2b512-212">Úloha `all` bude `clean` `concat`spuštěna `uglify`v `jshint` pořadí .</span><span class="sxs-lookup"><span data-stu-id="2b512-212">The `all` task will run `clean`, `concat`, `jshint` and `uglify`, in order.</span></span>

![alias grunt úkoly](using-grunt/_static/alias-tasks.png)

## <a name="watching-for-changes"></a><span data-ttu-id="2b512-214">Sledování změn</span><span class="sxs-lookup"><span data-stu-id="2b512-214">Watching for changes</span></span>

<span data-ttu-id="2b512-215">Úkol `watch` sleduje soubory a adresáře.</span><span class="sxs-lookup"><span data-stu-id="2b512-215">A `watch` task keeps an eye on files and directories.</span></span> <span data-ttu-id="2b512-216">Hodinky spustí úlohy automaticky, pokud zjistí změny.</span><span class="sxs-lookup"><span data-stu-id="2b512-216">The watch triggers tasks automatically if it detects changes.</span></span> <span data-ttu-id="2b512-217">Přidejte níže uvedený kód do initConfig a sledujte změny souborů \*JS v adresáři TypeScript.</span><span class="sxs-lookup"><span data-stu-id="2b512-217">Add the code below to initConfig to watch for changes to \*.js files in the TypeScript directory.</span></span> <span data-ttu-id="2b512-218">Pokud dojde ke změně `watch` souboru `all` JavaScriptu, spustí se úloha.</span><span class="sxs-lookup"><span data-stu-id="2b512-218">If a JavaScript file is changed, `watch` will run the `all` task.</span></span>

```javascript
watch: {
  files: ["TypeScript/*.js"],
  tasks: ["all"]
}
```

<span data-ttu-id="2b512-219">Přidáním volání `loadNpmTasks()` zobrazíte `watch` úkol v Průzkumníkovi úloh.</span><span class="sxs-lookup"><span data-stu-id="2b512-219">Add a call to `loadNpmTasks()` to show the `watch` task in Task Runner Explorer.</span></span>

```javascript
grunt.loadNpmTasks('grunt-contrib-watch');
```

<span data-ttu-id="2b512-220">Klikněte pravým tlačítkem myši na úlohu sledování v Průzkumníkovi běhu úloh a z kontextové nabídky vyberte Spustit.</span><span class="sxs-lookup"><span data-stu-id="2b512-220">Right-click the watch task in Task Runner Explorer and select Run from the context menu.</span></span> <span data-ttu-id="2b512-221">Ve příkazovém okně, které zobrazuje spuštěnou úlohu sledování, se zobrazí "Čekání..." Zprávu.</span><span class="sxs-lookup"><span data-stu-id="2b512-221">The command window that shows the watch task running will display a "Waiting…" message.</span></span> <span data-ttu-id="2b512-222">Otevřete jeden ze souborů TypeScriptu, přidejte mezeru a uložte soubor.</span><span class="sxs-lookup"><span data-stu-id="2b512-222">Open one of the TypeScript files, add a space, and then save the file.</span></span> <span data-ttu-id="2b512-223">Tím se spustí úloha sledování a ostatní úlohy spustit v pořadí.</span><span class="sxs-lookup"><span data-stu-id="2b512-223">This will trigger the watch task and trigger the other tasks to run in order.</span></span> <span data-ttu-id="2b512-224">Snímek obrazovky níže ukazuje ukázkový běh.</span><span class="sxs-lookup"><span data-stu-id="2b512-224">The screenshot below shows a sample run.</span></span>

![spuštění výstupu úloh](using-grunt/_static/watch-running.png)

## <a name="binding-to-visual-studio-events"></a><span data-ttu-id="2b512-226">Vazba na události sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2b512-226">Binding to Visual Studio events</span></span>

<span data-ttu-id="2b512-227">Pokud nechcete ručně spustit úkoly při každé práci v sadě Visual Studio, svázat úkoly **před sestavením**, **po sestavení**, **vyčistit**a **otevřít projekt** události.</span><span class="sxs-lookup"><span data-stu-id="2b512-227">Unless you want to manually start your tasks every time you work in Visual Studio, bind tasks to **Before Build**, **After Build**, **Clean**, and **Project Open** events.</span></span>

<span data-ttu-id="2b512-228">Bind `watch` tak, aby se spustí při každém otevření sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="2b512-228">Bind `watch` so that it runs every time Visual Studio opens.</span></span> <span data-ttu-id="2b512-229">V Průzkumníku úloh y klikněte pravým tlačítkem myši na úkol sledování a z kontextové nabídky vyberte **možnost Vazby** > **projektu Otevřít.**</span><span class="sxs-lookup"><span data-stu-id="2b512-229">In Task Runner Explorer, right-click the watch task and select **Bindings** > **Project Open** from the context menu.</span></span>

![svázat úkol s otevřením projektu](using-grunt/_static/bindings-project-open.png)

<span data-ttu-id="2b512-231">Uvolnění a opětovné načtení projektu.</span><span class="sxs-lookup"><span data-stu-id="2b512-231">Unload and reload the project.</span></span> <span data-ttu-id="2b512-232">Při znovunačtení projektu se úloha sledování spustí automaticky.</span><span class="sxs-lookup"><span data-stu-id="2b512-232">When the project loads again, the watch task starts running automatically.</span></span>

## <a name="summary"></a><span data-ttu-id="2b512-233">Souhrn</span><span class="sxs-lookup"><span data-stu-id="2b512-233">Summary</span></span>

<span data-ttu-id="2b512-234">Grunt je výkonný plánovač úloh, který lze použít k automatizaci většiny úloh sestavení klienta.</span><span class="sxs-lookup"><span data-stu-id="2b512-234">Grunt is a powerful task runner that can be used to automate most client-build tasks.</span></span> <span data-ttu-id="2b512-235">Grunt využívá NPM k doručování svých balíčků a nabízí integraci nástrojů s Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="2b512-235">Grunt leverages NPM to deliver its packages, and features tooling integration with Visual Studio.</span></span> <span data-ttu-id="2b512-236">Průzkumník Průzkumník úloh sady Visual Studio detekuje změny konfiguračních souborů a poskytuje pohodlné rozhraní pro spouštění úloh, zobrazení spuštěných úloh a svázání úloh s událostmi sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="2b512-236">Visual Studio's Task Runner Explorer detects changes to configuration files and provides a convenient interface to run tasks, view running tasks, and bind tasks to Visual Studio events.</span></span>
